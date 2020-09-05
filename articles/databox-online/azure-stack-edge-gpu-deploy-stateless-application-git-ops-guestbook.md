---
title: Azure Stack Edge GPU 장치에서 PHP 방명록 앱을 Arc 사용 Kubernetes에 배포 | Microsoft Docs
description: Azure Stack Edge 장치의 Arc enabled Kubernetes 클러스터에서 GitOps를 사용 하 여 Redis를 사용 하 여 PHP 방명록 상태 비저장 응용 프로그램을 배포 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 83ac012c861a0d066bdc47d8e15cbe7ac398aa23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254222"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-gpu"></a>Azure Stack Edge GPU에서 Redis on Arc enabled Kubernetes cluster를 사용 하 여 PHP 방명록 상태 비저장 응용 프로그램 배포

이 문서에서는 Kubernetes 및 Azure Arc를 사용 하 여 간단한 다중 계층 웹 응용 프로그램을 빌드하고 배포 하는 방법을 보여 줍니다. 이 예제는 다음과 같은 구성 요소로 구성 됩니다.

- 방명록 항목을 저장할 단일 인스턴스 Redis 마스터
- 읽기를 제공 하기 위해 여러 복제 된 Redis 인스턴스
- 여러 웹 프런트 엔드 인스턴스

배포는 Azure Stack Edge 장치의 Arc enabled Kubernetes 클러스터에서 GitOps를 사용 하 여 수행 됩니다. 

이 절차는 [Azure Stack Edge 장치에서 Kubernetes 작업](azure-stack-edge-gpu-kubernetes-workload-management.md) 을 검토 하 고 [Azure Arc Enabled Kubernetes (미리 보기)](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)의 개념에 대해 잘 알고 있는 사용자를 위한 것입니다.


## <a name="prerequisites"></a>필수 구성 요소

상태 비저장 응용 프로그램을 배포 하기 전에 장치에서 장치에 액세스 하는 데 사용할 클라이언트 및 장치에 대 한 다음 필수 구성 요소를 완료 했는지 확인 합니다.

### <a name="for-device"></a>디바이스의 경우

1. 1 노드 Azure Stack Edge 장치에 대 한 로그인 자격 증명이 있습니다.
    1. 장치가 활성화 됩니다. [장치 활성화를](azure-stack-edge-gpu-deploy-activate.md)참조 하세요.
    1. 장치에 Azure Portal를 통해 구성 된 계산 역할이 있으며, Kubernetes 클러스터가 있습니다. [Compute 구성](azure-stack-edge-gpu-deploy-configure-compute.md)을 참조 하세요.

1. 장치의 기존 Kubernetes 클러스터에서 Azure Arc를 사용 하도록 설정 하 고 Azure Portal에 해당 하는 Azure Arc 리소스가 있습니다. 자세한 단계는 [Azure Stack Edge 장치에서 Azure Arc 사용](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)을 참조 하세요.

### <a name="for-client-accessing-the-device"></a>장치에 액세스 하는 클라이언트

1. Azure Stack Edge 장치에 액세스 하는 데 사용 되는 Windows 클라이언트 시스템이 있습니다.
  
    - 클라이언트에서 Windows PowerShell 5.0 이상을 실행 하 고 있습니다. 최신 버전의 Windows PowerShell을 다운로드 하려면 [Windows Powershell 설치](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)로 이동 합니다.
    
    - [지원 되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 를 사용 하는 다른 클라이언트도 있을 수 있습니다. 이 문서에서는 Windows 클라이언트를 사용 하는 절차에 대해 설명 합니다. 
    
1. [Azure Stack Edge 장치에서 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)에 설명 된 절차를 완료 했습니다. 수행한 작업은 다음과 같습니다.
    
    - `kubectl`클라이언트에 설치 됨  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - `kubectl`클라이언트 버전이 Azure Stack Edge 장치에서 실행 되는 Kubernetes 마스터 버전에서 둘 이상의 버전을 사용 하지 않는지 확인 합니다. 
      - `kubectl version`클라이언트에서 실행 되는 kubectl의 버전을 확인 하는 데 사용 합니다. 전체 버전을 기록해 둡니다.
      - Azure Stack Edge 장치의 로컬 UI에서 **개요** 로 이동 하 여 Kubernetes software 번호를 확인 합니다. 
      - 지원 되는 Kubernetes 버전에서 제공 되는 매핑과의 호환성을 위해 이러한 두 버전을 확인 합니다. <!--insert link-->.

1. [Azure Arc 배포를 실행 하는 데 사용할 수 있는 Gitops 구성이](https://github.com/kagoyal/dbehaikudemo)있습니다. 다음 파일을 사용 `yaml` 하 여 Azure Stack에 지 장치에 배포 합니다.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>구성 배포

Azure Portal를 통해 GitOps 구성을 배포 하도록 Azure Arc 리소스를 구성 하려면 다음 단계를 수행 합니다. 

1. Azure Portal에서 장치의 Kubernetes 클러스터에서 Azure Arc를 사용 하도록 설정할 때 만든 Azure Arc 리소스로 이동 합니다. 

    ![Azure Arc 리소스로 이동](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. **구성** 으로 이동 하 여 **+ 구성 추가**를 선택 합니다.

    ![구성으로 이동](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. **구성 추가**에서 필드에 적절 한 값을 입력 하 고 **적용**을 선택 합니다.

    |매개 변수  |설명 |
    |---------|---------|
    |구성 이름     | 구성 리소스의 이름입니다.        |
    |Operator 인스턴스 이름     |특정 구성을 식별 하는 운영자의 인스턴스 이름입니다. Name은 소문자, 영숫자, 하이픈 및 마침표만 가능 해야 하는 최대 253 자의 문자열입니다.         |
    |Operator 네임 스페이스     | 이는 배포에 지정 된 네임 스페이스와 일치 하므로 **demotestguestbook** 으로 설정 `yaml` 합니다. <br> 필드는 운영자가 설치 되는 네임 스페이스를 정의 합니다. Name은 소문자, 영숫자, 하이픈 및 마침표만 가능 해야 하는 최대 253 자의 문자열입니다.         |
    |리포지토리 URL     |<br>`http://github.com/username/repo` `git://github.com/username/repo` GitOps 구성이 있는 또는 형식의 git 리포지토리에 대 한 경로입니다.         |
    |연산자 범위     | **네임 스페이스**를 선택 합니다. <br>이는 운영자가 설치 되는 범위를 정의 합니다. 네임 스페이스로이를 선택 합니다. 운영자는 배포 yaml 파일에 지정 된 네임 스페이스에 설치 됩니다.       |
    |연산자 유형     | 기본값을 그대로 둡니다. <br>이는 기본적으로 flux로 설정 되는 연산자의 유형을 지정 합니다.        |
    |연산자 params     | 이 필드를 비워 둡니다. <br>이 필드에는 flux 연산자에 전달할 매개 변수가 포함 되어 있습니다.        |
    |Helm     | **사용 안 함**으로 설정 합니다. <br>차트 기반 배포를 수행 하는 경우이 옵션을 사용 하도록 설정 합니다.        |


    ![구성 추가](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. 구성 배포가 시작 되 고 **운영자 상태가** **보류 중**으로 표시 됩니다. 

    ![구성으로 이동](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. 배포에는 몇 분이 걸립니다. 배포가 완료 되 면 **운영자 상태** 는 **설치**됨으로 표시 됩니다.

    ![구성으로 이동](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)


## <a name="verify-deployment"></a>배포 확인

GitOps 구성을 통한 배포는 `demotestguestbook` git 리포지토리에 있는 배포 파일에 지정 된 대로 네임 스페이스를 만듭니다 `yaml` .

1. GitOps 구성을 적용 한 후 [장치의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.
1. 다음 명령을 실행 하 여 `demotestguestbook` 배포에 해당 하는 네임 스페이스에서 실행 중인 pod를 나열 합니다.

    `kubectl get pods -n <your-namespace>`
  
    샘플 출력은 다음과 같습니다.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. 이 예제에서 프런트 엔드 서비스는 형식 LoadBalancer로 배포 되었습니다. 이 서비스의 IP 주소를 확인 하 여 방명록을 확인 해야 합니다. 다음 명령을 실행합니다.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. 의 프런트 엔드 서비스에는 `type:LoadBalancer` 외부 IP 주소가 있습니다. 이 IP는 장치에서 계산 네트워크 설정을 구성할 때 외부 서비스에 대해 지정한 IP 주소 범위에서 가져온 것입니다. 이 IP 주소를 사용 하 여 URL에서 방명록을 볼 수 `https://<external-IP-address>` 있습니다.

    ![방명록 보기](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>배포 삭제

배포를 삭제 하려면 Azure Portal에서 구성을 삭제할 수 있습니다. 그러면 배포 및 서비스를 포함 하 여 만든 개체가 삭제 됩니다.

1. Azure Portal에서 Azure Arc 리소스 > 구성으로 이동 합니다. 
1. 삭제 하려는 구성을 찾습니다. ...를 선택 합니다. 상황에 맞는 메뉴를 호출 하 고 **삭제**를 선택 합니다.
    ![구성 삭제](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

구성을 삭제 하는 데 몇 분 정도 걸릴 수 있습니다.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>다음 단계

[Kubernetes 대시보드를 사용 하 여 Azure Stack Edge 장치에서 배포를 모니터링](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) 하는 방법을 알아봅니다.
