---
layout: default
title: "Release Notes"
--- 

New features, enhancements, known issues, and resolved issues for Pipelines for Applications.

## Recently added features and improvements

* **Custom release versions.** Designate a customized version number for a release instead of a default Pipelines version number by adding a `CustomVersionCmd:` section to your distelli-manifest.yml file.
* **Pin a pipeline branch.** If you have a pipeline with multiple branches, pin the branch you work with most frequently so that it is shown by default whenever you view your pipeline.
* **Redesigned deployment interface.** We’ve updated the deployment workflow to make it more streamlined and consistent, and equipped it to pre-fill variables (such as task type and environment names) where appropriate.

## Known issues

### Renamed required status check for protected branches in GitHub and GitHub Enteprise
As part of our rebranding efforts, we renamed a required status check for protected branches in GitHub and GitHub Enterprise. Users of protected branches who have enabled this required status check must follow the instructions below to switch to the new status check. 

To fix this issue: 

1. In the GitHub or GitHub enterprise web UI, go to your repository's **Settings** and click **Branches**, then select your protected branch.
1. In the status checks area, deselect **continuous-integration/distelli** and select **continuous-integration/puppet**, then save your changes.