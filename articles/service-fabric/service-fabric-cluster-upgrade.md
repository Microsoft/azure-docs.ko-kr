---
title: Azure Service Fabric 클러스터 업그레이드 | Microsoft Docs
description: Azure Service Fabric 클러스터의 버전 또는 구성 업그레이드에 대해 알아봅니다.  이 문서에서는 클러스터 업데이트 모드 설정, 인증서 업그레이드, 애플리케이션 포트 추가, OS 패치 수행 및 업그레이드를 수행하는 경우 얻을 수 있는 것을 설명합니다.
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
ms.openlocfilehash: 3ddda89b19a04bdcd45f392f297ee5e930833538
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711617"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Azure Service Fabric 클러스터 업그레이드 및 업데이트

최신 시스템의 경우 업그레이드 기능 디자인이 제품의 장기적 성공 달성의 비결입니다. Azure 서비스 패브릭 클러스터는 개인이 소유하지만 Microsoft에서 부분적으로 관리하는 리소스입니다. 이 문서는 자동으로 관리되는 것과 스스로 구성할 수 있는 것을 설명합니다.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>클러스터에서 실행되는 패브릭 버전 제어

클러스터에서 [지원되는 패브릭 버전](service-fabric-versions.md)이 항상 실행되도록 해야 합니다. 새로운 버전의 서비스 패브릭 릴리스를 발표하면 이전 버전은 해당 날짜부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 Service Fabric 팀 블로그에서 발표됩니다. 그러면 새로운 릴리스를 선택할 수 있습니다.

클러스터가 실행되는 릴리스가 만료되기 14일 전에, 클러스터를 경고 성능 상태로 전환하는 상태 이벤트가 생성됩니다. 지원되는 패브릭 버전으로 업그레이드할 때까지 클러스터는 경고 상태로 유지됩니다.

Microsoft에서 자동 패브릭 업그레이드를 릴리스하면 클러스터가 수신하도록 설정할 수 있습니다. 또는 클러스터를 배치하려는 지원되는 패브릭 버전을 선택할 수 있습니다.  자세히 알아보려면 [클러스터의 Service Fabric 버전 업그레이드](service-fabric-cluster-upgrade-version-azure.md)를 참조하세요.

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>자동 업그레이드 중 패브릭 업그레이드 동작
Microsoft는 Azure 클러스터에서 실행하는 패브릭 코드 및 구성을 유지 관리합니다. 필요한 기준으로 소프트웨어에 자동 모니터링된 업그레이드를 수행합니다. 이러한 업그레이드는 코드, 구성 또는 둘 모두가 될 수 있습니다. 애플리케이션이 이러한 업그레이드로 인해 영향이 없거나 최소한의 영향이 있는지 확인하기 위해 다음 단계로 업그레이드를 수행합니다.

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1단계: 모든 클러스터 상태 정책을 사용하여 업그레이드 수행
이 단계 동안 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행하고 클러스터에서 실행 중이던 애플리케이션은 가동 중지 시간 없이 계속 실행합니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 애플리케이션의 상태 조합)은 업그레이드의 기간을 준수합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 그런 다음 구독 소유자에게 전자 메일이 전송됩니다. 전자 메일에는 다음 정보가 포함되어 있습니다.

* 클러스터 업그레이드를 롤백해야 했다는 알림.
* 권장 수정 작업(있는 경우).
* 2단계를 실행할 때까지 일 수(n)

인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 전자 메일이 전송된 날짜에서 n일 후 2단계로 진행합니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다. 너무 많은 전자 메일을 전송하지 않도록 하며 전자 메일 수신은 정상에 대한 예외로 보여야 합니다. 클러스터 업그레이드가 애플리케이션 가용성에 영향을 주지 않고 성공되기를 예상합니다.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2단계: 기본 상태 정책만을 사용하여 업그레이드 수행
이 단계의 상태 정책은 업그레이드 시작 시 정상 상태가 업그레이드 프로세스의 기간 동안 동일하게 유지되는 애플리케이션의 수 방식으로 설정됩니다. 1단계와 마찬가지로 2단계 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행하고 클러스터에서 실행 중이던 애플리케이션은 가동 중지 시간 없이 계속 실행합니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 애플리케이션의 상태 조합)은 업그레이드의 기간을 준수합니다.

적용되는 클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 그런 다음 구독 소유자에게 전자 메일이 전송됩니다. 전자 메일에는 다음 정보가 포함되어 있습니다.

* 클러스터 업그레이드를 롤백해야 했다는 알림.
* 권장 수정 작업(있는 경우).
* 3단계를 실행할 때까지 일 수(n)

인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 미리 알림 전자 메일은 n일이 끝나기 몇 일 전에 전송됩니다. 전자 메일이 전송된 날짜에서 n일 후 3단계로 진행합니다. 2단계에서 전송한 전자 메일은 심각하게 받아들여져야 하며 수정 작업이 수행되어야 합니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3단계: 까다로운 상태 정책을 사용하여 업그레이드 수행
이 단계의 이러한 상태 정책은 애플리케이션의 상태보다 업그레이드 완료에 맞춰집니다. 이 단계에서 매우 적은 클러스터 업그레이드가 끝납니다. 클러스터가 이 단계에 도달하는 경우 애플리케이션이 비정상이 되고/되거나 가용성이 손실될 수 있습니다.

다른 두 단계와 마찬가지로 3단계 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 그런 다음 지원 및/또는 업그레이드를 더 이상 받지 않도록 클러스터가 고정됩니다.

이 정보가 있는 메일이 수정 작업과 함께 구독 소유자에게 전송됩니다. 3단계가 실패된 상태로 클러스터가 도달하지 않을 것입니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다.

## <a name="manage-certificates"></a>인증서 관리
Service Fabric은 클러스터 노드 간 통신을 보호하도록 클러스터를 만들고 클라이언트를 인증할 때 지정하는 [X.509 서버 인증서](service-fabric-cluster-security.md)를 사용합니다. [Azure Portal](https://portal.azure.com)에서 또는 PowerShell/Azure CLI를 사용하여 클러스터 및 클라이언트에 대한 인증서를 추가, 업데이트 또는 삭제할 수 있습니다.  자세히 알아보려면 [인증서 추가 또는 제거](service-fabric-cluster-security-update-certs-azure.md)를 참조하세요.

## <a name="open-application-ports"></a>애플리케이션 포트 열기
노드 유형에 연결된 부하 분산 장치 리소스 속성을 변경하여 애플리케이션 포트를 변경할 수 있습니다. Azure Portal을 사용하거나 PowerShell/Azure CLI를 사용할 수 있습니다. 자세한 내용은 [클러스터에 대한 애플리케이션 포트 열기](create-load-balancer-rule.md)를 참조하세요.

## <a name="define-node-properties"></a>노드 속성 정의
때로는 특정 워크로드가 클러스터의 특정 노드 형식에서만 실행되도록 하려고 할 수 있습니다. 예를 들어, 일부 워크로드는GPU 또는 SSD가 필요할 수 있습니다. 클러스터에서 각 노드 형식에 대해 클러스터 노드에 사용자 지정 노드 속성을 추가할 수 있습니다. 배치 제약 조건은 하나 이상의 노드 속성에 대해 선택하는 개별 서비스에 연결되는 명령문입니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다.

배치 제약 조건의 사용, 노드 속성 및 정의하는 방법에 대한 자세한 내용은 [노드 속성 및 배치 제약 조건](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)을 참조하세요.

## <a name="add-capacity-metrics"></a>용량 메트릭 추가
각 노드 유형의 경우 부하를 보고하도록 사용하려는 사용자 용량 메트릭을 애플리케이션에 추가할 수 있습니다. 부하를 보고하는 용량 메트릭 사용에 대한 자세한 내용은 서비스 패브릭 클러스터 리소스 관리자 설명서에서 [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md) 및 [메트릭 및 부하](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

## <a name="set-health-policies-for-automatic-upgrades"></a>자동 업그레이드에 대한 상태 정책 설정
패브릭 업그레이드에 대한 사용자 지정 상태 정책을 지정할 수 있습니다. 클러스터를 자동 패브릭 업그레이드로 설정한 경우 이러한 정책은 자동 패브릭 업그레이드의 1단계에 적용됩니다.
클러스터를 수동 패브릭 업그레이드로 설정한 경우 이러한 정책은 새 버전을 선택할 때마다 적용되며 그러면 시스템이 클러스터에서 패브릭 업그레이드를 시작하도록 트리거링합니다. 정책을 재정의하지 않으면 기본값이 사용됩니다.

사용자 지정 상태 정책을 지정하거나 "패브릭 업그레이드" 블레이드 아래에서 고급 업그레이드 설정을 선택하여 현재 설정을 검토할 수 있습니다. 다음 그림은 이 방법을 보여줍니다. 

![사용자 지정 상태 정책 관리][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>클러스터에 대한 패브릭 설정 사용자 지정
클러스터에서 클러스터 및 노드 속성의 안정성 수준과 같은 다양한 다른 구성 설정을 사용자 지정할 수 있습니다. 자세한 내용은 [Service Fabric 클러스터 패브릭 설정](service-fabric-cluster-fabric-settings.md)을 참조하세요.

## <a name="patch-the-os-in-the-cluster-nodes"></a>클러스터 노드에서 OS 패치
POA(패치 오케스트레이션 애플리케이션)는 Service Fabric 클러스터에서 가동 중지 시간 없이 운영 체제 패치를 자동화하는 Service Fabric 애플리케이션입니다. [Windows용 패치 오케스트레이션 애플리케이션](service-fabric-patch-orchestration-application.md) 또는 [Linux용 패치 오케스트레이션 애플리케이션](service-fabric-patch-orchestration-application-linux.md)을 오케스트레이션된 방식으로 패치를 설치하도록 클러스터에 배포할 수 있으므로 서비스를 항상 사용할 수 있도록 유지합니다. 


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
