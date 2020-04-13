# Manage administrator accounts

It is often necessary to standardize the local Windows Administrator password across an entire Windows deployment.

## Procedure

1.  To manage administrator accounts with Puppet, create a user resource with `'Administrator'` as the resource title like so:

    ```
    user { 'Administrator':
       ensure => present,
       password => 'yabbadabba'
    }
    ```

    **Note:** Securing the password used in the manifest is beyond the scope of this introductory example, but it’s common to use Hiera, a key/value lookup tool for configuration, with eyaml to solve this problem. Not only does this solution provide secure storage for the password value, but it also provides parameterization to support reuse, opening the door to easy password rotation policies across an entire network of Windows machines.


## Results

