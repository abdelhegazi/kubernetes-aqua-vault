# kubernetes-aqua-vault
This is an example of an implementation of integration between Openshift "Kubernetes" cluster, Hashicorp's vault, Consul "Storage backend" and Aquasec mounting glusterFS "for volume claims" but you can create your volumes based on any supported distributed filesystem like AWS EFS, Azure Filesystem, ceph ...etc. You would notie we use heketi here which is library we use to talk to Gluster APIs as there is notaive APIs for Gluster to talk to kubernetes.


Having look at this architecture we implemented you would notice the following:
- We used Aquasec as a first line security layer for image scanning, runtime protection, automated protection and for auditing and compliance profiles. 
- Aqua uses Hashicorp's vault as encypted secret storage.
- Vault uses Consul as storage backend, both containerss "vault and consul" run in one pods as side cars to each others and this is deployed as stateful set "HA".
- Any infrastructure related work is deployed onto region infra which is node label we use to refer to master nodes.
- GlusterFS is used due to the special condition around this environment where we created our storageClass
- Kubernetes cronjob runs every 30 minutes does one thing, backup encrypted data and save it on a separate volume, which is the woes scenario if all master nodes went down we still can restore all the secrets from the last backup cronjob.
- Secrest from Aqua could be accessed from the running pods as environment variables, so the only place you can see the value of the secrets is within the container itself not from anywhere else.
- No need to worry about managing consul cluster, this is consul's job itself and the statefulset is responsible for making sure every node has a running consulvault pod. 

