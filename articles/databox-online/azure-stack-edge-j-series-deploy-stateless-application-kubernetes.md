---
title: Kubectl를 사용 하 여 Azure Stack Edge GPU 장치에 Kubernetes 상태 비저장 응용 프로그램 배포 | Microsoft Docs
description: Microsoft Azure Stack Edge 장치에서 kubectl를 사용 하 여 Kubernetes 상태 비저장 응용 프로그램 배포를 만들고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 27502c58481444a9dc14120bf447d4614d051ccc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268862"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에서 kubectl를 통해 Kubernetes 상태 비저장 응용 프로그램 배포

이 문서에서는 기존 Kubernetes 클러스터에서 kubectl 명령을 사용 하 여 상태 비저장 응용 프로그램을 배포 하는 방법을 설명 합니다. 또한이 문서에서는 상태 비저장 응용 프로그램에서 pod을 만들고 설정 하는 과정을 안내 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Kubernetes 클러스터를 만들고 명령줄 도구를 사용 하기 전에 `kubectl` 다음을 확인 해야 합니다.

- 1 노드 Azure Stack Edge 장치에 대 한 로그인 자격 증명이 있습니다.

- Windows PowerShell 5.0 이상이 Windows 클라이언트 시스템에 설치 되어 Azure Stack Edge 장치에 액세스 합니다. 지원 되는 운영 체제를 사용 하는 다른 클라이언트도 있을 수 있습니다. 이 문서에서는 Windows 클라이언트를 사용 하는 절차에 대해 설명 합니다. 최신 버전의 Windows PowerShell을 다운로드 하려면 [Windows Powershell 설치](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)로 이동 합니다.

- Compute는 Azure Stack Edge 장치에서 사용 하도록 설정 됩니다. 계산을 사용 하도록 설정 하려면 장치의 로컬 UI에서 **계산** 페이지로 이동 합니다. 그런 다음 계산에 사용할 네트워크 인터페이스를 선택 합니다. **사용**을 선택합니다. 계산을 사용 하도록 설정 하면 장치에서 해당 네트워크 인터페이스의 가상 스위치가 생성 됩니다. 자세한 내용은 [Azure Stack Edge에서 compute 네트워크 사용](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)을 참조 하세요.

- Azure Stack Edge 장치에는 버전 v 1.9 이상 버전을 실행 하는 Kubernetes 클러스터 서버가 있습니다. 자세한 내용은 [Microsoft Azure Stack Edge 장치에서 Kubernetes 클러스터 만들기 및 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조 하세요.

- 을 (를) 설치 했습니다 `kubectl` .

## <a name="deploy-a-stateless-application"></a>상태 비저장 응용 프로그램 배포

시작 하기 전에 다음이 있어야 합니다.

1. Kubernetes 클러스터를 만들었습니다.
2. 네임 스페이스를 설정 합니다.
3. 네임 스페이스와 사용자를 연결 합니다.
4. 사용자 구성을에 저장 `C:\Users\<username>\.kube` 했습니다.
5. 설치 됨 `kubectl` .

이제 Azure Stack Edge 장치에서 상태 비저장 응용 프로그램 배포의 실행 및 관리를 시작할 수 있습니다. 사용을 시작 하기 전에 `kubectl` 올바른 버전의가 있는지 확인 해야 `kubectl` 합니다.

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Kubectl의 올바른 버전이 있는지 확인 하 고 구성을 설정 합니다.

버전을 확인 하려면 `kubectl` 다음을 수행 합니다.

1. 의 버전이 `kubectl` 1.9 보다 크거나 같은지 확인 합니다.

   ```powershell
   kubectl version
   ```
    
   다음은 출력의 예입니다.
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   이 경우 kubectl의 클라이언트 버전은 v 1.15.2 이며 계속 진행 하기 위해 호환 됩니다.

2. Kubernetes 클러스터에서 실행 중인 pod의 목록을 가져옵니다. Pod는 Kubernetes 클러스터에서 실행 되는 응용 프로그램 컨테이너 또는 프로세스입니다.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   다음은 명령 사용의 예입니다.
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   출력에는 클러스터에서 실행 중인 응용 프로그램이 없기 때문에 리소스 (pod)가 없다는 것을 명시 해야 합니다.

   이 명령은 "C:\Users username. kube의 디렉터리 구조를 \\ &lt; &gt; \\ \" 구성 파일에 채웁니다. Kubectl 명령줄 도구는 이러한 파일을 사용 하 여 Kubernetes 클러스터에서 상태 비저장 응용 프로그램을 만들고 관리 합니다.

3. "C:\Users username. kube의 디렉터리 구조를 수동으로 확인 \\ &lt; &gt; \\ \" 하 여 *kubectl* 가 다음 하위 폴더로 채워졌는지 확인 합니다.

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> 모든 kubectl 명령 목록을 보려면을 입력 `kubectl --help` 합니다.

### <a name="create-a-stateless-application-using-a-deployment"></a>배포를 사용 하 여 상태 비저장 응용 프로그램 만들기

Kubectl 명령줄 버전이 올바르고 필요한 구성 파일이 있는지 확인 했으므로 상태 비저장 응용 프로그램 배포를 만들 수 있습니다.

Pod는 만들거나 배포 하는 Kubernetes 개체 모델에서 가장 작은 가장 간단한 단위인 Kubernetes 응용 프로그램의 기본 실행 단위입니다. Pod는 저장소 리소스, 고유한 네트워크 IP 및 컨테이너가 실행 되는 방법을 제어 하는 옵션을 캡슐화 합니다.

사용자가 만드는 상태 비저장 응용 프로그램의 형식은 nginx 웹 서버 배포입니다.

상태 비저장 응용 프로그램 배포를 만들고 관리 하는 데 사용 하는 모든 kubectl 명령은 구성과 연결 된 네임 스페이스를 지정 해야 합니다. 을 사용 하 여 [Microsoft Azure Stack edge 장치에서 Kubernetes 클러스터 만들기 및 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md) 자습서의 Azure Stack Edge 장치에서 클러스터에 연결 된 상태에서 네임 스페이스를 만들었습니다 `New-HcsKubernetesNamespace` .

Kubectl 명령에서 네임 스페이스를 지정 하려면를 사용 `kubectl <command> -n <namespace-string>` 합니다.

Nginx 배포를 만들려면 다음 단계를 따르세요.

1. Kubernetes 배포 개체를 만들어 상태 비저장 응용 프로그램을 적용 합니다.

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   이 예제에서 응용 프로그램 YAML 파일의 경로는 외부 원본입니다.

   다음은 명령 및 출력을 사용 하는 샘플입니다.

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   또는 로컬 컴퓨터에 다음 markdown를 저장 하 고 *-f* 매개 변수에서 경로와 파일 이름을 대체할 수 있습니다. 예를 들어 "C:\Kubernetes\deployment.yaml"입니다. 응용 프로그램 배포에 대 한 구성은 다음과 같습니다.

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   이 명령은 응용 프로그램을 실행 하기 위한 두 가지 pod를 포함 하는 기본 nginx 배포를 만듭니다.

2. 만든 Kubernetes nginx 배포에 대 한 설명을 가져옵니다.

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   다음은 명령 및 출력의 샘플 사용법입니다.
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   *복제본* 설정을 자세히 살펴보면 다음과 같이 표시 됩니다.
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   *복제본* 설정은 배포 사양이 두 개의 pod를 필요로 함을 나타냅니다. 이러한 pod는 생성 및 업데이트 되 고 사용할 수 있습니다.

   > [!NOTE]
   > 복제본 세트는 장치 노드 실패 또는 장치 업그레이드 중단 등의 이유로 삭제 되거나 종료 된 pod을 대체 합니다. 따라서 응용 프로그램에 단일 pod만 필요한 경우에도 복제본 집합을 사용 하는 것이 좋습니다.

3. 배포에 pod를 나열 하려면:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   다음은 명령 및 출력의 샘플 사용법입니다.
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   출력은 kubectl를 사용 하 여 참조할 수 있는 고유한 이름을 가진 두 개의 pod가 있는지 확인 합니다.

4. 배포의 개별 pod에 대 한 정보를 보려면 다음을 수행 합니다.

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

   다음은 명령 및 출력의 샘플 사용법입니다.

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>복제본 수를 늘려서 응용 프로그램 배포 크기를 조정 합니다.

각 pod는 지정 된 응용 프로그램의 단일 인스턴스를 실행 하기 위한 것입니다. 여러 인스턴스를 실행 하도록 응용 프로그램을 수평으로 확장 하려는 경우 각 인스턴스에 대해 하나씩 pod 수를 늘릴 수 있습니다. Kubernetes에서는이를 복제 라고 합니다.
새 YAML 파일을 적용 하 여 응용 프로그램 배포의 pod 수를 늘릴 수 있습니다. YAML 파일은 복제본 설정을 4로 변경 하 여 배포의 pod 수를 4 개의 pod로 늘립니다. Pod 수를 2에서 4로 늘리려면:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

또는 로컬 컴퓨터에 다음 markdown를 저장 하 고에 대 한 *-f* 매개 변수의 경로와 파일 이름을 대체할 수 있습니다 `kubectl apply` . 예를 들어 "C:\Kubernetes\deployment-scale.yaml"입니다. 응용 프로그램 배포 규모에 대 한 구성은 다음과 같습니다.

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

배포에 4 개의 pod 있는지 확인 하려면 다음을 수행 합니다.

```powershell
kubectl get pods -l app=nginx
```

다음은 크기 조정 배포에 대 한 예제 출력을 두 개에서 4 pod로 출력 합니다.

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

출력에서 볼 수 있듯이 이제 응용 프로그램을 실행할 수 있는 4 개의 pod 배포에 있습니다.

### <a name="delete-a-deployment"></a>배포 삭제

모든 pod를 포함 하 여 배포를 삭제 하려면 `kubectl delete deployment` 배포 *nginx* 이름 및 네임 스페이스 이름을 지정 하 여를 실행 해야 합니다. 배포를 삭제 하려면:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

명령 사용법 및 출력의 예는 다음과 같습니다.

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>다음 단계

[Kubernetes 개요](azure-stack-edge-gpu-kubernetes-overview.md)
