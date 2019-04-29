---
title: Azure Service Fabric 클러스터 업그레이드 | Microsoft Docs
description: 클러스터 업데이트 모드 설정, 인증서 업그레이드, 애플리케이션 포트 추가, OS 패치 수행 등을 포함하는 Service Fabric 클러스터를 실행하는 Service Fabric 코드 및/또는 구성을 업그레이드합니다. 업그레이드를 수행할 때 예상할 수 있는 것은 무엇입니까?
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
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714720"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>클러스터의 Service Fabric 버전 업그레이드

최신 시스템의 경우 업그레이드 기능 디자인이 제품의 장기적 성공 달성의 비결입니다. Azure 서비스 패브릭 클러스터는 개인이 소유하지만 Microsoft에서 부분적으로 관리하는 리소스입니다. 이 문서에서는 Azure 클러스터에서 실행되는 Service Fabric의 버전을 업그레이드하는 방법을 설명합니다.

Microsoft에서 자동 패브릭 업그레이드를 릴리스하면 클러스터가 수신하도록 설정할 수 있습니다. 또는 클러스터를 배치하려는 지원되는 패브릭 버전을 선택할 수 있습니다.

포털에서 "upgradeMode" 클러스터를 설정하거나 라이브 클러스터 생성 시 또는 나중에 Resource Manager를 사용하여 이 작업을 수행합니다. 

> [!NOTE]
> 클러스터에서 지원되는 패브릭 버전이 항상 실행되도록 해야 합니다. 새로운 버전의 서비스 패브릭 릴리스를 발표하면 이전 버전은 해당 날짜부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 [서비스 패브릭 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 발표됩니다. 그러면 새로운 릴리스를 선택할 수 있습니다. 
> 
> 

클러스터가 실행되는 릴리스가 만료되기 14일 전에, 클러스터를 경고 성능 상태로 전환하는 상태 이벤트가 생성됩니다. 지원되는 패브릭 버전으로 업그레이드할 때까지 클러스터는 경고 상태로 유지됩니다.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Azure Portal에서 업그레이드 모드 설정
클러스터를 만들 때 클러스터를 자동 또는 수동으로 설정할 수 있습니다.

![Create_Manualmode][Create_Manualmode]

라이브 클러스터인 경우 관리 환경을 사용하여 클러스터를 자동 또는 수동으로 설정할 수 있습니다. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>포털을 통해 수동 모드로 설정된 클러스터에서 새 버전으로 업그레이드
새 버전으로 업그레이드하려면 드롭다운 목록에서 사용 가능한 버전을 선택하고 저장하기만 하면 됩니다. 패브릭 업그레이드는 자동으로 시작됩니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 애플리케이션의 상태 조합)은 업그레이드의 기간을 준수합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 이 문서를 아래로 스크롤하여 사용자 지정 상태 정책을 설정하는 방법에 대해 자세히 알아보세요. 

롤백을 일으킨 문제를 수정했으면 이전과 동일한 단계에 따라 업그레이드를 다시 시작해야 합니다.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 업그레이드 모드 설정
아래 표시된 것처럼 "upgradeMode" 구성을 Microsoft.ServiceFabric/clusters 리소스 정의에 추가하고 "clusterCodeVersion"을 지원되는 패브릭 버전 중 하나로 설정한 후 템플릿을 배포합니다. "upgradeMode"에 대해 유효한 값은 "Manual" 또는 "Automatic"입니다.

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Resource Manager 템플릿을 통해 수동 모드로 설정된 클러스터에서 새 버전으로 업그레이드
클러스터가 수동 모드인 경우 새 버전으로 업그레이드하려면 "clusterCodeVersion"을 지원되는 버전으로 변경하고 배포합니다. 템플릿의 배포 시 패브릭 업그레이드는 자동으로 시작됩니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 애플리케이션의 상태 조합)은 업그레이드의 기간을 준수합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다.  

롤백을 일으킨 문제를 수정했으면 이전과 동일한 단계에 따라 업그레이드를 다시 시작해야 합니다.

## <a name="set-custom-health-polices-for-upgrades"></a>업그레이드에 대한 사용자 지정 상태 정책 설정
패브릭 업그레이드에 대한 사용자 지정 상태 정책을 지정할 수 있습니다. 클러스터를 자동 패브릭 업그레이드로 설정한 경우 이러한 정책은 [자동 패브릭 업그레이드의 1단계](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)에 적용됩니다.
클러스터를 수동 패브릭 업그레이드로 설정한 경우 이러한 정책은 새 버전을 선택할 때마다 적용되며 그러면 시스템이 클러스터에서 패브릭 업그레이드를 시작하도록 트리거링합니다. 정책을 재정의하지 않으면 기본값이 사용됩니다.

사용자 지정 상태 정책을 지정하거나 "패브릭 업그레이드" 블레이드 아래에서 고급 업그레이드 설정을 선택하여 현재 설정을 검토할 수 있습니다. 다음 그림은 이 방법을 보여줍니다. 

![사용자 지정 상태 정책 관리][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>지정된 구독의 모든 환경에 대해 사용 가능한 모든 버전 나열
다음 명령을 실행하면 다음과 유사한 결과가 표시됩니다.

"supportExpiryUtc"는 지정된 릴리스가 만료되거나 이미 만료되었음을 알려 줍니다. 최신 릴리스는 유효한 날짜를 포함하지 않으며 "9999-12-31T23:59:59.9999999" 값을 포함합니다. 이는 만료 날짜가 아직 설정되지 않음을 의미합니다.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

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
