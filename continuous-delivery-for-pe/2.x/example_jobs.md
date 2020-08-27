---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Testing Puppet code with jobs

Jobs are fully customizable tests for your Puppet code. You can create a job that runs any sort of test you wish, from module validation to linting.

**Important:** Make sure you have [set up job hardware](configure_job_hardware.md#) and installed any needed software \(such as Docker, Puppet Development Kit or the Puppet agent\) before you attempt add one of these jobs to a pipeline.

## What is a job?

In Continuous Delivery for PE, jobs are tests for your Puppet code. Jobs are created and stored in the web UI, where you can run them on demand and add them to an automated pipeline that runs tests every time new code is committed to a repository.

Jobs are fully customizable and can be written to test any aspect of your code. Jobs can be written to take advantage of Puppet Development Kit \(PDK\) and other testing tools created and maintained by the Puppet community, such as:

-   rspec-puppet

-   onceover

-   puppet-lint

-   rspec-puppet-facts


It's important to be aware of any prerequisites or dependencies needed to run your jobs, and to ensure that your job hardware has the necessary software, operating systems, and other resources installed before attempting to run the job.

## Install job dependencies

In order to run a job successfully, the selected job hardware must have any required dependencies installed, such as software or operating systems.

### About this task

Continuous Delivery for PE comes with six jobs pre-installed, so you don't need to create new jobs from scratch in order to get started testing your code. View these jobs by clicking **Jobs** in the web UI.

In order to run the pre-installed jobs, Docker must be installed on your job hardware.

### Procedure

1.  Follow the [Installing Docker](https://docs.docker.com/install/) instructions appropriate to your job hardware.

2.  In the Continuous Delivery for PE web UI, click **Hardware**. Locate your job server.

3.  Click **+ Add Capability** and enter DOCKER, then click **Save**.


### Result:

## Pre-built job reference

Continuous Delivery for PE offers users six pre-built Docker-based jobs for testing control repos and modules. These jobs run inside a Docker container, and must be run on job hardware on which you've set a Docker capability.

### Validate the syntax of a control repo's Puppetfile

To add this job to your Continuous Delivery for PE instance, click **New Job**. Fill in the fields indicated with the information provided, then click **Create Job**.

|Field|Content|
|-----|-------|
|Job Name|control-repo-puppetfile-syntax-validate|
|Description|Validate that a control repo’s Puppetfile is syntactically correct|
|Commands: Job|`rake -f /Rakefile r10k:syntax`|
|Docker Configuration|Run this job in a Docker container|
|Docker Image Name|puppet/puppet-dev-tools|
|Job Hardware: Capabilities|DOCKER|

Before running this job, [configure job hardware](configure_job_hardware.md#) and ensure that Docker is installed.

### Validate the syntax of a control repo's Puppet templates

To add this job to your Continuous Delivery for PE instance, click **New Job**. Fill in the fields indicated with the information provided, then click **Create Job**.

|Field|Content|
|-----|-------|
|Job Name|control-repo-template-syntax-validate|
|Description|Validate that a control repo’s Puppet templates are syntactically correct|
|Commands: Job|`rake -f /Rakefile syntax:templates`|
|Docker Configuration|Run this job in a Docker container|
|Docker Image Name|puppet/puppet-dev-tools|
|Job Hardware: Capabilities|DOCKER|

Before running this job, [configure job hardware](configure_job_hardware.md#) and ensure that Docker is installed.

### Validate the syntax of a control repo's Hiera data

To add this job to your Continuous Delivery for PE instance, click **New Job**. Fill in the fields indicated with the information provided, then click **Create Job**.

|Field|Content|
|-----|-------|
|Job Name|control-repo-hiera-syntax-validate|
|Description|Validate that a control repo’s Hiera data is syntactically correct|
|Commands: Job|`rake -f /Rakefile syntax:hiera`|
|Docker Configuration|Run this job in a Docker container|
|Docker Image Name|puppet/puppet-dev-tools|
|Job Hardware: Capabilities|DOCKER|

Before running this job, [configure job hardware](configure_job_hardware.md#) and ensure that Docker is installed.

### Validate the syntax of a control repo's Puppet manifest code

To add this job to your Continuous Delivery for PE instance, click **New Job**. Fill in the fields indicated with the information provided, then click **Create Job**.

|Field|Content|
|-----|-------|
|Job Name|control-repo-manifest-validate|
|Description|Validate that a control repo’s Puppet manifest code is syntactically correct|
|Commands: Job|`rake -f /Rakefile syntax:manifests`|
|Docker Configuration|Run this job in a Docker container|
|Docker Image Name|puppet/puppet-dev-tools|
|Job Hardware: Capabilities|DOCKER|

Before running this job, [configure job hardware](configure_job_hardware.md#) and ensure that Docker is installed.

### Validate the syntax of a module's Puppet manifest code

To add this job to your Continuous Delivery for PE instance, click **New Job**. Fill in the fields indicated with the information provided, then click **Create Job**.

|Field|Content|
|-----|-------|
|Job Name|module-pdk-validate|
|Description|Validate that a module’s Puppet manifest code is syntactically correct|
|Commands: Job|`pdk validate --parallel`|
|Docker Configuration|Run this job in a Docker container|
|Docker Image Name|puppet/puppet-dev-tools|
|Job Hardware: Capabilities|DOCKER|

Before running this job, [configure job hardware](configure_job_hardware.md#) and ensure that Docker is installed.

### Run rspec-puppet unit tests on a module

To add this job to your Continuous Delivery for PE instance, click **New Job**. Fill in the fields indicated with the information provided, then click **Create Job**.

|Field|Content|
|-----|-------|
|Job Name|module-rspec-puppet|
|Description|Run rspec-puppet unit tests on a module|
|Commands: Job|`pdk test unit`|
|Docker Configuration|Run this job in a Docker container|
|Docker Image Name|puppet/puppet-dev-tools|
|Job Hardware: Capabilities|DOCKER|

Before running this job, [configure job hardware](configure_job_hardware.md#) and ensure that Docker is installed.

## Sample non-Docker-based module jobs

This section lists sample jobs that you can use with Continuous Delivery for PE. These jobs can be entered as-is into the New Job creation screen in the Continuous Delivery for PE web UI, or you can make alterations to suit your deployment's needs.

### Puppet Development Kit validation tests

Use the sample jobs below to validate your module code against PDK. Select the version appropriate to your operating system.  

In order to use this job successfully, you must:

-   [Install PDK](https://puppet.com/docs/pdk/1.x/pdk_install.html) on your job hardware

-   Install `puppet-agent` on your job hardware


|Job name|Description|Commands|Capabilities|
|--------|-----------|--------|------------|
|module-pdk-validate-linux|Validate via PDK|`pdk validate`|LINUX|
|module-pdk-validate-windows|Validate via PDK|`powershell.exe -c "pdk validate"`|WINDOWS|

### Puppet Development Kit `rspec-puppet` tests

Use the sample jobs below to run unit tests on your module code with `rspec-puppet`. Select the version appropriate to your operating system.  

In order to use this job successfully, you must:

-   [Install PDK](https://puppet.com/docs/pdk/1.x/pdk_install.html) on your job hardware

-   Install `puppet-agent` on your job hardware


|Job name|Description|Commands|Capabilities|
|--------|-----------|--------|------------|
|module-pdk-test-unit-linux|Run unit tests via PDK|`pdk test unit`|LINUX|
|module-pdk-test-unit-windows|Run unit tests via PDK|`powershell.exe -c "pdk test unit"`|WINDOWS|

## Sample non-Docker-based control repo jobs

This section lists sample jobs that you can use with Continuous Delivery for PE. These jobs can be entered as-is into the New Job creation screen in the Continuous Delivery for PE web UI, or you can make alterations to suit your deployment's needs.

### Syntax validation

This job validates the syntax of everything in your control repo.

In order to use this job successfully, you must:

-   Use a \*nix host

-   Install `puppet-agent` on your job hardware


|Job name|Description|Commands|Capabilities|
|--------|-----------|--------|------------|
|control-repo-validate-linux|Validate syntax|\[See below\]|LINUX|

```
#!/bin/bash
 
shopt -s globstar nullglob
green="$(tput setaf 2)"
red="$(tput setaf 1)"
reset="$(tput sgr0)"
 
for f in **/**pp; do
   [[ $f =~ plans/ ]] && continue
 
   if puppet parser validate "$f"; then
      echo "${green}SUCCESS: $f${reset}"
   else
      echo "${red}FAILED: $f${reset}"
      failures+=("$f")
   fi
done
 
if (( ${#failures[@]} > 0 )); then
   echo "${red}Syntax validation on the Control Repo has failed in the following manifests:"
   echo -e "\t ${failures[@]}${reset}"
   exit 1
else
   echo "${green}Syntax validation on the Control Repo has succeeded.${reset}"
fi
```

### Puppet Linter

This job checks the Puppet code in your control repo for programming and stylistic errors.

In order to use this job successfully, you must:

-   Use a \*nix host

-   Install `puppet-agent` on your job hardware


|Job name|Description|Commands|Capabilities|
|--------|-----------|--------|------------|
|control-repo-lint-linux|Lint Puppet code|\[See below\]|LINUX|

```
#!/bin/bash
 
shopt -s globstar nullglob
green="$(tput setaf 2)"
red="$(tput setaf 1)"
reset="$(tput sgr0)"
 
sudo /opt/puppetlabs/puppet/bin/gem install puppet-lint || {
   echo "${red}Failed to install puppet-lint gem"
   exit 2
}
 
LINT_OPTS=("--fail-on-warnings" "--no-documentation-check" "--no-140chars-check" "--no-autoloader_layout-check" "--no-class_inherits_from_params_class-check")
 
for f in **/**pp; do
   [[ $f =~ plans/ ]] && continue
 
   if /opt/puppetlabs/puppet/bin/puppet-lint "${LINT_OPTS[@]}" "$f"; then
      echo "${green}SUCCESS: $f${reset}"
   else
      echo "${red}FAILED: $f${reset}"
      failures+=("$f")
   fi
done
 
if (( ${#failures[@]} > 0 )); then
   echo "${red}Puppet-lint validation on the Control Repo has failed in the following manifests:"
   echo -e "\t ${failures[@]}${reset}"
   exit 1
else
   echo "${green}Puppet-lint validation on the Control Repo has succeeded.${reset}"
fi
```

