---
layout: default
title: "Installing Puppet Enterprise Overview"
canonical: "/pe/latest/install_basic.html"
---


[downloadpe]: http://info.puppetlabs.com/download-pe.html

> ![windows logo](./images/windows-logo-small.jpg) This section covers \*nix operating systems. To install PE on Windows, see [Installing Windows Agents](./install_windows.html).

Installing Puppet Enterprise
-----

Your PE installation will go more smoothly if you know a few things in advance. Puppet Enterprise's functions are spread across several different components which get installed and configured when you run the installer. You can choose to install multiple components on a single node (a "monolithic install") or spread the components across multiple nodes (a "split install"), but you should note that the "agent" component gets installed on every node.

You should decide on your deployment needs before starting the install process. For each node where you'll install a PE component, you should know the fully qualified domain name where that node can be reached and you should ensure that firewall rules are set up to allow access to the [required ports](./install_system_requirements.html#firewall-configuration).

With that knowledge in hand, the installation process will proceed in **three stages**:

1. You choose an installation method.

2. You install the main components of PE---the Puppet master, PuppetDB (and database support), and the PE console.

3. You install the Puppet agent on all the nodes you wish to manage with PE. Refer to the [agent installation instructions](./install_agents.html)

### Choose an Installation Method

Before you begin, choose an installation method. We've provided a few paths to choose from.

- Perform a guided installation using the web-based interface. Think of this as an installation interview in which we ask you exactly how you want to install PE. If you're able to provide [SSH credentials](./install_pe_mono.html#ssh-prerequisites-and-notes), this method will get you up and running fairly quickly. Choose from one of the following installation types:

   * [Monolithic installation](./install_pe_mono.html): The base install type for a [standard monolithic installation](./install_system_requirements.html#monolithic-installation) or a [monolithic plus compile masters installation](./install_system_requirements.html#monolithic-plus-compile-masters-installation). 

   * [Split installation](./install_pe_split.html): The base install type for a [large environment installation](./install_system_requirements.html#large-environment-installation). 

- Use the web-based interface to create an answer file that you can then add as an argument to the installer script to perform an installation (e.g., `sudo ./puppet-enterprise-installer -a ~/my_answers.txt`). Refer to [Installing with an Answer File](./install_automated.html), which provides an overview on installing PE with an answer file.

- Edit an answer file provided in the PE installation tarball. Check [Installing with an Answer File](./install_automated.html) to get started.

See the [system requirements](./install_system_requirements.html) for any hardware-related specifications.

>**Note**: Before getting started, we recommend you read about [the Puppet Enterprise components](#about-puppet-enterprise-components) to familiarize yourself with the parts that make up a PE installation.

Downloading Puppet Enterprise
-----

Start by [downloading][downloadpe] the tarball for the current version of Puppet Enterprise, along with the GPG signature (.asc), from the Puppet Labs website.

### Choosing an Installer Tarball

Puppet Enterprise is distributed in tarballs specific to your OS version and architecture.

#### Available \*nix Tarballs

|      Filename ends with...        |                     Will install on...                 |
|-----------------------------------|-----------------------------------------------------|  |
| `-el-<version and arch>.tar.gz`      | RHEL, CentOS, Scientific Linux, or Oracle Linux  |
| `-ubuntu-<version and arch>.tar.gz`  | Ubuntu LTS                                       |
| `-sles-<version and arch>.tar.gz`    | SLES                                             |

### Verifying the Installer

To verify the PE installer, import the Puppet Labs public key and run a cryptographic verification of the tarball you downloaded. The Puppet Labs public key is certified by Puppet Labs and is available from public keyservers, such as `pgp.mit.edu`,  as well as Puppet Labs. You'll need to have GnuPG installed and the GPG signature (.asc file) that you downloaded with the PE tarball.

To import the Puppet Labs public key, run:

    wget -O - https://downloads.puppetlabs.com/puppetlabs-gpg-signing-key.pub | gpg --import

The result should be similar to

    gpg: key 4BD6EC30: public key "Puppet Labs Release Key (Puppet Labs Release Key) <info@puppetlabs.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

To print the fingerprint of our key, run:

    gpg --fingerprint 0x1054b7a24bd6ec30

You should also see an exact match of the fingerprint of our key, which is printed on the verification:

    Primary key fingerprint: 47bbe  B3 20EB 4C7C 375A A9DA  E1A0 1054 B7A2 4BD6 EC30

Next, verify the release signature on the tarball by running:

    $ gpg --verify puppet-enterprise-<version>-<platform>.tar.gz.asc

The result should be similar to

    gpg: Signature made Tue 18 Jun 2013 10:05:25 AM PDT using RSA key ID 4BD6EC30
    gpg: Good signature from "Puppet Labs Release Key (Puppet Labs Release Key)"

 **Note**: When you verify the signature but do not have a trusted path to one of the signatures on the release key, you will see a warning similar to

    Could not find a valid trust path to the key.
        gpg: WARNING: This key is not certified with a trusted signature!
        gpg:          There is no indication that the signature belongs to the owner.

This warning is generated because you have not created a trust path to certify who signed the release key; it can be ignored.

About Puppet Enterprise Services and Components
---------

Before beginning installation, you should familiarize yourself with the services and components that make up PE.

See the [PE architecture overview](./pe_architecture_overview.html) for more information.

Notes, Warnings, and Tips
---------

### Verifying Your License

When you purchased Puppet Enterprise, you should have been sent a `license.key` file that lists how many nodes you can deploy. For PE to run without logging license warnings, you should copy this file to the Puppet master node as `/etc/puppetlabs/license.key`. If you don't have your license key file, please email <sales@puppetlabs.com> and we'll re-send it.

Note that you can download and install Puppet Enterprise on up to ten nodes at no charge. No license key is needed to run PE on up to ten nodes.

### Puppet Enterprise Binaries and Symlinks

PE installs its binaries in `/opt/puppetlabs/bin`. To make essential Puppet tools available to all users, the installer automatically creates symlinks in `/usr/local/bin` for the `facter`, `puppet`, `pe-man`, `r10k`, `hiera`,  and `mco` binaries. Note that the symlinks will only be created if `/usr/local/bin` is writeable.

Note that AIX and Solaris 10/11 users need to add `/usr/local/bin` to their default path.

If you're running Mac OS X agents, note that symlinks are not created until the first successful Puppet run that applies the agents' catalogs.

Binaries provided by other PE components, such as those for interacting with PE's installed PostgreSQL server, PuppetDB, or Ruby packages do not have symlinks created. To include these binaries in your default `$PATH`, manually add them to your profile or run `PATH=/opt/puppetlabs/puppet/bin:/opt/puppetlabs/server/bin:$PATH;export PATH`.

Installing Agents
-----

Agent installation instructions can be found at [Installing PE Agents](./install_agents.html).

