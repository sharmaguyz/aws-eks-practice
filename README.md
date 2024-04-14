# aws-eks-practice
aws-eks-practice


https://github.com/kubernetes-sigs/aws-load-balancer-controller/releases/tag/v2.1.3


ECR images
013241004608.dkr.ecr.us-gov-west-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
151742754352.dkr.ecr.us-gov-east-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
558608220178.dkr.ecr.me-south-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
590381155156.dkr.ecr.eu-south-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.ap-northeast-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.ap-northeast-2.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.ap-northeast-3.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.ap-south-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.ap-southeast-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.ap-southeast-2.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.ca-central-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.eu-central-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.eu-north-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.eu-west-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.eu-west-2.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.eu-west-3.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.sa-east-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.us-east-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.us-east-2.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.us-west-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
602401143452.dkr.ecr.us-west-2.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
800184023465.dkr.ecr.ap-east-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
877085696533.dkr.ecr.af-south-1.amazonaws.com/amazon/aws-load-balancer-controller:v2.1.3
918309763551.dkr.ecr.cn-north-1.amazonaws.com.cn/amazon/aws-load-balancer-controller:v2.1.3
961992271922.dkr.ecr.cn-northwest-1.amazonaws.com.cn/amazon/aws-load-balancer-controller:v2.1.3




example for latest : -

013241004608.dkr.ecr.us-gov-west-1.amazonaws.com/amazon/aws-load-balancer-controller




https://repost.aws/knowledge-center/eks-vpc-subnet-discovery


https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.2/guide/ingress/annotations/#subnets



eksctl create iamserviceaccount \
  --cluster=eks-demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::046529161809:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve



eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=eks-demo-cluster --approve




Worked

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=eks-demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=vpc-0627fb601dced4e54 \
  --set image.repository=602401143452.dkr.ecr.us-east-1.amazonaws.com/amazon/aws-load-balancer-controller \
  --set alb.ingress.kubernetes.io/subnets={"project-subnet-public1-us-east-1a”} \
  --set alb.ingress.kubernetes.io/subnets={”project-subnet-public2-us-east-1b"} \
  --set tags.kubernetes.io/cluster=eks-demo-cluster \
  --set tags.kubernetes.io/role/elb=1 \
  --set tags.kubernetes.io/role/internal-elb=1




kubernetes.io/cluster/${cluster-name}
