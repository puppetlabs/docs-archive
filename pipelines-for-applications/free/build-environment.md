---
layout: default
title: "Docker build environments"
---

The below information represents the Pipelines for Applications Docker build environments when doing builds using the `distelli-manifest.yml` file with Pipelines build servers.

## Available Docker version

<table>
  <tr>
    <th></th>
    <th>Client</th>
    <th>Server</th>
  </tr>
  <tr>
    <td>Version</td>
    <td>1.12.0</td>
    <td>1.12.6</td>
  </tr>
  <tr>
    <td>API version</td>
    <td>1.24</td>
    <td>1.24</td>
  </tr>
  <tr>
    <td>Go version</td>
    <td>go1.6.3</td>
    <td>go1.6.4</td>
  </tr>
  <tr>
    <td>Git commit</td>
    <td>8eab29e</td>
    <td>78d1802</td>
  </tr>
  <tr>
    <td>Built</td>
    <td>Thu Jul 28 23:54:00 2016</td>
    <td>Wed Jan 11 00:23:16 2017</td>
  </tr>
  <tr>
    <td>OS/Arch</td>
    <td>linux/amd64</td>
    <td>linux/amd64</td>
  </tr>
</table>

## Docker build image specs

The below table shows what version of tools are available in which Pipelines for Applications Docker build image.

<table>
  <tr>
    <th> </th>
    <th>Haskell</th>
    <th>JavaScript</th>
    <th>Java/JVM</th>
    <th>Python</th>
    <th>Perl</th>
    <th>Ruby</th>
    <th>Go</th>
    <th>Base</th>
    <th>PHP</th>
    <th>Android</th>
    <th>Legacy</th>
  </tr>
  <tr>
    <td colspan="12"><b>OS specifications</b></td>
  </tr>
  <tr>
    <td>3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>DISTRIB_ID=Ubuntu<br>
DISTRIB_RELEASE=12.04<br>
DISTRIB_CODENAME=precise<br>
DISTRIB_DESCRIPTION="Ubuntu 12.04.5 LTS"</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Version control tools</b></td>
  </tr>
  <tr>
    <td>Git version 1.7.9.5</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>Mercurial Distributed SCM (version 2.0.2)</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>SVN version 1.6.17 (r1128011)</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Compilers and build tools</b></td>
  </tr>
  <tr>
    <td>GNU Automake 1.11.3</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>GNU Make 3.81</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>GCC (Ubuntu/Linaro 4.6.3-1ubuntu5) 4.6.3</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Networking tools</b></td>
  </tr>
  <tr>
    <td>curl 7.22.0</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>GNU Wget 1.13.4</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>Rsync version 3.0.9 protocol version 30</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>OpenSSH_5.9p1 Debian-5ubuntu1.7, OpenSSL 1.0.1 14 Mar 2012</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Package manager</b></td>
  </tr>
  <tr>
    <td>dpkg 1.16.1.2 (amd64)</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>apt 0.8.16~exp12ubuntu10.25 for amd64</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Haskell language</b></td>
  </tr>
  <tr>
    <td>The Glorious Glasgow Haskell Compilation System, version 7.4.1</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>cabal-install version 1.18.0.2 using version 1.18.1 of the Cabal library</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>JavaScript language</b></td>
  </tr>
  <tr>
    <td>node v0.10.36</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>npm 1.4.28</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>nvm</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Java/JVM language</b></td>
  </tr>
  <tr>
    <td>Java version "1.7.0_80"</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>jdk_switcher</td>
    <td></td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>javac 1.7.0_80</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>Apache Ant(TM) version 1.8.2 compiled on December 3 2011</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>Apache Maven 3.2.5</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>Gradle 2.2.1</td>
    <td></td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Python language</b></td>
  </tr>
  <tr>
    <td>Python 2.73</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td>pip 7.1.2</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Perl language</b></td>
  </tr>
  <tr>
	<td>perl5 (revision 5 version 14 subversion 2)</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Ruby language</b></td>
  </tr>
  <tr>
	<td>Ruby 1.9.3p551 (2014-11-13 revision 48407) [x86_64-linux]</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td></td>
  </tr>
  <tr>
	<td>rvm 1.26.8</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td></td>
	<td></td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td></td>
  </tr>
  <tr>
	<td>rvm 1.26.11</td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td>X</td>
	<td>X</td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
  </tr>
  <tr>
	<td>rbenv 0.4.0</td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td>X</td>
	<td></td>
	<td></td>
	<td>X</td>
	<td>X</td>
	<td></td>
  </tr>
  <tr>
    <td colspan="12"><b>Go language</b></td>
  </tr>
  <tr>
	<td>Go version go1.4.2 linux/amd64</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td></td>
  </tr>
  <tr>
	<td>Gimme v0.2.2</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td>X</td>
	<td></td>
  </tr>
  <tr>
    <td colspan="12"><b>PHP language</b></td>
  </tr>
  <tr>
	<td>PHP 5.6.5 (cli)</td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td>X</td>
	<td></td>
	<td></td>
  </tr>
  <tr>
	<td>Phpenv rbenv 0.4.0-154-g9e664b5</td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td></td>
	<td>X</td>
	<td></td>
	<td></td>
  </tr>
</table>

<b>X</b> - Available and working in this image

## Legacy Image Specifications

<ul>
	<li><b>OS Specifications</b></li>
	<ul>
		<li>3.19.0-25-generic #26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux</li>
		<li>DISTRIB_ID=Ubuntu<br>
		DISTRIB_RELEASE=14.04<br>
		DISTRIB_CODENAME=trusty<br>
		DISTRIB_DESCRIPTION=&quot;Ubuntu 14.04.2 LTS&quot;</li>
	</ul>
	<li><b>Version	Control Tools</b></li>
	<ul>
		<li>git version	1.91</li>
		<li>Mercurial	Distributed SCM (version 2.8.2)</li>
	</ul>
	<li><b>Compilers and	Build Tools</b></li>
	<ul>
		<li>GNU Make 3.81</li>
	</ul>
	<li><b>Networking Tools</b></li>
	<ul>
		<li>GNU Wget 1.15</li>
		<li>rsync version 3.1.0  protocol version 31</li>
		<li>OpenSSH_6.6.1p1	Ubuntu-2ubuntu2, OpenSSL 1.0.1f 6 Jan 2014</li>
	</ul>
	<li><b>Package	Manager</b></li>
	<ul>
		<li>dpkg 1.17.5	(amd64)</li>
	</ul>
	<li><b>Language – Javascript</b></li>
	<ul>
		<li>node v0.12.2</li>
		<li>npm 2.7.4</li>
	</ul>
	<li><b>Language – Java/JVM</b></li>
	<ul>
		<li>java version &quot;1.8.0_45&quot;</li>
		<li>javac	1.8.0_45</li>
		<li>Apache Maven 3.3.3</li>
	</ul>
	<li><b>Language – Python</b></li>
	<ul>
		<li>Python 2.7.6</li>
	</ul>
	<li><b>Language – Perl</b></li>
	<ul>
		<li>perl5	(revision 5 version 18 subversion 2)</li>
	</ul>
</ul>

For more information on installing build dependencies see [Build Language Reference](./build-language.html) and see [Installing Dependencies](./manifest-dependencies.html).

<h3><a name="hardware"></a>Hardware</h3>

Pipelines for Applications build servers are hosted on Google Compute Engine machines in the us-central zone.

