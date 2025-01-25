# Kubernetes 설치 가이드

## 1. Swap 비활성화

```bash
sudo swapoff -a 
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab 
```

---

## 2. Containerd 설치

### Docker Repository 사용

```bash
# 시스템 업데이트 및 필수 패키지 설치
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

# Docker GPG 키 설정
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

# Containerd 설치
sudo apt update
sudo apt install -y containerd.io

# Containerd 설정
cat <<EOF | sudo tee -a /etc/containerd/config.toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
SystemdCgroup = true
EOF

# 비활성화된 플러그인 설정 주석 처리
sudo sed -i 's/^disabled_plugins \=/\#disabled_plugins \=/g' /etc/containerd/config.toml

# Containerd 재시작
sudo systemctl restart containerd

# 소켓 확인
ls /var/run/containerd/containerd.sock
```

---

## 3. Kubernetes 설치 스크립트 작성 및 실행

### 설치 스크립트 생성

```bash
cat <<EOF > kube_install.sh
# /etc/apt/keyrings 폴더 생성 및 권한 부여
sudo mkdir -p -m 755 /etc/apt/keyrings

# 1. apt 패키지 색인 업데이트 및 필수 패키지 설치
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# 2. Google Cloud 공개 서명 키 다운로드
sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

# 3. Kubernetes apt 리포지터리 추가
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# 4. apt 패키지 색인 업데이트 및 kubelet, kubeadm, kubectl 설치
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
EOF
```

### 스크립트 실행

```bash
sudo bash kube_install.sh 

# 설치된 kubeadm 버전 확인
kubeadm version
```
## 4. 넷필터 설치
- 도커 설치하면 자동으로 되는데 컨테이너-D 설치라 직접 해야함
```
sudo -i
modprobe br_netfilter
echo 1 > /proc/sys/net/ipv4/ip_forward
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
exit

```

## 5. 마스터 노드
```
sudo kubeadm init

# 에러 발생시
sudo rm /etc/containerd/config.toml // disabled_plugins = ["cri"]라는 줄 삭제
sudo systemctl restart containerd
sudo kubeadm init

# 워커노드 JOIN 발생시 동일
sudo rm /etc/containerd/config.toml
sudo systemctl restart containerd

```


## 6. 워커 노드
