# Full terraform example

You can run `terraform apply` from this directory to build example cluster with VPC and IAM resources. `staging` uses public subnets.  (You can also enable another cluster or add more cluster.. NOTE: if you add more clusters it will use shared VPC.)

Note that this example is using non-existent domain and private DNS mode so a configuration change is required to connect to the Kubernetes API after creation:

```shell
MASTER_ELB_CLUSTER1=$(terraform state show module.staging.aws_elb.master | grep dns_name | cut -f2 -d= | xargs)
kubectl config set-cluster staging.k8.thoutam.com --insecure-skip-tls-verify=true --server=https://$MASTER_ELB_CLUSTER1
```

And then test:

```shell
kubectl cluster-info
Kubernetes master is running at https://cluster1-master-999999999.eu-west-1.elb.amazonaws.com
KubeDNS is running at https://staging-master-999999999.eu-west-1.elb.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns/proxy

kubectl get nodes
NAME                                          STATUS    ROLES     AGE       VERSION
ip-172-20-25-99.eu-west-1.compute.internal    Ready     node    2m        v1.8.11
ip-172-20-26-11.eu-west-1.compute.internal    Ready     master    3m      v1.8.11
ip-172-20-26-209.eu-west-1.compute.internal   Ready     node      27s     v1.8.11
ip-172-20-27-107.eu-west-1.compute.internal   Ready     node    2m        v1.8.11
```

In real use you should use a valid public Route53 zone and remove the private DNS mode option.
