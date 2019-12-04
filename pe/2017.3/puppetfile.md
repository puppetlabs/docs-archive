# Managing environment content with a Puppetfile

A Puppetfile specifies detailed information about each environment's Puppet code and data, including where to get that code and data from, where to install it, and whether to update it.

Both Code Manager and r10k use a Puppetfile to install and manage the content of your environments.

## The Puppetfile

A Puppetfile is a text file that specifies what content you want in each environment, what version of that content you want, and where you want to get the content from.

Typically, a Puppetfile controls content such as:

-   Modules from the Forge
-   Modules from Git repositories
-   Data from Git repositories

For each environment that you want to manage content in, you need a Puppetfile. You'll create a base Puppetfile in your default environment \(usually `production`\). As you create new branches based on your default branch, each environment inherits this base Puppetfile. You can then edit each environment's Puppetfile as needed.

## Managing modules with code management

With code management, you install and manage your modules with a Puppetfile.

Almost all Puppet manifests are kept in modules, collections of Puppet code and data with a specific directory structure. By default, Code Manager and r10k install content in a modules directory \(`./modules`\) in the same directory the Puppetfile is in. For example, declaring the `puppetlabs-apache` module in the Puppetfile normally installs the module into `./modules/apache`. To learn more about modules, see the [module](https://docs.puppet.com/puppet/5.3/modules_fundamentals.html) documentation.

With Code Manager and r10k, **do not** use the `puppet module` command to install or manage modules. Instead, code management depends on the Puppetfile in each of your environments to install, update, and manage your modules. If you've installed modules to the live code directory with `puppet module install`, Code Manager deletes them.

**Note:** The Puppetfile does NOT include dependency resolution. You must make sure that you have every module needed for all of your specified modules to run. In addition, symlinks are unsupported; when you install modules with a Puppetfile, symlinks are not installed.

### Including your own modules

If you develop your own modules, maintain them in version control and include them in your Puppetfile as you would declare any module from a repository. If you have content in your control repo's module directory that is *not* listed in your Puppetfile, code management purges it. \(The control repo module directory is, by default, `./modules` relative to the location of the Puppetfile.\)

## Creating a Puppetfile

Your Puppetfile manages the content you want to maintain in that environment.

In a Puppetfile, you can declare:

-   Modules from the Forge.
-   Modules from a Git repository.
-   Data or other non-module content \(such as Hiera data\) from a Git repository.

You can declare any or all of this content as needed for each environment. It's best to create your first Puppetfile in your production branch. Then, as you create branches based on your production branch, edit each branch's Puppetfile as needed.

## Create a Puppetfile

Create a Puppetfile that manages the content maintained in your environment.

### Before you begin

Set up a control repo, with `production` as the default branch. To learn more about control repositories, see the [control repository](https://puppet.com/docs/pe/2019.0/control_repo.html) documentation.

### About this task

Create a Puppetfile in your production branch, and then edit it to declare the content in your production environment with the `mod` directive.

### Procedure

1.  On your production branch, in the root directory, create a file named `Puppetfile`.

2.  In your text editor, edit the Puppetfile, declaring modules and data content for your environment.

    You can declare modules from the Forge or you can declare Git repositories in your Puppetfile. See the related topics about declaring content in the Puppetfile for details and code examples.


### What to do next

After creating your Puppetfile, configure Code Manager or r10k.

## Change the Puppetfile module installation directory

If needed, you can change the directory to which the Puppetfile installs all modules.

### About this task

To specify a module installation path other than the default modules directory \(`./modules`\), use the `moduledir` directive.

This directive applies to *all* content declared in the Puppetfile. You must specify this as a relative path at the top of the Puppetfile, ****before**** you list any modules.

To change the installation paths for only certain modules or data, declare those content sources as Git repositories and set the `install_path` option. This option overrides the `moduledir` directive. See the related topic about how to declare content as a Git repo for instructions.

### Procedure

1.  Add the `moduledir` directive at the top of the Puppetfile, specifying your module installation directory relative to the location of the Puppetfile.

    `moduledir 'thirdparty'`


## Declare Forge modules in the Puppetfile

Declare Forge modules in your Puppetfile, specifying the version and whether or not code management should keep the module updated.

### About this task

Specify modules by their full name. You can specify the most recent version of a module, with or without updates, or you can specify a specific version of a module.

**Note:** Some existing Puppetfiles contain a `forge` setting that provides legacy compatibility with `librarian-puppet`. This setting is non-operational for Code Manager and r10k. To configure how Forge modules are downloaded, specify `forge_settings` in Hiera instead. See the topics about configuring the Forge settings for Code Manager or r10k for details. 

### Procedure

1.  In your Puppetfile, specify the modules to install with the `mod` directive. For each module, pass the module name as a string, and optionally, specify what version of the module you want to track.

    If you specify no options, code management installs the latest version and keeps the module at that version. To keep the module updated, specify `:latest`. To install a specific version of the module and keep it at that version, specify the version number as a string.

    ```
    mod 'puppetlabs/apache'
    mod 'puppetlabs/ntp', :latest
    mod 'puppetlabs/stdlib', '0.10.0'
    ```

    This example:

    -   Installs the latest version of the apache module, but does not update it.

    -   Installs the latest version of the ntp module, and updates it when environments are deployed.

    -   Installs version 0.10.0 of the stdlib module, and does not update it.


## Declare Git repositories in the Puppetfile

List the modules, data, or other non-module content that you want to install from a Git repository.

### About this task

To specify any environment content as a Git repository, use the `mod` directive with with the `:git` option. This is useful for modules you don't get from the Forge, such as your own modules, as well as data or other non-module content. 

### Procedure

1.  To install content and keep it updated to the master branch, declare the content name and specify the repository with the `:git` directive. Optionally, specify an `:install_path` for the content.

    ```
    mod 'apache',
        :git => 'https://github.com/puppetlabs/puppetlabs-apache'
    mod 'site_data', 
        :git => 'git@git.example.com:site_data.git',
        :install_path => 'hieradata'
    
    ```

    This example installs the apache module and keeps that module updated with the master branch of the listed repository. It also installs site data content from a Git repository into the environment's `./hieradata/site_data` subdirectory.


### Results

**Note:** Content is installed in the modules directory and treated as a module, unless you use the `:install_path option`. Use this option with non-module content to keep your data separate from your modules.

### Specify installation paths for repositories

You can set individual installation paths for any of the repositories that you declare in the Puppetfile.

#### About this task

The `:install_path` option allows you to separate non-module content in your directory structure or to set specific installation paths for individual modules. When you set this option for a specific repository, it overrides the `moduledir` setting.

#### Procedure

1.  To install the content into a subdirectory in the environment, specify the directory with the `install_path` option. To install into the root of the environment, specify an empty value.

    ```
    mod 'site_data_1', 
        :git => 'git@git.example.com:site_data_1.git',
        :install_path => 'hieradata'
    mod 'site_data_2',
      :git => 'git@git.example.com:site_data_2.git',
      :install_path => ''
    
    ```

    This example installs site data content from the `site_data_1` repository into `./hieradata/site_data` and content from `site_data_2` into `./site_data` subdirectory.


### Declare module or data content with SSH private key authentication

To declare content protected by SSH private keys, declare the content as a repository, and then configure the private key setting in your code management tool.

#### About this task

#### Procedure

1.  Declare your repository content, specifying the Git repo by the SSH URL.

    ```
    `mod 'myco/privatemod',`                 `
     :git => 'git@git.example.com:myco/privatemod.git'`
                      
    ```

2.  Configure the correct private key by setting Code Manager or r10k parameters in Hiera:

    -   To set a key for all Git operations, use the private key setting under `git-settings`.
    -   To set a private key for an individual remote, set the private key in the `repositories` hash in `git-settings` for each specific remote.

### Keep repository content at a specific version

The Puppetfile can maintain repository content at a specific version.

#### About this task

To specify a particular repository version, declare the version you want to track with the following options. Setting these options maintains the repository at that version and deploys any updates made to that particular version.

-   `ref`: Specifies the Git reference to check out. This option can reference either a tag, a commit, or a branch.

-   `tag`: Specifies the repo by a certain tag value.

-   `commit`: Specifies the repo by a certain commit.

-   `branch`: Specifies a certain branch of the repo.

-   `default_branch`: Specifies a default branch to use for deployment if the specified `ref`, `tag`, `commit`, or `branch` cannot be deployed. You must also specify one of the other version options. This is useful if you are tracking a relative branch of the control repo.


#### Procedure

1.  In the Puppetfile, declare the content, specifying the repository and version you want to track.

    To install `puppetlabs/apache` and specify the '0.9.0' tag, use the `tag` option.

    ```
    mod 'apache',
      :git => 'https://github.com/puppetlabs/puppetlabs-apache',
      :tag => '0.9.0'
    ```

    To install `puppetlabs/apache` and use the `branch` option to track the ‘proxy\_match’ branch.

    ```
    mod 'apache',
      :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
      :branch => 'proxy_match'
    ```

    To install `puppetlabs/apache` and use the `commit` option to track the '8df51aa' commit.

    ```
    mod 'apache',
      :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
      :commit => '8df51aa'
    ```


### Declare content from a relative control repo branch

The `branch` option also has a special `:control_branch` option, which allows you to deploy content from a control repo branch relative to the location of the Puppetfile.

#### About this task

Normally, `branch` tracks a specific named branch of a repo, such as testing. If you set it to `:control_branch`, it instead tracks whatever control repo branch the Puppetfile is in. For example, if your Puppetfile is in the production branch, content from the production branch is deployed; if a duplicate Puppetfile is located in testing, content from testing is deployed. This means that as you create new branches, you don't have to edit the inherited Puppetfile as extensively.

#### Procedure

1.  To track a branch within the control repo, declare the content with the `:branch` option set to `:control_branch`.

    ```
    mod 'hieradata',
      :git    => 'git@git.example.com:organization/hieradata.git',
      :branch => :control_branch 
    ```


### Set a default branch for content deployment

Set a `default_branch` option to specify what branch code management should deploy content from if the given option for your repository cannot be deployed.

#### About this task

If you specified a `ref`, `tag`, `commit`, or `branch` option for your repository, and it cannot be resolved and deployed, code management can instead deploy the `default_branch`. This is mostly useful when you set `branch` to the `:control_branch` value.

If your specified content cannot be resolved and you have not set a default branch, or if the default branch cannot be resolved, code management logs an error and does not deploy or update the content.

#### Procedure

1.  In the Puppetfile, in the content declaration, set the `default_branch` option to the branch you want to deploy if your specified option fails. 

    ```
    `# Track control branch and fall-back to master if no matching branch.
    mod 'hieradata',
         :git    => 'git@git.example.com:organization/hieradata.git',
         :branch => :control_branch,
         :default_branch => 'master'
    `
    ```


