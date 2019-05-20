Ansible Role: tamay.httpd
=========

This role installs the httpd webserver.

You can install additional modules (like ```mod_auth_openidc```), set the MPM mode and configure the global SSL settings.

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

    # Configure global ssl settings in /etc/httpd/conf.d/ssl.conf
    # Defaults to modern settings from
    # https://mozilla.github.io/server-side-tls/ssl-config-generator/
    httpd_ssl_protocol: all -SSLv3 -TLSv1 -TLSv1.1
    httpd_ssl_cipher: ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256
    httpd_ssl_honor_cipher: yes
    httpd_ssl_certificate: /etc/pki/tls/certs/localhost.crt
    httpd_ssl_key: /etc/pki/tls/private/localhost.key
    httpd_ssl_chain: 'EMPTY'
    httpd_ssl_ca: 'EMPTY'
    httpd_ssl_hsts: 15768000
    httpd_ssl_oscp_stapling: yes

Configure settings in ```/etc/httpd/conf.d/ssl.conf```. Except for the certificate options and HSTS, they are globally valid. 

    # <VirtualHost *:80> configuration
    httpd_vhosts: []
    # Example:
    #  - servername: www.example.com
    #    options: |
    #      Redirect permanent / https://www.example.com/
    #  - servername: cloud.example.com
    #    documentroot: /var/www/cloud/
    #    errorlog: /var/log/httpd/cloud.example.com-error.log
    #    customlog: /var/log/httpd/cloud.example.com-access.log

With ```httpd_vhosts``` you can create VirtualHost files. They are only for http (Port80). Only ```servername``` is mandatory. Optional are ```documentroot```, ```errorlog```, ```customlog``` and ```options```.  
With ```options``` you can define everything else, that is not covered by variables. See the examples. 
    
    # <VirtualHost *:443> configuration
    httpd_vhosts_ssl: []
    # Example:
    #httpd_ssl_vhosts:
    #  - servername: www.example.com
    #      documentroot: /var/www/html/
    #      errorlog: /var/log/httpd/www.example.com-error.log
    #      customlog: /var/log/httpd/www.example.com-access.log
    #      options: |
    #        <Directory /var/www/html/>
    #            Options +FollowSymlinks
    #            AllowOverride All
    #        </Directory>
    #      ssl_protocol: "{{ httpd_ssl_protocol }}"
    #      ssl_cipher: "{{ httpd_ssl_cipher }}"
    #      ssl_honor_cipher: "{{ httpd_ssl_honor_cipher }}"
    #      ssl_certificate: "{{ httpd_ssl_certificate }}"
    #      ssl_key: "{{ httpd_ssl_key }}"
    #      ssl_chain: "{{ httpd_ssl_chain }}"
    #      ssl_ca: "{{ httpd_ssl_ca }}"
    #      ssl_hsts: "{{ httpd_ssl_hsts }}"
    #  - servername: cloud.example.com
    #  - servername: mail.example.com
    #      ssl_certificate: "/etc/pki/tls/certs/mail.example.com.pem"
    #      ssl_key: "/etc/pki/tls/private/mail.example.com.key"
    
```httpd_vhosts_ssl``` configures the SSL VirtualHosts. The configuration is like ```httpd_vhosts```, with the exception of the added ```ssl_```-variables. They are however all optional. The necessary options like key and certificate will then use the values from ```httpd_ssl_certificate``` and ```httpd_ssl_key```.

In this example, the item ```www.example.com``` has every available option set. 

Dependencies
------------

None.

Example Playbook
----------------

- Install *mod_auth_openidc*
- Use the *event* MPM module and configure it
- Use *wildcard.example.com* certificate 


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
        httpd_ssl_certificate: /etc/pki/tls/certs/wildcard.example.com.crt
        httpd_ssl_key: /etc/pki/tls/private/wildcard.example.com.key
    
      roles:
      - tamay.httpd

License
-------

MIT

Author Information
------------------

tamay.mueller@gmail.com