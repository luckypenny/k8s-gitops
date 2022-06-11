# GitOps With k8s, ArgoCD, GithubAction
## 내용
- k8s 클러스터에 Argo CD와 깃헙 액션을 사용하여 GitOps를 구현하는 샘플 예제 입니다. 
- kustomize로 k8s에 사용되는 사용자 앱들을 정의하고 있습니다. (app-nodejs, app-react, app-react)
- 일부 앱에는 DataDog APM Agent를 설정하는 예제를 포함하고 있습니다.
- EKS 클러스터 노드 운영 관련 스크릡트
## Argo-helm-official
- Install with helm chart file
- version 1.7
### Chart Repository
- argocd: argo/argo-cd
```bash
# Download a chart from a repository
helm pull <CHART_REPO> 

# Config
helm show values <CHART_REPO> > values.yaml
cp values.yaml <ENV>.values.yaml

# Install
helm install -f <ENV>.values.yaml \
  -n <NAMESPACE> \
  <RELEASE> \
  <CHART>

# Upgrade
helm upgrade -f <ENV>.values.yaml \
  -n <NAMESPACE> \
  <RELEASE> \
  <CHART>
```
## k8s
- _argo : define ingress and root kustomization file
- _resources : define db, kafka, redis configmaps and secrets
- app-springboot : springboot app deployments, service, datadog apm example
- app-nodejs : nodejs app deployements, service example
- app-react : react app deployments, service example
## eks-nodegroup
- eks nodegroup yaml ( eksctl )
### ekctl nodegroup scripts
```shell
# get nodegroup 
eksctl get nodegroup --cluster prod-eks --region ap-southeast-1
# cordon / uncordon
k cordon ip-10-10-10-10.ap-southeast-1.compute.internal
k uncordon ip-10-10-10-10.ap-southeast-1.compute.internal

# create node group 
eksctl create nodegroup --config-file=prod-cluster.yml --include='worker'
eksctl create nodegroup --config-file=prod-cluster.yml --include='management'

# scale nodegroup
eksctl scale nodegroup --cluster=prod-eks --name=worker --nodes=1 --nodes-min=1 --nodes-max=1 --region ap-southeast-1
eksctl scale nodegroup --cluster=prod-eks --name=management --nodes=1 --nodes-min=1 --nodes-max=1 --region ap-southeast-1

# delete nodegroup
eksctl delete nodegroup --cluster=prod-eks --name=worker --region=ap-southeast-1
eksctl delete nodegroup --cluster=prod-eks --name=management --region=ap-southeast-1

# EKSCTL 로깅 활성
ref) https://eksctl.io/usage/cloudwatch-cluster-logging/
eksctl utils --cluster prod-eks update-cluster-logging --enable-types all --region ap-southeast-1 --approve

# control plane & addons upgrade 
# 버전 확인 (eks cp, kube-proxy, core-dns, amazon-k8s-cni)
$ kubectl version --short
$ kubectl get daemonset kube-proxy --namespace kube-system -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
$ kubectl get deployment coredns --namespace kube-system -o=jsonpath='{$.spec.template.spec.containers[:1].image}'
$ kubectl describe daemonset aws-node --namespace kube-system | grep Image | cut -d "/" -f 2

# 업그레이드 명령어 (eksctl)
$ eksctl upgrade cluster --name prod-eks --region ap-southeast-1
$ eksctl utils update-kube-proxy --cluster prod-eks --region ap-southeast-1
$ eksctl utils update-coredns --cluster prod-eks --region ap-southeast-1
$ eksctl utils update-aws-node --cluster prod-eks --region ap-southeast-1
```