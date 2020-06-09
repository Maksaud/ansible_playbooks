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
