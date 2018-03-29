# ansible-kubeadm

Ansible scripts to spin up your Kubernetes cluster on any Debian machine with SSH access

The aim of this ansible package is to provide a way to:

- Safely create a Kubernetes cluster (including etcd storage)
- Be able to scale both Kubernetes and etcd clusters
- Be able to recover from partial cluster outages without major issues

## Requirements

This playbooks are thought to be run on **Debian** machines, and suppose that
the user has SSH access and a root/sudoer account, as many of the operations
executed require administration rights.

## Software installed

- Docker (latest version available on official repos)
- Kubeadm (latest versiona vailable on official repos)
- Kubernetes (version indicated in config vars)
- Etcd (version indicated in config vars)

## Playbooks included

* **create_all.yml**: It creates the Etcd cluster and spawns the kubernetes
cluster. Also, it is able to scale both things if it's run over an existing
infrastructure.

* **create_cluster.yml**: It just creates the cluster taking the assumption of
having the Etcd cluster running and it's secrets on the `secrets` folder.

* **etcd.yml**: it just spawns the Etcd cluster and copies the secrets to local.

* **drop_cluster.yml**: Delete Kubernetes cluster from all nodes.

## Secrets

After running the playbooks, a copy of some secrets are stored in `secrets`
folder, including:

- Kubeconfig file to access the cluster
- CA of Kubernetes (and it's private key)
- Front proxy CA of Kubernetes (and it's provate key)
- Service Accounts public and private keys
- Etcd's CA (and it's private key)
- Etcd's client key (and it's private key)

Those secrets **should never** be commited to this repository.

In case of loosing them, the playbooks try to fetch them from the first host
of each group (`kube-controllers` and `etcd`) in order to avoid regenerating
the whole KPI, which can lead to a (short) outage time.

## High Availability

The playbooks are made with HA mode in mind:

- Etcd cluster supports multiple nodes (always a non multiple of 2), and has been
tested in situations of nodes failures/recovery

- Kubernetes supports multiple controllers, creating a local NGiNX load balancer
on the worker nodes to proxy api server requests to available controllers.

For more information about the approach followed, you can
[check this link](https://kubernetes.io/docs/setup/independent/high-availability/)

## Extra documentation

Some extra documentation:

- [Etcd cluster operation](https://github.com/odarriba/ansible-kubeadm/blob/master/docs/etcd.md)

## TODO

- Scale nodes playbook
- Delete a node playbook

## License

This software and configuration is licensed under MIT License, which can be
checked on LICENSE file.
