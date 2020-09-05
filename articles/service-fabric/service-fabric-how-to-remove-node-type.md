---
title: Azure Service Fabric에서 노드 유형 제거 | Microsoft Docs
description: Azure에서 실행 중인 Service Fabric 클러스터에서 노드 유형을 제거하는 방법을 알아봅니다.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: chrpap
ms.openlocfilehash: ede999bee9ce1a4a9dd10652a2c52a840d5b24be
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163580"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Service Fabric 노드 유형을 제거 하는 방법
이 문서에서는 클러스터에서 기존 노드 유형을 제거하여 Azure Service Fabric 클러스터의 크기를 조정하는 방법을 설명합니다. Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 머신 또는 VM을 노드라고 합니다. 가상 머신 확장 집합은 가상 머신의 모음을 집합으로 배포하고 관리하는 데 사용할 수 있는 Azure 컴퓨팅 리소스입니다. Azure 클러스터에 정의된 모든 노드 유형은 [별도의 확장 집합으로 설정](service-fabric-cluster-nodetypes.md)됩니다. 각 노드 형식을 별도로 관리할 수 있습니다. Service Fabric 클러스터를 만든 후에 노드 유형(가상 머신 확장 집합) 및 모든 노드를 제거하여 클러스터를 수평 확장할 수 있습니다.  클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다.  클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

> [!WARNING]
> 이 방법을 사용 하 여 프로덕션 클러스터에서 노드 형식을 제거 하는 것은 자주 사용 하지 않는 것이 좋습니다. 노드 유형 뒤의 가상 머신 확장 집합 리소스가 삭제되므로 위험한 명령입니다. 

## <a name="durability-characteristics"></a>내구성 특징
AzServiceFabricNodeType를 사용 하는 경우 안전은 속도 보다 우선 순위가 지정 됩니다. 다음과 같은 이유로 노드 유형은 실버 또는 골드 [내구성 수준](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)이어야 합니다.
- Bronze는 상태 정보 저장이 전혀 보장되지 않습니다.
- 실버 및 골드 내구성은 확장 집합에 대한 모든 변경 내용을 트래핑합니다.
- 골드의 경우 확장 집합 아래에 있는 Azure 업데이트를 제어할 수도 있습니다.

Service Fabric은 데이터가 손실되지 않도록 내부 변경 및 업데이트를 "오케스트레이션"합니다. 그러나 청동 내구성이 있는 노드 유형을 제거 하면 상태 정보가 손실 될 수 있습니다. 주 노드 유형을 제거 하 고 응용 프로그램이 상태 비저장 인 경우에는 브론즈를 사용할 수 있습니다. 프로덕션에서 상태 저장 워크로드를 실행하는 경우에는 최소 구성이 Silver여야 합니다. 마찬가지로 프로덕션 시나리오의 경우 주 노드 유형은 항상 Silver 또는 Gold여야 합니다.

### <a name="more-about-bronze-durability"></a>Bronze 내구성에 대한 자세한 정보

Bronze 노드 유형을 제거하면 노드 유형의 모든 노드가 즉시 다운됩니다. Service Fabric은 브론즈 노드 확장 집합 업데이트를 트래핑하지 않기 때문에 모든 VM이 즉시 다운됩니다. 해당 노드에 상태 저장 항목이 있으면 데이터가 손실됩니다. 이제 상태 비저장이더라도 Service Fabric의 모든 노드가 링에 참여하기 때문에 전체 환경이 손실되어 클러스터 자체가 불안정해질 수 있습니다.

## <a name="remove-a-node-type"></a>노드 형식 제거

1. 프로세스를 시작 하기 전에이 필수 구성 요소를 처리 하십시오.

    - 클러스터가 정상입니다.
    - 노드 형식이 제거 된 후에도 용량이 충분 합니다 (예:). 필요한 복제본 수를 저장할 노드 수입니다.

2. 배치 제약 조건이 있는 모든 서비스를 이동 하 여 노드 유형 밖의 노드 유형을 사용 합니다.

    - 응용 프로그램/서비스 매니페스트를 수정 하 여 더 이상 노드 유형을 참조 하지 않습니다.
    - 변경 내용을 배포 합니다.

    그런 후 다음을 확인 합니다.
    - 위에서 수정 된 모든 서비스는 노드 형식에 속하는 노드에서 더 이상 실행 되지 않습니다.
    - 모든 서비스가 정상 상태입니다.

3. 노드 형식을 비기본 (주 노드가 아닌 형식에 대해 건너뜀)로 표시 해제

    - 배포에 사용 되는 Azure Resource Manager 템플릿을 찾습니다.
    - Service Fabric 섹션에서 노드 형식과 관련 된 섹션을 찾습니다.
    - IsPrimary 속성을 false로 변경 합니다. * *이 작업에서 노드 형식과 관련 된 섹션을 제거 하지 마십시오.
    - 수정 된 Azure Resource Manager 템플릿을 배포 합니다. * * 클러스터 구성에 따라이 단계는 시간이 오래 걸릴 수 있습니다.
    
    그런 후 다음을 확인 합니다.
    - 포털의 Service Fabric 섹션에서는 클러스터가 준비 되었음을 나타냅니다.
    - 클러스터가 정상입니다.
    - 노드 형식에 속하는 모든 노드는 초기값 노드로 표시 되지 않습니다.

4. 노드 형식에서 각 노드를 사용 하지 않도록 설정 합니다.

    PowerShell을 사용 하 여 클러스터에 연결 하 고 다음 단계를 실행 합니다.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - 청동 내구성의 경우 모든 노드가 비활성 상태가 될 때까지 기다립니다.
    - 실버 및 골드 내구성의 경우 일부 노드가 사용 하지 않도록 설정 되 고 나머지는 비활성화 상태로 전환 됩니다. 상태를 사용 하지 않도록 설정 하는 노드의 세부 정보 탭을 확인 합니다. 모두 인프라 서비스 파티션에 대 한 쿼럼을 보장 하는 데 문제가 있으면 계속 하는 것이 안전 합니다.

5. 노드 형식에 대 한 데이터를 중지 합니다.

    PowerShell을 사용 하 여 클러스터에 연결 하 고 다음 단계를 실행 합니다.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    노드 형식에 대 한 모든 노드가 아래로 표시 될 때까지 기다립니다.

6. 원본 가상 머신 확장 집합에서 노드 할당 취소
    
    확장 집합이 배포 된 Azure 구독에 로그인 하 고 가상 머신 확장 집합을 제거 합니다. 

    ```powershell
    $scaleSetName="myscaleset"
    $scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"
    
    Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
    ```

    
7. 노드 형식에 대 한 데이터를 제거 합니다.

    PowerShell을 사용 하 여 클러스터에 연결 하 고 다음 단계를 실행 합니다.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    모든 노드가 클러스터에서 제거 될 때까지 기다립니다. 노드가 SFX에 표시 되어서는 안 됩니다.

8. Service Fabric 섹션에서 노드 유형을 제거 합니다.

    - 배포에 사용 되는 Azure Resource Manager 템플릿을 찾습니다.
    - Service Fabric 섹션에서 노드 형식과 관련 된 섹션을 찾습니다.
    - 노드 형식에 해당 하는 섹션을 제거 합니다.
    - 실버 이상 내구성이 있는 클러스터의 경우에만 템플릿에서 클러스터 리소스를 업데이트 하 고 `applicationDeltaHealthPolicies` 아래 지정 된 대로 클러스터 리소스 아래에를 추가 하 여 패브릭:/시스템 응용 프로그램 상태를 무시 하도록 상태 정책을 구성 `properties` 합니다. 아래 정책은 기존 오류는 무시 하지만 새 상태 오류는 허용 하지 않습니다. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - 수정 된 Azure Resource Manager 템플릿을 배포 합니다. * *이 단계는 시간이 오래 걸립니다 (일반적으로 최대 2 시간). 이 업그레이드는 설정이 InfrastructureService 변경 되므로 노드를 다시 시작 해야 합니다. 에서이 경우 `forceRestart` 는 무시 됩니다. 
    매개 변수는 `upgradeReplicaSetCheckTimeout` 아직 안전 상태가 아닌 경우 파티션이 안전한 상태가 될 때까지 Service Fabric 대기 하는 최대 시간을 지정 합니다. 안전 검사가 노드의 모든 파티션에 대해 통과 하면 Service Fabric는 해당 노드에서의 업그레이드를 진행 합니다.
    매개 변수의 값은 `upgradeTimeout` 6 시간으로 줄일 수 있지만 최대 보안을 12 시간으로 사용 해야 합니다.

    그런 후 다음을 확인 합니다.
    - 포털의 Service Fabric 리소스에 준비 됨이 표시 됩니다.

9. ARM 템플릿에서 노드 유형과 관련 된 리소스에 대 한 모든 참조를 제거 합니다.

    - 배포에 사용 되는 Azure Resource Manager 템플릿을 찾습니다.
    - 템플릿의 노드 유형과 관련 된 가상 머신 확장 집합 및 기타 리소스를 제거 합니다.
    - 변경 내용을 배포합니다.

    그런 다음:
    - 배포가 완료 될 때까지 기다립니다.
    
10. 더 이상 사용 하지 않는 노드 유형과 관련 된 리소스를 제거 합니다. 예 Load Balancer 및 공용 IP. 

    - 이러한 리소스를 제거 하려면 6 단계에서 사용한 것과 같은 PowerShell 명령을 사용 하 여 특정 리소스 종류와 API 버전을 지정할 수 있습니다. 

> [!Note]
> 동일한 Load Balancer 경우이 단계는 선택 사항이 며 노드 형식 간에 IP가 다시 사용 됩니다.

## <a name="next-steps"></a>다음 단계
- 클러스터 [내구성 특징](./service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)에 대해 자세히 알아봅니다.
- [노드 유형과 Virtual Machine Scale Sets](service-fabric-cluster-nodetypes.md)에 대해 자세히 알아봅니다.
- [Service Fabric 클러스터 크기 조정](service-fabric-cluster-scaling.md)에 대해 자세히 알아봅니다.
