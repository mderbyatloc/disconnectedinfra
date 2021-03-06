# Ansible Role: Packer
Uses Ansible to kick off Packer builds with several included reference builds (currently RHEL7 and VYOS).

## Requirements
* Packer binary (will be called via command module)
* date package installed on source system
* If installing from source, Git is required

## Role variables
Action variable will determine whether the systems are created or deployed and tested.  Most often the role will be called twice to build and subsequently test the system.
  action: [ build, test ]

You can include your vsphere information in the role or the inventory
vsphere:
  vcenter_server: vcenter.infrabuild.xyz
  cluster: main
  datacenter: Datacenter
  datastore: Datastore1
  folder: Templates
  network: BUILDNET
  username: administrator@vsphere.infrabuild.xyz
  password: BadPassword1234$
  esxi_hostname: 192.168.5.202

In order to update Red Hat based systems, you will need your Red Hat Subscription Manager login ID and pool id.

rhsm:
  username:     Red Hat Subscription Manager username
  password:     Red Hat Subscriptoin Manager password
  pool_id: Red Hat Subscripton Manager Pool ID 

## Example Inventory
all:
  children:
    build:
      hosts:
        localhost:
      vars:
        config_directory: /build_config
        artifact_directory: /build_artifact
        vsphere:
          vcenter_server: vcenter.infrabuild.xyz
          cluster: main
          datacenter: Datacenter
          datastore: Datastore1
          folder: Templates
          network: BUILDNET
          username: administrator@vsphere.infrabuild.xyz
          password: BadPassword1234$
          esxi_hostname: 192.168.5.202
        env:
          VMWARE_USER: "{{ vsphere.username }}"
          VMWARE_PASSWORD: "{{ vsphere.password }}"
          RHSM_PASSWORD: testpassword
          PACKER_CACHE_DIR: "{{ artifact_directory }}"
        packer_default:
          variables:
            ssh_username: vyos

## Example Playbook
---
- hosts: build

  roles:
  - role: image_creation
    vars:
      action: build 
  - role: image_creation
    vars:
      action: test
