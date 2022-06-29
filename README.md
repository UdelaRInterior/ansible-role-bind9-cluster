Ansible role BIND9 cluster
=========

This role DNS configures a set of BIN9 NS servers as authoritative NS, master or slaves, of the internet DNS zones defined in its variables. 

Requirements
------------

For the configuration of BIND9 in each host as such, this role uses the role [systemli.bind9](https://galaxy.ansible.com/systemli/bind9). 

The main task of present role is to build, from the global description of the cluster's zones for each server considered, the content of the variables required by systemli.bind9 role. 


Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

 [systemli.bind9](https://galaxy.ansible.com/systemli/bind9)

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior.

Licenced under GPL-v3

Author Information
------------------

[@ulvida](https://github.com/ulvida)
