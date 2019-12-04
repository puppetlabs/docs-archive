---
layout: default
title: "Installing Puppet Enterprise overview"
canonical: "/pe/latest/install_basic.html"
---


[downloadpe]: http://info.puppetlabs.com/download-pe.html

> ![windows logo](./images/windows-logo-small.jpg) This section covers \*nix operating systems. To install PE on Windows, see [installing Windows agents](./install_windows.html).

Downloading Puppet Enterprise
-----

Start by [downloading][downloadpe] the tarball for the current version of Puppet Enterprise, along with the GPG signature (.asc), from the Puppet website.

### Choosing an installer tarball

Puppet Enterprise is distributed in tarballs specific to your OS version and architecture.

#### Available \*nix tarballs

|      Filename ends with...        |                     Will install on...                 |
|-----------------------------------|-----------------------------------------------------|  |
| `-el-<version and arch>.tar.gz`      | RHEL, CentOS, Scientific Linux, or Oracle Linux  |
| `-ubuntu-<version and arch>.tar.gz`  | Ubuntu LTS                                       |
| `-sles-<version and arch>.tar.gz`    | SLES                                             |

### Verifying the installer

To verify the PE installer, import the Puppet public key and run a cryptographic verification of the tarball you downloaded. The Puppet public key is certified by Puppet and is available from public keyservers, such as `pgp.mit.edu`, as well as Puppet. You'll need to have GnuPG installed and the GPG signature (.asc file) that you downloaded with the PE tarball.

To import the Puppet public key, run:

    wget -O - https://downloads.puppetlabs.com/puppet-gpg-signing-key.pub | gpg --import

The result should be similar to:

    gpg: key EF8D349F: public key "Puppet, Inc. Release Key (Puppet, Inc. Release Key) <release@puppet.com>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

To print the fingerprint of our key, run:

    gpg --fingerprint 0x7F438280EF8D349F

You should also see an exact match of the fingerprint of our key, which is printed on the verification:

    Primary key fingerprint: 6F6B 1550 9CF8 E59E 6E46  9F32 7F43 8280 EF8D 349F

Next, verify the release signature on the tarball by running:

    $ gpg --verify puppet-enterprise-<version>-<platform>.tar.gz.asc

The result should be similar to:

    gpg: Signature made Tue 18 Sep 2016 10:05:25 AM PDT using RSA key ID EF8D349F
    gpg: Good signature from "Puppet, Inc. Release Key (Puppet, Inc. Release Key)"

 **Note**: When you verify the signature but do not have a trusted path to one of the signatures on the release key, you will see a warning similar to:

    Could not find a valid trust path to the key.
        gpg: WARNING: This key is not certified with a trusted signature!
        gpg:          There is no indication that the signature belongs to the owner.

This warning is generated because you have not created a trust path to certify who signed the release key; it can be ignored.

Installing Puppet Enterprise
-----

Your PE installation will go more smoothly if you know a few things in advance. Puppet Enterprise's functions are spread across several different components which get installed and configured when you run the installer. You can choose to install multiple components on a single node (a "monolithic install") or spread the components across multiple nodes (a "split install"), but you should note that the "agent" component gets installed on every node.

You should decide on your deployment needs before starting the install process. For each node where you'll install a PE component, you should know the fully qualified domain name where that node can be reached and you should ensure that firewall rules are set up to allow access to the [required ports](./sys_req_sysconfig.html#firewall-configuration).

With that knowledge in hand, the installation process will proceed in **three stages**:

1. You choose an installation method.

2. You install the main components of PE---the Puppet master, PuppetDB (and database support), and the PE console.

3. You install the Puppet agent on all the nodes you wish to manage with PE. Refer to the [agent installation instructions](./install_agents.html)

### Choose an installation method

Before you begin, choose an installation method. We've provided a few paths to choose from, and provided links to the corresponding installation instructions.

   * [Web-based monolithic installation](./install_pe_mono.html): The base install type for a [standard monolithic installation](./sys_req_hw.html#monolithic-installation) or a [monolithic plus compile masters installation](./sys_req_hw.html#monolithic-plus-compile-masters-installation). 

    * [Text-mode installation](./install_text_mode.html): Use the example `pe.conf` file provided in the PE installation tarball or create your own to install PE. Refer to the text-mode installation overview for more information about this installation mode. With text-mode, you can create a split or monolithic installation. 

See the [system requirements](./sys_req_hw.html) for additional hardware-related specifications.

>**Note**: Before getting started, we recommend you read about [the Puppet Enterprise components](#about-puppet-enterprise-components) to familiarize yourself with the parts that make up a PE installation.

About Puppet Enterprise components
---------

Before beginning installation, you should familiarize yourself with the services and components that make up PE.

See the [PE architecture overview](./pe_architecture_overview.html) for more information.

Installing agents
-----

Agent installation instructions can be found at [installing PE agents](./install_agents.html).

