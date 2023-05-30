<h1 align="center"> NFS-For-Kubernetes </h1>
<h2 align="center"> Raising your own NFS server, which will act as a Shared, Persistent Storage across the Kubernetes Cluster </h2>

![imagegit](https://github.com/shankar439/Images/assets/70714976/9ae712cd-457f-4507-8308-325ff27f6c0c)
### Points to note before Creating our own NFS server for Kubernetes in your environment.
  - `This kind of setup need to choose wisely according to your requirement, organization and infrastructure.`
  - ### Pros
    - Significant reduction in cost rather purchacing managed NFS from cloud providers
  - ### Cons
    - We need to manage our NFS server.
    - Not available in multiple Zone, Bound with single Zone.
    - For Disaster Recovery regular snap-shot need to be backed-up. 

### prerequisite
  - Ubuntu server with 2Vcpu, 2GB ram and 200-GB disk (storage according to your need)
  - Kubernetes Cluster 
  - Domain Name 

<br>
<br>

<h1 align="center">Lets Setup </h1>

- Login to the server and change to Root user
- Update the package manager and upgrade it.
```
sudo su
apt update
apt upgrade
```

<br>

## Create Directory for NFS

- Before Installing NFS server, First create a Directory that used to store and retrieve data for Kubernetes cluster.
- I choose var directory, you can choose according to your preference. 
```
mkdir /var/nfs/k8s/
```

## Permission

- Change the user and group for that directory to nobody and nogroup accordingly. 
- And change the permission to read and write by anyone.
```
chown -R nobody:nogroup /var/nfs/
chmod 777 /var/nfs/
```
`Note: I mentioned all sub folders under /var/nfs/ need to be impact`

<br>

## Install NFS

- Install the NFS server using the below cmd
```
apt install nfs-kernel-server
```

- Now lets mention NFS server to store all the Data by using the dirctory we create before in this file `/etc/exports`.
```
nano /etc/exports
```

- And insert the below cmd
```
/var/nfs/k8s/ 10.3.65.9(rw,async,no_subtree_check,no_root_squash)
```

<br>

- We can configure multiple directories to be exported by adding them to the `/etc/exports` file.
- And we can configure which server can access and the permissions in  `/etc/exports` file.

For Example:
```
/srv     *(ro,sync,subtree_check)
/srv/log     10.4.78.0/24(ro,sync,subtree_check)
/home/directory1    *.shankar.com(rw,sync,no_subtree_check)
/var/lib 10.3.65.9(rw,async,no_subtree_check,no_root_squash)
```

<br>
<br>

<h2 align="center"> Deploy NFS Subdir External Provisioner </h2>

### [Deploy NFS Subdir External Provisioner to our Kubernetes cluster using Helm](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner)


```console
$ helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
$ helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=x.x.x.x \
    --set nfs.path=/var/nfs/k8s
```



# END