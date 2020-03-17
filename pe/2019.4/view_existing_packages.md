# View existing packages

Chocolatey has a custom provider for the package resource type, so you can use `puppet resource` to view existing packages.

1.  In the command prompt, run `puppet resource package --param provider | more`

    The additional provider parameter in this command outputs all types of installed packages that are detected by multiple providers.


**Parent topic:**[Create, install and repackage packages with the chocolatey module](create_install_repackage_with_chocolatey.md)

