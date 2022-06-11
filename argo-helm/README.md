## Install ArgoCD
- argocd: https://argoproj.github.io/argo-helm
```bash
# helm repo update
helm repo update

# helm search repo argo
helm search repo argo

# Add repo 
helm repo add argo https://argoproj.github.io/argo-helm

# Install argo/argo-cd
helm install argocd argo/argo-cd -f argo.values.yaml
helm install argocd argo/argo-cd

# Update
helm upgrade argocd argo/argo-cd -f argocd.values.yaml

# Uninstall
helm uninstall argocd

# Value
helm show values argo/argo-cd --version 2.6.2 > argocd.values.yaml

# 재설치가 되지 않을 경우 아래 리소스를 삭제한다. 
k delete clusterrole argocd-application-controller
k delete clusterrole argocd-server
k delete clusterrolebindings.rbac.authorization.k8s.io argocd-server
k delete clusterrolebindings.rbac.authorization.k8s.io argocd-application-controller

# Get Values
helm show values argo/argocd-notification > argocd-notifications.values.yaml

# Install ArgoCd Notifications
helm install argocd-notifications argo/argocd-notifications -f argocd-notifications.values.yaml

# Notification Upgrade
helm upgrade argocd-notifications argo/argocd-notifications -f argocd-notifications.values.yaml

# ArgoCd Upgrade history
# 헬름 업그레이드 
helm upgrade argocd argo/argo-cd --version 2.7.4

# 변경된 버전의 value 값 얻기
helm show values argo/argo-cd --version 2.7.4 > upgrade.yaml

# 두개의 파일을 비교하여 변경 하고자 하는 파일로 만듬 ( 버전 정보 및 추가 필드 삽입 - 기본값 사용 )

# 헬름차트 업그레이드 
helm upgrade argocd argo/argo-cd -f argocd.values.yaml

## 사용자 추가 및 패스워드 변경
kubectl port-forward service/argocd-server -n management 8080:443
argocd login localhost:8080
argocd user list
argocd account update-password --account developer

## 아르고 시디 노티피케이션 업데이트 
argocd-notifications argo/argocd-notifications
helm show values argo/argocd-notification --version 1.0.11 > argocd-notifications.1.0.11.values.yaml
helm show values argo/argocd-notifications --version 1.0.11
```