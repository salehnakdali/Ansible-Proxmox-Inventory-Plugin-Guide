# Ansible Proxmox Inventory Plugin Guide

This guide demonstrates how to use the Ansible Proxmox inventory plugin to dynamically manage your Proxmox inventory in Oracle Linux Automation Manager (OLAM) or AWX.

community.general.proxmox inventory – Proxmox inventory source
Link to the [Ansible Proxmox Inventory Plugin Documentation](https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_inventory.html) for more details.


## Overview

The Proxmox inventory plugin allows you to automatically discover and organize virtual machines from your Proxmox Virtual Environment into Ansible inventories, making infrastructure management more efficient.

## Setup Instructions

### 1. Create a Proxmox Inventory File

Create a new inventory file with the `.proxmox.yml` or `.proxmox.yaml` extension.

**Example**: See the [`proxmox.yml`](proxmox.yml) file in this repository which includes:
- API connection configuration
- Tag-based grouping for Linux/Windows servers
- Environment-specific groupings (test/prod)
- OS version groupings

### 2. Configure Credential Type in OLAM/AWX

1. Navigate to **Credentials type → New**
2. Set name: `proxmox`
3. Configure **Input Configuration**:

```yaml
fields:
  - id: proxmox_url
    type: string
    label: PROXMOX URL
  - id: proxmox_user
    type: string
    label: PROXMOX USER
  - id: proxmox_token_id
    type: string
    label: PROXMOX TOKEN ID
  - id: proxmox_token
    type: string
    label: PROXMOX Token
    secret: true
  - id: proxmox_validate_certs
    type: boolean
    label: Validate SSL
required:
  - proxmox_url
  - proxmox_user
  - proxmox_token_id
  - proxmox_token
  - proxmox_validate_certs
```

4. Configure **Injector Configuration**:

```yaml
env:
  PROXMOX_URL: '{{ proxmox_url }}'
  PROXMOX_USER: '{{ proxmox_user }}'
  PROXMOX_TOKEN_ID: '{{ proxmox_token_id }}'
  PROXMOX_TOKEN_SECRET: '{{ proxmox_token }}'
  proxmox_validate_certs: '{{ proxmox_validate_certs }}'
```

### 3. Add Proxmox Credential

1. Navigate to **OLAM → Credentials → Add**
2. Set name: `prox`
3. Select type: `proxmox`
4. Fill in the following details:
   - **PROXMOX URL**: `https://XXX.XXX.XXX.XXX:8006`
   - **PROXMOX USER**: `ansible@pve`
   - **PROXMOX TOKEN ID**: `ansibleid`
   - **PROXMOX Token**: Your token (e.g., `1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef`)
   - **Validate SSL**: Check as needed

### 4. Add Inventory Source

1. Navigate to **OLAM → Inventories → Add new inventory**
2. Set name: `proxmox-inventory`
3. In the inventory, go to **Sources → Add new source**
4. Configure the source:
   - **Name**: `proxmox-pve1`
   - **Source**: Source from a project
   - **Organization**: Default
   - **Credential**: prox
   - **Project**: Your project repository
   - **Source path**: [`proxmox.yml`](proxmox.yml)
5. Under update options, check both:
   - **Override**
   - **Override variables**
6. Save the source and run the sync

After syncing, hosts will be dynamically added to your inventory. You can then create smart inventories based on the Proxmox groups defined in your configuration.

## Additional Resources

For more information on the Proxmox inventory plugin, see the [official Ansible documentation](https://docs.ansible.com/ansible/latest/collections/community/general/proxmox_inventory.html).
