Ansible role BIND9 cluster
=========

This role will help you to build [systemli.bind9](https://galaxy.ansible.com/systemli/bind9) role's coherent configurations for a set of BIN9 NS servers as authoritative NS, master or slaves, for the internet DNS zones defined in its variables. 

Requirements
------------

For the configuration of BIND9 in each host as such, this role will use the role [systemli.bind9](https://galaxy.ansible.com/systemli/bind9). 

The task of present role is to build, from the global description of the cluster's zones for each server considered, the content of the variables required by systemli.bind9 role. It doesn't run any remote task, it just builds locally `systemli.bind9` role's configuration files

Role Variables
--------------

The role's main variable is a meta structure called `bind9_cluster_zones`, that provides all the information needed for the zone's configuration in each of the zone's BIND9 authoritative nameserver, as well as the zone definition itself with all its DNS ressource records, that can also optionally defined in a classical BIND9 zone's file (_static_ and _dynamic_ concepts in `systemli.bind9` role).

First levels are:
```yaml 
bind9_cluster_zones: 
- name: my_zone.org
  bind9:
    primary:
      nameserver:
      clauses:
    secondaries:
    - nameserver:
      clauses:
    - nameserver:
      clauses:
  zone:
    serial: 2022050602
    refresh: 1D
    retry: 2H
    expire: 1000H
    minimum: 10M
    default_ttl: 10M
    primary: ns1.my_zone.org
    admin: postmaster.my_zone.org
    ns_records:
    - ns1.my_zone.org
    - ns2.my_zone.org
    - ns3.my_zone.org
    rrs:
    - {label: "@", type: MX, rdata: 10 mailgateway.mailprovider.org.}
    - {label: www, type: CNAME, rdata: server.my_zone.org.}
    - {label: @, type: A, rdata: <IP>}
    - {label: server, type: A, rdata: <IP>}

- name: my_zone2.org
  bind9:
    primary:
      nameserver:
    secondaries:
    - nameserver:
    - nameserver:
      clauses:

- name: my_zone.net
  bind9:
    primary:
      nameserver:
    secondaries:
    - nameserver:
    - nameserver:

```
Each leave `nameserver` has the following structure: 
```yaml
bind9_cluster_ns_auth_a:
  name:
  addresses:
  - 
  - 
  hostname:
```
that we can worthly set separately, once for each NS server in the set of the cluster: 

```yaml
bind9_cluster_ns_auth_a:
  name: our_first_NS
  addresses:
  - <IPv4>
  - <IPv6>
  hostname: nsa.mydomain.uy    # ansible hostname in the IaC inventory
  # fqdn:                   
```
and reference them in the zone's meta structure this way: 

```yaml
bind9:
  primary:
    nameserver: '{{ bind9_cluster_ns_auth_a }}'
  secondaries:
  - nameserver: '{{ bind9_cluster_ns_auth_b }}'
  - nameserver: '{{ bind9_cluster_ns_auth_c }}'
```

Those nameserver structures _must_ also be declared in the `bind9_masters` or `bind9_masters_extra` of the role systemli.bind9, to have the appropriate ACLs and masters statement set in BIND9 config. 

The other variables for systemli.bind9 should be defined separately, either in the NS cluster group variables for those they share, either in the host's varialbles if they are specific for each one. Note, however `bind9_slaves` variable becomes unuseful and  `bind9_masters` loose its meaning of "default masters", as in the `bind9_zones_*` that this role produces, primary and secundaries servers are always set, for each and every zone defined. s


See [default variables's file](defaults/main.yml) for a detailed description of the role's variable structure. 

Dependencies
------------

 [systemli.bind9](https://galaxy.ansible.com/systemli/bind9)

Example Playbook
----------------

Wit a structure of one or several zones, and one or several NS master hostnames, that we aslo put in an ansible group called, for instance, `my_bind9_cluster_group`, running the following playbook: 

    - hosts: my_bind9_cluster_group
      roles:
         - role: udelarinterior.bind9_cluster 
  

will create or update, locally in your ansible controller, in the `/host_vars/<hostname>/vars/` of each NS server host, a vars file with the necessary ones for the zones in the role [systemli.bind9](https://galaxy.ansible.com/systemli/bind9). 

License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior.

Licenced under GPL-v3

Author Information
------------------

[@ulvida](https://github.com/ulvida)
