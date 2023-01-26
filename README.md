# Octopus Underwater App

This app is a simple web application that displays relevant resources for users that have completed their first deployment. It contains an underwater scene and links to blog post.

## Create cluster
```
eksctl create cluster \
--name jenkins-cluster \
--region us-west-1 \
--fargate
```
## Setup Load Balancer (ALB)
https://aws.amazon.com/premiumsupport/knowledge-center/eks-alb-ingress-controller-fargate/

```
eksctl utils associate-iam-oidc-provider --cluster YOUR_CLUSTER_NAME --approve --region us-west-1
```
```
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.4/docs/install/iam_policy.json
```
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json \
    --region us-west-1
```
```
eksctl create iamserviceaccount \
  --cluster=jenkins-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --region us-west-1 \
  --approve
```
```
helm repo add eks https://aws.github.io/eks-charts
kubectl apply -k "github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master"
```
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
    --set clusterName=YOUR_CLUSTER_NAME \
    --set serviceAccount.create=false \
    --set region=YOUR_REGION_CODE \
    --set vpcId=<VPC_ID> \
    --set serviceAccount.name=aws-load-balancer-controller \
    -n kube-system
```
## Jenkins plug-ins
- Amazon ECR
- CloudBees AWS Credentials
- Docker Pipeline
- AWS Steps

Add AWS credentials in Jenkins