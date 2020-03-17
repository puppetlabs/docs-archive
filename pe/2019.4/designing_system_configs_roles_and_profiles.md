---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Designing system configs: roles and profiles

Your typical goal with Puppet is to build complete system configurations, which manage all of the software, services, and configuration that you care about on a given system. The roles and profiles method can help keep complexity under control and make your code more reusable, reconfigurable, and refactorable.

-   **[The roles and profiles method](the_roles_and_profiles_method.md#)**  
The *roles and profiles* method is the most reliable way to build reusable, configurable, and refactorable system configurations.
-   **[Roles and profiles example](roles_and_profiles_example.md#)**  
This example demonstrates a complete roles and profiles workflow. Use it to understand the roles and profiles method as a whole. Additional examples show how to design advanced configurations by refactoring this example code to a higher level of complexity.
-   **[Designing advanced profiles](designing_advanced_profiles.md#)**  
In this advanced example, we iteratively refactor our basic roles and profiles example to handle real-world concerns. The final result is — with only minor differences — the Jenkins profile we use in production here at Puppet.
-   **[Designing convenient roles](designing_convenient_roles.md#)**  
There are several approaches to building roles, and you must decide which ones are most convenient for you and your team.

**Parent topic:**[Managing nodes](managing_nodes.md)

