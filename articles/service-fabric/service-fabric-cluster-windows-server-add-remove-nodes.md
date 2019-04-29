---
title: 독립 실행형 Service Fabric 클러스터에 노드 추가 또는 제거 | Microsoft Docs
description: Windows Server를 실행하는 실제 또는 가상 머신에서 Azure 서비스 패브릭 클러스터에 노드를 추가하거나 제거하는 방법(온-프레미스 또는 클라우드에 위치 가능)을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 585d918026ca40bc1a04c55e2bac454492c55936
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711036"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Windows Server가 실행되는 독립 실행형 서비스 패브릭 클러스터에 노드 추가 또는 제거 | Microsoft Azure
[Windows Server 컴퓨터에 독립 실행형 Service Fabric 클러스터를 만든](service-fabric-cluster-creation-for-windows-server.md) 후 비즈니스 요구는 변경될 수 있으며 클러스터에 노드를 추가 또는 제거해야 할 수 있습니다. 이 문서에서는 이 목표를 달성하는 자세한 단계를 제공합니다. 노드 추가/제거 기능은 로컬 개발 클러스터에서 지원되지 않습니다.

## <a name="add-nodes-to-your-cluster"></a>클러스터에 노드 추가

1. [Service Fabric 클러스터 배포 계획 및 준비](service-fabric-cluster-creation-for-windows-server.md)에 설명된 단계에 따라 클러스터에 추가하려는 VM/컴퓨터를 준비합니다.
2. 이 VM/컴퓨터를 추가하려는 장애 도메인 및 업그레이드 도메인을 식별합니다.
3. 클러스터에서 추가하려는 VM/컴퓨터로 RDP(원격 데스크톱)를 수행합니다.
4. VM/컴퓨터에 [Windows Server용 Service Fabric에 대한 독립 실행형 패키지를 복사 또는 다운로드](https://go.microsoft.com/fwlink/?LinkId=730690)하고 패키지 압축을 풉니다.
5. 상승된 권한으로 Powershell을 실행하고 압축을 푼 패키지의 위치로 이동합니다.
6. 추가할 새 노드를 설명하는 매개 변수를 사용하여 *AddNode.ps1* 스크립트를 실행합니다. 아래 예제에서는 NodeType0 유형 및 IP 주소 182.17.34.52를 사용하여 VM5라는 새 노드를 UD1 및 fd:/dc1/r0에 추가합니다. *ExistingClusterConnectionEndPoint* 는 기존 클러스터에 이미 있는 노드에 대한 연결 엔드포인트입니다. 이는 클러스터에 있는 *모든* 노드의 IP 주소가 될 수 있습니다.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    스크립트가 실행을 완료하면 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet을 실행하여 새 노드가 추가됐는지 확인할 수 있습니다.

7. 클러스터의 서로 다른 노드 간에 일관성을 보장하려면 구성 업그레이드를 시작해야 합니다. [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps)을 실행하여 최신 구성 파일을 가져오고 새로 추가된 노드를 "노드" 섹션에 추가합니다. 또한 항상 동일한 구성 사용 하 여 클러스터를 다시 배포 해야 하는 경우 사용할 수 있는 최신 클러스터 구성으로는 것이 좋습니다.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)를 실행하여 업그레이드를 시작합니다.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Service Fabric Explorer에서 업그레이드의 진행률을 모니터링할 수 있습니다. 또한 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)를 실행할 수 있습니다.

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>gMSA를 사용하여 Windows 보안으로 구성된 클러스터에 노드 추가
gMSA(그룹 관리 서비스 계정)로 구성된 클러스터의 경우(https://technet.microsoft.com/library/hh831782.aspx)) 구성 업그레이드를 사용하여 새 노드를 추가할 수 있습니다.
1. 기존 노드 중 하나에서 [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps)을 실행하여 최신 구성 파일을 가져오고 "노드" 섹션에 추가하려는 새 노드에 대한 세부 정보를 추가합니다. 새 노드가 동일한 그룹 관리 계정의 일부인지 확인합니다. 이 계정은 모든 컴퓨터에서 관리자여야 합니다.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)를 실행하여 업그레이드를 시작합니다.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Service Fabric Explorer에서 업그레이드의 진행률을 모니터링할 수 있습니다. 또한 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)를 실행할 수 있습니다.

### <a name="add-node-types-to-your-cluster"></a>클러스터에 노드 형식 추가
새 노드 형식을 추가하기 위해 "속성" 아래의 "NodeTypes" 섹션에 새 노드 형식을 포함하도록 구성을 수정하고 [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)를 사용하여 구성 업그레이드를 시작합니다. 업그레이드가 완료되면 이 노드 형식으로 클러스터에 새 노드를 추가할 수 있습니다.

## <a name="remove-nodes-from-your-cluster"></a>클러스터에서 노드 제거
다음과 같은 방식으로 구성 업그레이드를 사용하여 클러스터에서 노드를 제거할 수 있습니다.

1. [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps)을 실행하여 최신 구성 파일을 가져오고 "노드" 섹션에서 노드를 *제거*합니다.
"FabricSettings" 섹션 내의 "설정" 섹션에 "NodesToBeRemoved" 매개 변수를 추가합니다. "값"은 제거되어야 하는 노드의 노드 이름의 쉼표로 구분된 목록이어야 합니다.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)를 실행하여 업그레이드를 시작합니다.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Service Fabric Explorer에서 업그레이드의 진행률을 모니터링할 수 있습니다. 또한 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)를 실행할 수 있습니다.

> [!NOTE]
> 노드를 제거하여 여러 업그레이드를 시작할 수 있습니다. 일부 노드는 `IsSeedNode=”true”` 태그로 표시되고 `Get-ServiceFabricClusterManifest`를 사용하여 클러스터 매니페스트를 쿼리하여 확인할 수 있습니다. 시드 노드는 이러한 시나리오에서 이동되어야 하므로 이러한 노드의 제거는 다른 항목보다 더 오래 걸릴 수 있습니다. 클러스터는 최소 3가지 주 노드 유형 노드를 유지해야 합니다.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>클러스터에서 노드 유형 제거
노드 유형을 제거하기 전에 노드 유형을 참조하는 노드가 있는지 다시 확인합니다. 해당 노드 유형을 제거하기 전에 이러한 노드를 제거합니다. 모든 해당 노드가 제거된 후 클러스터 구성에서 NodeType을 제거하고 [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps)를 사용하여 구성 업그레이드를 시작할 수 있습니다.


### <a name="replace-primary-nodes-of-your-cluster"></a>클러스터의 주 노드 교체
주 노드 교체는 일괄 처리로 제거한 다음 추가하는 대신 차례로 수행되어야 합니다.


## <a name="next-steps"></a>다음 단계
* [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)
* [X509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](service-fabric-windows-cluster-x509-security.md)
* [Windows를 실행하는 Azure VM에서 독립 실행형 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-with-windows-azure-vms.md)

