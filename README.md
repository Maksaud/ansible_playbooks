# Playbooks

## A simple playbook

- A playbook is saved using standard file extension .yml
- Indentation with space character indicates the structure of the data in the file.
- YAML does not place strict requirements on how many spaces are used for the indentation, but there are two basic rules

```
Data elements at the same level in the hierarchy mush have the same indentation
Items that are children of another item must be indented more than their parents.
```

- Only the space character can be used for indentation. Tab characters are not allowed.

### A playbook needs:

1. A name for the play to help document its intended purpose.
2. Hosts or groups on which the play will run, taken from the inventory.
3. This play will perform privilege escalation.
4. The beginning of the list of tasks in the play.
5. clear descriptive names for each task help document what each task does.
6. Each task uses one module to perform the work, in this case `user`
7. Argument for the user module to specify the user to manage, its UID number, and that it should exists

## Using varaibles

- Ansible supports variables that can be used to store values for reuse throughout an Ansible project.
- This simplifies the creation and maintenance of a project and reduce the number of errors
- Variables provide a convenient way to manage dynamic values.

### Scope

- Global
    - The calue is set for all hosts.
    - variables set in the job template is an example.

- Host
    - The value is set for a particular host or group.
    - Example: inventory variables or host_vars directory.

- Play
    - The value is set for all hosts in the context of the current play.
    - `vars` and `include_vars` are example

### Examples of variables in playbook

you can use `vars` block to create variables

```YAML
- hosts: all
  vars:
    user_name: maksaud
    user_state: present
```

You can also use an external file for variables

```YAML
- hosts: all
  vars_files:
    - vars/users.yml
```

### Refering to a variable

- variables are refered by using double braces. {{ variable_name }}
- When refrencing on evariables as another variable's value, and the curly braces start the value, myst use quotes around the value.

### Host variables and group variables

- Host variables apply to a specific host.
- Group bariables apply to all hosts in a host group or in a group of host groups
- Host variables and group variables can be defined
    - In the inventory itself
    - In `host_vars` and `group_vars` directories in the same directory as the inventory.