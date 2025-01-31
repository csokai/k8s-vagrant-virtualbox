# k8s-vagrant-virtualbox
Create a local kubernetes cluster using virtualbox.  A modification of the great work [danielepolencic](https://github.com/danielepolencic) did in his [gist](https://gist.github.com/danielepolencic/ef4ddb763fd9a18bf2f1eaaa2e337544).

The vagrant file will do the following:
1.  Provision all local VMs using VirtualBox
2.  Patch the OS
3.  Install Docker
4.  Install k8s control plane
5.  Initialize cluster with Flannel CIDR block & install Flannel or Calico just comment out and edit calico.yaml if necessary:
    Change this to your needs:
      - name: CALICO_IPV4POOL_CIDR
        value: "192.168.0.0/16"
    Change Vagrantfile also  in line 102 kuebadmin init parameter at #Start cluster:
    --pod-network-cidr=192.168.0.0/16
6.  Join the nodes to the master
7.  Create and copy the SSH key to all machines so you can SSH to any node from the Master.  Add names & IPs to the local hosts file on each master and node.  Create alias in vagrant home for kubectl...just use k
8.  Make required Ubuntu OS mods for the cluster to function properly

## Dependencies

You should install [VirtualBox](https://www.virtualbox.org/wiki/Downloads) and [Vagrant](https://www.vagrantup.com/downloads.html) before you start.

Open a shell and install the vagrant disksize plugin:
```bash
$ vagrant plugin install vagrant-disksize
```

## Make sure git is installed

Instal [git](https://git-scm.com/downloads) if you don't already have it.

## Open a shell and clone

```bash
$ git clone https://github.com/csokai/k8s-vagrant-virtualbox.git
$ cd k8s-vagrant-virtualbox
```

## Starting the cluster

You can create the cluster with:

```bash
$ vagrant up
```

## Clean up

You can delete the cluster with:

```bash
$ vagrant destroy -f
```

## SSH and other Commands

SSH to Master and other Nodes:

```bash
$ vagrant ssh master
$ vagrant ssh node1
$ vagrant ssh node2
$ vagrant ssh node3
```

Get the status of the Nodes:

```bash
$ k get nodes -o wide
NAME     STATUS   ROLES    AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
master   Ready    master   16m     v1.17.4   10.0.0.10     <none>        Ubuntu 18.04.4 LTS   4.15.0-88-generic   docker://19.3.6
node1    Ready    <none>   11m     v1.17.4   10.0.0.11     <none>        Ubuntu 18.04.4 LTS   4.15.0-88-generic   docker://19.3.6
node2    Ready    <none>   6m31s   v1.17.4   10.0.0.12     <none>        Ubuntu 18.04.4 LTS   4.15.0-88-generic   docker://19.3.6
node3    Ready    <none>   102s    v1.17.4   10.0.0.13     <none>        Ubuntu 18.04.4 LTS   4.15.0-88-generic   docker://19.3.6
```

SSH to other Nodes in the cluster from the Master:

```bash
$ ssh node1
$ ssh node2
$ ssh node3
```
When needed you can label the worker nodes from master:

```bash
# label up worker nodes
kubectl label node node{1..3} node-role.kubernetes.io/worker=worker

kubectl get nodes
NAME     STATUS   ROLES                  AGE   VERSION
master   Ready    control-plane,master   19m   v1.21.2
node1    Ready    worker                 16m   v1.21.2
node2    Ready    worker                 13m   v1.21.2
node3    Ready    worker                 10m   v1.21.2

```
