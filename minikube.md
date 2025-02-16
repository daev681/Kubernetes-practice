# Minikube 기반 Kubernetes 테스트 환경 구축 (Ubuntu 24.04 LTS)

## 1. 사전 준비
### 1.1 필수 패키지 설치
```bash
sudo apt update
sudo apt install -y curl apt-transport-https
```

### 1.2 Docker 설치 (Minikube 드라이버)
```bash
sudo apt install -y docker.io
sudo systemctl enable --now docker
```

✅ **현재 사용자(예: ubuntu)를 Docker 그룹에 추가 (재로그인 필요)**  
```bash
sudo usermod -aG docker $USER
newgrp docker
```

🚀 **Docker 정상 동작 확인**  
```bash
docker run hello-world
```

---

## 2. Minikube 설치
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

설치 확인:
```bash
minikube version
```

---

## 3. Minikube 실행
### 3.1 Minikube 시작 (싱글 노드)
```bash
minikube start --driver=docker
```

🚀 **정상적으로 실행되었는지 확인**  
```bash
kubectl get nodes
```
출력 예제:
```
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   1m    v1.30.0
```

✅ **"Ready" 상태면 정상 실행!**

---

## 4. 간단한 Kubernetes 테스트
### 4.1 테스트용 Nginx 배포
```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
```

### 4.2 서비스 확인
```bash
kubectl get svc nginx
```

### 4.3 Minikube에서 서비스 URL 확인
```bash
minikube service nginx --url
```

🚀 **웹 브라우저에서 해당 URL로 접속하면 Nginx 페이지가 떠야 정상!**

---

## 5. Minikube 주요 명령어
| 명령어 | 설명 |
|--------|------|
| `minikube status` | Minikube 상태 확인 |
| `kubectl get nodes` | 노드 상태 확인 |
| `kubectl get pods` | 실행 중인 Pod 확인 |
| `kubectl get svc` | 서비스 확인 |
| `minikube dashboard` | 웹 대시보드 실행 |
| `minikube stop` | Minikube 중지 |
| `minikube delete` | Minikube 삭제 |

---

## 🎯 최종 정리
✅ Ubuntu 24.04 LTS + Minikube 설치  
✅ Kubernetes 싱글 노드 클러스터 실행  
✅ Nginx 배포 및 서비스 확인  
