gitweb
======

Ansible role which helps to install and configure GitWeb.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
---

- name: Example of how to install GitWeb with default configuration
  hosts: all
  roles:
    - gitweb

- name: Installation using Apache
  hosts: all
  roles:
    - gitweb
    - apache
```


Role variables
--------------

```
# Package to be installed (explicit version can be specified here)
gitweb_pkg: gitweb

# Web service to restart when configuration changes (e.g. httpd)
gitweb_service: null

# Whether to restart the Web service
gitweb_service_restart: no


# Path to the Apache config
gitweb_apache_config_path: /etc/httpd/conf.d/git.conf


# Source and destination of the alias
gitweb_apache_config_options_alias_src: /git
gitweb_apache_config_options_alias_dst: /var/www/git

# Default top-level options
gitweb_apache_config_options__default:
  - Alias:
      - "{{ gitweb_apache_config_options_alias_src }}"
      - "{{ gitweb_apache_config_options_alias_dst }}"

# Custom top-level options
gitweb_apache_config_options__custom: []

# Final top-level options
gitweb_apache_config_options: "{{
  gitweb_apache_config_options__default +
  gitweb_apache_config_options__custom }}"


# Directory location
gitweb_apache_config_dir_location: /var/www/git


# Default list of options for the directory options
gitweb_apache_config_dir_options_options__default:
  - +ExecCGI

# Custom list of options for the directory options
gitweb_apache_config_dir_options_options__custom: []

# Custom list of options for the directory options
gitweb_apache_config_dir_options_options: "{{
  gitweb_apache_config_dir_options_options__default +
  gitweb_apache_config_dir_options_options__custom }}"


# Default handler definition for the directory
gitweb_apache_config_dir_options_handler__default:
  - cgi-script
  - .cgi

# Custom handler definition for the directory
gitweb_apache_config_dir_options_handler__custom: []

# Final handler definition for the directory
gitweb_apache_config_dir_options_handler: "{{
  gitweb_apache_config_dir_options_handler__default +
  gitweb_apache_config_dir_options_handler__custom }}"


# Directory index
gitweb_apache_config_dir_options_index: gitweb.cgi


# Default directory options
gitweb_apache_config_dir_options__default:
  - Options: "{{ gitweb_apache_config_dir_options_options }}"
  - AddHandler: "{{ gitweb_apache_config_dir_options_handler }}"
  - DirectoryIndex: "{{ gitweb_apache_config_dir_options_index }}"

# Custom directory options
gitweb_apache_config_dir_options__custom: []

# Final directory options
gitweb_apache_config_dir_options: "{{
  gitweb_apache_config_dir_options__default +
  gitweb_apache_config_dir_options__custom }}"


# Final Apache config
gitweb_apache_config:
  content:
    - options: "{{ gitweb_apache_config_options }}"
    - sections:
        - name: Directory
          param: "{{ gitweb_apache_config_dir_location }}"
          content:
            - options: "{{ gitweb_apache_config_dir_options }}"


# Path to the GitWeb config
gitweb_config_path: /etc/gitweb.conf

# Values of the default config options
gitweb_config_projectroot: /var/lib/git
gitweb_config_git_base_url_list:
  - git://git.example.com
  - ssh://git.example.com/var/lib/git
gitweb_config_feature_blame_default: "[1];"
gitweb_config_feature_blame_override: 1;
gitweb_config_feature_snapshot_default: "[];"
gitweb_config_feature_snapshot_override: 1;
gitweb_config_feature_grep_default: "[0];"
gitweb_config_feature_grep_override: 1;
gitweb_config_feature_pickaxe_default: "[0];"
gitweb_config_feature_pickaxe_override: 1;

# Default config
gitweb_config__default:
  "our $projectroot": "'/var/lib/git';"
  "our @git_base_url_list": qw({{ gitweb_config_git_base_url_list | join(' ') }});
  "$feature{'blame'}{'default'}": "{{ gitweb_config_feature_blame_default }}"
  "$feature{'blame'}{'override'}": "{{ gitweb_config_feature_blame_override }}"
  "$feature{'snapshot'}{'default'}": "{{ gitweb_config_feature_snapshot_default }}"
  "$feature{'snapshot'}{'override'}": "{{ gitweb_config_feature_snapshot_override }}"
  "$feature{'grep'}{'default'}": "{{ gitweb_config_feature_grep_default }}"
  "$feature{'grep'}{'override'}": "{{ gitweb_config_feature_grep_override }}"
  "$feature{'pickaxe'}{'default'}": "{{ gitweb_config_feature_pickaxe_default }}"
  "$feature{'pickaxe'}{'override'}": "{{ gitweb_config_feature_pickaxe_override }}"

# Custom config
gitweb_config__custom: {}

# Final config
gitweb_config: "{{
  gitweb_config__default.update(gitweb_config__custom) }}{{
  gitweb_config__default }}"
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)
- [`apache`](https://github.com/jtyr/ansible-apache) (optional)


License
-------

MIT


Author
------

Jiri Tyr
