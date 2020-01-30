---
layout: default
title: "Docker build environments"
---

The below information represents the Pipelines for Containers Docker build environments when doing builds using the `distelli-manifest.yml` file with Pipelines build servers.

<h3><a name="distelli-build-docker-specs"></a>Docker build image specs</h3>

The below table shows what version of tools are available in which Pipelines for Containers Docker build image.

<table width="100%" cellpadding="4" cellspacing="0">
	<col width="93*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<col width="15*">
	<tr valign="top">
		<td width="36%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Haskell
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Javascript
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Java/JVM
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Python
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Perl
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Ruby
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Go
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Base
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			PHP
		</td>
		<td width="6%" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			Android
		</td>
		<td width="6%" bgcolor="#808080" style="border: 1px solid #000000; padding: 0.04in">
			Legacy
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			OS Specifications</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">3.19.0-25-generic
			#26~14.04.1-Ubuntu SMP Fri Jul 24 21:16:20 UTC 2015 x86_64 x86_64
			x86_64 GNU/Linux</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">DISTRIB_ID=Ubuntu<br>DISTRIB_RELEASE=12.04<br>DISTRIB_CODENAME=precise<br>DISTRIB_DESCRIPTION=&quot;Ubuntu
			12.04.5 LTS&quot;</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Version Control Tools</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">git
			version 1.7.9.5</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Mercurial
			Distributed SCM (version 2.0.2)</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">svn,
			version 1.6.17 (r1128011)</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Compilers and Build Tools</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">automake
			(GNU automake) 1.11.3</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">GNU
			Make 3.81</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">gcc
			(Ubuntu/Linaro 4.6.3-1ubuntu5) 4.6.3</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Networking Tools</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="right"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">curl
			7.22.0</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">GNU
			Wget 1.13.4</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">rsync
			version 3.0.9 protocol version 30</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">OpenSSH_5.9p1
			Debian-5ubuntu1.7, OpenSSL 1.0.1 14 Mar 2012</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Package Manager</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">dpkg
			1.16.1.2 (amd64)</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">apt
			0.8.16~exp12ubuntu10.25 for amd64</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language – Haskell</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">The
			Glorious Glasgow Haskell Compilation System, version 7.4.1</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">cabal-install version 1.18.0.2 using version 1.18.1 of the Cabal library </font></font>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language - Javascript</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">node
			v0.10.36</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">npm
			1.4.28</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">nvm</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language – Java/JVM</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">java
			version &quot;1.7.0_80&quot;</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>


	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">jdk_switcher</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg"> </font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>





	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">javac
			1.7.0_80</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Apache
			Ant(TM) version 1.8.2 compiled on December 3 2011</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Apache
			Maven 3.2.5</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Gradle
			2.2.1</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language – Python</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Python
			2.73</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">pip
			7.1.2</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language – Perl</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">perl5
			(revision 5 version 14 subversion 2)</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language – Ruby</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Ruby
			1.9.3p551 (2014-11-13 revision 48407) [x86_64-linux]</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">rvm
			1.26.8</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">rvm
			1.26.11</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">rbenv
			0.4.0</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language – Go</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">go
			version go1.4.2 linux/amd64</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Gimme
			v0.2.2</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="center"><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p align="left"> <font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt"><b>-
			Language – PHP</b></font></font></p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">PHP
			5.6.5 (cli)</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
	<tr valign="top">
		<td width="36%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg"><font size="2" style="font-size: 9pt">Phpenv
			rbenv 0.4.0-154-g9e664b5</font></font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><font face="Proxima Nova Rg">X</font></p>
		</td>
		<td width="6%" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
		<td width="6%" bgcolor="#808080" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
			<p><br>
			</p>
		</td>
	</tr>
</table>


<b>X</b> - Available and working in this image

<h3><a name="legacy-image-specifications"></a>Legacy image specifications</h3>

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

For more information on installing build dependencies see [Build Language Reference](https://puppet.com/docs/pipelines-for-apps/team/build-language.html) and see [Installing Dependencies](https://puppet.com/docs/pipelines-for-apps/team/manifest-dependencies.html).

<h3><a name="hardware"></a>Hardware</h3>

Pipelines for Applications build servers are hosted on Google Compute Engine machines in the us-central zone.

