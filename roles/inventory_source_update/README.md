# controller_configuration.inventory_source_update
## Description
An Ansible Role to update a list of inventory sources on Ansible Controller.

## Requirements
ansible-galaxy collection install  -r tests/collections/requirements.yml to be installed
Currently:
  awx.awx
  or
  ansible.controller

## Variables

### Authentication
|Variable Name|Default Value|Required|Description|Example|
|:---:|:---:|:---:|:---:|:---:|
|`controller_state`|"present"|no|The state all objects will take unless overriden by object default|'absent'|
|`controller_hostname`|""|yes|URL to the Ansible Controller Server.|127.0.0.1|
|`controller_validate_certs`|`True`|no|Whether or not to validate the Ansible Controller Server's SSL certificate.||
|`controller_username`|""|yes|Admin User on the Ansible Controller Server.||
|`controller_password`|""|yes|Controller Admin User's password on the Ansible Controller Server.  This should be stored in an Ansible Vault at vars/controller-secrets.yml or elsewhere and called from a parent playbook.||
|`controller_oauthtoken`|""|yes|Controller Admin User's token on the Ansible Controller Server.  This should be stored in an Ansible Vault at or elsewhere and called from a parent playbook.||
|`controller_inventory_sources`|`see below`|yes|Data structure describing controller inventory sources to update Described below.||

### Secure Logging Variables
The following Variables compliment each other.
If Both variables are not set, secure logging defaults to false.
The role defaults to False as normally the inventory source update task does not include sensitive information.
controller_configuration_inventory_source_update_secure_logging defaults to the value of controller_configuration_secure_logging if it is not explicitly called. This allows for secure logging to be toggled for the entire suite of controller configuration roles with a single variable, or for the user to selectively use it.

|Variable Name|Default Value|Required|Description|
|:---:|:---:|:---:|:---:|
|`controller_configuration_inventory_source_update_secure_logging`|`False`|no|Whether or not to include the sensitive ad_hoc_command role tasks in the log.  Set this value to `True` if you will be providing your sensitive values from elsewhere.|
|`controller_configuration_secure_logging`|`False`|no|This variable enables secure logging as well, but is shared accross multiple roles, see above.|

## Data Structure
### Variables
|Variable Name|Default Value|Required|Type|Description|
|:---:|:---:|:---:|:---:|:---:|
|`name`|""|yes|str|The name or id of the inventory source to update.|
|`inventory`|""|yes|str|Name or id of the inventory that contains the inventory source(s) to update.|
|`organization`|""|no|str|Name of the inventory source's inventory's organization.|
|`wait`|""|no|bool|Wait for the job to complete.|
|`interval`|""|no|int|The interval to request an update from controller.|
|`timeout`|""|no|int|If waiting for the job to complete this will abort after this amount of seconds.|

### Standard Project Data Structure
#### Yaml Example
```yaml
---
controller_inventory_sources:
  - name: RHVM-01
    source: scm
    source_project: Test Inventory source project
    source_path: phillips_hue/hosts
    inventory: RHVM-01
    organization: Satellite
    credential: admin@internal-RHVM-01
    overwrite: true
    update_on_launch: true
    update_cache_timeout: 0
    wait: true



```

## Playbook Examples
### Standard Role Usage
```yaml
---
- name: Playbook to configure ansible controller post installation
  hosts: localhost
  connection: local
  # Define following vars here, or in controller_configs/controller_auth.yml
  # controller_hostname: ansible-controller-web-svc-test-project.example.com
  # controller_username: admin
  # controller_password: changeme
  pre_tasks:
    - name: Include vars from controller_configs directory
      include_vars:
        dir: ./yaml
        ignore_files: [controller_config.yml.template]
        extensions: ["yml"]
  roles:
    - {role: redhat_cop.controller_configuration.inventory_source_update, when: controller_inventory_sources is defined}

```
## License
[MIT](LICENSE)

## Author
[Sean Sullivan](https://github.com/sean-m-sullivan)
