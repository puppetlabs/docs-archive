# Upgrading to 3.x

The Continuous Delivery for Puppet Enterprise \(PE\) 3.x series is now available. When you're ready to upgrade from the 2.x series to the 3.x series, follow the directions on this page appropriate to your installation method.

The Continuous Delivery for PE 3.x series includes numerous exciting new features, including:

-   View Hiera changes in impact analysis reports
-   Manage your pipelines as a YAML file
-   Compose your own custom deployment policies

For a full list of new features, enhancements, and resolved issues, see the [3.x series release notes](https://puppet.com/docs/continuous-delivery/3.x/cd_release_notes.html).

**Upgrades from the 2.x series to the 3.x series are not automatic.** Instead, you must upgrade your version of the software by following the appropriate instructions on this page.

## Upgrade to 3.x by updating your `cd4pe` module classification

If you installed Continuous Delivery for PE using the `cd4pe` module or from the PE console, follow these instructions to upgrade to the 3.x series.

1.  In the PE console, click **Classification**.
2.  Expand the **PE Infrastructure** group, and select the **Continuous Delivery for PE** node group.
3.  Click **Configuration**.
4.  Under **Class: cd4pe**, in the **Parameter** field, select **cd4pe\_version**. In the **Value** field, enter 3.x. Click **Add parameter** and commit your changes.
5.  To apply the new version, run Puppet on the node group. At the top of the page, click the **Run** selector and choose **Puppet**. On the Run Puppet page that opens, make any adjustments and click **Run Job**.

When the Puppet run is complete, your Continuous Delivery for PE instance will be running the 3.x series. You'll now automatically upgrade to new 3.x series versions as they are released. See the [3.x series release notes](https://puppet.com/docs/continuous-delivery/3.x/cd_release_notes.html) for the latest new features, fixes, and enhancements.

## Upgrade to 3.x by updating your Docker image

If you installed Continuous Delivery for PE using Docker, follow these instructions to upgrade to the 3.x series.

1.  Update your container runtime of choice to use the following image:

    ```
    docker pull puppet/continuous-delivery-for-puppet-enterprise:3.x
    ```

2.  Deploy the new Continuous Delivery for PE containers.

When the deployment is complete, your Continuous Delivery for PE instance will be running the 3.x series. You'll now automatically upgrade to new 3.x series versions as they are released. See the [3.x series release notes](https://puppet.com/docs/continuous-delivery/3.x/cd_release_notes.html) for the latest new features, fixes, and enhancements.

