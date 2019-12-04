# Razor known issues

These are the known issues for Razor in this release.

## Temp files aren't removed in a timely manner

Temp files used by Razor might not be removed as quickly as expected. Files are eventually removed when the object is finalized.

## Updates might be required for VMware ESXi 5.5 igb files

You might have to update your VMware ESXi 5.5 ISO with updated igb drivers before you can install ESXi with Razor. See the VMware [driver package download](https://my.vmware.com/web/vmware/details?downloadGroup=DT-ESXI55-INTEL-IGB-42168&productId=353) page for updated igb drivers.

## Razor commands resulted in JSON warning

When you run Razor commands, you might get this warning: `MultiJson is using the default adapter (ok_json). We recommend loading a different JSON library to improve performance.`

You can disregard the warning. However, if you're using Ruby 1.8.7, you can install a separate JSON library, such as `json_pure`, to prevent the warning from appearing.

## `pe-razor` doesn't allow `java_args` configuration

Most PE services enable you to configure `java_args` in the console, but Razor requires you to hard code them in the `init` script.

