---
layout: default
title: "<# PAGE TITLE #>"
canonical: "/pe/latest/<# PAGE URL #>.html"

---

<!--Overviews introduce the content of a page, telling users what they can expect to learn here. For a process, like installing or upgrading, use the overview to provide high-level conceptual information about the process. -->

<!--overview-->
<# SHORTDESC -- Insert a 1-2 sentence description of the process's purpose #>

<# PARAGRAPH (optional) -- Use paragraphs to elaborate on any conceptual info about the process, as needed. #>

<!--multi-task parent-->
## <# TITLE -- Insert an imperative phrase that describes the process, like "Set up a Razor environment". This parent task title is likely to be the imperative version of the page title. #>

<# SHORTDESC -- Insert a 1-2 sentence description of the process, like "Set up a PXE environment using the DHCP and TFTP service of your choice." #>

<# CONTEXT (optional) -- Insert contextual information about the actual process, such as notes that apply to all tasks. #>

<# PREREQ (optional) -- Insert imperative statements, preceded by "**Before you begin:**", describing high-level tasks the user must complete before performing this process. For example, "**Before you begin**: Install PE." Include only those prereqs that affect the entire process. #>

<!--multi-task child-->
### <# TITLE -- Insert an imperative phrase that describes the subtask. For example, "Load iPXE software". #>

<# SHORTDESC -- Insert a 1-2 sentence description of the subtask’s purpose. #>

<# CONTEXT (optional) -- If the short description doesn’t provide enough background about the subtask, insert an additional paragraph to provide more context. #>

<# PREREQ (optional) -- Insert imperative statements, preceded by "**Before you begin:**", describing tasks the user must complete before performing *this* subtask. #>

1. <# IMPERATIVE COMMAND #>

2. <# IMPERATIVE COMMAND WITH SCREENSHOT
   ![<IMAGE TITLE>](./images/<FILENAME.PNG>) #>

3. <# IMPERATIVE COMMAND WITH NOTE
   On a new, indented line, insert important information about a step, preceded with a note, caution, tip, important, remember, or restriction label. For example, "**Note:** You need about 1GB of space in `/tmp` to unpack the installer tarball." #>

4. <# IMPERATIVE COMMAND WITH INFO
   On a new, indented line, provide additional information about a step. For example, "PE opens your text editor with an example `pe.conf` file containing the parameters needed to perform a basic upgrade." #>

5. <# IMPERATIVE COMMAND WITH RESULTS
   On a new, indented line, tell users how they can confirm a step is complete, if necessary. For example, "When you save the file, the upgrader runs automatically." #>

6. <# IMPERATIVE COMMAND WITH SUBSTEPS
   1. SUBSTEP ONE
   2. SUBSTEP TWO
   3. SUBSTEP THREE #>

7. <# IMPERATIVE COMMAND WITH CHOICES -- Describe user action that depends on a variable, with the choices displayed in an unordered list. For example, "Run one of these commands, depending on <DECIDING FACTOR>:"
   - <CHOICE ONE> -- ...
   - <CHOICE TWO> -- ... #>

<!--multi-task child-->
### <# TITLE -- Insert an imperative phrase that describes the subtask. For example, "Register a node with the microkernel". #>

<# SHORTDESC -- Insert a 1-2 sentence description of the subtask’s purpose. #>

<# CONTEXT (optional) -- If the short description doesn’t provide enough background about the subtask, insert an additional paragraph to provide more context. #>

<# PREREQ (optional) -- Insert imperative statements, preceded by "**Before you begin:**", describing tasks the user must complete before performing this subtask. *Do* include previous subtasks in a multi-task process. For example, "**Before you begin:** [Load iPXE software](#load-ipxe-software)." #>

1. <# IMPERATIVE COMMAND #>

<!--Insert additional child tasks as needed.-->

* * *
