# crossplane-aws-infra
This repository shows the options to configure the AWS provider for Crossplane correctly. 


## Pre-requisites
The following are the requisites:

- The EKS cluster available.
- The required permissions to deploy the AWS resources and create the IAM roles.
- Kubectl tool.
- Any IDE to manipulate files.
- Have installed a Crossplane in the EKS cluster.

## AWS Provider
The provider used in these examples is maintained by [upbound.io](https://marketplace.upbound.io/providers/crossplane-contrib/provider-aws/v0.44.0)

### Via Credentials
This option is straightforward and is only recommended for testing because it's insecure and hard to maintain in enterprise environments. 

1. Go to crds/credentials
2. Populate the AWS credentials with enoght permissions for deploy the all resources you want. 
3. Execute the following command:

```shell
kubectl create secret generic aws-secret -n crossplane-system --from-file=creds=./credentials.txt
```
4. Create the provider configurations
   
```shell
kubectl apply -f provider-config.yaml && kubectl apply -f provider.yaml
```


### Via IRSA
This method is more suittable for enterprise environments because it requires a previous OIDS configuration in AWS to decouple and delegate the authorization process through Services Accounts which this becomes more secure and extensible.

[Applying IRSA Tutorial](https://www.eksworkshop.com/docs/security/iam-roles-for-service-accounts/add_irsa/)

Once the IRSA is created and have handly the ARN IAM Role configured, you would execute the following steps to setup the AWS provider for Crossplane.

1. Go to crds/irsa/provider-config.yaml and modify the field ```eks.amazonaws.com/role-arn``` setting the ARN IRSA.
2. Run the following command to setup the AWS provider.

```shell
kubectl apply -f controller-config.yaml && kubectl apply -f provider-config.yaml && kubectl apply -f provider.yaml
```

### Provider validation
To check the provider was installed correctly execute the following command:

```shell
kubectl get providers
```

**Expected result**
```
NAME           INSTALLED   HEALTHY   PACKAGE                                                   AGE
aws-provider   True        True      xpkg.upbound.io/crossplane-contrib/provider-aws:v0.44.0   27m
```


### Run examples
To see AWS resources managed by Crossplane, you can go to the /examples folder and apply whatever file using ```Kubectl apply -f <file>``` and then validate the creation resource looking for the CRDS. 

Also, you could check the AWS resource through the AWS console. 

**Available examples so far**
- IAM Role
  
**Future examples**
- EKS cluster  (Crossplane Composition)
- Networking Resources (VPC, Subnets, NAT Gateways )
- IRSA Role (Crossplane Composition)