# ch 11.애플리케이션 배포를 위한 고급 설정


## 11.1 포드의 자원 사용량 제한
* 자원 사용률 높이기
* overcommit 방법
* ResourceQuota 와 LimitRange

### 11.1.1 컨테이너와 포드의 자원 사용량 제한 : Limits
* pod 의 cpu와 memory 제한
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-limit-pod
  labels:
    name: resource-limit-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    resources:
      limits:
        memory: "256Mi" # 256Mi로 제한
        cpu: "1000m"    # 1개 라는 뜻
```
### 11.1.2 Requests
* limits 은 해당 pod 의 최대로 사용할수 있는 상한선
* Request 는 적어도 이 만큼의 자원은 컨테이너에게 보장 (최소한)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-limit-with-request-pod
  labels:
    name: resource-limit-with-request-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    resources:
      limits: # 최대 사용선
        memory: "256Mi"
        cpu: "1000m"
      requests: # 최소
        memory: "128Mi"   
        cpu: "500m"
```
* memory 1G node (서버) 에 request 500MB, limit 750MB pod 두개 일때,
  * 한 pod 이 500 초과 사용하려고 하면 OOM 발생 ->
  * pod의 node 할당 기준은 requests (limits 이 아님)

### 11.1.3 Cpu 자원 사용량의 제한 원리
* cpu를 m(밀리코어) 단위로 제한, `1개 cpu = 1000m`
* cpu 자원설정은 docker run 의 --cpu-shares 옵션과 동일
  * 자원이 남으면 limits 만큼 다 사용하고, 모자르면 비율만큼 사용

* cpu 는 압축 가능한 리소스 (cpu throtlle 을 통해 억제), memory와 storage는 incompressive resource (우선순위 낮은 프로세스가 종료)

### 11.1.4 Qos (Quality Of Service) 클래스와 메모리 자원 사용량 제한 원리
* memory 경합 시 우선순위가 낮은 포드 종료
  * eviction: k8s에서 강제 종료된 포드가 다른 노드로 옮겨감
* k describe node 를 하면 확인 가능. MeoryPressure가 평소에는 false이고 100Mi 이하면 true 로 바뀜
  * true 일때는 더이상 pod 할당 안함
* 강제 종료 선정
  * 1. oom_score_adj
  * 2. oom_score
  * docker deamon은 기본적으로 -999 로 강제 종료 안됨
* qos class 종류
  * https://box0830.tistory.com/293
  * Guaranteed class (requests = limits, oom_score_adj:-998)
    * 기본적으로 requests만 적으면 limits와 동일
  * BestEffort class (No requests, limits)
    * requests와 limits 설정 안함 (resources 항목 사용 안함)
      * limits 없으므로 제한 없이 모든 자원 사용 가능 (반대로도 가능)
  * Burstable class (requests < limits)
    * requests 보다 많이 사용하는 pod 가 우선순위 낮게 설정됨
* 재시작 정책
  * Guaranteed > Burstable > BestEffort
    * 다만, Burstable BestEffort는 현재 메모리를 얼마나 사용하느냐에 따라 다름 (많이 사용할수록 우선순위 낮음)
* 자원 오버커밋이 k8s에 있다고 무조건 사용하진 말자

### 11.1.5 ResourceQuota 와 LimitRange
* ResourceQuota 와 LimitRange object 를 이용해 자원사용량 관리
  * namespace에 할당할 자원 (cpu, memory, PVC 크기) 총합 제한
  * namespace에 생성할 리소스 (Service, deployment) 갯수 제한
* 이유
  * 클러스터 자원 고갈 막기

#### 11.1.5.1 ResourceQuota 로 자원 사용량 제한
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: resource-quota-example
  namespace: default
spec: # requests, limits 함께 제한 안해도 되고, cpu, memory를 따로 적어도 됨
  hard:
    requests.cpu: "1000m"
    requests.memory: "500Mi"
    limits.cpu: "1500m"
    limits.memory: "1000Mi"
    count/pods: 3
    count/services: 5
```
* ResourceQuota 이전에 생성된 pod가 이미 넘치게 사용해도 기존 pod 종료 안함

* 추가 정보: deployment -> replicaset -> pod
  * limits에 의해 pod이 생성 안된 로그는 replicaset에 남음

* `kubectl api-resources` 명령어로 object 가 어느 api 그룹에 속하는지 확인

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: besteffort-quota
  namespace: default
spec:
  hard:
    count/pods: 1
  scopes:
    - BestEffort
```
* 위처럼 하면 BestEffort pod 한개만 생성 가능
* 명시적으로 BestEffort 갯수 지정안하고 memory와 cpu를 제한해두면 BestEffort pod 생성 못함
#### 11.1.5.2 LimitRange
* 특정 namespace에 할당되는 자원의 범위나 기본값을 지정
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:                # 1. 자동으로 설정될 기본 Limit 값
      memory: 256Mi
      cpu: 200m
    defaultRequest:        # 2. 자동으로 설정될 기본 Request 값
      memory: 128Mi
      cpu: 100m
    max:                   # 3. 자원 할당량의 최대값 (Limits최대값)
      memory: 1Gi
      cpu: 1000m
    min:                   # 4. 자원 할당량의 최소값 (Requests최소값)
      memory: 16Mi
      cpu: 50m
    type: Container        # 5. 각 컨테이너에 대해서 적용 (pod, pvc 입력 가능)
```
* min, max 범위 안 맞는 pod 생성은 실패함

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limitrange-ratio
spec:
  limits:
  - maxLimitRequestRatio:
      memory: 1.5 # Limits, Requests의 비율이 1.5보다 작아야 함
      cpu: 1      # Limits, Requests 같아야 함 (Guaranteed class 강요 가능)
    type: Container
```

### 11.1.6 ResourceQuota, LimitRange의 원리 : Admission Controller
https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/
* Admission Controller: 사용자 api 요청 적절한지 검증하고 필요에 따라 api 요청을 변형하는 단계
  * serviceAccounts, ResourceQuota, LimitRange 등
  * 역할
    * 검증(Validating): api 요청 검사
    * 변형(mutating): api 요청을 적절히 수정
  * 이용: 개발자 실수 수정, sidecar container 자동 주입 (Istio)

## 11.2 쿠버네티스 스케쥴링
* container나 가상머신 같은 instance 생성 시 어느 노드에 생성할지 결정

### 11.2.1 포드가 실제로 노드에 생성되기까지의 과정
* 스케쥴링 수행
  * 사용자 인증 및 인가 -> adminssion Controller가 pod 요청을 적절히 변형 및 검증 -> worker node 중 하나에 생성
    * 3번째 단계에서 스케쥴링
* k8s 구성: kube-apiserver / kube-scheduler / etcd: distribute coordinator (ex. zookeeper)
  * api로 etcd에 nodeName없는 pod 데이터 저장 생성 -> 스케쥴러가 api 서버의 watch를 통해 정보 전달받음
    -> scheduler가 적절한 노드 선택해서 api 에 바인딩 요청

### 11.2.2 포드가 생성될 노드를 선택하는 스케쥴링 과정
 * node filtering : pod 할당 node와 그렇지 않은 node 분리 (pod 자원, node 상태 고려)
 * node scoring : score 계산 (Image Locality, Least Requested)
### 11.2.3 NodeSelector와 Node Affinity, Pod Affinity
 * nodeName과 NodeSelector
  * pod 생성 시 nodeName 지정하거나 NodeSelector 사용 (label 사용)
    * node label: os, 고 spec, linux 버전 등
  * pod 각각에게 적용됨
 * node Affinity 를 이용한 스케쥴링
  * requiredDuringSchedulingIgnoreDuringExecution : 반드시
  * preferredDuringSchedulingIgnoreDuringExecution : 선호 (weight 를 통해 스코어링에 적용)
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-nodeaffinity-required
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: mylabel/disk         
            operator: In          # values의 값 중 하나만 만족하면 됩니다.
            values:
            - ssd
            - hdd
  containers:
  - name: nginx
    image: nginx:latest
```
 * pod Affinity를 이용한 스케쥴링방법 (반대는 pod Anti-affinity)
  * 특정 조건을 만족하는 포드와 함께 실행하도록 스케쥴링
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-podaffinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: mylabel/database
            operator: In
            values:
            - mysql
        topologyKey: failure-domain.beta.kubernetes.io/zone # 해당 라벨을 가지는 토폴로지 범위의 노드를 선택
  containers:
  - name: nginx
    image: nginx:latest
```

### 11.2.4 Taints 와 Tolerations 사용하기
 * Taints(얼룩) 을 node에 지정해서 해당 node에 할당 막기
  * taints 효과: NoSchedule, NoExecute, PreferNoSchedule
  * 예제: master node에는 pod 이 실행 못하도록 taints 됨
 * Tolerations(용인)을 pod 에 설정하면 Tains 설정된 node에 할당 가능

### 11.2.5 Cordon, Drain 및 PodDistributionBudget
 * `kubectl cordon <nodeName>` 해당 노드에 스케쥴링 막기 (해지는 uncordon)
 * `kubectl drain <nodeName>` 실행중이던 포드는 다른 노드로 옮겨지도록 Eviction 수행
  * daemonset 이 있으면 옵션을 추가해서 명령어 날리기, 단일 pod으로 생성되었으면 직접 제거 (혹은 force옵션)해야 함
  * PodDistributionBudget: drain 명령어 등으로 eviction 발생 시 특정 개수나 비율의 pod은 정상 유지

### 11.2.6 커스텀 스케쥴러 및 스케쥴러 확장


ref : https://blog.naver.com/PostView.naver?blogId=alice_k106&logNo=221511412970&parentCategoryNo=&categoryNo=20&viewDate=&isShowPopularPosts=false&from=postView

## ch 11.3 쿠버네티스 애플리케이션 상태와 배포
  * spinnaker, helm, kustomize 또는 argoCd, jenkins CD 사용

### 11.3.1 디플로이먼트를 통해 롤링 업데이트
 * 배포에 --recode 옵션으로 리비전 남기기
 * maxUnavailable : rolling update 중 사용 불가능한 pod 개수
 * maxSurge: deployment의 replicas 갯수보다 얼마나 더 많이 존재 가능한지
### 11.3.2 포드의 생애주기 (Lifecycle)
 * 상태
   * pending: 생성 되지 않은 상태
   * running: 실행 중 (Desired)
   * completed: 정상 실행 종료
   * Error: 정상실행하지 않은 상태로 종료
   * Terminating: 삭제 상태 머뭄
 * application 상태 검사
  * livenessProve: 살아있는지 검사, restartPolicy에 의해 재시작
  * readinessProve: 사용자 요청을 처리할 준비가 됬는지, 실패 시 service routing 대상에서 제외 (초기화 끝났는지)
### 11.3.3 HPA (Horizontal Pod Autoscaler)를 활용한 오토스케쥴링
 * 리소스 사용량에 따라 pod 개수 조절
