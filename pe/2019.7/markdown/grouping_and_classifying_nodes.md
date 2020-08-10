# Grouping and classifying nodes

Configure nodes by assigning classes, parameters, and variables to them. This is called classification.

The main steps involved in classifying nodes are:

1.  Create node groups.

2.  Add nodes to groups, either manually or dynamically, with rules.

3.  Assign classes to node groups.


Nodes can match the rules of many node groups. They receive classes, class parameters, and variables from all the node groups that they match.

## How node group inheritance works

Node groups exist in a hierarchy of parent and child relationships. Nodes inherit classes, class parameters and variables, and rules from all ancestor groups.

-   Classes – If an ancestor node group has a class, all descendent node groups also have the class.

-   Class parameters and variables – Descendent node groups inherit class parameters and variables from ancestors unless a different value is set for the parameter or variable in the descendent node group.

-   Rules – A node group can only match nodes that all of its ancestors also match. Specifying rules in a child node group is a way of narrowing down the nodes in the parent node group to apply classes to a specific subset of nodes.


Because nodes can match multiple node groups from separate hierarchical lineages, it’s possible for two equal node groups to contribute conflicting values for variables and class parameters. Conflicting values cause a Puppet run on an agent to fail.

**Tip:** In the console, you can see how node groups are related on the Classification page, which displays a hierarchical view of node groups. From the command line, you can use the group children endpoint to review group lineage.

**Related information**  


[GET /v1/group-children/:id](group_children_endpoint.md#)

## Best practices for classifying node groups

To organize node groups, start with the high-level functional groups that reflect the business requirements of your organization, and work down to smaller segments within those groups.

For example, if a large portion of your infrastructure consists of web servers, create a node group called web servers and add any classes that need to be applied to all web servers.

Next, identify subsets of web servers that have common characteristics but differ from other subsets. For example, you might have production web servers and development web servers. So, create a dev web child node group under the web servers node group. Nodes that match the dev web node group get all of the classes in the parent node group in addition to the classes assigned to the dev web node group.

## Create node groups

You can create node groups to assign either an environment or classification.

-   **Environment node groups** assign environments to nodes, such as test, development, or production.

-   **Classification node groups** assign classification data to nodes, including classes, parameters, and variables.


### Create environment node groups

Create custom environment node groups so that you can target deployment of Puppet code.

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group and then click **Add**.

    -   **Parent name** – Select the top-level environment node group in your hierarchy. If you're using default environment node groups, this might be **Production environment** or **All environments**. Every environment node group you add must be a descendant of the top-level environment node group.

    -   **Group name** – Enter a name that describes the role of this environment node group, for example, Test environment.

    -   **Environment** – Select the environment that you want to assign to nodes that match this node group.

    -   **Environment group** - Select this option.


You can now add nodes to your environment node group to control which environment each node belongs to.

### Create classification node groups

Create classification node groups to assign classification data to nodes.

1.  In the console, click **Classification**, and click **Add group**.

2.  Specify options for the new node group and then click **Add**.

    -   **Parent name** – Select the name of the classification node group that you want to set as the parent to this node group. Classification node groups inherit classes, parameters, and variables from their parent node group. By default, the parent node group is the **All Nodes** node group.

    -   **Group name** – Enter a name that describes the role of this classification node group, for example, Web Servers.

    -   **Environment** – Specify an environment to limit the classes and parameters available for selection in this node group.

**Note:** Specifying an environment in a classification node group does not assign an environment to any nodes, as it does in an environment node group.

    -   **Environment group** – Do not select this option.


You can now add nodes to your classification node group dynamically or statically.

## Add nodes to a node group

There are two ways to add nodes to a node group.

-   Individually pin nodes to the node group \(static\)

-   Create rules that match node facts \(dynamic\)


### Statically add nodes to a node group

If you have a node that needs to be in a node group regardless of the rules specified for that node group, you can pin the node to the node group.

A pinned node remains in the node group until you manually remove it. Adding a pinned node essentially creates the rule `<the certname of your node>` = `<the certname>`, and includes this rule along with the other fact-based rules.

1.  In the console, click **Classification**, and then find the node group that you want to pin a node to and select it.

2.  On the **Rules** tab, in the pinned nodes section, enter the certname of the node.

3.  Click **Pin node**, and then commit changes.


### Dynamically add nodes to a node group

Rules are the most powerful and scalable way to include nodes in a node group. You can create rules in a node group that are used to match node facts.

When nodes match the rules in a node group, they’re classified with all of the classification data \(classes, parameters, and variables\) for the node group.

When nodes no longer match the rules of a node group, the classification data for that node group no longer applies to the node.

1.  In the console, click **Classification**, and then find the node group that you want to add the rule to and select it.

2.  On the **Rules** tab, specify rules for the fact, then click **Add rule**.

3.  \(Optional\) Repeat step 2 as needed to add more rules.

    **Tip:** If the node group includes multiple rules, be sure to specify whether **Nodes must match all rules** or **Nodes can match any rule**.

4.  Commit changes.


### Writing node group rules

To dynamically assign nodes to a group, you must specify rules based on node facts. Use this reference to fill out the **Rules** tab for node groups.

|Option|Definition|
|------|----------|
|**Fact**|Specifies the fact used to match nodes.

Select from the list of known facts, or enter part of a string to view fuzzy matches.

To use structured or trusted facts, select the initial value from the dropdown list, then type the rest of the fact.

-   To descend into a hash, use dots \("."\) to designate path segments, such as `os.release.major` or `trusted.certname`.

-   To specify an item in an array, surround the numerical index of the item in square brackets, such as`processors.models[0]` or `mountpoints./.options[0]`.

-   To identify path segments that contain dots or UTF-8 characters, surround the segment with single or double quotes, such as `trusted.extensions."1.3.6.1.4.1.34380.1.2.1"`.

-   To use trusted extension short names, append the short name after a second dot, such as `trusted.extensions.pp_role`.


**Tip:** Structured and trusted facts don't provide type-ahead suggestions beyond the top-level name key, and the facts aren't verified when entered. After adding a rule for a structured or trusted fact, review the number of matching nodes to verify that the fact was entered correctly.

|
|**Operator**|Describes the relationship between the fact and value.

Operators include:

-   `=` — is

-   `!=` — is not

-   `~` — matches regex

-   `!~` — does not match regex

-   `>` — greater than

-   `>=` — greater than or equal to

-   `<` — less than

-   `<=` — less than or equal to


The numeric operators `>`, `>=`, `<`, and `<=` can be used only with facts that have a numeric value.

To match highly specific node facts, use **~** or **!~** with a regular expression for **Value**.

|
|**Value**|Specifies the value associated with the fact.|

### Using structured and trusted facts for node group rules

Structured facts group a set of related facts, whereas trusted facts are a specific type of structured fact.

[Structured facts](https://puppet.com/docs/facter/3.9/fact_overview.html#writing-structured-facts) group a set of related facts in the form of a hash or array. For example, the structured fact `os` includes multiple independent facts about the operating system, including architecture, family, and release. In the console, when you view facts about a node, you can differentiate structured facts because they’re surrounded by curly braces.

[Trusted facts](https://puppet.com/docs/puppet/6.10/lang_facts_and_builtin_vars.html#trusted-facts) are a specific type of structured fact where the facts are immutable and extracted from a node’s certificate. Because they can’t be changed or overridden, trusted facts enhance security by verifying a node’s identity before sending sensitive data in its catalog.

You can use structured and trusted facts in the console to dynamically add nodes to groups.

**Note:** If you’re using trusted facts to specify certificate extensions, in order for nodes to match to rules correctly, you must use short names for Puppet [registered IDs](https://puppet.com/docs/puppet/6.10/ssl_attributes_extensions.html#puppet-specific-registered-ids) and numeric IDs for [private extensions](https://docs.puppet.com/docs/puppet/6.10/ssl_attributes_extensions.html#recommended-oids-for-extensions). Numeric IDs are required for private extensions whether or not you specify a short name in the `custom_trusted_oid_mapping.yaml` file.

## Declare classes

Classes are the blocks of Puppet code used to configure nodes and assign resources to them.

The class that you want to apply must exist in an installed module. You can download modules from the Forge or create your own module.

1.  In the console, click **Classification**, and then find the node group that you want to add the class to and select it.

2.  On the **Configuration** tab, in the **Add new class** field, select the class to add.

    The **Add new class** field suggests classes that the master knows about and that are available in the environment set for the node group.

3.  Click **Add class** and then commit changes.

    **Note:** Classes don’t appear in the class list until they’re retrieved from the master and the environment cache is refreshed. By default, both of these actions occur every three minutes. To override the default refresh period and force the node classifier to retrieve the classes from the master immediately, click the **Refresh** button.


## Enable data editing in the console

The ability to edit configuration data in the console is enabled by default in new installations. If you upgrade from an earlier version and didn't previously have configuration data enabled, you must manually enable classifier configuration data, because enabling requires edits to your `hiera.yaml` file.

1.  On your master, edit `/etc/puppetlabs/puppet/hiera.yaml` to add:

    ```
    hierarchy: 
    - name: "Classifier Configuration Data"
      data_hash: classifier_data        
    ```

    Place any additional hierarchy entries, such as `hiera-yaml` or `hiera-eyaml` under the same `hierarchy` key, preferably below the `Classifier Configuration Data` entry.

    **Note:** If you enable data editing in the console, add both **Set environment** and **Edit configuration data** to groups that set environment or modify class parameters in order for users to make changes.


If your environment is configured for high availability, you must also update `hiera.yaml` on your replica.

## Define data used by node groups

The console offers multiple ways to specify data used in your manifests.

-   **Configuration data** — Specify values through automatic parameter lookup.

-   **Parameters** — Specify resource-style values used by a declared class.

-   **Variables**— Specify values to make available in Puppet code as top-scope variables.


### Set configuration data

Configuration data set in the console is used for automatic parameter lookup, the same way that Hiera data is used. Console configuration data takes precedence over Hiera data, but you can combine data from both sources to configure nodes.

**Tip:** In most cases, setting configuration data in Hiera is the more scalable and consistent method, but there are some cases where the console is preferable. Use the console to set configuration data if:

-   You want to override Hiera data. Data set in the console overrides Hiera data when configured as recommended.
-   You want to give someone access to set or change data and they don’t have the skill set to do it in Hiera.
-   You simply prefer the console user interface.


1.  In the console, click **Classification**, then find the node group that you want to add configuration data to and select it.

2.  On the **Configuration** tab in the **Data** section, specify a **Class** and select a **Parameter** to add.

    You can select from existing classes and parameters in the node group's environment, or you can specify free-form values. Classes aren’t validated, but any class you specify must be present in the node’s catalog at runtime in order for the parameter value to be applied.

    When you select a parameter, the **Value** field is automatically populated with the inherited or default value.

3.  Change the default parameter **Value**.


**Related information**  


[Enable data editing in the console](grouping_and_classifying_nodes.md#)

### Set parameters

Parameters are declared resource-style, which means they can be used to override other data; however, this override capability can introduce class conflicts and declaration errors that cause Puppet runs to fail.

1.  In the console, click **Classification**, and then find the node group that you want to add a parameter to and select it.

2.  On the **Configuration** tab, in the **Classes** section, select the class you want to modify and the **Parameter** to add.

    The **Parameter** drop-down list shows all of the parameters that are available for that class in the node group’s environment. When you select a parameter, the **Value** field is automatically populated with the inherited or default value.

3.  \(Optional\) Change the default **Value**.


### Set variables

Variables set in the console become top-scope variables available to all Puppet manifests.

1.  In the console, click **Classification**, and then find the node group that you want to set a variable for and select it.

2.  On the **Variables** tab, enter options for the variable:

    -   **Key** – Enter the name of the variable.

    -   **Value** – Enter the value that you want to assign to the variable.

3.  Click **Add variable**, and then commit changes.


### Tips for specifying parameter and variable values

Parameters and variables can be structured as JSON. If they can't be parsed as JSON, they're treated as strings.

Parameters and variables can be specified using these data types and syntax:

-   Strings \(for example, `"centos"`\)

    -   Variable-style syntax, which interpolates the result of referencing a fact \(for example, `"I live at $ipaddress."`\)

    -   Expression-style syntax, which interpolates the result of evaluating the embedded expression \(for example, `${$os"release"}`\)

    **Note:** Strings must be double-quoted, because single quotes aren't valid JSON.

    **Tip:** To enter a value in the console that contains a literal dollar sign, like a password hash — for example, `$1$nnkkFwEc$safFMXYaUVfKrDV4FLCm0/` — escape each dollar sign with a backslash to disable interpolation.

-   Booleans \(for example, `true` or `false`\)
-   Numbers \(for example, `123`\)
-   Hashes \(for example, `{"a": 1}`\)

    **Note:** Hashes must use colons rather than hash rockets.

-   Arrays \(for example, `["1","2.3"]`\)

#### Variable-style syntax

Variable-style syntax uses a dollar sign \($\) followed by a Puppet fact name.

Example: `"I live at $ipaddress"`

Variable-style syntax is interpolated as the value of the fact. For example, `$ipaddress` resolves to the value of the `ipaddress` fact.

**Important:** The endpoint variable `$pe_node_groups` cannot be interpolated when used as a classifier in class variable values.

Indexing cannot be used in variable-style syntax because the indices are treated as part of the string literal. For example, given the following fact: `processors => {"count" => 4, "physicalcount" => 1}`, if you use variable-style syntax to specify `$processors[count]`, the value of the `processors` fact is interpolated but it is followed by a literal "\[count\]". After interpolation, this example becomes `{"count" => 4,"physicalcount" => 1}[count]`.

**Note:** Do not use the `::` top-level scope indication because the console is not aware of Puppet variable scope.

#### Expression-style syntax

Use expression-style syntax when you need to index into a fact \(`${$os[release]}`\), refer to trusted facts \(`"My name is ${trusted[certname]}"`\), or delimit fact names from strings \(`"My ${os} release"`\).

The following is an example of using expression-style syntax to access the full release number of an operating system:

```
${$os"release"}
```

Expression-style syntax uses the following elements in order:

-   an initial dollar sign and curly brace \(`${`\)
-   a legal Puppet fact name preceded by an optional dollar sign
-   any number of index expressions \(the quotations around indices are optional but are required if the index string contains spaces or square brackets\)
-   a closing curly brace \(`}`\)

Indices in expression-style syntax can be used to access individual fields of structured facts, or to refer to trusted facts. Use strings in an index if you want to access the keys of a hashmap. If you want to access a particular item or character in an array or string based on the order in which it is listed, you can use an integer \(zero-indexed\).

Examples of legal expression-style interpolation:

-   `${os}`
-   `${$os}`
-   `${$os[release]}`
-   `${$os['release']}`
-   `${$os["release"]}`
-   `${$os[2]}` \(accesses the value of the third \(zero-indexed\) key-value pair in the `os` hash\)
-   `${$osrelease}` \(accesses the value of the third key-value pair in the `release` hash\)

In the console, an index can be only simple string literals or decimal integer literals. An index cannot include variables or operations \(such as string concatenation or integer arithmetic\).

Examples of illegal expression-style interpolation:

-   `${$::os}`
-   `{$os[$release]}`
-   `${$os[0xff]}`
-   `${$os[6/3]}`
-   `${$os[$family + $release]}`
-   `${$os + $release}`

#### Trusted facts

Trusted facts are considered to be keys of a hashmap called `trusted`. This means that all trusted facts must be interpolated using expression-style syntax. For example, the certname trusted fact would be expressed like this: `"My name is ${trusted[certname]}"`. Any trusted facts that are themselves structured facts can have further index expressions to access individual fields of that trusted fact.

**Note:** Regular expressions, resource references, and other keywords \(such as ‘undef’\) are not supported.

## View nodes in a node group

To view all nodes that currently match the rules specified for a node group:

1.  In the console, click **Classification**, and then find the node group that you want to view and select it.

2.  Click **Matching nodes**.


You see the number of nodes that match the node group’s rules, along with a list of the names of matching nodes. This is based on the facts collected during the node's last Puppet run. The matching nodes list is updated as rules are added, deleted, and edited. Nodes must match rules in ancestor node groups as well as the rules of the current node group in order to be considered a matching node.

