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

IAM Policy download link - https://github.com/kubernetes-sigs/aws-load-balancer-controller/blob/main/docs/install/iam_policy.json

IAM policy create command - 

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy_latest.json


Make a note of Policy ARN
Make a note of Policy ARN as we are going to use that in next step when creating IAM Role.
# Policy ARN 
Policy ARN:  arn:aws:iam::046529161809:policy/AWSLoadBalancerControllerIAMPolicy
    
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
  --set tags.kubernetes.io/role/elb=1 \        #This tag is applied to AWS resources to designate them as public-facing Elastic Load Balancers (ELBs).
  --set tags.kubernetes.io/role/internal-elb=1  # This tag is applied to AWS resources to designate them as private, internal-facing Elastic Load Balancers (ELBs).




kubernetes.io/cluster/${cluster-name}



===========






ingress1.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-namedbasedvhost-demo1
  annotations:
    alb.ingress.kubernetes.io/load-balancer-name: namedbasedvhost-ingress.    #load-balancer name is common
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/success-codes: '200'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'   
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}, {"HTTP":80}]'
    alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:180789647333:certificate/d86de939-8ffd-410f-adce-0ce1f5be6e0d
    alb.ingress.kubernetes.io/ssl-redirect: '443'
    external-dns.alpha.kubernetes.io/hostname: default101.stacksimplify.com 
spec:
  ingressClassName: my-aws-ingress-class
  defaultBackend:
    service:
      name: app3-nginx-nodeport-service
      port:
        number: 80     
  rules:
    - host: app101.stacksimplify.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app1-nginx-nodeport-service
                port: 
                  number: 80


ingress2.yaml



apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-namedbasedvhost-demo2
  annotations:
    alb.ingress.kubernetes.io/load-balancer-name: namedbasedvhost-ingress.     #load-balancer name is common
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/success-codes: '200'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'   
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}, {"HTTP":80}]'
    #alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:180789647333:certificate/d86de939-8ffd-410f-adce-0ce1f5be6e0d. #comment this line because certs will discovery host based
    alb.ingress.kubernetes.io/ssl-redirect: '443'
    external-dns.alpha.kubernetes.io/hostname: default101.stacksimplify.com 
spec:
  ingressClassName: my-aws-ingress-class
  defaultBackend:
    service:
      name: app3-nginx-nodeport-service
      port:
        number: 80     
  rules:
    - host: app201.stacksimplify.com
      http:
        paths:                  
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app2-nginx-nodeport-service
                port: 
                  number: 80



Final worked

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-namedbasedvhost-demo
  annotations:
    alb.ingress.kubernetes.io/load-balancer-name: namedbasedvhost-ingress  # alb-ingress-lb-name
    alb.ingress.kubernetes.io/scheme: internet-facing                       # internet facing in public subnet
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/success-codes: '200'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'   
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}, {"HTTP":80}]'
    #alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:us-east-1:180789647333:certificate/d86de939-8ffd-410f-adce-0ce1f5be6e0d
    alb.ingress.kubernetes.io/ssl-redirect: '443'
    external-dns.alpha.kubernetes.io/hostname: "*.stacksimplify.com"           # also define this like
spec:
  ingressClassName: my-aws-ingress-class
  rules:
    - host: app101.stacksimplify.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app1-nginx-nodeport-service
                port: 
                  number: 80
    - host: app201.stacksimplify.com
      http:
        paths:                  
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app2-nginx-nodeport-service
                port: 
                  number: 80









