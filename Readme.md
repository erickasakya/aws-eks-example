## Things to note.
If your aws credentials have a profile name other than default, remember to always pass --profile <profile-name> flag to eksctl and aws commands  

I modified the policy document specification the statement on the loadbalancer to add permission ``"elasticloadbalancing:AddTags"``

To delete the cluster or resources use cloudformation.

## To create the cluster use the command below
``eksctl create cluster -f cluster.yaml``

## To check the config context for the kubectl
``kubectl config get-contexts``


## create IAM policy with permissions to create loadbalancer
``aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json ``

## Deploy kubernete resources.
1. namespace.yaml
2. load-balancer-controller.yaml
3. ingress-class.yaml
4. pod-info-deployment.yaml, pod-info-service.yaml, pod-info-ingress.yaml
5. quota-app-deployment.yaml, quota-app-service.yaml, quota-app-ingress.yaml

``kubectl apply -f <file-name.yaml>``

Remember that service accounts are need before deploying ingress components.

``` eksctl utils associate-iam-oidc-provider --cluster lil-eks --approve ```

## create service account using the iam policy 
eksctl create iamserviceaccount \
    --cluster=lil-eks \
    --name=aws-load-balancer-controller \
    --namespace=kube-system \
    --attach-policy-arn=arn:aws:iam::xxxxxxxxxxxx:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve

## Deploy cert-manager for ssl
kubectl apply \
    --validate=false \
    -f https://github.com/jetstack/cert-manager/releases/download/v1.5.4/cert-manager.yaml

