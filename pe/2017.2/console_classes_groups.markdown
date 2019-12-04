---
layout: default
title: "Grouping and classifying nodes"
canonical: "/pe/latest/console_classes_groups.html"
---


[lang_classes]: {{puppet}}/lang_classes.html
[learn]: /learning/
[forge]: http://forge.puppetlabs.com
[modules]: {{puppet}}/modules_fundamentals.html
[topscope]: {{puppet}}/lang_scope.html#top-scope
[classifying]: ./console_classes_groups_getting_started.html
[environments]: {{puppet}}/environments.html
[agent_config_assign]: {{puppet}}/environments_assigning.html#assigning-environments-via-the-agents-config-file
[creating environments]: {{puppet}}/environments_creating.html

## Types of node groups

You can create two types of node groups:

- **Environment node groups**, which assign environments to nodes.
- **Classification node groups**, which assign classification data to nodes.

A node group assigns either environment _or_ classification data, not both. The role of an environment node group is to set the environment, such as test, development, or production, for your nodes. The role of a classification node group is to assign classification data, such as classes, parameters, and variables, to nodes.

> **Tip:** For more information on using an environment-based workflow in the PE console, see [Working With Environments](./console_classes_groups_environment_override.html).

> **Important**: The node classifier uses role-based access control (RBAC) to manage access to tasks, including the tasks described on this page. For info about managing access, see the [RBAC documentation](./rbac_intro.html).

## Preconfigured environment node groups

To help you manage your infrastructure, PE comes with several preconfigured node groups, including two environment node groups:

* The **Production environment** group comes with a rule that matches all nodes, and assigns the production environment. This means your nodes will default to the production environment unless you specify otherwise.
* The **Agent-specified environment** group matches no nodes by default. This group doesn't assign an environment, so nodes that you add to the group use the environment from their config file. Add nodes to this group if you need them to preserve their agent-specified environment.

For more information about these and other preconfigured node groups, see [Preconfigured Node Groups](./console_classes_groups_preconfigured_groups.html).

## Creating environment node groups

Create environment node groups so that your nodes aren't all in the production environment.

1. In the console, click **Classification**, and click **Add group**.
2. Specify options for the new node group:
   - **Parent name** -- Select **Production environment**. Every environment node group you add must be a descendent of this group. This lets the new group inherit the ability to override a member node's agent-specified environment setting.
   - **Group name** -- Enter a name that describes the role of this environment node group, for example, *Test environment*.
   - **Environment** -- Select the environment that you want to assign to nodes that match this node group. If you haven’t [created environments][creating environments] yet, you see only the  **production** and **agent-specified** environments.
   - **Environment group** -- Select this option.
3. Click **Add**.

You can now add nodes to your environment node group to control which environment each node belongs to.

## Creating classification node groups

1. In the console, click **Classification**, and click **Add group**.
2. Specify options for the new node group:
   - **Parent name** -- Select the name of the classification node group that you want to set as the parent to this node group. Classification node groups inherit classes, parameters, and variables from their parent node group. By default, the parent node group is the **All Nodes** node group.

     > **Tip:** Node group inheritance allows you to assign a class once in a parent node group and avoid assigning it in each descendent node group. For more information about node group inheritance, see [How Does Inheritance Work?](./console_classes_groups_inheritance.html)
   - **Group name** -- Enter a name that describes the role of this classification node group, for example, *Web Servers*.

   - **Environment** -- Specify an environment to limit the classes and parameters available for selection in this node group.

     > **Note:** Specifying an environment in a *classification* node group doesn't assign an environment to any nodes, as it does in an [environment node group](#creating-environment-node-groups).

   - **Environment group** -- *Do not* select this option.
3. Click **Add**.

You can now add nodes to your classification node group [dynamically](#adding-nodes-dynamically) or [statically](#adding-nodes-statically). After that, you can specify the Puppet classes you want to apply to the nodes in the classification node group.

To change a classification node group later, see:

* [Editing node groups](./console_classes_groups_making_changes.html#editing-node-groups)
* [Deleting node groups](./console_classes_groups_making_changes.html#deleting-node-groups)


## Adding nodes to a node group

There are two ways to add nodes to a node group:

1. Create rules that match node facts (dynamic)

2. Individually pin nodes to the node group (static)


### Adding nodes dynamically
Rules are the most powerful and scalable way to include nodes in a node group. You can create rules in a node group that are used to match [node facts]({{facter}}/core_facts.html). When nodes match the rules in a node group, they're classified with all of the classification data (classes, parameters, and variables) for the node group.

When nodes no longer match the rules of a node group, the classification data for that node group no longer applies to the node.

**To add a rule:**

1. In the console, click **Classification**, and then find the node group that you want to add the rule to and select it.

2. On the **Rules** tab, specify rules for the fact, then click **Add rule**.

   <table>
     <tr>
       <th>Option</th>
       <th>Definition</th>
     </tr>
     <tr>
       <td><b>Fact</b></td>
       <td><p> Specifies the fact used to match nodes.</p>
        <p>Select from the list of known facts, or enter part of a string to view fuzzy matches.</p>
        <p>To use structured or trusted facts, select the initial value from the dropdown list, then type the rest of the fact.</p>
        <p>
        <ul>
         <li>To descend into a hash, use dots (".") to designate path segments, such as <code>os.release.major</code> or <code>trusted.certname</code>.</li>
         <li>To specify an item in an array, surround the numerical index of the item in square brackets, such as <code>processors.models[0]</code> or <code>mountpoints./.options[0]</code>.</li>
         <li>To identify path segments that contain dots or UTF-8 characters, surround the segment with single or double quotes, such as <code>trusted.extensions."1.3.6.1.4.1.34380.1.2.1"</code>.</li>
         <li>To use trusted extension short names, append the short name after a second dot, such as <code>trusted.extensions.pp_role</code>.</li>
         </ul></p>
         <p><b>Tip</b>: Structured and trusted facts don't provide type-ahead suggestions beyond the top-level name key, and the facts aren't verified when entered. After adding a rule for a structured or trusted fact, review the number of matching nodes to verify that the fact was entered correctly.</p></td>
     </tr>
     <tr>
       <td><b>Operator</b></td>
       <td><p>Describes the relationship between the fact and value.</p>
        <p>Operators include:
        <ul>
         <li><b>=</b> &mdash; is</li>
         <li><b>!=</b> &mdash; is not</li>
         <li><b>~</b> &mdash; matches regex</li>
         <li><b>!~</b> &mdash; does not match regex</li>
         <li><b>></b> &mdash; greater than</li>
         <li><b>>=</b> &mdash; greater than or equal to</li>
         <li><b><</b> &mdash; less than</li>
         <li><b><=</b> &mdash; less than or equal to</li>
         </ul></p>
         <p>The numeric operators <b>></b>, <b>>=</b>, <b><</b>, and <b><=</b> can be used only with facts that have a numeric value.</p>
        <p>To match highly specific node facts, use <b>~</b> or <b>!~</b> with a regular expression for <b>Value</b>.</p></td>
     </tr>
     <tr>
       <td><b>Value</b></td>
       <td>Specifies the value associated with the fact.</td>
     </tr>
   </table>

3. (Optional) Repeat step 2 as needed to add more rules.

   > **Tip**: If the node group includes multiple rules, be sure to specify whether **Nodes must match all rules** or **Nodes may match any rule**.

4. Commit changes.

#### Using structured and trusted facts for node group rules

[Structured facts]({{facter}}/fact_overview.html#writing-structured-facts) group a set of related facts in the form of a hash or array. For example, the structured fact `os` includes multiple independent facts about the operating system, including architecture, family, and release. In the console, when you view facts about a node, you can differentiate structured facts because they're surrounded by curly braces.

[Trusted facts]({{puppet}}/lang_facts_and_builtin_vars.html#trusted-facts) are a specific type of structured fact where the facts are immutable and extracted from a node's certificate. Because they can't be changed or overridden, trusted facts enhance security by verifying a node's identity before sending sensitive data in its catalog. For details, see [More about certificate extensions]({{puppet}}/config_file_oid_map.html#more-about-certificate-extensions).

You can use structured and trusted facts in the console to [dynamically add nodes to groups](#adding-nodes-dynamically).

**Note**: If you're using trusted facts to specify certificate extensions, in order for nodes to match to rules correctly, you must use short names for [Puppet-specific registered IDs]({{puppet}}/ssl_attributes_extensions.html#puppet-specific-registered-ids) and numeric IDs for [private extensions]({{puppet}}/ssl_attributes_extensions.html#recommended-oids-for-extensions). Numeric IDs are required for private extensions whether or not you specify a short name in the `custom_trusted_oid_mapping.yaml` file.



### Adding nodes statically
If you have a node that needs to be in a node group regardless of the rules that have been specified for that node group, you can *pin* the node to the node group. A pinned node is not affected by rules and will remain in the node group until you manually remove it. Adding a pinned node essentially creates the rule `<the certname of your node>` **=** `<the certname>`, and includes this rule along with the other fact-based rules.

**To pin a node to a node group:**

1. In the console, click **Classification**, and then find the node group that you want to pin a node to and select it.

2. On the **Rules** tab, in the pinned nodes section, enter the [**Certname**]({{puppet}}/configuration.html#certname) of the node.

3. Click **Pin node**, and then commit changes.

[Removing Nodes From a Node Group](./console_classes_groups_making_changes.html#removing-nodes-from-a-node-group)

## Adding classes to a node group

[Classes][lang_classes] are the blocks of Puppet code used to configure your nodes and assign resources to them. To add a class to a node classification group, first create the class in a module. You'll then need to install the module.

> **Creating Puppet classes**
>
> Before you can add a class to a node group, you need to make the class available to the Puppet master. This means that the class must be located in an installed [module][modules]. There are two ways to get modules:
>
> 1. [Download modules from the Puppet Forge][forge]. In addition to the many public modules that are available, the Puppet Forge also provides supported modules and approved modules. Supported modules are rigorously tested with PE and are supported by Puppet via the usual [support channels](http://puppet.com/services/customer-support). Approved modules have passed Puppet's quality and reliability standards and are recommended by Puppet for use with PE.
>
> 2. [Write your own classes][lang_classes], and put them in a [module][modules].
>
> **Tip:** If you are new to Puppet and have not written Puppet code before, [follow the Learning Puppet tutorial][learn], which walks you through the basics of Puppet code, classes, and modules.

**To add a class to a node group:**

1. In the console, click **Classification**, and then find the node group that you want to add the class to and select it.

2. On the **Classes** tab, in the **Add new class** field, select the class to add.

   The **Add new class** field suggests classes that the Puppet master knows about and that are available in the environment set for the node group. The list filters as you type. You can enter substrings from anywhere within the class name.

3. Click **Add class** and then commit changes.

> **Note:** If you created a new class within the last three minutes, it may not appear in the list of available classes yet. There are two things that need to happen before the class appears in the list:
>
> 1. The node classifier needs to retrieve classes from the master. (By default, the node classifier retrieves classes from the master every 3 minutes. To change the default setting, see [Configuring and Tuning the Console](./console_config.html#tuning-the-classifier-synchronization-period)).
>
> 2. The [environment cache needs to refresh]({{puppet}}/configuration.html#environmenttimeout). (By default, the environment cache refreshes every 3 minutes.)
>
> To override the default refresh period and force the node classifier to retrieve the classes from the master immediately, click the **Refresh** button.

[Removing classes from a node group](./console_classes_groups_making_changes.html#removing-classes-from-a-node-group)

## Defining the data used by classes
You can use either parameters or variables to define the data used by classes. Parameters are scoped to the class, while variables are scoped to the node group.

### Setting class parameters

Classes will automatically use default parameters and values, or parameters and values inherited from parent node groups. However, if the nodes in a node group need to be an exception to the general case, you can override default and parent values by specifying new parameter values.

**To add a parameter:**

1. In the console, click **Classification**, and then find the node group that you want to add a parameter to and select it.

2. On the **Classes** tab, click the **Parameter name** drop-down list under the appropriate class and select the parameter to add. The drop-down list shows all of the parameters that are available in the node group’s environment.

3. (Optional) Change the default **Value**. When you select a parameter, the **Value** field is automatically populated with the default value.

> **Tips on specifying parameter and variable values**
>
> Parameters and variables can be structured as JSON. If they cannot be parsed as JSON, they will be treated as strings.
>
> Parameters and variables can be specified using the following data types and syntax:
>
>    * Strings (e.g. `"centos"`)
		- Variable-style syntax, which interpolates the result of referencing a fact (e.g. `"I live at $ipaddress."`)
		- Expression-style syntax, which interpolates the result of evaluating the embedded expression (e.g. `${$os["release"]["full"]}`)
>    * Booleans (e.g. `true` or `false`)
>    * Numbers (e.g. `123`)
>    * Hashes (e.g. `{"a": 1}`)
>    * Arrays (e.g. `["1","2.3"]`)
>
> **Variable-style syntax**
>
> Variable-style syntax uses a dollar sign ($) followed by a Puppet fact name.
>
> Example: `"I live at $ipaddress"`
>
> Variable-style syntax is interpolated as the value of the fact. For example, `$ipaddress` resolves to the value of the `ipaddress` fact.
>
> Indexing cannot be used in variable-style syntax because the indices are treated as part of the string literal. For example, given the following fact:
>
> `processors => {"count" => 4, "physicalcount" => 1}`,
>
> if you use variable-style syntax to specify `$processors[count]`, the value of the `processors` fact is interpolated but it is followed by a literal "[count]". After interpolation, this example becomes `{"count" => 4,"physicalcount" => 1}[count]`.
>
> **Note:** Do not use the `::` top-level scope indication because the console is not aware of Puppet's variable scope.
>
> **Expression-style syntax**
>
> Use expression-style syntax when you need to index into a fact (`${$os[release]}`), refer to trusted facts (`"My name is ${trusted[certname]}"`), or delimit fact names from strings (`"My ${os} release"`).
>
> The following is an example of using expression-style syntax to access the full release number of an operating system:
>
> 		${$os["release"]["full"]}
>
> Expression-style syntax uses:
>
> * an initial dollar sign and curly brace (`${`), followed by
> * a legal Puppet fact name preceded by an optional dollar sign, followed by
> * any number of index expressions (the quotations around indices are optional but are required if the index string contains spaces or square brackets), followed by
> * a closing curly brace (`}`).
>
> Indices in expression-style syntax can be used to access individual fields of structured facts, or to refer to trusted facts. Use strings in an index if you want to access the keys of a hashmap. If you want to access a particular item or character in an array or string based on the order in which it is listed, you can use an integer (zero-indexed).
>
> Examples of legal expression-style interpolation:
>
> * `${os}`
> * `${$os}`
> * `${$os[release]}`
> * `${$os['release']}`
> * `${$os["release"]}`
> * `${$os[2]}` (accesses the value of the third (zero-indexed) key-value pair in the `os` hash)
> * `${$os[release][2]}` (accesses the value of the third key-value pair in the `release` hash)
>
> In the PE console, an index can only be simple string literals or decimal integer literals. An index cannot include variables or operations (such as string concatenation or integer arithmetic).
>
> Examples of illegal expression-style interpolation:
>
> * `${$::os}`
> * `{$os[$release]}`
> * `${$os[0xff]}`
> * `${$os[6/3]}`
> * `${$os[$family + $release]}`
> * `${$os + $release}`
>
> **Trusted facts**
>
> Trusted facts are considered to be keys of a hashmap called `trusted`. This means that all trusted facts must be interpolated using expression-style syntax. For example, the certname trusted fact would be expressed like this: `"My name is ${trusted[certname]}"`. Any trusted facts that are themselves structured facts can have further index expressions to access individual fields of that trusted fact. For an overview of trusted facts, see the [Puppet Reference Manual]({{puppet}}/lang_facts_and_builtin_vars.html#trusted-facts).
>
> **Note:** Regular expressions, resource references, and other keywords (such as ‘undef’) are not supported.

[Editing Parameters](./console_classes_groups_making_changes.html#editing-parameters)

[Deleting Parameters](./console_classes_groups_making_changes.html#deleting-parameters)

### Setting variables

Variables set in the console become [top-scope variables available to all Puppet manifests][topscope]. When you define a variable, any class in the node group that references the variable will be given the value that you set here.

**To set a variable:**

1. In the console, click **Classification**, and then find the node group that you want to set a variable for and select it.

2. On the **Variables** tab, enter options for the variable:

   - **Key** -- Enter the name of the variable.

   - **Value** -- Enter the value that you want to assign to the variable.

3. Click **Add variable**, and then commit changes.

> **Note**: For information on the permitted syntax for specifying variable values, see "Tips on specifying parameter and variable values" in [Setting Class Parameters](#setting-class-parameters).

> **Note:** Nodes can match multiple node groups, and node groups are not necessarily arranged in a strict hierarchy. It is therefore possible for two equal node groups to contribute conflicting values for variables and class parameters. Conflicting values will cause a Puppet run on an agent to fail.

[Editing variables](./console_classes_groups_making_changes.html#editing-variables)

[Deleting variables](./console_classes_groups_making_changes.html#deleting-variables)

## Viewing the nodes that are in a node group

To view all nodes that currently match the rules specified for a node group:

1. In the console, click **Classification**, and then find the node group that you want to view and select it.

2. Click **Matching nodes**.

   You see the number of nodes that match the node group’s rules, along with a list of the names of matching nodes. This is based on the facts collected during the node's last Puppet run. The matching nodes list is updated as rules are added, deleted, and edited. Don’t forget that [nodes must match rules in ancestor node groups](./console_classes_groups_inheritance.html) as well as the rules of the current node group before they are actually considered to be a matching node.

> **Note**: If you have not set any rules for this node group yet, there will not be any matching nodes.

### Viewing the nodes managed by Puppet

To view the total number of nodes currently being managed by Puppet, go to **Inventory**. At the top of the page, you will see the number of nodes that have checked in with a Puppet master since the [last deletion of data from PuppetDB]({{puppetdb}}/maintain_and_tune.html#clean-up-old-reports).

This page also shows a list of the managed nodes.



