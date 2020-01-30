---
layout: default
title: "Puppet Container Registry Enterprise Edition release notes"
---

## Version 1.3.1-441259

Released 24 August 2018

Resolved in this release:

* **Integration of Google Container Registry accounts with Puppet Container Registry.** Users could not integrate Google Container Registry with Puppet Container Registry due to a bug in the parsing and saving of Google Cloud credentials. 
* **100 images limit in Elastic Container Registry accounts.** Elastic Container Registry accounts integrated with Puppet Container Registry did not display events for repositories with a large number of images. 
* **Syncing of updates to AWS Elastic Container Registry image repositories.** Delivery pipelines did not correctly push image repository changes from Puppet Container Registry to Elastic Container Registry repositories.