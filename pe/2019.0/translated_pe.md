---
author: Michelle Fredette <michelle.fredette@puppet.com\>
---

# Translated PE

For both Puppet Enterprise and Puppet, certain resources are available in Japanese, and internationalized for the future addition of other languages.  

We provide these resources in Japanese:

-   Select interface text, including logs, error messages, and some command-line text. To see translated strings, your system locale and browser language must be set to Japanese, and for the text-based installer, you need [gettext](https://www.gnu.org/software/gettext/). Translated product strings are managed with the `disable_i18n` setting, and by default, are enabled \(`puppet_enterprise::master::disable_i18n: false`\) in PE 2018.1 and later.

-   Select product documentation. See the [Japanese docs landing page](http://puppet.com/ja/docs/.).

-   Module READMEs and descriptions for Puppet-supported modules for NTP, Server, Stdlib, AWS, Tomcat, MySQL, Tagmail, PostgreSQL, Apache, and Azure. View the Japanese READMEs on the Puppet Forge by setting your browser language preference to Japanese. You can also find them in your modules directory. The default location is `./readmes/README_ja_JP.md`.

-   The fully internationalized MySQL module. This is the first module to be released with end-to-end internationalization. To see error and warning messages from the MySQL module in Japanese, set your system locale to Japanese.

-   The Puppet Learning VM, an interactive, guided tour that teaches you how to use PE. Both the Quest Guide and the Learning VM itself are available in Japanese. To view the Quest Guide in Japanese, set your browser to Japanese. To use the Learning VM in Japanese, set your system locale to Japanese. The Quest Guide contains instructions for changing your locale settings on the virtual machine. The Quest Guide and Learning VM are available at [learn.puppet.com](https://learn.puppet.com/)


**Note:** Select [Spanish](https://puppet.com/es/docs/pe/2017.2/index.html) and [German](https://puppet.com/de/docs/pe/current/index.html) documentation is also available.

