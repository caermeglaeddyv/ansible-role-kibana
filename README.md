Ansible role: Kibana
=========

This role is used to install kibana from elastic stack.

For now, it does the following:
- generates kibana root CA and server/peer certificates if needed
- does all necessary OS preparations
- configures kibana options, adds elastic user password, xpack security and reporting encryption keys to kibana keystore
- separate playbook to create kibana spaces and roles if defined


Requirements
------------

This is not strict requirements and it may not work with other versions than tested ones.
Anyway. feel free to test by yourself, suggest addition of new functionality and contribute.

Role is tested with:
- Ansible version >= 2.8.6
- CentOS version >= 7.6 (1803)

Currently supports installation of Kibana versions >= 7.3.

cfssl and cfssljson must be installed on localhost if kibana_cfssl variable is set to True (default).


Role Variables
--------------

Variables and their descriptions copied from defaults/main.yml

```bash

---
# defaults file for kibana


# Use CFSSL to create kibana Root CA and self-signed certificates:
kibana_cfssl: true

# Variable which is common for most projects, used in
# configuration files or file/directory names.
# By default used as reference for kibana_project_dir variable:
kibana_project_name: test

# Variable which is common for most projects, used as
# project working directory on the localhost for the role.
# Currently is used to store created certificates:
kibana_project_dir: "{{ kibana_project_name }}"

# Version of kibana package which will be installed:
kibana_version: 7

# Revision of kibana package which will be installed:
kibana_revision: 3.2

# Kibana server name:
kibana_server_name: kibana

# ssl verification mode defined in elasticsearch server configuration,
# if this set to other than "none", elasticsearch server keypair and
# CA ertificate files must be named to "elasticsearch-key.pem",
# "elasticsearch.pem" and "elasticsearch-ca.pem" and located inside
# "{{ kibana_project_dir }}/kibana/" directory on localhost:
kibana_elasticsearch_ssl_verification_mode: none

# Elasticsearch hosts to connect to:
kibana_elasticsearch_hosts: []
# - ""

# Time zone to use in event logging:
kibana_logging_timezone: UTC

# Kibana UI browser session timeout:
kibana_session_timeout: 900000

# Password for "kibana" system user in elasticsearch:
kibana_pass: kibana

# Kibana ecnryption key for xpack security and reporting:
kibana_encryption_key: encryptionkey

# Password for "elastic" database admin user in elasticsearch:
kibana_elastic_pass: elastic

# Kibana spaces to create/update:
kibana_spaces: []
# - space: ""
#   method: ""
#   body:
#     id: ""
#     name: ""
#     description: ""
#     color: ""
#     disabledFeatures: []
#     _reserved:

# Kibana roles to create/update:
kibana_roles: []
# - role: ""
#   body:
#     metadata: {}
#     elasticsearch:
#       cluster:
#       - ""
#       indices:
#       - names:
#         - ""
#         privileges:
#         - ""
#         allow_restricted_indices:
#       run_as:
#       - ""
#     kibana:
#     - base:
#       - ""
#       feature:
#         discover:
#         - ""
#         visualize:
#         - ""
#         dashboard:
#         - ""
#         dev_tools:
#         - ""
#       spaces:
#       - ""

```


Dependencies
------------

cfssl and cfssljson must be installed on localhost if kibana_cfssl variable is set to True (default).


Example Playbook
----------------

```bash
---
- hosts: localhost
  gather_facts: false
  become: no
  tasks:
  - name: Check ansible version >=2.8.6
    assert:
      msg: Ansible must be v2.8.6 or higher
      that:
      - ansible_version.string is version("2.8.6", ">=")
    tags:
    - check
  vars:
    ansible_connection: local

- hosts: all
  become: yes
  tasks:
  - import_role:
      name: kibana
  # import this if you want to create/update kibana spaces and roles only:
  - import_role:
      name: kibana
      tasks_from: crud_objects

```

More detailed examples ( inventories, playbooks etc. ) of this and other roles can be found [here](https://github.com/caermeglaeddyv/examples/tree/dev/ansible).

It's highly recommended to start you test deploys from there, especially if you use Google Cloud Platform or VMware vCenter as your infrastructure, for now that [repository](https://github.com/caermeglaeddyv/examples) contains [Packer](https://github.com/caermeglaeddyv/examples/tree/dev/packer) and [Terraform](https://github.com/caermeglaeddyv/examples/tree/dev/terraform) examples to build templates and deploy machines on this platforms.


License
-------

[Apache 2.0](https://github.com/caermeglaeddyv/ansible-role-rear/blob/dev/LICENSE)


Author Information
------------------

Copyright 2020 [caermeglaeddyv](https://github.com/caermeglaeddyv)
