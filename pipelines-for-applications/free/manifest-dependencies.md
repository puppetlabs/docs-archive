---
layout: default
title: "Installing dependencies"
---

<br>
Builds and deployments typically need to pre-install dependencies before building or executing deployed software. This may include extra services or libraries not installed by default.

You have the ability to install dependencies during your Puppet Pipelines builds and deployments by taking advantage of the flexibility of the Pipelines manifest.

For builds use the [PreRelease section of the Pipelines manifest](./manifest-build.html).

For deployment use the [PreInstall section of the Pipelines manifest](./manifest-deploy.html).

## Installing packages Using apt-get

The Advanced Packaging Tool (apt) has a broad install and support base for numerous flavors of linux. When using apt-get to install applications, it is strongly recommended that you first do an apt-get update.

To install packages, add something like this to your distelli-manifest.yml:

~~~
    - sudo apt-get -y update
    - sudo apt-get -y install make build-essential ruby curl git
~~~

Before using apt-get to install any packages, ensure you to an `apt-get update` to ensure your index is up to date.

Use of the `-y` parameter when running apt-get to avoid any requirements for human interaction. This will make sure the deploy will not stall waiting for user input.

## Installing packages Using yum

The Yellowdog Updater, Modified (yum) utility works with linux systems that use the RPM Package Manager (originally Red Hat Package Manager).

To install packages, add something like this to your distelli-manifest.yml:

~~~
    - sudo yum -y makecache
    - sudo yum -y install zlib-dev openssl-devel
~~~

Use of the `-y` parameter when running yum to avoid any requirements for human interaction. This will make sure the deploy will not stall waiting for user input.

## Installing applications using PowerShell

Using PowerShell on Windows you can download and unzip application dependencies during a deployment.

Below are two examples based on differing versions of powershell.

### Powershell v3+ example

~~~
  - powershell -Command (new-object System.Net.WebClient).DownloadFile('http://windows.php.net/downloads/releases/archives/php-5.6.8-Win32-VC11-x64.zip','php.zip')
  - powershell add-type -assemblyname "system.io.compression.filesystem" ; [System.io.compression.zipfile]::ExtractToDirectory('"%DISTELLI_INSTALLHOME%\php.zip"','"%APPDIR%"')zip\','\%APPDIR%\')
~~~

### Powershell v2 example

~~~
  - powershell -Command (new-object System.Net.WebClient).DownloadFile('http://windows.php.net/downloads/releases/archives/php-5.6.8-Win32-VC11-x64.zip','php.zip')
  - PowerShell (New-Object -COM Shell.Application).NameSpace('"%APPDIR%"').CopyHere((New-Object -COM Shell.Application).NameSpace('"%DISTELLI_INSTALLHOME%\php.zip"').Items(), 16);
~~~

## Installing packages from custom repository

Some packages you may wish to install are not part of the default repository and you may need to add custom repositories. Here are some examples:

~~~
  - sudo add-apt-repository -y ppa:rethinkdb/ppa
  - sudo apt-get -y update
  - sudo apt-get -y install rethinkdb
~~~

Enabling the EPEL (Extra Packages for Enterprise Linux) repository example:

~~~
   - sudo yum -y install python
   - sudo rpm -iUvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
   - sudo yum -y update
   - sudo yum -y install python-pip
   - sudo yum -y install python-virtualenv
~~~

## Installing packages without a repository

For some applications, there may be a package, but no corresponding repository. These require the extra step of downloading.

~~~
  - wget http://http.us.debian.org/debian/pool/main/p/python-debian/python-debian_0.1.27_all.deb
  - sudo dpkg -i python-debian_0.1.27_all.deb
~~~

Another example:

~~~
  - wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1404-3.0.3.tgz
  - tar xvfz mongodb-linux-x86_64-ubuntu1404-3.0.3.tgz
  - sudo mkdir -p /usr/local/mongodb
  - sudo cp -R -n mongodb-linux-x86_64-ubuntu1404-3.0.3/ /usr/local/mongodb
~~~

Another example:

~~~
  - wget https://protobuf.googlecode.com/files/protobuf-2.4.1.tar.gz
  - tar -xzvf protobuf-2.4.1.tar.gz
  - cd protobuf-2.4.1 && ./configure --prefix=/usr && make && sudo make install
~~~

## Installing projects from source

Some dependencies can be installed only from a source package.

You can easily include the steps in your manifest and run the appropriate commands to install the dependencies.

Here is an example installing rvm from rvm.io.

~~~
  - gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
  - curl -sSL https://get.rvm.io | bash -s stable --ruby
~~~

## Installing Mac packages

Using Homebrew over installing from scratch has several benefits. For a lot of packages, it has binary packages available, removing the need to compile packages when installing them. However, should one of them need to be compiled from source, Homebrew can also manage dependencies and the installation process for you.
Here is an example:

~~~
  - brew update
  - brew install beanstalk
~~~

Note the `brew update` which is similar to `apt-get update`. This ensures Homebrew has the most recent packages in its index.

## Conditional installations

In the situation where you will be redeploying on top of an existing deployment, you may want to first check if something is already installed before re-installing it. Here is an example installing Ruby rbenv:

~~~
  - echo "Begin Dependency Install"
  - if [ -a ~/.rbenv/bin/rbenv ]; then
  -   echo "rbenv already installed"
  - else
  -   echo "Installing rbenv"
  -   sudo apt-get -y update
  -   sudo apt-get -y install git curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev
  -   git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
  -   echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
  -   export PATH="$HOME/.rbenv/bin:$PATH"
  -   echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
  -   set +e
  -   eval "$(~/.rbenv/bin/rbenv init -)"
  -   set -e
  -   git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
  -   echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bash_profile
  -   export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"
  - fi
  - set +e
  - eval "$(~/.rbenv/bin/rbenv init -)"
  - set -e
  - VARRUBY="$(ruby -v || true)"
  - if [[ ${VARRUBY:0:10} == "ruby 1.9.3" ]]; then
  -   echo "ruby 1.9.3 exists"
  - else
  -   rbenv install -v 1.9.3-p551
  -   rbenv global 1.9.3-p551
  -   ruby -v
  - fi
  - if [ -a ~/.rbenv/shims/bundle ]; then
  -   echo "bundle already installed"
  - else
  -   echo "Installing bundler"
  -   gem install bundler
  -   set +e
  -   eval "$(~/.rbenv/bin/rbenv init -)"
  -   set -e
  - fi
~~~



