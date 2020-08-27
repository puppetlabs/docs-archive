---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Testing Puppet code with jobs

Jobs are fully customizable tests for your Puppet code. You can create a job that runs any sort of test you wish, from module validation to linting.

This page lists sample jobs that you can use with Continuous Delivery for Puppet Enterprise \(PE\). These jobs can be entered as-is into the New Job creation screen in the Continuous Delivery for PE web UI, or you can make alterations to suit your deployment's needs.

**Important:** Make sure you have [set up job hardware](configure_job_hardware.md#) and installed any needed software \(such as Puppet Development Kit or the Puppet agent\) before you attempt to dispatch one of these sample jobs or add it to a pipeline.

## Example module jobs

You can use the jobs in this section to test your module code.

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

## Example control repo jobs

You can use the jobs in this section to test the Puppet code in your control repos.

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

