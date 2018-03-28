# Etcd cluster

Etcd cluster is formed by nodes inside the `etcd` group, and they will:

- Share a CA and server/client certificates
- Have each own it's own peer certificate including it's published URLs
- Have `etcd` and `etcdctl` installed locally, and running the cluster as a
service

During all operations a backup is made in prevention of disasters. Thos backups
can be found on `/var/lib/etcd/backup`.

Etcd-only tasks can be run using `etcd.yml` playbook, but take in mind that it
won't upgrade the etcd addresses in Kubernetes!

## Scale the cluster up

The cluster can be scaled by just adding the new nodes on the `etcd` group and
running the playbook that creates the whole cluster again.

Note that it's mandatory to have a non-divisible-by-2 number of nodes to avoid
issues in case of a divvission in the cluster.

## Resolve partial outages on the Etcd cluster

There could be the situtation which one or more nodes of your etcd cluster can
be damaged:

```
$ etcdctl --endpoints "https://127.0.0.1:2379" --ca-file /etc/etcd/pki/ca.pem --cert-file /etc/etcd/pki/client.pem --key-file /etc/etcd/pki/client-key.pem cluster-health
failed to check the health of member 9d0eda13030ca920 on https://172.17.8.101:2379: Get https://172.17.8.101:2379/health: dial tcp 172.17.8.101:2379: i/o timeout
member 9d0eda13030ca920 is unreachable: [https://172.17.8.101:2379] are all unreachable
member bdabc9d73941bb97 is healthy: got healthy result from https://172.17.8.104:2379
member de2cb633919eda01 is healthy: got healthy result from https://172.17.8.102:2379
```

In this case, we can resolve the partial outage by removing the node itself:

```
$ etcdctl --endpoints "https://27.0.0.1:2379" --ca-file /etc/etcd/pki/ca.pem --cert-file /etc/etcd/pki/client.pem --key-file /etc/etcd/pki/client-key.pem member remove 9d0eda13030ca920
Removed member 9d0eda13030ca920 from cluster
```

and then add a new node to the `etcd` group of the inventory and re-run the
`create_cluster.yml` playbook. After that we would see how the new etcd node is
integrated in the cluster and can be used to receive etcd traffic.
