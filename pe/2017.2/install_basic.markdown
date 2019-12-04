---
layout: default
title: "Downloading and installing Puppet Enterprise"
canonical: "/pe/latest/install_basic.html"
---


To get started with Puppet Enterprise, download and verify the PE installation package and then choose your installation type.

## Download and Verify Puppet Enterprise Installation Package

PE is distributed in packages specific to supported OS versions and architectures.

The lastest versions of Puppet Enterprise are available for download from our website. The downloads include the full PE installation tarball and a GPG signature (.asc) file to verify authenticity. 

### Download Puppet Enterprise

PE is available for download from our website.

1. Choose the appropriate tarball to [download](http://info.puppetlabs.com/download-pe.html) the current version of Puppet Enterprise. 

   |      Filename ends with...        |                     Will install on...                       |
|-----------------------------------|-----------------------------------------------------|  |
| `-el-<version and arch>.tar.gz`      | RHEL, CentOS, Scientific Linux, or Oracle Linux  |
| `-ubuntu-<version and arch>.tar.gz`  | Ubuntu LTS                                       |
| `-sles-<version and arch>.tar.gz`    | SLES                                             |

2. Continue to the next task to verify the installation package.
   

### Verify the installation package

Before proceeding with your PE installation, you can import the Puppet public key and run a cryptographic verification of the PE installation package you downloaded. 

Before you begin:

You'll need to have GnuPG installed. You'll also need the GPG signature (.asc file) that you downloaded with the PE installation package.

1. Import the Puppet public key.

   ~~~
   wget -O - https://downloads.puppetlabs.com/puppet-gpg-signing-key.pub | gpg --import
   ~~~
   
2. Print the fingerprint of the Puppet key.

   ~~~
   gpg --fingerprint 0x7F438280EF8D349F
   ~~~
   
   You should see an exact match of the fingerprint of our key, which is printed on the verification.
   
   ~~~
   Primary key fingerprint: 6F6B 1550 9CF8 E59E 6E46  9F32 7F43 8280 EF8D 349F
   ~~~
   
3. Verify the release signature of the installation package.

   ~~~
   $ gpg --verify puppet-enterprise-<version>-<platform>.tar.gz.asc
   ~~~
   
   The result should be similar to the following:
   
   ~~~
   gpg: Signature made Tue 18 Sep 2016 10:05:25 AM PDT using RSA key ID EF8D349F
   gpg: Good signature from "Puppet, Inc. Release Key (Puppet, Inc. Release Key)"
   ~~~
   
   **Note:** When you verify the signature but do not have a trusted path to one of the signatures on the release key, you will see a warning similar to the following:
   
   ~~~
   Could not find a valid trust path to the key.
        gpg: WARNING: This key is not certified with a trusted signature!
        gpg:          There is no indication that the signature belongs to the owner.
   ~~~
   
   This warning is generated because you have not created a trust path to certify who signed the release key; it can be ignored.

## Installing Puppet Enterprise: overview

Your PE installation will go more smoothly if you know a few things in advance.

Puppet Enterprise's functions are spread across several different components which get installed and configured when you run the installer. You can choose to install multiple components on a single node (a "monolithic install") or spread the components across multiple nodes (a "split install"), but you should note that the "agent" component gets installed on every node.

You should decide on your deployment needs before starting the install process. For each node where you'll install a PE component, you should know the fully qualified domain name where that node can be reached and you should ensure that firewall rules are set up to allow access to the [required ports](./sys_req_sysconfig.html#firewall-configuration).

With that knowledge in hand, the installation process will proceed in **three stages**:

1. You choose an installation method.

2. You install the main components of PE---the Puppet master, PuppetDB (and database support), and the PE console.

3. You install the Puppet agent on all the nodes you wish to manage with PE. Refer to the [agent installation instructions](./install_agents.html).

### Choosing an installation method

Before you begin, choose an installation method. We've provided a few paths to choose from, and provided links to the corresponding installation instructions.

   * [Web-based monolithic installation](./install_pe_mono.html): The base install type for a [standard monolithic installation](./sys_req_hw.html#monolithic-installation) or a [monolithic plus compile masters installation](./sys_req_hw.html#monolithic-with-compile-masters-installation). 

   * [Text-mode installation](./install_text_mode.html): Use the example `pe.conf` file provided in the PE installation tarball or create your own to install PE. With text-mode, you can create a split or monolithic installation. 

See the [system requirements](./sys_req_hw.html) for additional hardware-related specifications.

>**Tip:** Before beginning installation, you should familiarize yourself with the services and components that make up PE. See the [PE architecture overview](./pe_architecture_overview.html) for more information.



* * *
