# Designing advanced profiles

In this advanced example, we iteratively refactor our basic roles and profiles example to handle real-world concerns. The final result is — with only minor differences — the Jenkins profile we use in production here at Puppet.

Along the way, we explain our choices and point out some of the common trade-offs you encounter as you design your own profiles.

Here's the basic Jenkins profile we're starting with:

```
# /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
class profile::jenkins::master (
  String $jenkins_port = '9091',
  String $java_dist    = 'jdk',
  String $java_version = 'latest',
) {

  class { 'jenkins':
    configure_firewall => true,
    install_java       => false,
    port               => $jenkins_port,
    config_hash        => {
      'HTTP_PORT'    => { 'value' => $jenkins_port },
      'JENKINS_PORT' => { 'value' => $jenkins_port },
    },
  }

  class { 'java':
    distribution => $java_dist,
    version      => $java_version,
    before       => Class['jenkins'],
  }
}

```

**Parent topic:**[Designing system configs: roles and profiles](designing_system_configs_roles_and_profiles.md)

**Related information**  


[Rules for profile classes](the_roles_and_profiles_method.md#)

## First refactor: Split out Java

We want to manage Jenkins masters *and* Jenkins agent nodes. We won't cover agent profiles in detail, but the first issue we encountered is that they also need Java.

We could copy and paste the Java class declaration; it's small, so keeping multiple copies up-to-date might not be too burdensome. But instead, we decided to break Java out into a separate profile. This way we can manage it one time, then include the Java profile in both the agent and master profiles.

**Note:** This is a common trade-off. Keeping a chunk of code in only one place \(often called the DRY — "don't repeat yourself" — principle\) makes it more maintainable and less vulnerable to rot. But it has a cost: your individual profile classes become less readable, and you must view more files to see what a profile actually does. To reduce that readability cost, try to break code out in units that make inherent sense. In this case, the Java profile's job is simple enough to guess by its name — your colleagues don't have to read its code to know that it manages Java 8. Comments can also help.

First, decide how configurable Java needs to be on Jenkins machines. After looking at our past usage, we realized that we use only two options: either we install Oracle's Java 8 distribution, or we default to OpenJDK 7, which the Jenkins module manages. This means we can:

-   Make our new Java profile really simple: hardcode Java 8 and take no configuration.
-   Replace the two Java parameters from `profile::jenkins::master` with one Boolean parameter \(whether to let Jenkins handle Java\).

**Note:** This is rule 4 in action. We reduce our profile's configuration surface by combining multiple questions into one.

Here's the new parameter list:

```puppet
class profile::jenkins::master (
  String  $jenkins_port = '9091',
  Boolean $install_jenkins_java = true,
) { # ...
```

And here's how we choose which Java to use:

```puppet
  class { 'jenkins':
    configure_firewall => true,
    install_java       => $install_jenkins_java,    # <--- here
    port               => $jenkins_port,
    config_hash        => {
      'HTTP_PORT'    => { 'value' => $jenkins_port },
      'JENKINS_PORT' => { 'value' => $jenkins_port },
    },
  }

  # When not using the jenkins module's java version, install java8.
  unless $install_jenkins_java  { include profile::jenkins::usage::java8 }
```

And our new Java profile:

```
::jenkins::usage::java8
# Sets up java8 for Jenkins on Debian
#
class profile::jenkins::usage::java8 {
  motd::register { 'Java usage profile (profile::jenkins::usage::java8)': }

  # OpenJDK 7 is already managed by the Jenkins module.
  # ::jenkins::install_java or ::jenkins::agent::install_java should be false to use this profile
  # this can be set through the class parameter $install_jenkins_java
  case $::osfamily {
    'debian': {
      class { 'java':
        distribution => 'oracle-jdk8',
        version      => '8u92',
      }

      package { 'tzdata-java':
        ensure => latest,
      }
    }
    default: {
      notify { "profile::jenkins::usage::java8 cannot set up JDK on ${::osfamily}": }
```

### Diff of first refactor

```
@@ -1,13 +1,12 @@
 # /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
 class profile::jenkins::master (
-  String $jenkins_port = '9091',
-  String $java_dist    = 'jdk',
-  String $java_version = 'latest',
+  String  $jenkins_port = '9091',
+  Boolean $install_jenkins_java = true,
 ) {

   class { 'jenkins':
     configure_firewall => true,
-    install_java       => false,
+    install_java       => $install_jenkins_java,
     port               => $jenkins_port,
     config_hash        => {
       'HTTP_PORT'    => { 'value' => $jenkins_port },
@@ -15,9 +14,6 @@ class profile::jenkins::master (
     },
   }

-  class { 'java':
-    distribution => $java_dist,
-    version      => $java_version,
-    before       => Class['jenkins'],
-  }
+  # When not using the jenkins module's java version, install java8.
+  unless $install_jenkins_java  { include profile::jenkins::usage::java8 }
 }
```

## Second refactor: Manage the heap

At Puppet, we manage the Java heap size for the Jenkins app. Production servers didn't have enough memory for heavy use.

The Jenkins module has a `jenkins::sysconfig` defined type for managing system properties, so let's use it:

```puppet
  # Manage the heap size on the master, in MB.
  if($::memorysize_mb =~ Number and $::memorysize_mb > 8192)
  {
    # anything over 8GB we should keep max 4GB for OS and others
    $heap = sprintf('%.0f', $::memorysize_mb - 4096)
  } else {
    # This is calculated as 50% of the total memory.
    $heap = sprintf('%.0f', $::memorysize_mb * 0.5)
  }
  # Set java params, like heap min and max sizes. See
  # https://wiki.jenkins-ci.org/display/JENKINS/Features+controlled+by+system+properties
  jenkins::sysconfig { 'JAVA_ARGS':
    value => "-Xms${heap}m -Xmx${heap}m -Djava.awt.headless=true -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -Dhudson.model.DirectoryBrowserSupport.CSP=\\\"default-src 'self'; img-src 'self'; style-src 'self';\\\"",
  }
```

**Note:** Rule 4 again — we couldn't hardcode this, because we have some smaller Jenkins masters that can't spare the extra memory. But because our production masters are always on more powerful machines, we can calculate the heap based on the machine's memory size, which we can access as a fact. This lets us avoid extra configuration.

### Diff of second refactor

```
@@ -16,4 +16,20 @@ class profile::jenkins::master (

   # When not using the jenkins module's java version, install java8.
   unless $install_jenkins_java  { include profile::jenkins::usage::java8 }
+
+  # Manage the heap size on the master, in MB.
+  if($::memorysize_mb =~ Number and $::memorysize_mb > 8192)
+  {
+    # anything over 8GB we should keep max 4GB for OS and others
+    $heap = sprintf('%.0f', $::memorysize_mb - 4096)
+  } else {
+    # This is calculated as 50% of the total memory.
+    $heap = sprintf('%.0f', $::memorysize_mb * 0.5)
+  }
+  # Set java params, like heap min and max sizes. See
+  # https://wiki.jenkins-ci.org/display/JENKINS/Features+controlled+by+system+properties
+  jenkins::sysconfig { 'JAVA_ARGS':
+    value => "-Xms${heap}m -Xmx${heap}m -Djava.awt.headless=true -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -Dhudson.model.DirectoryBrowserSupport.CSP=\\\"default-src 'self'; img-src 'self'; style-src 'self';\\\"",
+  }
+
 }

```

## Third refactor: Pin the version

We dislike surprise upgrades, so we pin Jenkins to a specific version. We do this with a direct package URL instead of by adding Jenkins to our internal package repositories. Your organization might choose to do it differently.

First, we add a parameter to control upgrades. Now we can set a new value in `.../data/groups/ci/dev.yaml` while leaving `.../data/groups/ci.yaml` alone — our dev machines get the new Jenkins version first, and we can ensure everything works as expected before upgrading our prod machines.

```puppet
class profile::jenkins::master (
  Variant[String[1], Boolean] $direct_download = 'http://pkg.jenkins-ci.org/debian-stable/binary/jenkins_1.642.2_all.deb',
  # ...
) { # ...
```

Then, we set the necessary parameters in the Jenkins class:

```puppet
  class { 'jenkins':
    lts                => true,                    # <-- here
    repo               => true,                    # <-- here
    direct_download    => $direct_download,        # <-- here
    version            => 'latest',                # <-- here
    service_enable     => true,
    service_ensure     => running,
    configure_firewall => true,
    install_java       => $install_jenkins_java,
    port               => $jenkins_port,
    config_hash        => {
      'HTTP_PORT'    => { 'value' => $jenkins_port },
      'JENKINS_PORT' => { 'value' => $jenkins_port },
    },
  }
```

This was a good time to explicitly manage the Jenkins *service,* so we did that as well.

### Diff of third refactor

```
@@ -1,10 +1,17 @@
 # /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
 class profile::jenkins::master (
-  String  $jenkins_port = '9091',
-  Boolean $install_jenkins_java = true,
+  String                      $jenkins_port = '9091',
+  Variant[String[1], Boolean] $direct_download = 'http://pkg.jenkins-ci.org/debian-stable/binary/jenkins_1.642.2_all.deb',
+  Boolean                     $install_jenkins_java = true,
 ) {

   class { 'jenkins':
+    lts                => true,
+    repo               => true,
+    direct_download    => $direct_download,
+    version            => 'latest',
+    service_enable     => true,
+    service_ensure     => running,
     configure_firewall => true,
     install_java       => $install_jenkins_java,
     port               => $jenkins_port,
```

## Fourth refactor: Manually manage the user account

We manage a lot of user accounts in our infrastructure, so we handle them in a unified way. The `profile::server` class pulls in `virtual::users`, which has a lot of virtual resources we can selectively realize depending on who needs to log into a given machine.

**Note:** This has a cost — it's action at a distance, and you need to read more files to see which users are enabled for a given profile. But we decided the benefit was worth it: because all user accounts are written in one or two files, it's easy to see all the users that might exist, and ensure that they're managed consistently.

We're accepting difficulty in one place \(where we can comfortably handle it\) to banish difficulty in another place \(where we worry it would get out of hand\). Making this choice required that we know our colleagues and their comfort zones, and that we know the limitations of our existing code base and supporting services.

So, for this example, we change the Jenkins profile to work the same way; we manage the `jenkins` user alongside the rest of our user accounts. While we're doing that, we also manage a few directories that can be problematic depending on how Jenkins is packaged.

Some values we need are used by Jenkins agents as well as masters, so we're going to store them in a params class, which is a class that sets shared variables and manages no resources. This is a heavyweight solution, so wait until it provides real value before using it. In our case, we had a lot of OS-specific agent profiles \(not shown in these examples\), and they made a params class worthwhile.

**Note:** Just as before, "don't repeat yourself" is in tension with "keep it readable." Find the balance that works for you.

```puppet
  # We rely on virtual resources that are ultimately declared by profile::server.
  include profile::server

  # Some default values that vary by OS:
  include profile::jenkins::params
  $jenkins_owner          = $profile::jenkins::params::jenkins_owner
  $jenkins_group          = $profile::jenkins::params::jenkins_group
  $master_config_dir      = $profile::jenkins::params::master_config_dir

  file { '/var/run/jenkins': ensure => 'directory' }

  # Because our account::user class manages the '${master_config_dir}' directory
  # as the 'jenkins' user's homedir (as it should), we need to manage
  # `${master_config_dir}/plugins` here to prevent the upstream
  # rtyler-jenkins module from trying to manage the homedir as the config
  # dir. For more info, see the upstream module's `manifests/plugin.pp`
  # manifest.
  file { "${master_config_dir}/plugins":
    ensure  => directory,
    owner   => $jenkins_owner,
    group   => $jenkins_group,
    mode    => '0755',
    require => [Group[$jenkins_group], User[$jenkins_owner]],
  }

  Account::User <| tag == 'jenkins' |>

  class { 'jenkins':
    lts                => true,
    repo               => true,
    direct_download    => $direct_download,
    version            => 'latest',
    service_enable     => true,
    service_ensure     => running,
    configure_firewall => true,
    install_java       => $install_jenkins_java,
    manage_user        => false,                    # <-- here
    manage_group       => false,                    # <-- here
    manage_datadirs    => false,                    # <-- here
    port               => $jenkins_port,
    config_hash        => {
      'HTTP_PORT'    => { 'value' => $jenkins_port },
      'JENKINS_PORT' => { 'value' => $jenkins_port },
    },
  }
```

Three things to notice in the code above:

-   We manage users with a homegrown `account::user` defined type, which declares a `user` resource plus a few other things.
-   We use an `Account::User` resource collector to realize the Jenkins user. This relies on `profile::server` being declared.
-   We set the Jenkins class's `manage_user`, `manage_group`, and `manage_datadirs` parameters to false.
-   We're now explicitly managing the `plugins` directory and the `run` directory.

### Diff of fourth refactor

```
@@ -5,6 +5,33 @@ class profile::jenkins::master (
   Boolean                     $install_jenkins_java = true,
 ) {

+  # We rely on virtual resources that are ultimately declared by profile::server.
+  include profile::server
+
+  # Some default values that vary by OS:
+  include profile::jenkins::params
+  $jenkins_owner          = $profile::jenkins::params::jenkins_owner
+  $jenkins_group          = $profile::jenkins::params::jenkins_group
+  $master_config_dir      = $profile::jenkins::params::master_config_dir
+
+  file { '/var/run/jenkins': ensure => 'directory' }
+
+  # Because our account::user class manages the '${master_config_dir}' directory
+  # as the 'jenkins' user's homedir (as it should), we need to manage
+  # `${master_config_dir}/plugins` here to prevent the upstream
+  # rtyler-jenkins module from trying to manage the homedir as the config
+  # dir. For more info, see the upstream module's `manifests/plugin.pp`
+  # manifest.
+  file { "${master_config_dir}/plugins":
+    ensure  => directory,
+    owner   => $jenkins_owner,
+    group   => $jenkins_group,
+    mode    => '0755',
+    require => [Group[$jenkins_group], User[$jenkins_owner]],
+  }
+
+  Account::User <| tag == 'jenkins' |>
+
   class { 'jenkins':
     lts                => true,
     repo               => true,
@@ -14,6 +41,9 @@ class profile::jenkins::master (
     service_ensure     => running,
     configure_firewall => true,
     install_java       => $install_jenkins_java,
+    manage_user        => false,
+    manage_group       => false,
+    manage_datadirs    => false,
     port               => $jenkins_port,
     config_hash        => {
       'HTTP_PORT'    => { 'value' => $jenkins_port },
```

## Fifth refactor: Manage more dependencies

Jenkins always needs Git installed \(because we use Git for source control at Puppet\), and it needs SSH keys to access private Git repos and run commands on Jenkins agent nodes. We also have a standard list of Jenkins plugins we use, so we manage those too.

Managing Git is pretty easy:

```puppet
  package { 'git':
    ensure => present,
  }
```

SSH keys are less easy, because they are sensitive content. We can't check them into version control with the rest of our Puppet code, so we put them in a custom mount point on one specific Puppet server.

Because this server is different from our normal Puppet servers, we made a rule about accessing it: you must look up the hostname from data instead of hardcoding it. This lets us change it in only one place if the secure server ever moves.

```puppet
  $secure_server = lookup('puppetlabs::ssl::secure_server')

  file { "${master_config_dir}/.ssh":
    ensure => directory,
    owner  => $jenkins_owner,
    group  => $jenkins_group,
    mode   => '0700',
  }

  file { "${master_config_dir}/.ssh/id_rsa":
    ensure => file,
    owner  => $jenkins_owner,
    group  => $jenkins_group,
    mode   => '0600',
    source => "puppet://${secure_server}/secure/delivery/id_rsa-jenkins",
  }

  file { "${master_config_dir}/.ssh/id_rsa.pub":
    ensure => file,
    owner  => $jenkins_owner,
    group  => $jenkins_group,
    mode   => '0640',
    source => "puppet://${secure_server}/secure/delivery/id_rsa-jenkins.pub",
  }
```

Plugins are also a bit tricky, because we have a few Jenkins masters where we want to manually configure plugins. So we put the base list in a separate profile, and use a parameter to control whether we use it.

```puppet
class profile::jenkins::master (
  Boolean                     $manage_plugins = false,
  # ...
) {
  # ...
  if $manage_plugins {
    include profile::jenkins::master::plugins
  }
```

In the plugins profile, we can use the `jenkins::plugin` resource type provided by the Jenkins module.

```
# /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master/plugins.pp
class profile::jenkins::master::plugins {
  jenkins::plugin { 'audit2db':          }
  jenkins::plugin { 'credentials':       }
  jenkins::plugin { 'jquery':            }
  jenkins::plugin { 'job-import-plugin': }
  jenkins::plugin { 'ldap':              }
  jenkins::plugin { 'mailer':            }
  jenkins::plugin { 'metadata':          }
  # ... and so on.
}

```

### Diff of fifth refactor

```
@@ -1,6 +1,7 @@
 # /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
 class profile::jenkins::master (
   String                      $jenkins_port = '9091',
+  Boolean                     $manage_plugins = false,
   Variant[String[1], Boolean] $direct_download = 'http://pkg.jenkins-ci.org/debian-stable/binary/jenkins_1.642.2_all.deb',
   Boolean                     $install_jenkins_java = true,
 ) {
@@ -14,6 +15,20 @@ class profile::jenkins::master (
   $jenkins_group          = $profile::jenkins::params::jenkins_group
   $master_config_dir      = $profile::jenkins::params::master_config_dir

+  if $manage_plugins {
+    # About 40 jenkins::plugin resources:
+    include profile::jenkins::master::plugins
+  }
+
+  # Sensitive info (like SSH keys) isn't checked into version control like the
+  # rest of our modules; instead, it's served from a custom mount point on a
+  # designated server.
+  $secure_server = lookup('puppetlabs::ssl::secure_server')
+
+  package { 'git':
+    ensure => present,
+  }
+
   file { '/var/run/jenkins': ensure => 'directory' }

   # Because our account::user class manages the '${master_config_dir}' directory
@@ -69,4 +84,29 @@ class profile::jenkins::master (
     value => "-Xms${heap}m -Xmx${heap}m -Djava.awt.headless=true -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -Dhudson.model.DirectoryBrowserSupport.CSP=\\\"default-src 'self'; img-src 'self'; style-src 'self';\\\"",
   }

+  # Deploy the SSH keys that Jenkins needs to manage its agent machines and
+  # access Git repos.
+  file { "${master_config_dir}/.ssh":
+    ensure => directory,
+    owner  => $jenkins_owner,
+    group  => $jenkins_group,
+    mode   => '0700',
+  }
+
+  file { "${master_config_dir}/.ssh/id_rsa":
+    ensure => file,
+    owner  => $jenkins_owner,
+    group  => $jenkins_group,
+    mode   => '0600',
+    source => "puppet://${secure_server}/secure/delivery/id_rsa-jenkins",
+  }
+
+  file { "${master_config_dir}/.ssh/id_rsa.pub":
+    ensure => file,
+    owner  => $jenkins_owner,
+    group  => $jenkins_group,
+    mode   => '0640',
+    source => "puppet://${secure_server}/secure/delivery/id_rsa-jenkins.pub",
+  }
+
 }
```

## Sixth refactor: Manage logging and backups

Backing up is usually a good idea.

We can use our homegrown `backup` module, which provides a `backup::job` resource type \(`profile::server` takes care of its prerequisites\). But we should make backups optional, so people don't accidentally post junk to our backup server if they're setting up an ephemeral Jenkins instance to test something.

```puppet
class profile::jenkins::master (
  Boolean                     $backups_enabled = false,
  # ...
) {
  # ...
  if $backups_enabled {
    backup::job { "jenkins-data-${::hostname}":
      files => $master_config_dir,
    }
  }
}
```

Also, our teams gave us some conflicting requests for Jenkins logs:

-   Some people want it to use syslog, like most other services.
-   Others want a distinct log file so syslog doesn't get spammed, and they want the file to rotate more quickly than it does by default.

That implies a new parameter. We can make one called `$jenkins_logs_to_syslog` and default it to `undef`. If you set it to a standard syslog facility \(like `daemon.info`\), Jenkins logs there instead of its own file.

We use `jenkins::sysconfig` and our homegrown `logrotate::job` to do the work:

```puppet
class profile::jenkins::master (
  Optional[String[1]]         $jenkins_logs_to_syslog = undef,
  # ...
) {
  # ...
  if $jenkins_logs_to_syslog {
    jenkins::sysconfig { 'JENKINS_LOG':
      value => "$jenkins_logs_to_syslog",
    }
  }
  # ...
  logrotate::job { 'jenkins':
    log     => '/var/log/jenkins/jenkins.log',
    options => [
      'daily',
      'copytruncate',
      'missingok',
      'rotate 7',
      'compress',
      'delaycompress',
      'notifempty'
    ],
  }
}
```

### Diff of sixth refactor

```
@@ -1,8 +1,10 @@
 # /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
 class profile::jenkins::master (
   String                      $jenkins_port = '9091',
+  Boolean                     $backups_enabled = false,
   Boolean                     $manage_plugins = false,
   Variant[String[1], Boolean] $direct_download = 'http://pkg.jenkins-ci.org/debian-stable/binary/jenkins_1.642.2_all.deb',
+  Optional[String[1]]         $jenkins_logs_to_syslog = undef,
   Boolean                     $install_jenkins_java = true,
 ) {

@@ -84,6 +86,15 @@ class profile::jenkins::master (
     value => "-Xms${heap}m -Xmx${heap}m -Djava.awt.headless=true -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -Dhudson.model.DirectoryBrowserSupport.CSP=\\\"default-src 'self'; img-src 'self'; style-src 'self';\\\"",
   }

+  # Forward jenkins master logs to syslog.
+  # When set to facility.level the jenkins_log uses that value instead of a
+  # separate log file, for example daemon.info
+  if $jenkins_logs_to_syslog {
+    jenkins::sysconfig { 'JENKINS_LOG':
+      value => "$jenkins_logs_to_syslog",
+    }
+  }
+
   # Deploy the SSH keys that Jenkins needs to manage its agent machines and
   # access Git repos.
   file { "${master_config_dir}/.ssh":
@@ -109,4 +120,29 @@ class profile::jenkins::master (
     source => "puppet://${secure_server}/secure/delivery/id_rsa-jenkins.pub",
   }

+  # Back up Jenkins' data.
+  if $backups_enabled {
+    backup::job { "jenkins-data-${::hostname}":
+      files => $master_config_dir,
+    }
+  }
+
+  # (QENG-1829) Logrotate rules:
+  # Jenkins' default logrotate config retains too much data: by default, it
+  # rotates jenkins.log weekly and retains the last 52 weeks of logs.
+  # Considering we almost never look at the logs, let's rotate them daily
+  # and discard after 7 days to reduce disk usage.
+  logrotate::job { 'jenkins':
+    log     => '/var/log/jenkins/jenkins.log',
+    options => [
+      'daily',
+      'copytruncate',
+      'missingok',
+      'rotate 7',
+      'compress',
+      'delaycompress',
+      'notifempty'
+    ],
+  }
+
 }

```

## Seventh refactor: Use a reverse proxy for HTTPS

We want the Jenkins web interface to use HTTPS, which we can accomplish with an Nginx reverse proxy. We also want to standardize the ports: the Jenkins app always binds to its default port, and the proxy always serves over 443 for HTTPS and 80 for HTTP.

If we want to keep vanilla HTTP available, we can provide an `$ssl` parameter. If set to `false` \(the default\), you can access Jenkins via both HTTP and HTTPS. We can also add a `$site_alias` parameter, so the proxy can listen on a hostname other than the node's main FQDN.

```puppet
class profile::jenkins::master (
  Boolean                     $ssl = false,
  Optional[String[1]]         $site_alias = undef,
  # IMPORTANT: notice that $jenkins_port is removed.
  # ...
```

Set `configure_firewall => false` in the Jenkins class:

```puppet
  class { 'jenkins':
    lts                => true,
    repo               => true,
    direct_download    => $direct_download,
    version            => 'latest',
    service_enable     => true,
    service_ensure     => running,
    configure_firewall => false,                # <-- here
    install_java       => $install_jenkins_java,
    manage_user        => false,
    manage_group       => false,
    manage_datadirs    => false,
    # IMPORTANT: notice that port and config_hash are removed.
  }
```

We need to deploy SSL certificates where Nginx can reach them. Because we serve a lot of things over HTTPS, we already had a profile for that:

```puppet
  # Deploy the SSL certificate/chain/key for sites on this domain.
  include profile::ssl::delivery_wildcard
```

This is also a good time to add some info for the message of the day, handled by puppetlabs/motd:

```puppet
  motd::register { 'Jenkins CI master (profile::jenkins::master)': }

  if $site_alias {
    motd::register { 'jenkins-site-alias':
      content => @("END"),
                 profile::jenkins::master::proxy

                 Jenkins site alias: ${site_alias}
                 |-END
      order   => 25,
    }
  }
```

The bulk of the work is handled by a new profile called `profile::jenkins::master::proxy`. We're omitting the code for brevity; in summary, what it does is:

-   Include `profile::nginx`.
-   Use resource types from the jfryman/nginx to set up a vhost, and to force a redirect to HTTPS if we haven't enabled vanilla HTTP.
-   Set up logstash forwarding for access and error logs.
-   Include `profile::fw::https` to manage firewall rules, if necessary.

Then, we declare that profile in our main profile:

```puppet
  class { 'profile::jenkins::master::proxy':
    site_alias  => $site_alias,
    require_ssl => $ssl,
  }
```

**Important:**

We are now breaking rule 1, the most important rule of the roles and profiles method. Why?

Because `profile::jenkins::master::proxy` is a "private" profile that belongs solely to `profile::jenkins::master`. It will never be declared by any role or any other profile.

This is the only exception to rule 1: if you're separating out code *for the sole purpose of readability* --- that is, if you could paste the private profile's contents into the main profile for the exact same effect --- you can use a resource-like declaration on the private profile. This lets you consolidate your data lookups and make the private profile's inputs more visible, while keeping the main profile a little cleaner. If you do this, you must make sure to document that the private profile is private.

If there is any chance that this code might be reused by another profile, obey rule 1.

### Diff of seventh refactor

```
@@ -1,8 +1,9 @@
 # /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
 class profile::jenkins::master (
-  String                      $jenkins_port = '9091',
   Boolean                     $backups_enabled = false,
   Boolean                     $manage_plugins = false,
+  Boolean                     $ssl = false,
+  Optional[String[1]]         $site_alias = undef,
   Variant[String[1], Boolean] $direct_download = 'http://pkg.jenkins-ci.org/debian-stable/binary/jenkins_1.642.2_all.deb',
   Optional[String[1]]         $jenkins_logs_to_syslog = undef,
   Boolean                     $install_jenkins_java = true,
@@ -11,6 +12,9 @@ class profile::jenkins::master (
   # We rely on virtual resources that are ultimately declared by profile::server.
   include profile::server

+  # Deploy the SSL certificate/chain/key for sites on this domain.
+  include profile::ssl::delivery_wildcard
+
   # Some default values that vary by OS:
   include profile::jenkins::params
   $jenkins_owner          = $profile::jenkins::params::jenkins_owner
@@ -22,6 +26,31 @@ class profile::jenkins::master (
     include profile::jenkins::master::plugins
   }

+  motd::register { 'Jenkins CI master (profile::jenkins::master)': }
+
+  # This adds the site_alias to the message of the day for convenience when
+  # logging into a server via FQDN. Because of the way motd::register works, we
+  # need a sort of funny formatting to put it at the end (order => 25) and to
+  # list a class so there isn't a random "--" at the end of the message.
+  if $site_alias {
+    motd::register { 'jenkins-site-alias':
+      content => @("END"),
+                 profile::jenkins::master::proxy
+
+                 Jenkins site alias: ${site_alias}
+                 |-END
+      order   => 25,
+    }
+  }
+
+  # This is a "private" profile that sets up an Nginx proxy -- it's only ever
+  # declared in this class, and it would work identically pasted inline.
+  # But because it's long, this class reads more cleanly with it separated out.
+  class { 'profile::jenkins::master::proxy':
+    site_alias  => $site_alias,
+    require_ssl => $ssl,
+  }
+
   # Sensitive info (like SSH keys) isn't checked into version control like the
   # rest of our modules; instead, it's served from a custom mount point on a
   # designated server.
@@ -56,16 +85,11 @@ class profile::jenkins::master (
     version            => 'latest',
     service_enable     => true,
     service_ensure     => running,
-    configure_firewall => true,
+    configure_firewall => false,
     install_java       => $install_jenkins_java,
     manage_user        => false,
     manage_group       => false,
     manage_datadirs    => false,
-    port               => $jenkins_port,
-    config_hash        => {
-      'HTTP_PORT'    => { 'value' => $jenkins_port },
-      'JENKINS_PORT' => { 'value' => $jenkins_port },
-    },
   }

   # When not using the jenkins module's java version, install java8.

```

## The final profile code

After all of this refactoring \(and a few more minor adjustments\), here’s the final code for `profile::jenkins::master`.

```
# /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
# Class: profile::jenkins::master
#
# Install a Jenkins master that meets Puppet's internal needs.
#
class profile::jenkins::master (
  Boolean                     $backups_enabled = false,
  Boolean                     $manage_plugins = false,
  Boolean                     $ssl = false,
  Optional[String[1]]         $site_alias = undef,
  Variant[String[1], Boolean] $direct_download = 'http://pkg.jenkins-ci.org/debian-stable/binary/jenkins_1.642.2_all.deb',
  Optional[String[1]]         $jenkins_logs_to_syslog = undef,
  Boolean                     $install_jenkins_java = true,
) {

  # We rely on virtual resources that are ultimately declared by profile::server.
  include profile::server

  # Deploy the SSL certificate/chain/key for sites on this domain.
  include profile::ssl::delivery_wildcard

  # Some default values that vary by OS:
  include profile::jenkins::params
  $jenkins_owner          = $profile::jenkins::params::jenkins_owner
  $jenkins_group          = $profile::jenkins::params::jenkins_group
  $master_config_dir      = $profile::jenkins::params::master_config_dir

  if $manage_plugins {
    # About 40 jenkins::plugin resources:
    include profile::jenkins::master::plugins
  }

  motd::register { 'Jenkins CI master (profile::jenkins::master)': }

  # This adds the site_alias to the message of the day for convenience when
  # logging into a server via FQDN. Because of the way motd::register works, we
  # need a sort of funny formatting to put it at the end (order => 25) and to
  # list a class so there isn't a random "--" at the end of the message.
  if $site_alias {
    motd::register { 'jenkins-site-alias':
      content => @("END"),
                 profile::jenkins::master::proxy

                 Jenkins site alias: ${site_alias}
                 |-END
      order   => 25,
    }
  }

  # This is a "private" profile that sets up an Nginx proxy -- it's only ever
  # declared in this class, and it would work identically pasted inline.
  # But because it's long, this class reads more cleanly with it separated out.
  class { 'profile::jenkins::master::proxy':
    site_alias  => $site_alias,
    require_ssl => $ssl,
  }

  # Sensitive info (like SSH keys) isn't checked into version control like the
  # rest of our modules; instead, it's served from a custom mount point on a
  # designated server.
  $secure_server = lookup('puppetlabs::ssl::secure_server')

  # Dependencies:
  #   - Pull in apt if we're on Debian.
  #   - Pull in the 'git' package, used by Jenkins for Git polling.
  #   - Manage the 'run' directory (fix for busted Jenkins packaging).
  if $::osfamily == 'Debian' { include apt }

  package { 'git':
    ensure => present,
  }

  file { '/var/run/jenkins': ensure => 'directory' }

  # Because our account::user class manages the '${master_config_dir}' directory
  # as the 'jenkins' user's homedir (as it should), we need to manage
  # `${master_config_dir}/plugins` here to prevent the upstream
  # rtyler-jenkins module from trying to manage the homedir as the config
  # dir. For more info, see the upstream module's `manifests/plugin.pp`
  # manifest.
  file { "${master_config_dir}/plugins":
    ensure  => directory,
    owner   => $jenkins_owner,
    group   => $jenkins_group,
    mode    => '0755',
    require => [Group[$jenkins_group], User[$jenkins_owner]],
  }

  Account::User <| tag == 'jenkins' |>

  class { 'jenkins':
    lts                => true,
    repo               => true,
    direct_download    => $direct_download,
    version            => 'latest',
    service_enable     => true,
    service_ensure     => running,
    configure_firewall => false,
    install_java       => $install_jenkins_java,
    manage_user        => false,
    manage_group       => false,
    manage_datadirs    => false,
  }

  # When not using the jenkins module's java version, install java8.
  unless $install_jenkins_java  { include profile::jenkins::usage::java8 }

  # Manage the heap size on the master, in MB.
  if($::memorysize_mb =~ Number and $::memorysize_mb > 8192)
  {
    # anything over 8GB we should keep max 4GB for OS and others
    $heap = sprintf('%.0f', $::memorysize_mb - 4096)
  } else {
    # This is calculated as 50% of the total memory.
    $heap = sprintf('%.0f', $::memorysize_mb * 0.5)
  }
  # Set java params, like heap min and max sizes. See
  # https://wiki.jenkins-ci.org/display/JENKINS/Features+controlled+by+system+properties
  jenkins::sysconfig { 'JAVA_ARGS':
    value => "-Xms${heap}m -Xmx${heap}m -Djava.awt.headless=true -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -Dhudson.model.DirectoryBrowserSupport.CSP=\\\"default-src 'self'; img-src 'self'; style-src 'self';\\\"",
  }

  # Forward jenkins master logs to syslog.
  # When set to facility.level the jenkins_log uses that value instead of a
  # separate log file, for example daemon.info
  if $jenkins_logs_to_syslog {
    jenkins::sysconfig { 'JENKINS_LOG':
      value => "$jenkins_logs_to_syslog",
    }
  }

  # Deploy the SSH keys that Jenkins needs to manage its agent machines and
  # access Git repos.
  file { "${master_config_dir}/.ssh":
    ensure => directory,
    owner  => $jenkins_owner,
    group  => $jenkins_group,
    mode   => '0700',
  }

  file { "${master_config_dir}/.ssh/id_rsa":
    ensure => file,
    owner  => $jenkins_owner,
    group  => $jenkins_group,
    mode   => '0600',
    source => "puppet://${secure_server}/secure/delivery/id_rsa-jenkins",
  }

  file { "${master_config_dir}/.ssh/id_rsa.pub":
    ensure => file,
    owner  => $jenkins_owner,
    group  => $jenkins_group,
    mode   => '0640',
    source => "puppet://${secure_server}/secure/delivery/id_rsa-jenkins.pub",
  }

  # Back up Jenkins' data.
  if $backups_enabled {
    backup::job { "jenkins-data-${::hostname}":
      files => $master_config_dir,
    }
  }

  # (QENG-1829) Logrotate rules:
  # Jenkins' default logrotate config retains too much data: by default, it
  # rotates jenkins.log weekly and retains the last 52 weeks of logs.
  # Considering we almost never look at the logs, let's rotate them daily
  # and discard after 7 days to reduce disk usage.
  logrotate::job { 'jenkins':
    log     => '/var/log/jenkins/jenkins.log',
    options => [
      'daily',
      'copytruncate',
      'missingok',
      'rotate 7',
      'compress',
      'delaycompress',
      'notifempty'
    ],
  }

}
```

