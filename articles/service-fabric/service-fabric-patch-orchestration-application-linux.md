---
title: Linux용 Azure Service Fabric 패치 오케스트레이션 애플리케이션 | Microsoft Docs
description: Linux Service Fabric 클러스터에 운영 체제 패치를 자동화하는 애플리케이션입니다.
services: service-fabric
documentationcenter: .net
author: novino
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 537450dbc386a94fa5c2e0d9334435dce041a32f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647159"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Service Fabric 클러스터에서 Linux 운영 체제 패치

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

패치 오케스트레이션 애플리케이션은 Service Fabric 클러스터에서 가동 중지 시간 없이 운영 체제 패치를 자동화하는 Azure Service Fabric 애플리케이션입니다.

패치 오케스트레이션 앱은 다음과 같은 기능을 제공합니다.

- **자동 운영 체제 업데이트 설치**. 운영 체제 업데이트가 자동으로 다운로드되고 설치됩니다. 클러스터 노드는 필요에 따라 클러스터 가동 중지 시간 없이 다시 부팅됩니다.

- **클러스터 인식 패치 및 상태 통합**. 패치 오케스트레이션 앱은 업데이트를 적용하는 동안 클러스터 노드의 상태를 모니터링합니다. 클러스터 노드는 한 번에 하나의 노드씩 또는 한 번에 하나의 업그레이드 도메인씩 업그레이드됩니다. 패치 프로세스로 인해 클러스터의 상태가 다운되는 경우 문제가 악화되는 것을 방지하기 위해 프로세스가 중지됩니다.

## <a name="internal-details-of-the-app"></a>앱 내부 세부 정보

패치 오케스트레이션 앱은 다음 하위 구성 요소로 구성됩니다.

- **코디네이터 서비스**: 이 상태 저장 서비스는 다음과 같은 역할을 합니다.
    - 전체 클러스터에서 OS Update 작업을 조정합니다.
    - 완료된 OS 업데이트 작업의 결과를 저장합니다.
- **노드 에이전트 서비스**: 이 상태 비저장 서비스는 모든 Service Fabric 클러스터 노드에서 실행됩니다. 서비스는 다음과 같은 역할을 합니다.
    - Linux에서 노드 에이전트 디먼 부트스트랩
    - 디먼 서비스 모니터링
- **노드 에이전트 디 몬**: 이 Linux 디 몬 서비스는 더 높은 수준의 권한 (루트)에서 실행 됩니다. 반면, 노드 에이전트 서비스 및 코디네이터 서비스는 하위 수준의 권한에서 실행됩니다. 서비스는 모든 클러스터 노드에서 다음과 같은 업데이트 작업을 수행하는 일을 담당합니다.
    - 노드에서 자동 OS Update 비활성화
    - 사용자가 제공한 정책에 따라 OS 업데이트 다운로드 및 설치
    - 필요한 경우 OS 업데이트 설치 이후 컴퓨터 다시 시작
    - 코디네이터 서비스에 OS 업데이트 결과 업로드
    - 모든 재시도가 끝난 다음 작업이 실패한 경우 상태 보고서 보고

> [!NOTE]
> 패치 오케스트레이션 앱은 Service Fabric 복구 관리자 시스템 서비스를 사용하여 노드를 사용하도록 또는 사용하지 않도록 설정하고 상태 검사를 수행합니다. 패치 오케스트레이션 앱에서 만든 복구 작업은 각 노드에 대한 업데이트 진행률을 추적합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Azure VM에서 Ubuntu 16.04를 실행하고 있는지 확인합니다.
이 문서 작성 당시 Ubuntu 16.04(`Xenial Xerus`)가 지원되는 유일한 버전입니다.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Service Fabric Linux 클러스터가 버전 6.2.x 이상인지 확인합니다.

패치 오케스트레이션 앱 linux는 서비스 패브릭 런타임 버전 6.2.x 이상에만 제공되는 런타임의 특정 기능을 사용합니다.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>복구 관리자 서비스 사용(아직 실행되지 않은 경우)

패치 오케스트레이션 앱은 복구 관리자 시스템 서비스를 클러스터에서 사용하도록 설정해야 합니다.

#### <a name="azure-clusters"></a>Azure 클러스터

실버 및 골드 내구성 계층의 Azure linux 클러스터에는 복구 관리자 서비스가 기본적으로 사용하도록 설정되어 있습니다. 브론즈 내구성 계층의 Azure 클러스터에는 복구 관리자 서비스가 기본적으로 사용하도록 설정되어 있지 않습니다. 서비스가 이미 사용하도록 설정되어 있는 경우 Service Fabric Explorer의 시스템 서비스 섹션에서 서비스가 실행되고 있는 것을 볼 수 있습니다.

##### <a name="azure-portal"></a>Azure portal
클러스터를 설정할 때 Azure Portal에서 복구 관리자를 설정할 수 있습니다. 클러스터를 구성할 때 **추가 기능**에서 **복구 관리자 포함** 옵션을 선택합니다.
![Azure Portal에서 복구 관리자 사용 이미지](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager 배포 모델
또는 [Azure Resource Manager 배포 모델](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)을 사용하여 신규 및 기존 Service Fabric 클러스터에서 복구 관리자 서비스를 사용하도록 설정할 수 있습니다. 배포하려는 클러스터에 대한 템플릿을 가져옵니다. 예제 템플릿을 사용하거나 사용자 지정 Azure Resource Manager 배포 모델을 만들 수 있습니다. 

[Azure Resource Manager 배포 모델 템플릿](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)을 사용하여 복구 관리자 서비스를 사용하도록 설정하려면:

1. 먼저 `Microsoft.ServiceFabric/clusters` 리소스에 대해 `apiversion`이 `2017-07-01-preview`로 설정되었는지 확인합니다. 값이 다르면 `apiVersion`을 `2017-07-01-preview` 값 이상으로 업데이트해야 합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 이제 아래와 같이 다음 `addonFeatures` 섹션을 `fabricSettings` 섹션 뒤에 추가하여 복구 관리자 서비스를 사용하도록 설정합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 이러한 변경 내용으로 클러스터 템플릿을 업데이트한 후 변경 내용을 적용하고 업그레이드가 완료되도록 합니다. 이제 클러스터에서 복구 관리자 시스템 서비스가 실행되고 있는 것을 볼 수 있습니다. Service Fabric Explorer의 시스템 서비스 섹션에 있는 `fabric:/System/RepairManagerService`입니다. 

### <a name="standalone-on-premises-clusters"></a>독립 실행형 온-프레미스 클러스터

독립 실행형 Service Fabric Linux 클러스터는 이 문서 작성 시 지원되지 않습니다.

### <a name="disable-automatic-os-update-on-all-nodes"></a>모든 노드에서 자동 OS 업데이트 사용 안 함

자동 OS 업데이트로 가용성이 손실되거나 실행 중인 애플리케이션의 동작이 변경될 수 있습니다. 패치 오케스트레이션 앱은 기본적으로 각 클러스터 노드에서 이러한 시나리오를 방지하도록 자동 OS 업데이트를 사용하지 않으려 합니다.
Ubuntu의 경우 [무인 업그레이드](https://help.ubuntu.com/community/AutomaticSecurityUpdates)는 패치 오케스트레이션 앱에서 비활성화됩니다.

## <a name="download-the-app-package"></a>앱 패키지 다운로드

설치 스크립트와 함께 애플리케이션을 [보관 링크](https://go.microsoft.com/fwlink/?linkid=867984)에서 다운로드할 수 있습니다.

sfpkg 형식의 애플리케이션은 [sfpkg 링크](https://aka.ms/POA/POA_v2.0.3.sfpkg)에서 다운로드할 수 있습니다. 이 링크를 통해 [Azure Resource Manager 기반 애플리케이션을 쉽게 배포](service-fabric-application-arm-resource.md)할 수 있습니다.

## <a name="configure-the-app"></a>앱 구성

요구 사항에 맞게 패치 오케스트레이션 앱의 동작을 구성할 수 있습니다. 애플리케이션 만들기 또는 업데이트 중에 애플리케이션 매개 변수를 전달하여 기본값을 재정의합니다. 애플리케이션 매개 변수는 `Start-ServiceFabricApplicationUpgrade` 또는 `New-ServiceFabricApplication` cmdlet에 `ApplicationParameter`를 지정하여 제공될 수 있습니다.

|**매개 변수**        |**형식**                          | **세부 정보**|
|:-|-|-|
|MaxResultsToCache    |long                              | 캐시되어야 하는 업데이트 결과의 최대 수입니다. <br>기본값은 3000입니다. <br> - 노드 수는 20입니다. <br> - 매월 노드에서 발생하는 업데이트 수는 5입니다. <br> - 작업당 결과 수는 10일 수 있습니다. <br> - 지난 3개월 동안의 결과를 저장해야 합니다. |
|TaskApprovalPolicy   |열거형 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy는 Service Fabric 클러스터 노드에서 업데이트를 설치하기 위해 코디네이터 서비스에서 사용하는 정책을 나타냅니다.<br>                         허용되는 값은 다음과 같습니다. <br>                                                           <b>NodeWise</b>. 업데이트가 한 번에 하나의 노드에 설치됩니다. <br>                                                           <b>UpgradeDomainWise</b>. 업데이트가 한 번에 하나의 업그레이드 도메인에 설치됩니다. (최대, 한 업그레이드 도메인에 속하는 모든 노드가 업데이트에 해당될 수 있습니다.)
| UpdateOperationTimeOutInMinutes | Int <br>(기본값: 180)                   | 업데이트 작업에 대한 시간 제한을 지정합니다(다운로드 또는 설치). 지정된 시간 제한 내에 작업이 완료되지 않으면 중단됩니다.       |
| RescheduleCount      | Int <br> (기본값: 5)                  | 작업이 영구적으로 실패하는 경우 서비스에서 OS 업데이트를 다시 예약하는 최대 횟수입니다.          |
| RescheduleTimeInMinutes  | Int <br>(기본값: 30) | 오류가 계속 발생하는 경우 서비스에서 OS 업데이트를 다시 예약하는 간격입니다. |
| UpdateFrequency           | 쉼표로 구분된 문자열(기본값: "Weekly, Wednesday, 7:00:00")     | 클러스터에서 OS 업데이트를 설치하는 빈도입니다. 형식 및 가능한 값은 다음과 같습니다. <br>-   매월, DD, HH:MM:SS(예: 매월, 5,12:22:32) <br> -   매주, DAY, HH:MM:SS(예: 매주, 화요일, 12:22:32)  <br> -   매일, HH:MM:SS(예: 매일, 12:22:32)  <br> -   없음은 업데이트를 수행하지 않음을 나타냅니다.  <br><br> 모든 시간은 UTC 형식입니다.|
| UpdateClassification | 쉼표로 구분된 문자열(기본값: "securityupdates") | 클러스터 노드에 설치되어야 하는 업데이트의 유형입니다. 사용 가능한 값은 securityupdates, 모두입니다. <br> -  securityupdates - 보안 업데이트만 설치 <br> -  모두 - apt에서 사용 가능한 모든 업데이트 설치|
| ApprovedPatches | 쉼표로 구분된 문자열(기본값: "") | 클러스터 노드에 설치되어야 하는 승인된 업데이트 목록입니다. 쉼표로 구분된 목록은 승인된 패키지 및 필요에 따라 원하는 대상 버전을 포함합니다.<br> 예: "apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16" <br> 위의 내용은 다음을 설치합니다. <br> - apt-캐시에 사용 가능한 경우 버전 1.2.10ubuntu1의 apt-utils 해당 특정 버전을 사용할 수 없는 경우 작동하지 않습니다. <br> - 사용 가능한 최신 버전으로 python3-jwt 업그레이드 패키지가 없으면 작동하지 않습니다. <br> - 1.2.194 이하인 가장 높은 버전으로 apt-transport-https 업그레이드 이 버전이 없으면 작동하지 않습니다. <br> - 229-4ubuntu16보다 크거나 같은 가장 높은 버전으로 libsystemd0 업그레이드 이러한 버전이 없는 경우 작동하지 않습니다.|
| RejectedPatches | 쉼표로 구분된 문자열(기본값: "") | 클러스터 노드에 설치하면 안 되는 업데이트 목록입니다. <br> 예: "bash, sudo" <br> 앞의 항목은 모든 업데이트 수신에서 bash, sudo를 필터링합니다. |


> [!TIP]
> OS 업데이트가 즉시 처리되도록 하려면 애플리케이션 배포 시간에 관한 `UpdateFrequency`를 설정합니다. 예를 들어 5노드 테스트 클러스터가 있고 약 5PM UTC에 앱을 배포할 계획이라고 가정할 수 있습니다. 애플리케이션 업그레이드 또는 배포에 최대 30분이 소요된다고 가정하는 경우 UpdateFrequency를 "매일, 17:30:00"으로 설정합니다.

## <a name="deploy-the-app"></a>앱 배포

1. 모든 필수 구성 요소 단계를 완료하여 클러스터를 준비합니다.
2. 다른 Service Fabric 앱과 마찬가지로 패치 오케스트레이션 앱을 배포합니다. PowerShell 또는 Azure Service Fabric CLI를 사용하여 앱을 배포할 수 있습니다. [PowerShell을 사용하여 애플리케이션 배포 및 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) 또는 [Azure Service Fabric CLI를 사용하여 애플리케이션 배포](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)의 단계를 따릅니다.
3. 배포 시 애플리케이션을 구성하려면 `ApplicationParameter`를 `New-ServiceFabricApplication` cmdlet 또는 제공된 스크립트에 전달합니다. 사용자 편의를 위해 powershell(Deploy.ps1) 및 bash(Deploy.sh) 스크립트는 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

    - Service Fabric 클러스터에 연결합니다.
    - 배포 스크립트를 실행합니다. 필요에 따라 스크립트에 애플리케이션 매개 변수를 전달합니다. 예: .\배포.ps1 -ApplicationParameter @{ UpdateFrequency = "Daily, 11:00:00"} 또는 ./배포.sh "{\"UpdateFrequency\":\"매일, 11:00:00\"}" 

> [!NOTE]
> 스크립트 및 애플리케이션 폴더 PatchOrchestrationApplication을 동일한 디렉터리에 유지합니다.

## <a name="upgrade-the-app"></a>앱 업그레이드

기존 패치 오케스트레이션 앱을 업그레이드하려면 [PowerShell을 사용하여 Service Fabric 애플리케이션 업그레이드](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) 또는 [Azure Service Fabric CLI를 사용하여 Service Fabric 애플리케이션 업그레이드](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)의 단계를 따릅니다.

## <a name="remove-the-app"></a>앱 제거

애플리케이션을 제거하려면 [PowerShell을 사용하여 애플리케이션 배포 및 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) 또는 [Azure Service Fabric CLI를 사용하여 애플리케이션 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)의 단계를 따릅니다.

사용자 편의를 위해 powershell(Undeploy.ps1) 및 bash(Undeploy.sh) 스크립트는 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

  - Service Fabric 클러스터에 연결합니다.
  - 스크립트 Undeploy.ps1 또는 Undeploy.sh를 실행합니다.

> [!NOTE]
> 스크립트 및 애플리케이션 폴더 PatchOrchestrationApplication을 동일한 디렉터리에 유지합니다.

## <a name="view-the-update-results"></a>업데이트 결과 보기

패치 오케스트레이션 앱은 사용자에게 기록 결과를 표시하는 REST API를 노출합니다. 다음은 샘플 결과입니다. ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

JSON의 필드가 다음과 같이 설명되어 있습니다.

필드 | 값 | 세부 정보
-- | -- | --
OperationResult | 0 - 성공<br> 1 - 성공하였으나 오류 발생<br> 2 - 실패<br> 3 - 중단<br> 4 - 시간 제한으로 중단 | 전체 작업의 결과를 나타냅니다(일반적으로 하나 이상의 업데이트 설치 포함).
ResultCode | OperationResult와 동일 | 이 필드는 개별 업데이트에 대한 설치 작업의 결과를 나타냅니다.
OperationType | 1 - 설치<br> 0 - 검색 및 다운로드.| 설치는 기본적으로 결과에 표시되는 유일한 OperationType입니다.
UpdateClassification | 기본값은 "securityupdates"입니다. | 업데이트 작업 중에 설치된 업데이트의 유형
UpdateFrequency | 기본값은 "매주, 수요일, 7:00:00"입니다. | 이 업데이트에 대해 구성된 업데이트 빈도입니다.
RebootRequired | true - 다시 부팅 필요<br> false - 다시 부팅 필요 없음 | 업데이트 설치를 완료하는 데 다시 부팅이 필요한지를 표시합니다.
ApprovedList | 기본값은 ""입니다. | 이 업데이트에 대한 승인된 패치 목록
RejectedList | 기본값은 ""입니다. | 이 업데이트에 대한 거부된 패치 목록

업데이트가 아직 예약되어 있지 않으면 결과 JSON은 비어 있습니다.

업데이트 결과를 쿼리하려면 클러스터에 로그인합니다. 그런 다음, 주 코디네이터 서비스의 복제본 주소를 찾아 브라우저에서 URL을 입력합니다(http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults).

코디네이터 서비스의 REST 엔드포인트에는 동적 포트가 있습니다. 정확한 URL을 확인하려면 Service Fabric Explorer를 참조하세요. 예를 들어 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`에 결과가 제공됩니다.

![REST 엔드포인트의 이미지](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>진단/상태 이벤트

### <a name="diagnostic-logs"></a>진단 로그

Service Fabric 런타임 로그의 일부로 패치 오케스트레이션 앱 로그가 수집됩니다.

선택한 진단 도구/파이프라인을 통해 로그를 캡처하려고 합니다. 패치 오케스트레이션 애플리케이션은 다음의 고정된 공급자 ID를 사용하여 [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)를 통해 이벤트를 기록합니다.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>상태 보고서

패치 오케스트레이션 앱은 다음과 같은 경우에도 코디네이터 서비스 또는 노드 에이전트 서비스에 대한 상태 보고서를 게시합니다.

#### <a name="an-update-operation-failed"></a>업데이트 작업 실패

노드에서 업데이트 작업이 실패하면 노드 에이전트 서비스에 대한 상태 보고서가 생성됩니다. 상태 보고서의 세부 정보에 문제가 있는 노드 이름이 포함됩니다.

문제가 있는 노드에서 패치 작업이 성공적으로 완료되면 보고서가 자동으로 지워집니다.

#### <a name="the-node-agent-daemon-service-is-down"></a>노드 에이전트 디먼 서비스 중단

노드에서 노드 에이전트 디먼 서비스가 중단되면 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다.

#### <a name="the-repair-manager-service-is-not-enabled"></a>복구 관리자 서비스가 사용하도록 설정되지 않음

복구 관리자 서비스가 클러스터에 없는 경우 코디네이터 서비스에 대해 경고 수준 상태 보고서가 생성됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

Q. **패치 오케스트레이션 앱이 실행 중인 경우 오류 상태인 클러스터가 표시되는 이유는 무엇인가요?**

a. 설치 과정에서 패치 오케스트레이션 앱은 노드를 비활성화하거나 다시 시작합니다. 이 작업으로 일시적으로 클러스터가 중지 상태가 될 수 있습니다.

애플리케이션의 정책에 따라 패치 작업 중에 하나의 노드가 중단되거나 *또는* 전체 업그레이드 도메인이 동시에 중단될 수 있습니다.

설치가 끝날 때까지 노드는 다시 시작된 후 다시 사용하도록 설정됩니다.

다음 예제에서는 두 노드가 중단되고 MaxPercentageUnhealthyNodes 정책이 위반되었으므로 클러스터가 일시적으로 오류 상태가 되었습니다. 이 오류는 패치 작업이 진행될 때까지 일시적으로 지속됩니다.

![비정상 클러스터의 이미지](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

문제가 계속되는 경우 문제 해결 섹션을 참조하세요.

Q. **패치 오케스트레이션 앱이 경고 상태입니다.**

a. 애플리케이션에 대해 게시된 상태 보고서가 근본 원인인지 확인합니다. 일반적으로 경고에는 문제에 대한 세부 정보가 포함되어 있습니다. 문제가 일시적인 경우 애플리케이션은 이 상태에서 자동으로 복구됩니다.

Q. **클러스터가 비정상 상태이나 긴급한 운영 체제 업데이트를 수행해야 하는 경우 어떻게 해야 하나요?**

a. 클러스터 상태가 정상이 아닌 경우 패치 오케스트레이션 앱은 업데이트를 설치하지 않습니다. 패치 오케스트레이션 앱 워크플로의 차단을 해제하기 위해 클러스터를 정상 상태로 전환합니다.

Q. **클러스터에 패치를 실행하는 데 시간이 너무 오래 걸리는 이유는 무엇인가요?**

a. 패치 오케스트레이션 앱에 필요한 시간은 대개 다음과 같은 요인에 따라 달라집니다.

- 코디네이터 서비스 정책 
  - 기본 정책인 `NodeWise`에 따라, 한 번에 하나의 노드에만 패치를 적용합니다. 특히 클러스터의 크기가 큰 경우 `UpgradeDomainWise` 정책을 사용하여 클러스터의 패치 적용 속도를 빠르게 하는 것이 좋습니다.
- 다운로드 및 설치에 사용할 수 있는 업데이트 수 
- 업데이트를 다운로드하고 설치하는 데 필요한 평균 시간, 1-2시간을 넘지 않아야 함
- VM 및 네트워크 대역폭의 성능

Q. **패치 오케스트레이션 앱에서 보안 업데이트인 업데이트를 어떻게 결정하나요?**

a. 패치 오케스트레이션 앱은 사용 가능한 업데이트 중에 보안 업데이트인 업데이트를 확인하기 위해 배포별 논리를 사용합니다. 예를 들면 다음과 같습니다. Ubuntu에서 앱 $RELEASE에서 업데이트에 대 한 검색-보안, $RELEASE-업데이트 ($RELEASE = xenial 또는 linux 표준 기준 릴리스 버전). 

 
Q. **특정 버전의 패키지로 어떻게 잠그나요?**

a. ApprovedPatches 설정을 사용하여 패키지를 특정 버전으로 잠급니다. 


Q. **Ubuntu에서 활성화된 자동 업데이트는 어떻게 되나요?**

a. 클러스터에 패치 오케스트레이션 앱을 설치하는 즉시 클러스터 노드의 무인 업그레이드는 사용할 수 없습니다. 모든 정기적 업데이트 워크플로는 패치 오케스트레이션 앱에서 파생됩니다.
클러스터에서 환경의 일관성을 가지려면 패치 오케스트레이션 앱만을 통해 업데이트를 설치하는 것이 좋습니다. 
 
Q. **업그레이드 후 패치 오케스트레이션 앱은 사용되지 않는 패키지의 정리를 수행하나요?**

a. 예, 정리는 설치 후 단계의 일부로 발생합니다. 

Q. **패치 오케스트레이션 앱을 사용하여 개발자 클러스터(1노드 클러스터)에 패치를 적용할 수 있나요?**

a. 아니요, 패치 오케스트레이션 앱을 사용하여 1노드 클러스터에 패치를 적용할 수 없습니다. 이 제한은 기본적으로 [Service Fabric 시스템 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) 또는 고객 앱이 가동 중지되고 패치에 대한 모든 복구 작업이 복구 관리자에서 승인되지 않으므로 이 제한은 기본적으로 적용됩니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="a-node-is-not-coming-back-to-up-state"></a>노드가 활성 상태로 전환되지 않음

**노드는 다음과 같은 이유로 사용 불가능 상태에 멈춰 있을 수 있습니다**.

안전 검사가 보류 중입니다. 이 문제를 해결하려면 정상 상태에서 충분히 노드를 사용할 수 있는지 확인합니다.

**노드는 다음과 같은 이유로 사용 안 함 상태에 멈춰 있을 수 있습니다**.

- 노드가 수동으로 사용하지 않도록 설정되었습니다.
- 노드가 진행 중인 Azure 인프라 작업으로 인해 사용하지 않도록 설정되었습니다.
- 노드가 노드를 패치하는 패치 오케스트레이션 앱에 의해 일시적으로 사용하지 않도록 설정되었습니다.

**노드는 다음과 같은 이유로 중단 상태에 멈춰 있을 수 있습니다**.

- 노드가 수동으로 중단되었습니다.
- 노드가 다시 시작하는 중입니다(패치 오케스트레이션 앱에 의해 트리거될 수 있음).
- 노드가 결함이 있는 VM, 컴퓨터 또는 네트워크 연결 문제로 인해 중단되었습니다.

### <a name="updates-were-skipped-on-some-nodes"></a>일부 노드에서 업데이트를 건너뛰었습니다.

패치 오케스트레이션 앱은 다시 예약 정책에 따라 업데이트를 설치하려고 합니다. 이 서비스는 노드를 복구하고 애플리케이션 정책에 따라 업데이트를 건너뛰려고 합니다.

이러한 경우에 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다. 업데이트 결과에도 가능한 실패 원인이 포함됩니다.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>업데이트가 설치되는 동안 클러스터의 상태가 오류로 전환됨

잘못된 업데이트는 특정 노드 또는 업그레이드 도메인에 있는 애플리케이션 또는 클러스터의 상태를 중단시킬 수 있습니다. 패치 오케스트레이션 앱은 클러스터가 다시 정상 상태가 될 때까지 후속 업데이트 작업을 중단합니다.

관리자가 개입하여 이전에 설치된 업데이트로 인해 애플리케이션 또는 클러스터가 비정상 상태가 된 이유를 확인해야 합니다.

## <a name="disclaimer"></a>고지 사항

패치 오케스트레이션 앱은 사용 및 성능을 추적하기 위해 원격 분석을 수집합니다. 애플리케이션의 원격 분석은 Service Fabric 런타임의 원격 분석 설정을 따릅니다(기본적으로 설정됨).

## <a name="release-notes"></a>릴리스 정보

### <a name="version-010"></a>버전 0.1.0
- 비공개 미리 보기 릴리스

### <a name="version-200"></a>버전 2.0.0
- 공개 릴리스

### <a name="version-201"></a>버전 2.0.1
- 최신 Service Fabric SDK를 사용하여 앱을 다시 컴파일함

### <a name="version-202"></a>버전 2.0.2 
- 다시 시작하는 동안 상태 경고가 남아 있는 문제가 해결되었습니다.

### <a name="version-203-latest"></a>버전 2.0.3 (최신)
- 문제를 해결 하 고 있는 노드 에이전트 디먼 서비스의 CPU 사용량에 도달 하면 최대 99% Standard_D1_v2 Vm에서 키를 누릅니다.
- 현재 노드 이름의 하위 집합 이름 사용 하는 노드가 없는 경우에 영향을 노드에서 패치 수명 주기는 문제를 해결 합니다. 그러한 노드의 경우 패치가 누락되거나 다시 부팅이 보류 중일 수 있습니다.
- 가 손상 설정 서비스에 전달 되 면 노드 에이전트 디먼 충돌 유지 하는 버그가 수정 되었습니다.