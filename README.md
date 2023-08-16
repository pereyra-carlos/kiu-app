# kiu-app



# Create the RDS + ECR
make rds-ecr-create

```
result last lines:

Apply complete! Resources: 30 added, 0 changed, 0 destroyed.

Outputs:

account_id = "578740199171"
db_address = "vote.ckxnxtpwrrto.us-east-2.rds.amazonaws.com"
ecr_repository_url = "578740199171.dkr.ecr.us-east-2.amazonaws.com/eks-rds-kiu"
project_name = "eks-rds-kiu"
region = "us-east-2"
 END  2023-15-08 17:10:35
 DURATION  245 seconds
```

# Create EKS
make eks-create  

```
result last lines:

eks_cluster_endpoint = "https://04B0A98F7AA2AA109246F86F923E26C6.yl4.us-east-2.eks.amazonaws.com"
eks_cluster_id = "eks-rds-kiu"
eks_nodegroup_rolearn = "arn:aws:iam::578740199171:role/green-eks-node-group-20230815205643245300000001"
project_name = "eks-rds-kiu"
region = "us-east-2"
vpc_id = "vpc-051a61a24b3e197bc"
vpc_private_subnets = tolist([
  "subnet-09ac06337ba189497",
  "subnet-0942745d880716523",
])
 END  2023-15-08 18:07:32
 DURATION  662 seconds
❯ k get nodes
NAME                                       STATUS   ROLES    AGE   VERSION
ip-10-0-3-189.us-east-2.compute.internal   Ready    <none>   16m   v1.23.17-eks-a5565ad


#Check SG port 5432 between EKS and RDS
```

# Create Image and Push to ECR
make ecr-push

```
result last lines:

Login Succeeded
The push refers to repository [578740199171.dkr.ecr.us-east-2.amazonaws.com/eks-rds-kiu]
88c29a02c0ef: Pushed 
7dad4bcad237: Pushed 
a39d67fb040d: Pushed 
efacba6c0e9c: Layer already exists 
77b897ea207d: Layer already exists 
```

# Test Connection with RDS from the EKS cluster

```
#Create a pod
kubectl run alpine --image=alpine -i --tty -n vote

#Connect to the shell pod
apk --update add postgresql-client

#Set environments
POSTGRES_USERNAME=master
POSTGRES_PASSWORD=masterpass
POSTGRES_HOST=vote.xxxxx.us-east-2.rds.amazonaws.com
POSTGRES_DATABASE=vote

#Test connection
psql postgresql://$POSTGRES_USERNAME:$POSTGRES_PASSWORD@$POSTGRES_HOST:5432/$POSTGRES_DATABASE?sslmode=require

vote=> select * from vote;
 name | value 
------+-------
 up   |     2
 down |     1
(2 rows)
```
#Create Deploy Pod and Service 
make kubectl-vote

#Get Public URL
make load-balancer
