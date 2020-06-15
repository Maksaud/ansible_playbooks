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


## Protecting sensitive data

### Ansible Vault

- Ansible Playbooks sometimes need access to sensitive data.
    - Passwords, API keys, other secrets
- These secrets are often passed to Ansible through variables.
- It is a security rist to store these secrets in plain text files.
- Ansible Vault provides a way to encrypt and decrypt files used by playbooks
- The ansible-vault command is used to manage these files

### Create, view, Edit, encrypt and decrypt.

You will need a password for Ansible vault

- Use `ansible-vault create filename` to create a new encrypted file.
- Use `ansible-vault view filename` to view a new encrypted file.
- Use `ansible-vault edit filename` to edit a new encrypted file.
- Use `ansible-vault encrypt filename` to encrypt existing files.
- Use `ansible-vault decrypt filename` to decrypt a file

To save the encrypted file to a new name using the option `--output=new_filename`

### Playbooks and Ansible vault

- Provide the vault password using the --vault-id option: `ansible-playbook --value-id @prompt filename`
- The `@prompt` option will prompt the user for the ansible vault password
- If you do not provide a password, ansible returns an error

### Multiple vault Password

- You can use the --vault-id option to set a label ton encrypted file.
- The following example sets the label vars on the file and prompts you for the password to use `ansible-vault encrypt filename --vault-id vars@prompt`
- If you have files encrypted with different passwords, you can use this to prompt multiple times: `ansible-playbook --vault-id vars@prompt --vault-id playbook@prompt site.yml

### Change password of an encrypted file

- Change the password of an encrypted file using `ansible-vault rekey filename`
- rekey subcommand can be used on multiple data files at once.
- It prompts for the current password, then the new password.

### Suppress output from a task

- Sometimes the output of ansible-playbook prints a sensitive value
- suppress the output of a task by adding no_log:true
- This can make troubleshooting harder, so use it only where needed.

## Iterations and loops

- Using loops saves admin from the need to write multiple tasks that use the same module.
- using the `loop` keyword you can iterate through tasks.
- Loops can be configured to repeat a task using each item in a list.
- The loop variable `item` holds the value used during each iteration.

eg:

```YAML
- name: Create users
  hosts: all
  vars:
    myusers:
        - maksaud
        - john
        - doe
    
  tasks:
    - name: create users
      user:
        name: "{{ item }}"
        state: present
      loop: "{{ myusers }}"
```

### Creating users in groups

- There are other more advanced looping constructs beside `loop`.
- `with_dict` takes a list, but each item in the list is actually a hash or a dictionary instead of a simple value

eg.

```YAML
- name: Create users
  hosts: all
    
  tasks:
    - name: create groups
      group:
        name: "{{ item }}"
      loop:
        - arsenal
        - mancity

    - name: create users in groups
      user:
        name: "{{ item.name }}"
        groups: "{{ item.groups }}"
      with_dict:
        - { name: 'Aubameyang', group: 'arsenal' }
        - { name: 'Aguero', group: 'mancity' }
```

## Conditionals

- Ansible can use conditionals to run or skip tasks when certain condition are met.
- Variables and facts can both be tested by conditionals.
- Operators such as greater than or less than compare strings, numerical data, or Boolean.

### Some Uses for conditional tasks

- Run a task if a fact reporting the available memory on a managed host is lower than a value.
- Run different tasks to create users on a managed host based on which domain it belongs to
- Skip a task if a certain variable is not set or is set to a specific value
- Use the results of a previous task to determine whether to run the task.

### Using conditionals

The `When` statement is used to run a task conditionally.
- If the condition is met, the task runs. If the condition is not met, the task is skipped.
- In the following example the task will only run if the value of run_my_task is true:

```YAML
---
- name: Simple bolean task demo
  hosts: all
  vars: run_my_task: true

  tasks:
    - name: httpd package is installed
      yum:
        name: httpd
      when: run_my_test
```

### Constructing conditions with ansible facts

- The `ansible_facts['distribution']` variable is a fact set when the play runs, which identifies the operating system of the current managed host.
- The `supported_os` variable contains a list of operating systems supported by the playbook.

## Ansible Handlers

- Ansible modules are designed to be idempotent
- A propery written playbook and its tasks can be run multiple ties without changing the managed host.
- Howeve, sometimes when a task does make a change to system, a further task may need to be run.
- For example, a change to a service's configuration file may need require that the service be reloaded so that the changed configuration takes effect.
- Handlers are tasks that responds to a notification triggered by other tasks
- Tasks only notify their handlers when the task changes something on a managed host.
- Each handler has a globally uniwue name and is triggered at the end of a block of tasks in a playbook
- If not, task notifies the handler by name then the handler will not run
- 
- Because handlers are tasks, administrators can use the same modules in handlers that they would use for any other task.
- Normally, handlers are tasks, admins 