### **kubeadm, kubelet 및 kubectl 설치**

모든 머신에 다음 패키지들을 설치한다.

- `kubeadm`: 클러스터를 부트스트랩하는 명령이다. 클러스터를 초기화하고 관리하는 기능을 갖는다.
- `kubelet`: 클러스터의 모든 머신에서 실행되는 파드와 컨테이너 시작과 같은 작업을 수행하는 컴포넌트이다. 데몬으로 동작하며 컨테이너를 관리한다.
- `kubectl`: 클러스터와 통신하기 위한 커맨드 라인 유틸리티이다. 클라이언트 전용 프로그램이다

---

### 스크립트 파드 삭제 , 생성 간략 예시

```
kubectl create deploy tc --image=consol/tomcat-7.0 --replicas=5
kubectl expose deploy tc --type=NodePort --port=80 --target-port=8080 // 구글 쿠버네티스랑 다름 , 아래 1번 사진으로 통신


kubectl get deployments // 
kubectl delete deployment tc 

kubectl delete pod -l app=tc // tc 파드 삭제
kubectl get pod,svc // 서비스 확인
```

![image](https://github.com/user-attachments/assets/888b37c1-0d9c-4bb5-8049-0f9e619e6270)


--- 
### 파드 생성 스크립트
1. yaml 작성
```
apiVersion: v1
kind: Pod
metadata:
  name: http-go
spec:
  containers:
  - name: http-go
    image: gasbugs/http-go
    ports:
    - containerPort: 8080
```
2. 파드 생성 , 상세 조회 , 포트포워딩
```
kubectl create -f go-http-pod.yaml

kubectl exec <container> -- curl 127.0.0.1:8080 // 성공확인
kubectl get pod http-go -o yaml // 파드 상세 조회
kubectl port-forward http-go 8080 // 포트포워딩
kubectl delete -f go-http-pod.yaml // 파드 삭제
kubectl logs http-go // 파드 로그 조회
kubectl annotate pod http-go // 주석 넣기


```

3. 레이블 조회 , 추가
```
kubectl get pod --show-label // 전체 레이블

kubectl get pod -L en // 부분 레이블
kubectl get pod -L creation_method

kubectl label pod http-go test=foo // 레이블 추가
kubectl label pod http-go test- // test 레이블 삭제



```
  
