# Write a simple manifest

Puppet manifest files are lists of resources that have a unique title and a set of named attributes that describe the desired state.

## Before you begin

You need a text editor, for example Visual Studio Code \(VS Code\), to create manifest files. Puppet has an [extension](https://puppet-vscode.github.io/docs/getting-started/) for VS Code that supports syntax highlighting of the Puppet language. Editors like Notepad++ or Notepad won't highlight Puppet syntax, but can also be used to create manifests.

## About this task

Manifest files are written in Puppet code, a domain specific language \(DSL\) that defines the desired state of resources on a system, such as files, users, and packages. Puppet compiles these text-based manifests into catalogs, and uses those to apply configuration changes.

## Procedure

1.  Create a file named `file.pp` and save it in `c:\myfiles\`

2.  With your text editor of choice, add the following text to the file:

    ```
    file { 'c:\\Temp\\foo.txt':
      ensure   => present,
      content  => 'This is some text in my file'
    }
    ```


## Results

Note the following details in this file resource example:

-   Puppet uses a basic syntax of `type { title: }`, where `type` is the resource type — in this case it’s `file`.

-   The resource title \(the value before the `:`\) is `C:\\Temp\\foo.txt`. The file resource uses the title to determine where to create the file on disk. A resource title must always be unique within a given manifest.

-   The `ensure` parameter is set to `present` to create the file on disk, if it's not already present. For `file` type resources, you can also use the value `absent`, which removes the file from disk if it exists.

-   The `content` parameter is set to `This is some text in my file`, which writes that value to the file.


