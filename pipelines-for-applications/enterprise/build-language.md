---
layout: default
title: "Build language reference"
---

This document outlines required and recommended steps for successful builds by language.

The instructions are specific to using [Pipelines for Applications build servers](./server.html) and the [Pipelines manifest](./manifest.html) for specifying build instructions.
<hr>
The Pipelines manifest provides distinct sections to execute instructions during distinct stages, including:


<table cellpadding="2" cellspacing="2">
  <tr>
    <th>
      <p>Action
      </p>
    </th>
    <th>
      <p>Manifest Phase
      </p>
    </th>
    <th>
      <p>Order
      </p>
    </th>
  </tr>

  <tr>
    <td>
      <p>Build</p>
    </td>
    <td>
      <p>PreBuild</p>
    </td>
    <td>
      <p>This phase is used to specify any pre-build setup or installation steps.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Build</p>
    </td>
    <td>
      <p>Build</p>
    </td>
    <td>
      <p>This phase is used to specify any build and/or test steps that
      must pass before a release is created.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Build</p>
    </td>
    <td>
      <p>AfterBuildSuccess</p>
    </td>
    <td>
      <p>This phase is run only after a successful build.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Build</p>
    </td>
    <td>
      <p>AfterBuildFailure</p>
    </td>
    <td>
      <p>This phase is run after a failed build. No other phases are processed after this phase.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Build</p>
    </td>
    <td>
      <p>PreRelease</p>
    </td>
    <td>
      <p>This phase is used to specify any steps that must be taken before bundling and creating a release.</p>
    </td>
  </tr>

  <tr>
    <td>
      <p>Deploy</p>
    </td>
    <td>
      <p>PreInstall</p>
    </td>
    <td>
      <p>Happens before Install. Application bundle has not been
      unbundled to the destination server.<br>Typically used to install
      system level prerequisites.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Deploy</p>
    </td>
    <td>
      <p>Install</p>
    </td>
    <td>
      <p style="margin-bottom: 0.2in">If this section is included in
      your manifest, the application bundle will not be unbundled to the
      destination server.</p>
      <p>If this section is not included in your manifest, it is during
      this phase that the application bundle is unbundled to the
      destination server.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Deploy</p>
    </td>
    <td>
      <p>PostInstall</p>
    </td>
    <td>
      <p>At this phase, after install, your application bundle is
      available on the destination server. Now is when you may want to
      run application specific install steps.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Deploy</p>
    </td>
    <td>
      <p>PreStart</p>
    </td>
    <td>
      <p>Steps to complete before starting or executing your
      application.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Deploy</p>
    </td>
    <td>
      <p>Start OR Exec</p>
    </td>
    <td>
      <p>This is the step(s) to launch your application. If both &quot;Start&quot;
      and &quot;Exec&quot; sections are included, only the Exec section
      will be executed.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Deploy</p>
    </td>
    <td>
      <p>PostStart</p>
    </td>
    <td>
      <p>Steps to complete after the application is started or executed.</p>
    </td>
  </tr>
</table>


Here is an example Pipelines manifest:

~~~
jdoe/NodeExpress:

  PkgInclude:
    - '*'

  PkgExclude:
    - node_modules/

  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get install nodejs -y
    - sudo apt-get install npm -y

  Build:
    - npm install
    - npm test

  PreInstall:
    - sudo apt-get -y update
    - sudo apt-get install nodejs -y
    - sudo apt-get install npm -y

  PostInstall:
    - npm install

  Exec:
    - /usr/bin/nodejs app.js
~~~

The goal of this document is to provide suggested build steps, by language, when building on Pipelines for Applications build servers.

## Go

<table cellpadding="2" cellspacing="2">
  <tr>
    <td>
      <p>Language:</p>
    </td>
    <td>
      <p>Go</p>
    </td>
    <td>
      <p>The Go programming language. <a href="https://golang.org/">https://golang.org/</a>
            </p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Versions:</p>
    </td>
    <td>
      <p>1.0.3+</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td>
      <p>Tools used:</p>
    </td>
    <td>
      <p>gimme: A go version manager</p>
    </td>
    <td>
      <p><a href="https://github.com/travis-ci/gimme">https://github.com/travis-ci/gimme</a></p>
    </td>
  </tr>
</table>


This example uses the `gimme` tool to download and install versions of `go`.

<h3>Installing Tools</h3>

> **Note:** If building on a Pipelines shared build Go Image, the tool <code>gimme</code> is already installed.

This code will ensure the apt-get index is up to date then install the necessary tools. If the tools already exist, apt-get will display a warning and the PreBuild phase will continue.

~~~
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install curl git
    - mkdir -p ~/bin
    - curl -sL -o ~/bin/gimme https://raw.githubusercontent.com/travis-ci/gimme/master/gimme
    - chmod +x ~/bin/gimme
  Build:
    - export PATH=$PATH:~/bin
~~~

> **Note:** If using the Pipelines shared build image <b>Distelli Go</b>, <i>gimme</i> is already installed.


<h3>Specifying a Go Version to Use</h3>

Using the `gimme` tool you can easily install a version of go. Here is an example installing version 1.7.1

~~~
    - gimme 1.7.1
~~~

Because the Build manifest is run in a separate shell, you must first `source` the gimme environment.

~~~
    - source ~/.gimme/envs/latest.env
~~~


<h3>Setting GOPATH Environment with Pipelines Go Build Image</h3>

When working with the Pipelines shared Go build image, some steps must be taken to support the GOPATH directory structure.

~~~
    - export DISTELLI_APPNAME=simple_go_distelli
    - export GOPATH="$(pwd)"
    - mkdir -p src
    - ln -s $(pwd) src/$DISTELLI_APPNAME
    - mkdir -p pkg
    - mkdir -p bin
    - cd "src/$DISTELLI_APPNAME"
~~~

To use the above correctly, you must set the <b>DISTELLI_APPNAME</b> to your Go application name. This is the first entry at the top.


<h3>Getting Go Dependencies</h3>

This will install any Go dependencies your application may need.

~~~
    - go get -x -v
~~~

<h3>Building a Go Application</h3>

This will build the Go application and name the binary output BINARY_APP.

~~~
    - go build -x -v
~~~

<h3>Running Go Tests</h3>

This will run tests.

~~~
    - go test -x -v
~~~

<h3>Full Go Example</h3>

~~~
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install curl git
    - mkdir -p ~/bin
    - curl -sL -o ~/bin/gimme https://raw.githubusercontent.com/travis-ci/gimme/master/gimme
    - chmod +x ~/bin/gimme
  Build:
    - export PATH=$PATH:~/bin
    - gimme 1.7.1
    - source ~/.gimme/envs/latest.env
    # --To accomodate for GOPATH use the following--
    - export DISTELLI_APPNAME=simple_go_distelli
    - export GOPATH="$(pwd)"
    - mkdir -p src
    - ln -s $(pwd) src/$DISTELLI_APPNAME
    - mkdir -p pkg
    - mkdir -p bin
    - cd "src/$DISTELLI_APPNAME"
    # --end of GOPATH section--
    - go get -x -v
    - go build -x -v
    - go test -x -v
~~~

## >NodeJS


<table cellpadding="2" cellspacing="2">
  <tr>
    <td>
      <p>Language:</p>
    </td>
    <td>
      <p>NodeJS</p>
    </td>
    <td>
      <p>javascript runtime <a href="https://nodejs.org/">https://nodejs.org/</a>
            </p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Version:</p>
    </td>
    <td>
      <p>v0.1.14+</p>
    </td>
    <td>
      <p>nvm ls-remote</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Tools Used:</p>
    </td>
    <td>
      <p>curl: A software transfer tool</p>
    </td>
    <td>
      <p><a href="http://curl.haxx.se/">http://curl.haxx.se/</a></p>
    </td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>nvm: Node version manager</p>
    </td>
    <td>
      <p><a href="https://github.com/creationix/nvm">https://github.com/creationix/nvm</a></p>
    </td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>build essentials: compiler, libraries, and tools</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>libssl-dev: ssl development libraries</p>
    </td>
    <td valign="top"></td>
  </tr>
</table>

This example uses the `nvm` tool to download and install versions of `NodeJS`.

<h3>Installing Tools</h3>

> **Note:** If building on Pipelines shared build Javascript Image, the tool <code>nvm</code> is already installed.

This code will ensure the apt-get index is up to date then install the necessary tools. If the tools already exist, apt-get display a warning and the PreBuild phase will continue.

~~~
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install build-essential libssl-dev curl
    - curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.0/install.sh | bash
  Build:
    - source ~/.nvm/nvm.sh
~~~

<h3>Specifying a NodeJS Version to Use</h3>

Using the `nvm` tool you can easily install a version of NodeJS. Here is an example installing version v4.4.7. Note, you could also install NodeJS version <i>stable</i>.

~~~
    - nvm install v4.4.7
~~~


<h3>Updating npm</h3>

Globally update to the latest version of npm.

~~~
    - npm install npm -g
~~~

<h3>Getting NodeJS Packages</h3>

This will install any NodeJS packages and dependencies your application may need.

~~~
    - npm install
~~~

<h3>Running NodeJS Tests</h3>

This will run tests.

~~~
  - npm test
~~~

<h3>Full NodeJS Example</h3>

~~~
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install build-essential libssl-dev curl
    - curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.26.0/install.sh | bash
  Build:
    - source ~/.nvm/nvm.sh
    - nvm install v4.4.7
    - npm install npm -g
    - npm install
    - npm test
~~~


## Java

<table cellpadding="2" cellspacing="2">
  <tr>
    <td>
      <p>Language:</p>
    </td>
    <td>
      <p>Java</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td>
      <p>Versions:</p>
    </td>
    <td>
      <p>6+</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td>
      <p>Tools:</p>
    </td>
    <td>
      <p>ant</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>gradle</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>maven</p>
    </td>
    <td>
      <p>version 3</p>
    </td>
  </tr>
</table>

<h3>Specifying a Java Version to use</h3>

Using the `webupd8team/java` repository we can easily install versions of Java.

~~~
  PreBuild:
    - sudo add-apt-repository ppa:webupd8team/java
    - sudo apt-get -y update
    - echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections
    - echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections
    - sudo apt-get -y install oracle-java7-installer
    - sudo apt-get -y install oracle-java7-set-default
~~~

<h3>Installing Tools</h3>

These steps will install the necessary tools. If the tools already exist, apt-get will display a warning and the PreBuild phase will continue.

~~~
    - sudo apt-get -y install ant
    - sudo apt-get -y install maven
    - sudo apt-get -y install gradle
~~~

<h3>Building the Java application</h3>

This code will build your Java application based on the existing build instruction file. In particular:

<ul>
<li>./gradlew</li>
<li>build.gradle</li>
<li>pom.xml</li>
</ul>

~~~
    - if [[ -f gradlew ]]; then
    -   ./gradlew assemble
    - elif [[ -f build.gradle ]]; then
    -   gradle assemble
    - elif [[ -f pom.xml ]]; then
    -   mvn install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
    - fi
~~~

<h3>Testing the Java application</h3>

This code will test your Java application based on the existing build instruction file. If no file is found it will use `ant test`.

~~~
    - if [[ -f gradlew ]]; then
    -   ./gradlew check
    - elif [[ -f build.gradle ]]; then
    -   gradle check
    - elif [[ -f pom.xml ]]; then
    -   mvn test -B
    - else
    -   ant test
    - fi
~~~


<h3>Full Java example</h3>


~~~
  PreBuild:
    - sudo add-apt-repository ppa:webupd8team/java
    - sudo apt-get -y update
    - echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections
    - echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections
    - sudo apt-get -y install oracle-java7-installer
    - sudo apt-get -y install oracle-java7-set-default
    - sudo apt-get -y install ant
    - sudo apt-get -y install maven
    - sudo apt-get -y install gradle

  Build:
    - if [[ -f gradlew ]]; then
    -   ./gradlew assemble
    - elif [[ -f build.gradle ]]; then
    -   gradle assemble
    - elif [[ -f pom.xml ]]; then
    -   mvn install -DskipTests=true -Dmaven.javadoc.skip=true -B -V
    - fi

    - if [[ -f gradlew ]]; then
    -   ./gradlew check
    - elif [[ -f build.gradle ]]; then
    -   gradle check
    - elif [[ -f pom.xml ]]; then
    -   mvn test -B
    - else
    -   ant test
    - fi
~~~

## Ruby


<table cellpadding="2" cellspacing="2">
  <tr>
    <td>
      <p>Language:</p>
    </td>
    <td>
      <p>Ruby<br>jruby<br>rbx</p>
    </td>
    <td>
      <p>Ruby programming language</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Versions:</p>
    </td>
    <td>
      <p>ruby 1.8.6+<br>jruby 1.6.8+<br>rbx 1.4.3+</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td>
      <p>Tools Used:</p>
    </td>
    <td>
      <p>curl: A software transfer tool</p>
    </td>
    <td>
      <p><a href="http://curl.haxx.se/">http://curl.haxx.se/</a>
      </p>
    </td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>rvm: A ruby version manager</p>
    </td>
    <td>
      <p><a href="https://rvm.io/">https://rvm.io/</a>
      </p>
    </td>
  </tr>
</table>


<h3>Installing Tools</h3>

This code will ensure the apt-get index is up to date then install the necessary tools. If the tools already exist, apt-get will display a warning and the PreRelease phase will continue.

~~~
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install curl
    - gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    - curl -sSL https://get.rvm.io | bash -s stable --autolibs=enabled
    - source ~/.rvm/scripts/rvm
~~~

<h3>Specifying a Ruby Version to Use</h3>

~~~
    - rvm install ruby-1.9.3
    - rvm use 1.9.3
~~~

<h3>Installing Build Dependencies</h3>


~~~
    - gem install bundle
~~~

<h3>Building the Application</h3>

~~~
    - if [[ -f Gemfile ]]; then
    - if [[ -f Gemfile.lock ]]; then
    - bundle install --jobs=3 --retry=3 --deployment
    - else
    - bundle install --jobs=3 --retry=3
    - fi
    - fi
~~~

<h3>Testing the Application</h3>

~~~
    - if [[ -f Gemfile ]]; then
    - bundle exec rake
    - else
    - rake
    - fi
~~~


<h3>Full Ruby Example</h3>


~~~
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install curl
    - gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    - curl -sSL https://get.rvm.io | bash -s stable --autolibs=enabled
  Build:
    - source ~/.rvm/scripts/rvm
    - rvm install ruby-1.9.3
    - rvm use 1.9.3
    - gem install bundle
    - if [[ -f Gemfile ]]; then
    -   if [[ -f Gemfile.lock ]]; then
    -     bundle install --jobs=3 --retry=3 --deployment
    -   else
    -     bundle install --jobs=3 --retry=3
    -   fi
    - fi
    - if [[ -f Gemfile ]]; then
    -   bundle exec rake
    - else
    -   rake
    - fi
~~~


## Rust

<table cellpadding="2" cellspacing="2">
  <tr>
    <td>
      <p>Language:</p>
    </td>
    <td>
      <p>Rust</p>
    </td>
    <td>
      <p>The Rust programming language <a href="https://www.rust-lang.org/">https://www.rust-lang.org/</a></p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Versions:</p>
    </td>
    <td>
      <p>0.7+</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td>
      <p>Tools Used:</p>
    </td>
    <td>
      <p>ppa:hansjorg/rust rust language repository</p>
    </td>
    <td>
      <p>rust version manager
      <a rel="nofollow" href="https://launchpad.net/~hansjorg/+archive/ubuntu/rust" target="_blank">https://launchpad.net/~hansjorg/+archive/ubuntu/rust</a>
            </p>
    </td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>cargo: rust package manager</p>
    </td>
    <td>
      <p><a rel="nofollow" href="https://crates.io" target="_blank">https://crates.io</a>
      </p>
    </td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>gcc: compiler</p>
    </td>
    <td>
      <p>To compile rust packages for cargo</p>
    </td>
  </tr>
</table>


> **Note:** This example uses ppa:hansjorg/rust to install versions of rust.

<h3>Installing Tools</h3>

This code will ensure the apt-get index is up to date, add the ppa:hansjorg/rust, and then install the necessary tools. If the tools already exist, apt-get will display a warning and the PreBuild phase will continue.

~~~
  PreBuild:
    - echo "Begin PreInstall"
    - sudo add-apt-repository -y ppa:hansjorg/rust
    - sudo apt-get -y update
    - sudo apt-get -y install gc
~~~

<h3>Specifying a Rust Version to Use </h3>

Using the `ppa:hansjorg/rust` you can easily install a version of rust and cargo. Here is an example installing the nightly build.

~~~
    - sudo apt-get -y install rust-nightly
~~~

Other install options include:

~~~
rust-0.10
rust-stable
rust-nightly
~~~

<h3>Getting Rust Package Manager</h3>

Cargo is the package manager for rust and is used to run the builds and tests.

~~~
    - sudo apt-get -y install cargo-nightly
~~~

<h3>Building a Rust Application</h3>

This will build the rust application.

~~~
    - cargo build --verbose
~~~

<h3>Running the Tests</h3>

The following will run your rust tests.

~~~
    - cargo test --verbose
~~~

<h3>Full Rust Example</h3>


~~~
  PreBuild:
    - sudo add-apt-repository -y ppa:hansjorg/rust
    - sudo apt-get -y update
    - sudo apt-get -y install gcc
    - sudo apt-get -y --force-yes install rust-stable
    - sudo apt-get -y --force-yes install cargo-nightly
  Build:
    - cargo build --verbose
    - cargo test --verbose
~~~


## PHP


<table cellpadding="2" cellspacing="2">
  <tr>
    <td>
      <p>Language:</p>
    </td>
    <td>
      <p>PHP</p>
    </td>
    <td>
      <p>The PHP language.</p>
    </td>
  </tr>
  <tr>
    <td>
      <p>Versions:</p>
    </td>
    <td>
      <p>5.2.17+</p>
    </td>
    <td></td>
  </tr>
  <tr>
    <td>
      <p>Tools Used:</p>
    </td>
    <td>
      <p>phpenv: php version manager</p>
    </td>
    <td>
      <p><a href="https://github.com/CHH/phpenv">https://github.com/CHH/phpenv</a></p>
    </td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>php-build: php version download and build tool</p>
    </td>
    <td>
      <p><a href="https://github.com/php-build/php-build">https://github.com/php-build/php-build</a></p>
    </td>
  </tr>
  <tr>
    <td></td>
    <td>
      <p>composer: PHP Package manager</p>
    </td>
    <td>
      <p><a href="https://getcomposer.org/">https://getcomposer.org</a></p>
    </td>
  </tr>
</table>


<h3>Setting the Database Password</h3>

This section sets an environment variable that will be used later to provision the database root login.

~~~
  Env:
    - MYSQL_ROOT_PASSWORD: "pa55w0rd"
~~~

<h3>Installing Tools</h3>

This code will ensure the apt-get index is up to date and then install the necessary dependencies.

~~~
  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install git
    - 'sudo debconf-set-selections <<< "mysql-server mysql-server/root_password password $MYSQL_ROOT_PASSWORD"'
    - 'sudo debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $MYSQL_ROOT_PASSWORD"'
    - sudo apt-get -y install build-dep php5
    - sudo apt-get -y install libmcrypt-dev libreadline-dev
~~~

Install phpenv the php version manager

~~~
    - git clone https://github.com/CHH/phpenv.git
    - phpenv/bin/phpenv-install.sh
    - export PATH="~/.phpenv/bin:$PATH"
    - eval "$(phpenv init -)"
~~~

Install php-build the php version downloader

~~~
    - git clone https://github.com/php-build/php-build.git
    - sudo php-build/install.sh
~~~

<h3>Specifying PHP Version to Use</h3>


~~~
    - php-build -i development 5.4.9 ~/.phpenv/versions/5.4.9
    - phpenv rehash
    - phpenv versions
    - phpenv global 5.4.9
    - php --version
~~~

<h3>Installing Composer Package Manager and Install Packages</h3>

> **Important:** Composer requires PHP 5.3.2+

~~~
    - curl -sS https://getcomposer.org/installer | php
    - if [[ -f composer.json ]]; then
    -   php composer.phar install
    - fi
    - php composer.phar require symfony/framework-bundle
~~~


You can `require` other dependencies in the manifest file at the end of this section, as the above example depicts installing symfony/framework-bundle.

<h3>Installing phpunit and Run Tests</h3>


~~~
    - wget https://phar.phpunit.de/phpunit.phar
    - chmod +x phpunit.phar
    - sudo mv phpunit.phar /usr/local/bin/phpunit
    - phpunit --version
    - phpunit
~~~

<h3>Full PHP Example</h3>

~~~
Env:
- MYSQL_ROOT_PASSWORD: \pa55w0rd\

PreInstall:
  Env:
    - MYSQL_ROOT_PASSWORD: "pa55w0rd"

  PreBuild:
    - sudo apt-get -y update
    - sudo apt-get -y install git
    - 'sudo debconf-set-selections <<< "mysql-server mysql-server/root_password password $MYSQL_ROOT_PASSWORD"'
    - 'sudo debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $MYSQL_ROOT_PASSWORD"'
    - sudo apt-get -y install build-dep php5
    - sudo apt-get -y install libmcrypt-dev libreadline-dev

    - git clone https://github.com/CHH/phpenv.git
    - phpenv/bin/phpenv-install.sh
    - export PATH="~/.phpenv/bin:$PATH"
    - eval "$(phpenv init -)"

    - git clone https://github.com/php-build/php-build.git
    - sudo php-build/install.sh

  Build:
    - php-build -i development 5.4.9 ~/.phpenv/versions/5.4.9
    - phpenv rehash
    - phpenv versions
    - phpenv global 5.4.9
    - php --version

    - curl -sS https://getcomposer.org/installer | php
    - if [[ -f composer.json ]]; then
    -   php composer.phar install
    - fi
    - php composer.phar require symfony/framework-bundle

    - wget https://phar.phpunit.de/phpunit.phar
    - chmod +x phpunit.phar
    - sudo mv phpunit.phar /usr/local/bin/phpunit
    - phpunit --version
    - phpunit
~~~



