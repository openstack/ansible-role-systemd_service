#### Ansible systemd_service

This Ansible role that installs and configures systemd unit files and all of its
corresponding services. This role requires the ``openstack-ansible-plugins``
repository to be available on your local system. The Ansible galaxy resolver
will not retrieve this role for you. To get this role in place clone the
plugins repository **before** installing this role.

``` bash
# git clone https://github.com/openstack/openstack-ansible-plugins /etc/ansible/roles/plugins
```

You can also use the ``ansible-galaxy`` command on the ``ansible-role-requirements.yml`` file.

``` bash
# ansible-galaxy install -r ansible-role-requirements.yml
```

----

###### Defaults (See actual role for more details)

``` yaml
# This is the default path for a given service. Set this for general service lookups or
#  define "program_override" option in the systemd_services dictionary.
systemd_bin_path: /usr/local/bin

systemd_user_name: root
systemd_group_name: root
systemd_slice_name: system
systemd_lock_path: /var/lock

# This is the prefix used for all temp files of a given type.
systemd_tempd_prefix: tempd

# Give a reasonable amount of time for the server to start up/shut down
systemd_TimeoutSec: 120
systemd_Restart: on-failure
systemd_RestartSec: 2

# Accounting options
systemd_CPUAccounting: true
systemd_BlockIOAccounting: true
systemd_MemoryAccounting: true
systemd_TasksAccounting: true

# Sandboxing options
systemd_PrivateTmp: true
systemd_PrivateDevices: false
systemd_PrivateNetwork: true
systemd_PrivateUsers: true

# Start service after a given target. This is here because we want to define common
#  after targets used on most services. This can be overridden or agumented using
#  the "systemd_services" dictionary option "init_config_overrides".
systemd_after_targets:
  - syslog.target
  - network.target

# Set the service enabled state. Valid options are: [yes, no]
systemd_service_enabled: yes

# Set global service overrides used within the service unit file.
systemd_service_config_overrides: {}

# The systemd services dictionary is a set of services that will be created. The dictionary
#  can contain the following options:
#  `service_name` -- (required) used to define the name of the service. This is typically the name of the executable.
#  `init_config_overrides` -- (optional) This allows any section or key=value pair to be set within the systemd unit file.
#  `program_config_options` -- (optional) Provides the ability to pass in flags to a given service for execution.
#  `program_override` -- (optional) sets the full path to the executable that will be run by the service file.
#  `enabled` -- (optional) Set the enabled state of the service.
#  `state` -- (optional) Set the running state of the service.

#  Examples:
#   systemd_services:
#     ServiceW:
#       service_name: ServiceW
#       init_config_overrides: {}  # This is used to add in arbitratry unit file options
#     ServiceX:
#       service_name: ServiceX
#       init_config_overrides: {}  # This is used to add in arbitratry unit file options
#       program_config_options: '--flag1 things --flag2 other'
#     ServiceY:
#       service_name: ServiceY
#       init_config_overrides: {}  # This is used to add in arbitratry unit file options
#       program_override: '/usr/bin/ServiceY'
#     ServiceZ:
#       service_name: ServiceZ
#       init_config_overrides: {}  # This is used to add in arbitratry unit file options
#       enabled: no
#       state: stopped

systemd_services: {}
```

----

###### Example playbook

``` yaml
- name: Create a systemd unit file for ServiceX
  hosts: localhost
  become: true
  roles:
    - role: "systemd_service"
      systemd_services:
        service_name: ServiceX
        init_config_overrides: {}
        program_config_options: '--flag1 things --flag2 other'
      tags:
        - servicex-init

```
