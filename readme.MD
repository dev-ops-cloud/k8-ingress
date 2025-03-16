# Ingress controller

REGION_CODE=us-east-1
CLUSTER_NAME=expense
ACC_ID=557690626059

### Permissions

* OIDC provider(# one time step administaration will do)
...
eksctl utils associate-iam-oidc-provider \
    --region $REGION_CODE \
    --cluster $CLUSTER_NAME \
    --approve
...

* IAM Policy
...

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.12.0/docs/install/iam_policy.json
...

* Create IAM policy
...

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
...
* Provide access to EKS through IAM policy
...
eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::$ACC_ID:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region $REGION_CODE \
--approve
.......

### install drivers

* Add the EKS chart repo to Helm
...
helm repo add eks https://aws.github.io/eks-charts
...

* Install AWS Load Balancer controller
...
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$CLUSTER_NAME --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
...


