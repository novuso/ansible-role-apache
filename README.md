# Ansible Role: Apache

[![MIT License](http://img.shields.io/badge/license-MIT-003399.svg)](http://opensource.org/licenses/MIT)

An Ansible Role that installs and configures Apache on Ubuntu 14.04

## Requirements

None

## Role Variables

### Global

Ansible variables are listed here along with their default values:

`apache_ppa_repo` is a list of Ubuntu PPA repositories that are managed for
this role. Each entry in the list may designate:

* **repo** *required* (ppa: is prepended)
* **state** (default is present)
* **update_cache** (default is yes)
* **validate_certs** (default is yes)

By default, the `ppa:ondrej/apache2` repository is used:

    apache_ppa_repo:
    - repo: "ondrej/apache2"

`apache_packages` is a list of Ubuntu packages that are managed for this role.
Each entry in the list may designate:

* **name** *required*
* **state** (default is present)

By default, the `apache2`, `apache2-mpm-prefork`, and `apache2-utils` packages
are used:

    apache_packages:
    - name: "apache2"
    - name: "apache2-mpm-prefork"
    - name: "apache2-utils"

`apache_run_user` sets the Apache user:

    apache_run_user: "www-data"

`apache_run_group` sets the Apache group:

    apache_run_group: "www-data"

`apache_logs_dir` sets the log directory:

    apache_logs_dir: "/var/log/apache2"

`apache_create_vhosts` flags whether or not to create virtual hosts:

    apache_create_vhosts: true

`apache_disable_default_vhost` flags whether or not to disable the 000-default
virtual host:

    apache_disable_default_vhost: true


`apache_listen_http` sets the default http port to listen on:

    apache_listen_http: "80"

`apache_listen_https` sets the default https port to listen on:

    apache_listen_https: "443"

`apache_server_name` sets the Apache ServerName setting:

    apache_server_name: "localhost"

`apache_server_tokens` set the Apache ServerTokens setting:

    apache_server_tokens: "Prod"

`apache_server_signature` sets the Apache ServerSignature setting:

    apache_server_signature: "Off"

`apache_trace_enable` sets the Apache TraceEnable setting:

    apache_trace_enable: "Off"

`apache_timeout` sets the Apache Timeout setting:

    apache_timeout: "300"

`apache_keep_alive` sets the Apache KeepAlive setting:

    apache_keep_alive: "On"

`apache_max_keep_alive_requests` sets the Apache MaxKeepAliveRequests setting:

    apache_max_keep_alive_requests: "100"

`apache_keep_alive_timeout` sets the Apache KeepAliveTimeout setting:

    apache_keep_alive_timeout: "5"

`apache_log_level` sets the LogLevel setting:

    apache_log_level: "warn"

`apache_mods` is a list of Apache modules to enable. Each entry in this list
may designate:

* **name** *required*
* **state** (default is present)

By default, the following modules are enabled:

    apache_mods:
    - name: "rewrite"
    - name: "headers"
    - name: "filter"
    - name: "expires"
    - name: "include"
    - name: "ssl"
    - name: proxy
    - name: proxy_http
    - name: proxy_fcgi

### Virtual Hosts

`apache_virtual_hosts` is a list of virtual host configurations. When
`apache_create_vhosts` is set to `true`, virtual hosts are created and enabled.

Each virtual host entry may designate:

* **name** *required*
    * This is generally lowercase hypenated - example: "project"
* **server_name** *required*
    * The primary domain name - example: "project.dev"
* **document_root** *required*
    * Absolute path to the document root - example: "/var/www/project/public"
* **server_aliases** *optional*
    * A list of server aliases - example: \["www\.project\.dev"\]
* **server_admin** *optional*
    * Server admin email address - example: "webmaster\@project\.dev"
* **directory_index** *optional*
    * Default index files for a directory - example: "index.html index.php"
* **ssl_enabled** *optional*
    * Whether or not to enable HTTPS - example: true
* **ssl_cert_file** *required if ssl_enabled is true*
    * Absolute path to the SSL certificate file - example: "/srv/ssl/certs/project.crt"
* **ssl_cert_key** *required is ssl_enabled is true*
    * Absolute path to the SSL key file - example: "/srv/ssl/private/project.key"
* **ssl_cert_chain** *optional*
    * Absolute path to the SSL cert chain file - example: "/srv/ssl/certs/project.pem"
* **files_match** *optional* (Hash of FilesMatch configuration)

`files_match` example:

    files_match:
        match: "\\.php$"
        handler: "proxy:fcgi://127.0.0.1:9000"

* **proxy_pass_match** *optional* (Hash of ProxyPassMatch configuration)

`proxy_pass_match` example:

    proxy_pass_match:
        match: "^/(.*\\.php(/.*)?)$"
        uri: "fcgi://127.0.0.1:9000/var/www/html/$1"

* **proxy** *optional* (Hash of Proxy configuration)

`proxy` example:

    proxy:
        match: "*"
        directives:
        - "Order allow,deny"
        - "Allow from all"

* **locations** *optional* (List of Location configurations)

`locations` example:

    locations:
    - match: "/"
      proxy_pass: "http://localhost:9000/"
      proxy_pass_reverse: "http://localhost:9000/"
    - match: "/static"
      proxy_pass: "!"

## Dependencies

None

## Example Playbook

    ---
    - hosts: all
      vars:
      - apache_mods_enable:
        - rewrite
        - headers
      - apache_virtual_hosts:
        - server_name: "application.com"
          document_root: "/var/www/html"
          directory_index: "index.php"
      roles:
      - ansible-role-apache

## License

This is released under the [MIT license](http://opensource.org/licenses/MIT).
