![](/images/snapshot_icon.png)

# Nutanix Snapshot Manager
An Ansible role that will create, restore, or delete a VM snapshot in a Nutanix cluster running AHV via Prism Central

> **Note:** *When restoring a snapshot of a VM that is powered on, it will be powered off during the restore then immediately powered back on when completed*

## Required Variables

**prism_central:** 172.16.1.100  
*FQDN or IP address of the Prism Central server*  
 
**prism_user:** admin  
*An account with permissions to manage snapshots*  
 
**prism_password:** super_secret_password  
*The account password should be stored in an Ansible vault file*  
 
**vm_name:** oh-snap  
*The VM to be snapshotted* 
> **Note:** *The value is case sensative and must match the exact name as it appears in Prism Central*
 
**snapshot_name:** before_some_changes  
*The name of the snapshot*  
 
**snapshot_action:** create  
*Valid options are create, restore, or delete depending on the desired action*

## Optional Variables

**global_debug:** True  
*Valid options are True or False whether to show more verbose output (Default value is False)*  
 
**enforce_unique_name:** True  
*Valid options are True or False whether to append the epoch time (In seconds) to the snapshot name to ensure it is unique (Default value is False)*  
 
**restore_network_configuration:** True  
*Valid options are True or False whether to restore the network configuration from the snapshot (Default value is True)*  

## Sample Playbook
```
---
- name: Create A VM Snapshot In A Nutanix Cluster
  hosts: localhost
  gather_facts: False
  connection: local

  vars:
    prism_central: "prismcentral.domain.com"
    prism_user: "admin"
    prism_password: "super_secret_password"
    vm_name: "oh-snap"
    snapshot_name: "before_some_changes"

  tasks:
    - name: Create Snapshot
      include_role:
        name: nutanix_snapshot_manager
      vars:
        snapshot_action: create

    - name: Pausing To Make Changes To The VM...
      pause:
        seconds: 30

    - name: Restore Snapshot To Undo Changes
      include_role:
        name: nutanix_snapshot_manager
      vars:
        snapshot_action: restore

    - name: Pausing...
      pause:
        seconds: 30

    - name: Delete Snapshot To Cleanup
      include_role:
        name: nutanix_snapshot_manager
      vars:
        snapshot_action: delete
```
