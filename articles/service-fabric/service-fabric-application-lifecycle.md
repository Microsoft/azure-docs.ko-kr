---
title: Service Fabric의 애플리케이션 수명 주기 | Microsoft Docs
description: 서비스 패브릭 애플리케이션 개발, 배포, 테스트, 업그레이드, 유지 관리 및 제거를 설명합니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 08837cca-5aa7-40da-b087-2b657224a097
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/19/2018
ms.author: atsenthi
ms.openlocfilehash: 53cab3591ea11721e36b48438f35df016e2a9f3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621493"
---
# <a name="service-fabric-application-lifecycle"></a>서비스 패브릭 애플리케이션 수명 주기
다른 플랫폼과 마찬가지로, Azure 서비스 패브릭 기반의 애플리케이션은 일반적으로 디자인, 개발, 테스트, 배포, 업그레이드, 유지 관리 및 제거 단계를 거칩니다. 서비스 패브릭은 개발부터 배포, 일상적인 관리, 유지 관리 및 최종적인 서비스 해제에 이르기까지 클라우드 애플리케이션의 전체 애플리케이션 수명 주기 관리에 대해 최고 수준의 지원을 제공합니다. 여러 역할이 애플리케이션 수명 주기에 독립적으로 참가할 수 있는 서비스 모델이 제공됩니다. 이 문서에서는 서비스 패브릭 애플리케이션 수명 주기의 모든 단계에서 여러 역할이 사용하는 방법 및 API에 대한 개요를 제공합니다.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>서비스 모델 역할
서비스 모델은 다음과 같습니다.

* **서비스 개발자**: 용도 변경 하 고 동일한 형식 또는 다른 형식의 여러 응용 프로그램에서 사용할 수 있는 모듈식 및 일반 서비스를 개발 합니다. 예를 들어 큐 서비스를 사용하여 발권 애플리케이션(헬프데스크) 또는 전자 상거래 애플리케이션(장바구니)을 만들 수 있습니다.
* **애플리케이션 개발자**: 특정 요구 사항 또는 시나리오를 충족하도록 서비스 컬렉션을 통합하여 애플리케이션을 만듭니다. 예를 들어 전자 상거래 웹 사이트에 "JSON 상태 비저장 프런트엔드 서비스", "경매 상태 저장 서비스" 및 "큐 상태 저장 서비스"를 통합하여 경매 솔루션을 빌드합니다.
* **애플리케이션 관리자**: 애플리케이션 구성(구성 템플릿 매개 변수를 입력), 배포(사용 가능한 리소스에 매핑) 및 서비스의 품질에 대한 결정을 내립니다. 예를 들어 애플리케이션 관리자가 애플리케이션의 언어 로캘을 결정합니다(예: 미국은 영어, 일본은 일본어). 배포된 다른 애플리케이션을 다르게 설정할 수 있습니다.
* **운영자**: 애플리케이션 관리자가 지정한 애플리케이션 구성 및 요구 사항에 따라 애플리케이션을 배포합니다. 예를 들어 운영자가 애플리케이션을 프로비전 및 배포하고 Azure에서 실행되고 있는지 확인합니다. 운영자는 애플리케이션 상태 및 성능 정보를 모니터링 하고 필요에 따라 실제 인프라를 유지 관리합니다.

## <a name="develop"></a>개발
1. *서비스 개발자*는 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 또는 [Reliable Services](service-fabric-reliable-services-introduction.md) 프로그래밍 모델을 사용하여 여러 유형의 서비스를 개발합니다.
2. *서비스 개발자* 는 하나 이상의 코드, 구성 및 데이터 패키지로 구성된 서비스 매니페스트 파일에 개발된 서비스 유형에 대한 내용을 선언적으로 설명합니다.
3. 그런 다음, *애플리케이션 개발자*는 다른 서비스 유형을 사용하여 애플리케이션을 빌드합니다.
4. *애플리케이션 개발자*는 구성 서비스의 서비스 매니페스트를 참조하여 애플리케이션에 대한 내용을 애플리케이션 매니페스트에 선언적으로 설명하고, 구성 서비스의 다른 구성 및 배포 설정을 적절하게 재정의 및 매개 변수화합니다.

이에 대한 예는 [Reliable Actors 시작](service-fabric-reliable-actors-get-started.md) 및 [Reliable Services 시작](service-fabric-reliable-services-quick-start.md)을 참조하세요.

## <a name="deploy"></a>배포
1. *애플리케이션 관리자*는 애플리케이션 매니페스트에 있는 **ApplicationType** 요소의 매개 변수를 적절하게 지정하여 애플리케이션 유형을 서비스 패브릭 클러스터에 배포할 특정 애플리케이션으로 맞춤화합니다.
2. *운영자*는 [**CopyApplicationPackage** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) 또는 [**Copy-ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)을 사용하여 애플리케이션 패키지를 클러스터 이미지 저장소에 업로드합니다. 애플리케이션 패키지는 애플리케이션 매니페스트 및 서비스 패키지 컬렉션을 포함합니다. 서비스 패브릭이 이미지 저장소에 저장된 애플리케이션 패키지의 애플리케이션을 배포합니다. ImageStore는 Azure Blob 저장소일 수도 있고 서비스 패브릭 시스템 서비스일 수도 있습니다.
3. 그런 다음, *운영자*가 [**ProvisionApplicationAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Register-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype) 또는 [**Provision an Application** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)을 사용하여 업로드된 애플리케이션 패키지의 대상 클러스터에 애플리케이션 유형을 프로비전합니다.
4. 애플리케이션을 프로비전한 후 *운영자*가 [**CreateApplicationAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**New-ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricapplication) 또는 [**애플리케이션 만들기** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/create-an-application)을 사용하여 *애플리케이션 관리자*가 제공한 매개 변수로 애플리케이션을 시작합니다.
5. 애플리케이션이 배포된 후 *운영자*가 [**CreateServiceAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [**New-ServiceFabricService** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice) 또는 [**Create Service** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/create-a-service)을 사용하여 제공되는 서비스 유형에 따라 애플리케이션에 대한 새 서비스 인스턴스를 만듭니다.
6. 이제 서비스 패브릭 클러스터에서 애플리케이션이 실행됩니다.

예는 [애플리케이션 배포](service-fabric-deploy-remove-applications.md)를 참조하세요.

## <a name="test"></a>테스트
1. 로컬 개발 클러스터 또는 클러스터에 배포한 후 *서비스 개발자*가 [**FailoverTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) 및 [**FailoverTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.failovertestscenario) 클래스 또는 [**Invoke-ServiceFabricFailoverTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario?view=azureservicefabricps)를 사용하여 기본 제공 장애 조치(failover) 테스트 시나리오를 실행합니다. 장애 조치(failover) 테스트 시나리오는 중요한 전환 및 장애 조치(failover)를 통해 지정된 서비스를 실행하여 서비스가 중단 없이 작동되도록 보장합니다.
2. 그런 다음 *서비스 개발자*가 [**ChaosTestScenarioParameters**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) 및 [**ChaosTestScenario**](https://docs.microsoft.com/dotnet/api/system.fabric.testability.scenario.chaostestscenario) 클래스 또는 [**Invoke-ServiceFabricChaosTestScenario** cmdlet](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario?view=azureservicefabricps)를 사용하여 기본 제공 비정상 상황 테스트 시나리오를 실행합니다. 비정상 상황 테스트 시나리오는 임의로 여러 노드, 코드 패키지 및 복제 오류를 클러스터로 유도합니다.
3. 그런 다음 *service developer* [서비스 간 통신을 테스트](service-fabric-testability-scenarios-service-communication.md) 합니다.

자세한 내용은 [오류 분석 서비스 소개](service-fabric-testability-overview.md) 를 참조하세요.

## <a name="upgrade"></a>업그레이드
1. *서비스 개발자*는 인스턴스화된 애플리케이션의 구성 서비스를 업데이트 하고/하거나 버그를 수정하고 새로운 서비스 매니페스트 버전을 제공합니다.
2. *애플리케이션 개발자*는 구성 서비스의 구성 및 배포 설정을 재정의 및 매개 변수화하고 새로운 애플리케이션 매니페스트 버전을 제공합니다. 그런 다음, 애플리케이션 개발자가 새로운 서비스 매니페스트 버전을 애플리케이션에 통합하고 업데이트된 애플리케이션 패키지에 새로운 버전의 애플리케이션 유형을 제공합니다.
3. *애플리케이션 관리자*는 적절한 매개 변수를 업데이트하여 새로운 버전의 애플리케이션 유형을 대상 애플리케이션에 통합합니다.
4. *운영자*는 [**CopyApplicationPackage** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) 또는 [**Copy-ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)을 사용하여 업데이트된 애플리케이션 패키지를 클러스터 이미지 저장소에 업로드합니다. 애플리케이션 패키지는 애플리케이션 매니페스트 및 서비스 패키지 컬렉션을 포함합니다.
5. 그런 다음, *운영자*가 [**ProvisionApplicationAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Register-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/register-servicefabricapplicationtype) 또는 [**Provision an Application** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/provision-an-application)을 사용하여 대상 클러스터에 새로운 애플리케이션 버전을 프로비전합니다.
6. *운영자*가 [**UpgradeApplicationAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Start-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) 또는 [**Upgrade an Application** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/upgrade-an-application)을 사용하여 대상 애플리케이션을 새 버전으로 업그레이드합니다.
7. *운영자*가 [**GetApplicationUpgradeProgressAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Get-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricapplicationupgrade) 또는 [**Get Application Upgrade Progress** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1)을 사용하여 업그레이드 진행 상황을 확인합니다.
8. 필요한 경우 *운영자*가 [**UpdateApplicationUpgradeAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Update-ServiceFabricApplicationUpgrade** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade) 또는 [**Update Application Upgrade** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/update-an-application-upgrade)을 사용하여 현재 애플리케이션 업그레이드의 매개 변수를 수정하고 다시 적용합니다.
9. 필요한 경우 *운영자*가 [**RollbackApplicationUpgradeAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Start-ServiceFabricApplicationRollback** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback) 또는 [**Rollback Application Upgrade** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/rollback-an-application-upgrade)을 사용하여 현재 애플리케이션 업그레이드를 롤백합니다.
10. 모든 구성 서비스가 제공되는 상태로 서비스 패브릭이 클러스터에서 실행되는 대상 애플리케이션을 업그레이드합니다.

예제는 [애플리케이션 업그레이드 자습서](service-fabric-application-upgrade-tutorial.md)를 참조하세요.

## <a name="maintain"></a>유지 관리
1. 운영 체제 업그레이드 및 패치의 경우 클러스터에서 실행 중인 모든 애플리케이션의 가용성을 보장하기 위해 서비스 패브릭이 Azure 인프라와 상호 작용합니다.
2. 서비스 패브릭 플랫폼 업그레이드 및 패치의 경우 클러스터에서 실행 중인 애플리케이션의 가용성을 손실하지 서비스 패브릭이 자체적으로 업그레이드됩니다.
3. *애플리케이션 관리자*가 용량 사용률 데이터 및 향후 예상 수요를 분석한 후 클러스터에 대한 노드 추가 또는 제거를 승인합니다.
4. *운영자*는 *애플리케이션 관리자*가 지정한 노드를 추가 또는 제거합니다.
5. 클러스터에 새 노드가 추가되거나 기존 노드 클러스터에서 노드가 제거되면 서비스 패브릭이 실행 중인 애플리케이션의 부하를 클러스터의 모든 노드로 분산하여 최적의 성능을 유지합니다.

## <a name="remove"></a>제거
1. *운영자*는 [**DeleteServiceAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient), [**Remove-ServiceFabricService** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice) 또는 [**Delete Service** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/delete-a-service)을 사용하여 전체 애플리케이션을 제거하지 않고 클러스터에서 실행 중인 특정 인스턴스를 삭제할 수 있습니다.  
2. 또한 *운영자*는 [**DeleteApplicationAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Remove-ServiceFabricApplication** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricapplication), or the [**Delete Application** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/delete-an-application)을 사용하여 애플리케이션 인스턴스 및 모든 서비스를 삭제할 수 있습니다.
3. 애플리케이션 및 서비스가 중지되면 *운영자*는 [**UnprovisionApplicationAsync** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [**Unregister-ServiceFabricApplicationType** cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/unregister-servicefabricapplicationtype) 또는 [**Unprovision an Application** REST 작업](https://docs.microsoft.com/rest/api/servicefabric/unprovision-an-application)을 사용하여 애플리케이션 유형의 프로비전을 해제할 수 있습니다. 애플리케이션 유형의 프로비전을 해제해도 ImageStore에서 애플리케이션 패키지가 제거되지는 않습니다. 따라서 애플리케이션 패키지를 수동으로 제거해야 합니다.
4. *운영자*가 [**RemoveApplicationPackage** 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) 또는 [**Remove-ServiceFabricApplicationPackage** cmdlet](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps)을 사용하여 ImageStore에서 애플리케이션 패키지를 제거합니다.

예는 [애플리케이션 배포](service-fabric-deploy-remove-applications.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
서비스 패브릭 애플리케이션 및 서비스의 개발, 테스트 및 관리에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [애플리케이션 배포](service-fabric-deploy-remove-applications.md)
* [애플리케이션 업그레이드](service-fabric-application-upgrade.md)
* [테스트 용이성 개요](service-fabric-testability-overview.md)
