---
title: Service Fabric 애플리케이션의 업그레이드 구성 | Microsoft Docs
description: Microsoft Visual Studio를 사용하여 Service Fabric 애플리케이션을 업그레이드하기 위한 설정을 구성하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikkelhegn
ms.openlocfilehash: 79120371ca2a62e5ef9f2bf38476635db12e9fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082845"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Visual Studio에서 Service Fabric 애플리케이션의 업그레이드 구성
Azure 서비스 패브릭에 대한 Visual Studio Tools는 로컬 또는 원격 클러스터에 게시하기 위한 업그레이드를 지원합니다. 테스트 및 디버그 중에 애플리케이션을 바꾸지 않고 새 버전으로 업그레이드하려는 세 가지 시나리오가 있습니다.

* 업그레이드하는 동안 애플리케이션 데이터가 손실되지 않습니다.
* 업그레이드 도메인에 서비스 인스턴스가 충분히 퍼져 있는 경우 가용성이 높은 상태를 유지하므로 업그레이드 중 서비스 중단이 발생하지 않습니다.
* 업그레이드하는 동안 애플리케이션에 대해 테스트를 실행할 수 있습니다.

## <a name="parameters-needed-to-upgrade"></a>업그레이드하기 위해 필요한 매개 변수
두 가지의 배포 형식(일반 또는 업그레이드)에서 선택할 수 있습니다. 일반 배포는 클러스터에서 이전 배포 정보 및 데이터를 지우는 반면 업그레이드 배포는 이러한 정보 및 데이터를 유지합니다. Visual Studio에서 Service Fabric 애플리케이션을 업그레이드하는 경우 애플리케이션 업그레이드 매개 변수 및 상태 검사 정책을 제공해야 합니다. 애플리케이션 업그레이드 매개 변수는 업그레이드를 제어하는 반면 상태 검사 정책은 업그레이드가 성공적인지 여부를 확인합니다. 자세한 내용은 [Service Fabric 애플리케이션 업그레이드: 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 참조하세요.

세 가지 업그레이드 모드: *모니터링할*, *UnmonitoredAuto*, 및 *UnmonitoredManual*합니다.

* Monitored 업그레이드는 업그레이드 및 애플리케이션 상태 검사를 자동화합니다.
* UnmonitoredAuto 업그레이드는 업그레이드를 자동화하지만 애플리케이션 상태 검사는 건너뜁니다.
* UnmonitoredManual 업그레이드를 수행하는 경우 수동으로 각 업그레이드 도메인을 업그레이드해야 합니다.

업그레이드 모드마다 서로 다른 매개 변수 집합이 필요합니다. 사용할 수 있는 업그레이드 옵션에 대해 자세히 알아보려면 [애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 참조하세요.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Visual Studio에서 Service Fabric 애플리케이션 업그레이드
Visual Studio Service Fabric 도구를 사용하여 Service Fabric 애플리케이션을 업그레이드하는 경우 **애플리케이션 업그레이드** 확인란을 선택하여 게시 프로세스가 일반 배포가 아닌 업그레이드가 되도록 지정할 수 있습니다.

### <a name="to-configure-the-upgrade-parameters"></a>업그레이드 매개 변수를 구성하려면
1. 확인란 옆의 **설정** 단추를 클릭합니다. **업그레이드 매개 변수 편집** 대화 상자가 나타납니다. **업그레이드 매개 변수 편집** 대화 상자는 Monitored, UnmonitoredAuto 및 UnmonitoredManual 업그레이드 모드를 지원합니다.
2. 사용하려는 업그레이드 모드를 선택한 다음 매개 변수 그리드를 채웁니다.

    각 매개 변수에는 기본값이 있습니다. 선택적 매개 변수 *DefaultServiceTypeHealthPolicy* 는 해시 테이블 입력을 사용합니다. 다음은 *DefaultServiceTypeHealthPolicy*에 대한 해시 테이블 입력 형식의 예제입니다.

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* 은 다음과 같은 형식으로 해시 테이블 입력을 사용하는 다른 선택적 매개 변수입니다.

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    다음은 실제 예제입니다.

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. UnmonitoredManual 업그레이드 모드를 선택하는 경우 수동으로 PowerShell 콘솔을 시작하여 업그레이드 프로세스를 완료해야 합니다. 수동 업그레이드 작업 방법을 알아보려면 [Service Fabric 애플리케이션 업그레이드: 고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하세요.

## <a name="upgrade-an-application-by-using-powershell"></a>PowerShell을 사용하여 애플리케이션 업그레이드
PowerShell cmdlet을 사용하여 서비스 패브릭 애플리케이션을 업그레이드할 수 있습니다. 자세한 내용을 보려면 [Service Fabric 애플리케이션 업그레이드 자습서](service-fabric-application-upgrade-tutorial.md) 및 [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade)를 참조하세요.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>애플리케이션 매니페스트 파일에 상태 검사 정책을 지정합니다.
Service Fabric 애플리케이션의 모든 서비스는 기본값을 재정의하는 자체 상태 정책 매개 변수를 가질 수 있습니다. 애플리케이션 매니페스트 파일에서 이러한 매개 변수 값을 제공할 수 있습니다.

다음 예제에서는 애플리케이션 매니페스트에서 각 서비스에 대한 고유한 상태 검사 정책을 적용하는 방법을 보여 줍니다.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>다음 단계
애플리케이션을 업그레이드하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)를 참조하세요.
