---
title: Azure Service Fabric 클러스터 노드에 원격 연결 | Microsoft Docs
description: 확장 집합 인스턴스(Service Fabric 클러스터 노드)에 원격으로 연결하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 4cc2d6355a0147c33048f1c2c27a3648b9223db4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110926"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>가상 머신 확장 집합 인스턴스 또는 클러스터 노드에 원격 연결
Azure에서 실행되는 Service Fabric 클러스터에서 정의한 각 클러스터 노드 형식은 [가상 머신별 규모를 설정](service-fabric-cluster-nodetypes.md)합니다.  특정 확장 집합 인스턴스(클러스터 노드)에 원격 연결할 수 있습니다.  단일 인스턴스 VM과 달리 확장 집합 인스턴스는 고유한 가상 IP 주소가 없습니다. 따라서 특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트를 찾는 것이 어려울 수 있습니다.

특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 특정 인스턴스를 찾으려면 다음 단계를 완료합니다.

1. RDP(원격 데스크톱 프로토콜)에 대한 인바운드 NAT 규칙을 가져옵니다.

    일반적으로 클러스터에 정의된 각 노드 형식에는 자체 가상 IP 주소와 전용 부하 분산 장치가 있습니다. 기본적으로 노드 유형에 대 한 부하 분산 장치는 다음 형식으로 이름이 지정 됩니다. *LB-{클러스터-이름}-{노드 형식}*; 예를 들어 *프런트 엔드 mycluster-LB-* 합니다. 
    
    Azure Portal의 부하 분산 장치 페이지에서 **설정** > **인바운드 NAT 규칙**을 선택합니다. 

    ![부하 분산 장치 인바운드 NAT 규칙](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    다음 스크린샷에서는 FrontEnd라는 노드 형식에 대한 인바운드 NAT 규칙을 보여 줍니다. 

    ![부하 분산 장치 인바운드 NAT 규칙](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    각 노드에 대해 IP 주소가 **대상** 열에 표시되며, **대상**은 확장 집합 인스턴스를 제공하고 **서비스** 열은 포트 번호를 제공합니다. 원격 연결의 경우 포트 3389부터 오름차순으로 각 노드에 포트가 할당됩니다.

    클러스터에 대한 Resource Manager 템플릿의 `Microsoft.Network/loadBalancers` 섹션에서 인바운드 NAT 규칙을 찾을 수도 있습니다.
    
2. 노드에 대한 인바운드 포트와 대상 포트 매핑을 확인하려면 해당 규칙을 클릭하고 **대상 포트** 값을 확인하면 됩니다. 다음 스크린샷에서는 이전 단계의 **FrontEnd (인스턴스 1)** 노드에 대한 인바운드 NAT 규칙을 보여 줍니다. 인바운드 포트 번호는 3390이지만 대상 포트는 대상의 RDP 서비스에 대한 포트인 포트 3389에 매핑됩니다.  

    ![대상 포트 매핑](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    기본적으로 Windows 클러스터의 경우 대상 포트는 포트 3389이며, 이는 대상 노드의 RDP 서비스에 매핑됩니다. Linux 클러스터의 경우 대상 포트는 포트 22이며, 이는 SSH(보안 셸) 서비스에 매핑됩니다.

3. 특정 노드(확장 집합 인스턴스)에 원격으로 연결합니다. 클러스터를 만들 때 설정한 사용자 이름 및 암호 또는 구성한 다른 자격 증명을 사용할 수 있습니다. 

    다음 스크린샷에서는 원격 데스크톱 연결을 사용하여 Windows 클러스터에서 **FrontEnd(인스턴스 1)** 노드에 연결하는 것을 보여 줍니다.
    
    ![원격 데스크톱 연결](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Linux 노드에서 SSH와 연결할 수 있습니다(다음 예제에서는 간단하게 나타내기 위해 동일한 IP 주소와 포트를 다시 사용함).

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


다음 단계는 아래 문서를 참조하세요.
* ["어디에나 배포" 기능의 개요 및 Azure 관리된 클러스터와 비교](service-fabric-deploy-anywhere.md)를 참조하세요.
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* 배포 후에 클러스터 VM에서 [RDP 포트 범위 값 업데이트](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 클러스터 VM에 대한 [관리자 사용자 이름 및 암호 변경](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

