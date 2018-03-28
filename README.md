# ansible-kubeadm

Ansible scripts to spin up your Kubernetes cluster on any Debian machine with SSH access

The aim of this ansible package is to provide a way to:

- Safely create a Kubernetes cluster (including etcd storage)
- Be able to scale both Kubernetes and etcd clusters
- Be able to recover from partial cluster outages without major issues

Some extra documentation:

- [Etcd cluster operation](https://github.com/odarriba/ansible-kubeadm/blob/master/docs/etcd.md)
