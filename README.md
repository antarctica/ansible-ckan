# CKAN (`ckan`)

**Part of the BAS Ansible Role Collection (BARC)**

Installs the CKAN open data portal

## Overview

* Installs a development instance of CKAN, including database and schema setup
* Configures CKAN to run as a WSGI application under Apache, using Nginx as a reverse proxy for static caching
* Optionally configures a CKAN user with system administrator privileges
* Optionally populates CKAN with test data

## Availability

This role is designed for internal use but if useful can be shared publicly.

## Usage

### Requirements

#### BAS Ansible Role Collection (BARC)

* `postgres-server`
* `solr-jetty`
* `python-2-virtualenv`
* `git`
* `apache-wsgi`
* `apache-rpaf`
* `nginx`

## Variables

* `ckan_ckan_version`
    * The version of CKAN that will be installed expressed as reference within the [CKAN Git repository](https://github.com/ckan/ckan)
    * This variable **MUST** be a valid branch, tag or other reference and **SHOULD** be a tag to ensure repeatability.
    * Default: "2.3"
* `ckan_installation_directory`
    * The directory into which CKAN will be installed and a Virtual Environment created
    * By default this variable uses the CKAN default installation directory, you **SHOULD NOT** change this.
    * Default: "/usr/lib/ckan/default"
* `ckan_config_directory`
    * The directory into which CKAN's configuration file will be held
    * By default this variable uses the CKAN default installation directory, you **SHOULD NOT** change this.
    * Default: "/etc/ckan/default"
* `ckan_storage_directory:`
    * The directory into which CKAN will store files (data-sets) uploaded by users
    * By default this variable uses the CKAN default storage directory.
    * Default: "/var/lib/ckan"
* `ckan_config_file:`
    * The file name of the CKAN configuration file
    * By default this variable uses the CKAN default file name for a production environment
    * If you are using CKAN in a non-production environment, you **SHOULD** change this variable to something appropriate (e.g. "development")
    * Default: "production.ini"
* `ckan_app_user_username`
    * The username of the OS user which will own the CKAN installation, configuration and storage directories
    * This variable **MUST** be the username of a valid OS user and this user **SHOULD NOT** have root permissions.
    * Default: "app"
* `ckan_setup_superuser_enable`
    * Feature flag to control whether a CKAN user should be created with system administrator privileges
    * If this variable is "true" this feature will be enabled.
    * Default: "true"
* `ckan_setup_test_data`
    * Feature flag to control whether CKAN should be populated with test data
    * CKAN has multiple 'sets' of test data, if enabled the "gov" and "hierarchy" sets will be used.
    * If this variable is "true" this feature will be enabled.
    * Default: "false"
* `ckan_default_config_database_scheme`
    * The type of database to be used for CKAN's database
    * This **MUST** be a valid *dialect+driver* as defined in a [sqlalchemy database URL](http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html#database-urls).
    * The required packages needed by sqlalchemy to connect to this type of database **MUST** be installed.
    * You **SHOULD NOT** need to change this value.
    * Default: "postgresql"
* `ckan_default_config_database_username`
    * The database username CKAN will use to interact with its database
    * This variable **MUST** be a valid *username* as defined in a [sqlalchemy database URL](http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html#database-urls).
    * This variable **MUST** be a valid database username whose user has sufficient privileges to perform tasks on the database specified by `ckan_default_config_database_database`.
    * Default: "ckan_default"
* `ckan_default_config_database_password`
    * The password for the database user whose username is specified by `ckan_default_config_database_username`
    * This variable **MUST** be a valid *password* as defined in a [sqlalchemy database URL](http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html#database-urls).
    * Default: "password"
* `ckan_default_config_database_host`
    * The database host for the CKAN database specified by `ckan_default_config_database_database`
    * This variable **MUST** be a valid *host* as defined in a [sqlalchemy database URL](http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html#database-urls).
    * If needed this variable **MUST** also contain the port for the database, this **MUST** be a valid *port* as defined in a [sqlalchemy database URL](http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html#database-urls).
    * Default: "localhost"
* `ckan_default_config_database_database`
    * The name of the database in which CKAN will store its data
    * This variable **MUST** be a valid database present within the database server specified by `ckan_default_config_database_host`.
    * This variable **MUST** be a valid *database* as defined in a [sqlalchemy database URL](http://docs.sqlalchemy.org/en/rel_0_9/core/engines.html#database-urls).
    * The database this name relates to **SHOULD** use `UTF-8` encoding.
    * Default: "ckan_default"
* `ckan_default_config_search_scheme`
    * The type of connection CKAN will use to communicate with the search application
    * This variable **MUST** be a valid scheme supported by both hosts (if different).
    * This variable **SHOULD** be either `HTTP` or `HTTPS`.
    * This varaible **SHOULD** be `HTTPS` if the search application is not hosted on the same host as CKAN.
    * Default: "http"
* `ckan_default_config_search_host`
    * The host on which the search application is located
    * This variable **MUST** be a valid hostname of IP address.
    * By default this variable will be set to the host defined by the `solr-jetty` role.
    * Default: "{{ solr_jetty_host }}"
* `ckan_default_config_search_port`
    * The port at which the search application is located
    * By default this variable uses the CKAN default value, you **SHOULD NOT** change this.
    * By default this variable will override the default port used by the `solr-jetty` role.
    * Default: "8983"
* `ckan_default_config_search_container`
    * The name/url of the search application with its container
    * By default this variable uses the CKAN default value, you **SHOULD NOT** change this.
    * Default: "solr"
* `ckan_super_user_username`
    * If enabled, the username of the CKAN system administrator user
    * This variable **MUST** be a valid CKAN username.
    * Default: "controller"
* `ckan_super_user_password`
    * If enabled, the username of the CKAN system administrator user
    * This variable **MUST** be a valid CKAN username.
    * Default: "password"
* `ckan_super_user_email`
    * If enabled, the username of the CKAN system administrator user
    * This variable **MUST** be a valid CKAN email address.
    * Default: "user@example.com"

### CKAN configuration (.ini) files

CKAN uses a `ini` file to control its various [configuration options](http://docs.ckan.org/en/ckan-2.2.2/configuration.html).

A python .ini plugin is used to parse and alter these configuration files, re-written files are stripped of comments 
but are otherwise the same as hand-edited versions. Using this approach allows any configuration option to be changed 
without having to explicitly convert each option into an Ansible variable, which would be tedious and hard to maintain.

An array of objects which represent options in the .ini file is used to selectively change options. 
Each object contains a reference to the .ini option and its updated value. The array of objects is an Ansible array and 
is configured like any other complex variable. Due to a limitation with Ansible's syntax it is not possible to push or 
pop items from array's. Therefore to override the default value of an Ansible object it would be necessary to replicate 
the entire Ansible array, including all other items you don't want to change, which, again, is tedious and hard to 
maintain (changing the default array directly is against best practice).

As a workaround two variables are used, a defaults array and a user array.
The defaults array is run before the user array (which is by default empty).
This means if you wanted to change a default value you would copy the array item from the default array into the user 
array with the override value.

* `ckan_default_config_default_options`
    * A set of default options that will be applied to the CKAN configuration file
    * Do not override this variable, use `ckan_default_config_user_options` instead.
    * Structured as an array of items where each item consists of a section name string and an array of options and values:
        * `section`
            * Name of section in ini file (e.g. "app:main")
        * `options` [array]
            * `option`
                * Name of option in ini file (e.g. "ckan.site_url")
            * `value`
                * Value for option in ini file (e.g. "http://{{ ansible_hostname }}")
    * Default: (see variable)
    
* `ckan_default_config_user_options`
    * Use to change values set by `ckan_default_config_default_options` or set additional CKAN configuration options
    * Structured as an array of items where each item consists of a section name string and an array of options and values:
        * `section`
            * Name of section in ini file (e.g. "app:main")
        * `options` [array]
            * `option`
                * Name of option in ini file (e.g. "ckan.site_url")
            * `value`
                * Value for option in ini file (e.g. "http://{{ ansible_hostname }}")
    * Default: "[] (empty array)"

## Contributing

This project welcomes contributions, see `CONTRIBUTING` for our general policy.

## Developing

### Committing changes

The [Git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow/) workflow is used to manage development of this package.

Discrete changes should be made within *feature* branches, created from and merged back into *develop* (where small one-line changes may be made directly).

When ready to release a set of features/changes create a *release* branch from *develop*, update documentation as required and merge into *master* with a tagged, [semantic version](http://semver.org/) (e.g. `v1.2.3`).

After releases the *master* branch should be merged with *develop* to restart the process. High impact bugs can be addressed in *hotfix* branches, created from and merged into *master* directly (and then into *develop*).

### Issue tracking

Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the BAS Web & Applications Team Jira project ([BASWEB](https://jira.ceh.ac.uk/browse/BASWEB)).

## License

Copyright 2015 NERC BAS. Licensed under the MIT license, see `LICENSE` for details.