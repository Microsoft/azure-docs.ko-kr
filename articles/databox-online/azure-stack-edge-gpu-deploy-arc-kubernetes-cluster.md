---
title: Azure Stack Edge GPU 장치에서 Kubernetes의 Azure Arc 사용 Microsoft Docs
description: Azure Stack Edge GPU 장치의 기존 Kubernetes 클러스터에서 Azure Arc를 사용 하도록 설정 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 5cd163b4c7514507d2a0563f1254c83dd22a3af2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268196"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에서 Kubernetes 클러스터에 Azure Arc 사용

이 문서에서는 Azure Stack Edge 장치의 기존 Kubernetes 클러스터에서 Azure Arc를 사용 하도록 설정 하는 방법을 보여 줍니다. 

이 절차는 [Azure Stack Edge 장치에서 Kubernetes 워크 로드](azure-stack-edge-gpu-kubernetes-workload-management.md) 를 검토 하 고 [Azure Arc Enabled Kubernetes (미리 보기)](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)의 개념에 대해 잘 알고 있는 사용자를 위한 것입니다.


## <a name="prerequisites"></a>필수 구성 요소

Kubernetes 클러스터에서 Azure Arc를 사용 하도록 설정 하기 전에 Azure Stack Edge 장치에서 장치에 액세스 하는 데 사용할 클라이언트에 대해 다음과 같은 필수 구성 요소를 완료 했는지 확인 합니다.

### <a name="for-device"></a>디바이스의 경우

1. 1 노드 Azure Stack Edge 장치에 대 한 로그인 자격 증명이 있습니다.
    1. 장치가 활성화 됩니다. [장치 활성화를](azure-stack-edge-gpu-deploy-activate.md)참조 하세요.
    1. 장치에 Azure Portal를 통해 구성 된 계산 역할이 있으며, Kubernetes 클러스터가 있습니다. [Compute 구성](azure-stack-edge-gpu-deploy-configure-compute.md)을 참조 하세요.

1. 구독에 대 한 소유자 액세스 권한이 있습니다. 서비스 사용자에 대 한 역할 할당 단계 중에이 액세스 권한이 필요 합니다.
 

### <a name="for-client-accessing-the-device"></a>장치에 액세스 하는 클라이언트

1. Azure Stack Edge 장치에 액세스 하는 데 사용 되는 Windows 클라이언트 시스템이 있습니다.
  
    - 클라이언트에서 Windows PowerShell 5.0 이상을 실행 하 고 있습니다. 최신 버전의 Windows PowerShell을 다운로드 하려면 [Windows Powershell 설치](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)로 이동 합니다.
    
    - [지원 되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 를 사용 하는 다른 클라이언트도 있을 수 있습니다. 이 문서에서는 Windows 클라이언트를 사용 하는 절차에 대해 설명 합니다. 
    
1. [Azure Stack Edge 장치에서 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)에 설명 된 절차를 완료 했습니다. 수행한 작업은 다음과 같습니다.
    
    - `kubectl`클라이언트에 설치 됨  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - `kubectl`클라이언트 버전이 Azure Stack Edge 장치에서 실행 되는 Kubernetes 마스터 버전에서 둘 이상의 버전을 사용 하지 않는지 확인 합니다. 
      - `kubectl version`클라이언트에서 실행 되는 kubectl의 버전을 확인 하는 데 사용 합니다. 전체 버전을 기록해 둡니다.
      - Azure Stack Edge 장치의 로컬 UI에서 **소프트웨어 업데이트** 로 이동 하 여 Kubernetes 서버 버전 번호를 확인 합니다. 
    
        ![Kubernetes 서버 버전 번호 확인](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - 이러한 두 버전이 호환 되는지 확인 합니다. 

<!-- az cli version requirements-->

## <a name="register-kubernetes-resource-providers"></a>Kubernetes 리소스 공급자 등록

Azure Arc를 통해 Kubernetes 클러스터를 구성 하기 전에 구독을 사용 하도록 설정 하 고 등록 해야 `Microsoft.Kubernetes` `Microsoft.KubernetesConfiguration` 합니다. 

1. 리소스 공급자를 사용 하도록 설정 하려면 Azure Portal에서 배포에 사용할 구독으로 이동 합니다. **리소스 공급자**로 이동 합니다. 
1. 오른쪽 창에서 추가 하려는 공급자를 검색 합니다. 이 예제에서는 `Microsoft.Kubernetes` 및 `Microsoft.KubernetesConfiguration` 입니다.

    ![Kubernetes 리소스 공급자 등록](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. 리소스 공급자를 선택 하 고 명령 모음 맨 위에서 **등록**을 선택 합니다. 등록은 몇 분 정도 걸립니다. 

    ![Kubernetes 리소스 공급자 등록](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. 리소스 공급자가 등록 되어 있는지 확인 하기 전까지 UI를 새로 고칩니다. 두 리소스 공급자에 대해이 프로세스를 반복 합니다.
    
    ![Kubernetes 리소스 공급자 등록](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

를 통해 리소스 공급자를 등록할 수도 있습니다 `az cli` . 자세한 내용은 [Azure Arc 활성화 된 Kubernetes에 대 한 두 공급자 등록](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes) 을 참조 하세요.

## <a name="create-service-principal-assign-role"></a>서비스 주체 만들기, 역할 할당

1. `Subscription ID`및 Azure Stack Edge 서비스에 대 한 리소스 배포에 사용한 리소스 그룹의 이름이 있는지 확인 합니다. 구독 ID를 가져오려면 Azure Portal에서 Azure Stack Edge 리소스로 이동 합니다. **개요 > Essentials**로 이동 합니다.

    ![구독 ID 가져오기](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    리소스 그룹 이름을 가져오려면 **속성**으로 이동 합니다.

    ![리소스 그룹 이름 가져오기](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. 서비스 주체를 만들려면를 통해 다음 명령을 사용 `az cli` 합니다.

    `az as sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    에 로그인 하는 방법에 대 한 자세한 내용은 `az cli` [에서 Cloud Shell를 시작 Azure Portal](../cloud-shell/quickstart-powershell.md?view=azure-cli-latest#start-cloud-shell)

    다음 예를 참조하세요. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. `appID` `name` `password` `tenantID` 다음 명령에서 입력으로 사용 하기 때문에,, 및를 기록해 둡니다.

1. 새 서비스 주체를 만든 후 `Kubernetes Cluster - Azure Arc Onboarding` 새로 만든 보안 주체에 역할을 할당 합니다. 이 역할은 제한 된 권한으로 기본 제공 Azure 역할 (명령에서 역할 ID 사용)입니다. 다음 명령을 사용합니다.

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    다음 예를 참조하세요.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    서비스 주체를 만들고 역할 할당을 수행 하는 방법에 대 한 자세한 내용은 [Azure Arc 사용 온 보 딩 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-arc/kubernetes/create-onboarding-service-principal)의 단계를 참조 하세요.


## <a name="enable-arc-on-kubernetes-cluster"></a>Kubernetes 클러스터에서 Arc 사용

Azure Arc 관리를 위해 Kubernetes 클러스터를 구성 하려면 다음 단계를 수행 합니다.

1. 장치의 [PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 합니다.

1. 유형:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`

    Azure Stack Edge 장치에 Azure Arc를 배포 하려면 [Azure arc에 대해 지원](../azure-arc/kubernetes/overview.md#supported-regions)되는 지역을 사용 하 고 있는지 확인 합니다. Azure Arc는 현재 미리 보기 상태입니다. 
    
    예를 들면 다음과 같습니다.
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "WestEurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    Azure Portal에서 이전 명령에서 제공한 이름으로 리소스를 만들어야 합니다.

    ![Azure Arc 리소스로 이동](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Azure Arc가 성공적으로 사용 하도록 설정 되었는지 확인 하려면 PowerShell 인터페이스에서 다음 명령을 실행 합니다.

    `kubectl get deployments -n azure-arc`

    이 명령은 `azure-arc` Azure Arc에 해당 하는 네임 스페이스에 배포 된 모든 응용 프로그램을 찾습니다.

    다음은 네임 스페이스의 Kubernetes 클러스터에 배포 된 Azure Arc 에이전트를 보여 주는 샘플 출력입니다 `azure-arc` . 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    네임 스페이스의 Kubernetes 클러스터에서 실행 되는 pod 목록을 가져올 수도 있습니다 `azure-arc` . Pod는 Kubernetes 클러스터에서 실행 되는 응용 프로그램 컨테이너 또는 프로세스입니다. 

    다음 명령을 사용합니다.
    
    `kubectl get pods -n azure-arc`
    
    샘플 출력은 다음과 같습니다.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


위의 출력과 같이 Azure Arc enabled Kubernetes는 네임 스페이스에 배포 된 클러스터에서 실행 되는 몇 개의 에이전트 (운영자)로 구성 됩니다 `azure-arc` .

- `config-agent`: 클러스터에 적용된 원본 제어 구성 리소스에 대해 연결된 클러스터를 감시하고 호환성 상태를 업데이트합니다.
- `controller-manager`: 연산자의 연산자이며 Azure Arc 구성 요소 간의 상호 작용을 조정합니다.
- `metrics-agent`: 다른 Arc 에이전트의 메트릭을 수집하여 이러한 에이전트가 최적의 성능을 발휘하는지 확인합니다.
- `cluster-metadata-operator`: 클러스터 메타 데이터를 수집 합니다. 클러스터 버전, 노드 수 및 Azure Arc 에이전트 버전
- `resource-sync-agent`: 위에서 언급한 클러스터 메타데이터를 Azure에 동기화합니다.
- `clusteridentityoperator`: Azure Arc enabled Kubernetes는 현재 시스템 할당 id를 지원 합니다. clusteridentityoperator는 다른 에이전트가 Azure와 통신 하기 위해 사용 하는 MSI (관리 서비스 id) 인증서를 유지 관리 합니다.
- `flux-logs-agent`: 소스 제어 구성의 일부로 배포 된 flux 연산자에서 로그를 수집 합니다.
- `connect-agent`: Azure Arc 리소스에 대해 통신 합니다.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Kubernetes 클러스터에서 호 제거

Azure Arc 관리를 제거 하려면 다음 단계를 수행 합니다.

1. 1. 장치의 [PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 합니다.
2. 유형:

    `Remove-HcsKubernetesAzureArcAgent` 


## <a name="next-steps"></a>다음 단계

Azure Arc 배포를 실행 하는 방법을 이해 하려면 [Azure Stack Edge 장치에서 GitOps를 통해 Redis을 사용 하 여 상태 비저장 PHP 방명록 응용 프로그램 배포](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md) 를 참조 하세요.
