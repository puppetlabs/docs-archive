---
layout: default
title: "Application manifest: Build section"
--- 

The **Build** section of the Pipelines for Applications application manifest contains instructions about how to build your application. A build will clone the repository, build the application, and (on success) create a release.

The manifest is read and processed in a specific order: 

1. BuildWith 
1. PreBuild
1. Build
1. PreRelease 
1. AfterBuildSuccess
1. AfterBuildFailure
1. CustomVersionCmd
1. ReleaseNotesCmd
1. CommitData
1. PkgInclude
1. PkgExclude

## BuildWith

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>BuildWith</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>NA</td>
<td><a href="./manifest-build.html#PreBuild">PreBuild</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section of the manifest specifies using a different vendor manifest to build from. Currently supported options include:

* `Travis` - for building with .travis.yml

This will run the build steps indicated in the vendor manifest and will run the build steps in the Pipelines manifest after.

~~~
username/AppName:
  BuildWith: Travis
~~~

## PreBuild

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PreBuild</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#BuildWith">BuildWith</a></td>
<td><a href="./manifest-build.html#Build">Build</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

Typically this section is used to install and/or configure any build requirements.

~~~
username/AppName:
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install npm
~~~

> **Note:** The Puppet Pipelines based [application environment variables](./environment-variable.html) are not available during this phase of the manifest.

## Build

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>Build</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#PreBuild">PreBuild</a></td>
<td><a href="./manifest-build.html#PreRelease">PreRelease</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td>PreBuild</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#BuildWith">BuildWith</a></td>
<td><a href="./manifest-build.html#Build">Build</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section of the manifest specifies the build and test instructions for the application.

~~~
username/AppName:
  Build:
    - cd src
    - go build -o ../bin/server
    - go test
~~~

## PreRelease

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PreRelease</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#Build">Build</a></td>
<td><a href="./manifest-build.html#AfterBuildSuccess">AfterBuildSuccess</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td></td>
<td><a href="./manifest-build.html#AfterBuildFailure">AfterBuildFailure</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section allows you to modify the build artifacts before they are bundled into a release.

~~~
username/AppName:
  PreRelease:
    - mv target/*.java /artifacts
~~~

> **Note:** The Puppet Pipelines based [application environment variables](./environment-variable.html) are not available during this phase of the manifest.

## AfterBuildSuccess

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>AfterBuildSuccess</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#PreRelease">PreRelease</a></td>
<td><a href="./manifest-build.html#ReleaseNotesCmd">ReleaseNotesCmd</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section occurs after the manifest Build section is successful.

~~~
username/AppName:
  AfterBuildSuccess:
    - DATE=$(date +"%Y%m%d%H%M%S%N")
    - touch BuildSuccess.$DATE
~~~

> **Note:** The Puppet Pipelines based [application environment variables](./environment-variable.html) are not available during this phase of the manifest.

## AfterBuildFailure

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>AfterBuildFailure</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#PreRelease">PreRelease</a></td>
<td>NA</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section occurs if the manifest Build section fails on any step.

~~~
username/AppName:
  AfterBuildFailure:
    - DATE=$(date +"%Y%m%d%H%M%S%N")
    - touch BuildFailure.$DATE
~~~

> **Note:** The Puppet Pipelines based [application environment variables](./environment-variable.html) are not available during this phase of the manifest.

## CustomVersionCmd

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>CustomVersionCmd</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#AfterBuildFailure">AfterBuildFailure</a></td>
<td><a href="./manifest-build.html#ReleaseNotesCmd">ReleaseNotesCmd</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section specifies a custom version number for the build. If a `CustomVersionCmd` section is not included, Pipelines for Applications will default to assigning incremental build version numbers. 

~~~
username/AppName:
  CustomVersionCmd:
    - echo "$CUSTOM_VER_MAJOR.$CUSTOM_VER_MINOR.$DISTELLI_BUILDNUM"
~~~

The same custom version number cannot be repeated on an application. If your application manifest directs Pipelines for Applications to reuse a previously assigned custom version number, you'll receive an error message. 

Custom version numbers are not case-sensitive. 

## ReleaseNotesCmd

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>ReleaseNotesCmd</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#AfterBuildSuccess">AfterBuildSuccess</a></td>
<td><a href="./manifest-build.html#CommitData">CommitData</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

The output of the commands in this section will be in the notes for the release. See [Viewing Release Notes](./release.html).

~~~
username/AppName:
  ReleaseNotesCmd:
    - 'hg log -l 10 -b default'
~~~

> **Note:** The Puppet Pipelines based [application environment variables](./environment-variable.html) are not available during this phase of the manifest.

## CommitData

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>CommitData</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#ReleaseNotesCmd">ReleaseNotesCmd</a></td>
<td><a href="./manifest-build.html#DockerInspect">DockerInspect</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section specifies details for your source control so Pipelines for Applications can automatically determine the commit point at which a particular release is built. There are two keys in this section: `RepoType` and `RepoPath`. 

~~~
username/AppName:
  CommitData:
    - RepoType: Git
    - RepoPath: .
~~~

## DockerInspect

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>Build</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#CommitData">CommitData</a></td>
<td><a href="./manifest-build.html#PkgInclude">PkgInclude</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

This section of the manifest specifies the Docker image(s) that were built and should be tracked as image events in your application or project.

~~~
username/AppName:
  DockerInspect:
    - 'doct15/k8s-mysql-image:$DISTELLI_BUILDNUM'
~~~

## PkgInclude

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PkgInclude</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#DockerInspect">DockerInspect</a></td>
<td><a href="./manifest-build.html#PkgExclude">PkgExclude</a></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

The list of files (artifacts) to include in the application release after a build. This is a list of strings that identify paths with optional wildcards.

~~~
username/AppName:
  PkgInclude:
    # Include a single file file.txt
    - 'file.txt'
    # Include the contents of "lib" directory and all subdirectories
    - 'lib/'
    # Include all .json files in the current directory and subdirectories
    - '*.json'
    # Include all files, but no subdirectories from subdirectory "temp"
    - 'temp/*'
~~~

You can use wildcards, such as <b>&#42;</b> to include everything or <b>/&#42;/</b> to include all files within subdirectories.
If you skip this section no files will be bundled in your release.

You can also take a file from one directory and have it packaged up else where. Here are some examples.

~~~
username/AppName:
  PkgInclude:
    # Include a war file, but move it to the root of the tarball
    - ["dir/to/File.war","."]
    # Include some files, but move them to lib/
    - ["target/output/*.exe","lib/"]
~~~

If your manifest includes an `Install` section, these files will not be unbundled to the destination server on deploy.

If a file is listed in `PkgInclude` and `PkgExclude`, it will be excluded.

## PkgExclude

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PkgExclude</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><a href="./manifest-build.html#PkgInclude">PkgInclude</a></td>
<td>NA</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>X</td>
<td> </td>
<td> </td>
<td> </td>
</tr>
</table>

The list of files (artifacts) to NOT include in the application release after a build. This is a list of strings that identify paths with optional wildcards.

~~~
username/AppName:
  PkgExclude:
    # Exclude a single file file.txt
    - 'file.txt'
    # Exclude the contents of "lib" directory and all subdirectories
    - 'lib/'
    # Exclude all .json files in the current directory
    - '*.json'
    # Exclude all files, but no subdirectories from subdirectory "temp"
    - 'temp/*'
~~~

If your manifest includes an `Install` section, these files will not be unbundled to the destination server on deploy.

If a file is listed in `PkgInclude` and `PkgExclude`, it will be excluded.


