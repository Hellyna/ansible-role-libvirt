# ansible-role-libvirt

A simple role manages libvirt with default network and default storage pool (atm)

`cloud-init` is supported.

All VM names at the moment are tied to their respective storage `.img` and `.iso`(cloud init generated file) names in the default pool. On destroy (`state: destroyed`) they will be deleted as well. Hence, non unique VM names are not supported at this moment. Do take note of this before you want to run a playbook with a VM set to be destroyed.

`state` refers to the state described in https://docs.ansible.com/ansible/latest/modules/virt_module.html.

# Example usage (vars):

```yaml
libvirt_vms:
  - name: test
    memory_megabytes: 1024
    cpus: 1
    volume_size: 10G
    state: running
    os_id: 'http://ubuntu.com/ubuntu/20.04'
    import_image_url: https://cloud-images.ubuntu.com/focal/current/focal-server-cloudimg-amd64.img
    import_image_url_checksum: sha1:cbfa2fe83d0acc5b8469c86b48d9643550c35ff8
    cloud_config:
      hostname: test
      fqdn: test.localdomain
      users:
        - name: admin
          sudo: ALL=(ALL) NOPASSWD:ALL
          groups: users, admin
          home: /home/admin
          shell: /bin/bash
          lock_passwd: true
      ssh_pwauth: false
      disable_root: false
      ssh-authorized-keys:
        - <your ssh pubkey>
    network_config:
      version: 2
      ethernets:
        enp1s0:
          dhcp4: false
          addresses:
            - 192.168.122.11/24
          gateway4: 192.168.122.1
          nameservers:
            addresses:
              - 1.1.1.1
              - 1.0.0.1
```

## Caveats

Make sure that your interfaces in the ethernets field exist in the system. Otherwise `cloud-init` will fail silently.

# Misc info

* [How default network is created](/templates/network.xml.j2)
* [How default storage is created](/tasks/ensure-default-storage-pool.yml)

Also look at files in the [defaults](/defaults) directory.
