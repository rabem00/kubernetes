# Ansible Playbooks for Kubernetes Clusters

There are two types of playbooks in this repository:

- `install.yml` - This is the main playbook that will configure a Kubernetes cluster. It will install the required packages, configure the cluster, and install the required applications.
- `upgrade.yml` - This playbook will upgrade the cluster to the latest version of Kubernetes.

The playbooks are designed to be run on a single Ansible controller, and will configure a Kubernetes cluster on a set of nodes. The nodes can be any combination of Raspberry Pi 4s, Raspberry Pi Compute Module 4s, and/or x86_64 servers.

## K8S - RHEL 8 Based Cluster

## K8S - Raspberry Pi and DeskPi super6C Setup

Running Raspberry Pi OS (64-bit, lite) on a set of six Raspberry Pi Compute Module 4s with 8GB of RAM. All are installed in a DeskPi super6C. Each node is configured to boot from NVME. Each NVME is flashed with Raspberry Pi OS using Raspberry Pi Imager.

To make network discovery and integration easier, I edited the advanced configuration in Imager (press Shift + Ctrl + X), and set the following options:

- Set hostname: `cl1m1.local` (set to cl1 for cluster1 and m1 for master1, etc. Set cl1w1 for worker1 etc.)
- Enable SSH: 'Allow public-key', and paste in my public SSH key(s)

After setting all those options, making sure the hostname is unique to each node (and matches what is in `inventory/cluster1.ini`).

### SSH connection test

To test the SSH connection from an Ansible controller (each server individually, and accepted the hostkey):

```
ssh pi@cl1m1.local
```

This ensures Ansible will also be able to connect via SSH in the following steps. Test Ansible's connection with:

```
ansible all -m ping
```

It should respond with a 'SUCCESS' message for each node.

### Cluster configuration

Run the playbook:

```
ansible-playbook main.yml
```

### Upgrading the cluster

Run the upgrade playbook:

```
ansible-playbook upgrade.yml
```

### Shutting down the cluster

The safest way to shut down the cluster is to run the following command:

```
ansible all -B 500 -P 0 -a "shutdown now" -b
```

Then after you confirm the nodes are shut down, hold down the power button for about 5 seconds, and the board will power off the CM4 slots. Then you can switch off or disconnect your power supply.
