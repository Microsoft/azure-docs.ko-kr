---
title: Azure Service Fabric 독립 실행형 클러스터 업그레이드 | Microsoft Docs
description: Azure Service Fabric 독립 실행형 클러스터의 버전 또는 구성 업그레이드에 대해 알아봅니다.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 1d96a2e81917af5e80bb847ea25610ccb71ad70f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711059"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Service Fabric 독립 실행형 클러스터 업그레이드 및 업데이트

최신 시스템의 경우 업그레이드 기능 디자인이 제품의 장기적 성공 달성의 비결입니다. Azure Service Fabric 독립 실행형 클러스터는 사용자가 소유하는 리소스입니다. 이 문서에서는 업그레이드 또는 업데이트할 수 있는 것을 설명합니다.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>클러스터에서 실행되는 패브릭 버전 제어
클러스터가 [지원되는 Service Fabric 버전](service-fabric-versions.md)을 항상 실행하는지 확인합니다. Microsoft에서 새로운 버전의 Service Fabric 릴리스를 발표하면 이전 버전은 해당 발표일로부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 발표됩니다. 그러면 해당 시점에 새로운 릴리스를 선택할 수 있습니다.

Microsoft에서 자동 패브릭 업그레이드를 릴리스하면 클러스터가 수신하도록 설정할 수 있습니다. 또는 클러스터를 배치하려는 지원되는 패브릭 버전을 수동으로 선택할 수 있습니다. 자세한 내용은 [클러스터에서 실행되는 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)를 참조하세요.

## <a name="customize-configuration-settings"></a>구성 설정 사용자 지정

*ClusterConfig.json* 파일에서 클러스터 및 노드 속성의 안정성 수준과 같은 다양한 다른 [구성 설정](service-fabric-cluster-manifest.md)을 설정할 수 있습니다.  자세히 알아보려면 [독립 실행형 클러스터의 구성 업그레이드](service-fabric-cluster-config-upgrade-windows-server.md)를 참조하세요.  다른 많은 고급 설정도 사용자 지정할 수 있습니다.  자세한 내용은 [Service Fabric 클러스터 패브릭 설정](service-fabric-cluster-fabric-settings.md)을 참조하세요.

## <a name="define-node-properties"></a>노드 속성 정의
때로는 특정 워크로드가 클러스터의 특정 노드 형식에서만 실행되도록 하려고 할 수 있습니다. 예를 들어, 일부 워크로드는GPU 또는 SSD가 필요할 수 있습니다. 클러스터에서 각 노드 형식에 대해 클러스터 노드에 사용자 지정 노드 속성을 추가할 수 있습니다. 배치 제약 조건은 하나 이상의 노드 속성에 대해 선택하는 개별 서비스에 연결되는 명령문입니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다.

배치 제약 조건의 사용, 노드 속성 및 정의하는 방법에 대한 자세한 내용은 [노드 속성 및 배치 제약 조건](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)을 참조하세요.
 

## <a name="add-capacity-metrics"></a>용량 메트릭 추가
각 노드 유형의 경우 부하를 보고하도록 사용하려는 사용자 용량 메트릭을 애플리케이션에 추가할 수 있습니다. 부하를 보고하는 용량 메트릭 사용에 대한 자세한 내용은 서비스 패브릭 클러스터 리소스 관리자 설명서에서 [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md) 및 [메트릭 및 부하](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

## <a name="patch-the-os-in-the-cluster-nodes"></a>클러스터 노드에서 OS 패치
POA(패치 오케스트레이션 애플리케이션)는 Service Fabric 클러스터에서 가동 중지 시간 없이 운영 체제 패치를 자동화하는 Service Fabric 애플리케이션입니다. [Windows용 패치 오케스트레이션 애플리케이션](service-fabric-patch-orchestration-application.md)을 오케스트레이션된 방식으로 패치를 설치하도록 클러스터에 배포할 수 있으므로 서비스를 항상 사용할 수 있도록 유지합니다. 


## <a name="next-steps"></a>다음 단계
*  [서비스 패브릭 클러스터 패브릭 설정](service-fabric-cluster-fabric-settings.md)
*  [클러스터를 확장 및 축소하는](service-fabric-cluster-scale-up-down.md)
* [애플리케이션 업그레이드](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
