Ansible Role: tamay.httpd
=========

This role installs httpd webserver. You can configure the mpm module it should use. 

Requirements
------------

None.

Role Variables
--------------

List of all variables, including default values.

    # Installs additional httpd modules
    httpd_modules: []

List of additional httpd modules, that should be installed. Examples: *mod_auth_kerb*, *mod_ldap*, ...
    
    # Selects mpm module (prefork, worker or event)
    httpd_mpm_module: 'prefork'

Apache-MPM module that should be used.
    
    # Additional config for mpm modules
    httpd_mpm_prefork_config: {}
    httpd_mpm_worker_config: {}
    httpd_mpm_event_config: {}
    # Example:
    #httpd_mpm_prefork_config:
    #  MaxRequestWorkers: 512
    #  ServerLimit: 512

Key-Value dictionary to further configure the used mpm module. Only the variables for the configured mpm-module should be used. See *mpm-common*, *event*, *worker* and *prefork* at http://httpd.apache.org/docs/2.4/en/mod/
    
    # Disables default welcome.conf
    httpd_disable_welcome: yes

Disables the default welcome website.

Dependencies
------------

None.

Example Playbook
----------------

    ---
    
    - hosts: all
    
      vars:
        httpd_modules:
          - mod_auth_openidc
        httpd_mpm_module: 'event'
        httpd_mpm_event_config:
          ServerLimit: 16
          MaxClients: 400
          StartServers: 3
          ThreadsPerChild: 25
          ThreadLimit: 64
        httpd_disable_welcome: yes
    
      roles:
      - tamay.httpd

License
-------

MIT

Author Information
------------------

tamay.mueller@gmail.com