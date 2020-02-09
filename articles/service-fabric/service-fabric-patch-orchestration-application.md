---
title: Service Fabric 클러스터에서 Windows 운영 체제 패치
description: 이 문서에서는 패치 오케스트레이션 응용 프로그램을 사용 하 여 Service Fabric 클러스터에서 운영 체제 패치를 자동화 하는 방법을 설명 합니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 3115c65c7027f5624b7b60b9be702ee4192d8cb6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464455"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric 클러스터에서 Windows 운영 체제 패치

> 
> [!IMPORTANT]
> 2019 년 4 월 30 일부 터 패치 오케스트레이션 응용 프로그램 버전 1.2. *는 더 이상 지원 되지 않습니다. 최신 버전으로 업그레이드 해야 합니다.

> [!NOTE]
> [가상 머신 확장 집합에서 자동 OS 이미지 업그레이드](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) 를 얻는 것이 Azure에서 운영 체제 패치를 유지 하는 가장 좋은 방법입니다. 가상 머신 확장 집합을 기반으로 하는 자동 OS 이미지 업그레이드는 확장 집합에서 은색 이상 내구성이 필요 합니다.
>

 POA (패치 오케스트레이션 응용 프로그램)는 azure에서 호스트 되지 않는 클러스터에 대해 구성 기반 OS 패치 일정을 예약할 수 있도록 하는 Azure Service Fabric Repair Manager 서비스에 대 한 래퍼입니다. Azure가 아닌 호스트 된 클러스터에는 POA가 필요 하지 않지만, 업데이트 도메인에의 한 패치 설치 일정은 가동 중지 시간 없이 클러스터 호스트 Service Fabric 패치를 수행 하는 데 필요 합니다.

POA는 가동 중지 시간을 발생 시 키 지 않고 Service Fabric 클러스터에서 운영 체제 패치를 자동화 하는 Service Fabric 응용 프로그램입니다.

POA는 다음과 같은 기능을 제공 합니다.

- **자동 운영 체제 업데이트 설치**. 운영 체제 업데이트가 자동으로 다운로드되고 설치됩니다. 클러스터 노드는 클러스터 가동 중지 시간 없이 필요에 따라 다시 부팅 됩니다.

- **클러스터 인식 패치 및 상태 통합**. POA는 업데이트를 적용 하는 동안 클러스터 노드의 상태를 모니터링 합니다. 클러스터 노드는 한 번에 하나의 노드 또는 한 번에 하나의 업데이트 도메인에 업데이트 됩니다. 패치 프로세스로 인해 클러스터의 상태가 중단 된 경우 패치가 중지 되어 문제를 감지한 하지 않습니다.

## <a name="internal-details-of-poa"></a>POA의 내부 세부 정보

POA는 다음과 같은 하위 구성 요소로 구성 됩니다.

- **코디네이터 서비스**: 이 상태 저장 서비스는 다음과 같은 역할을 합니다.
    - 전체 클러스터에서 Windows Update 작업을 조정합니다.
    - 완료된 Windows 업데이트 작업의 결과를 저장합니다.

- **노드 에이전트 서비스**: 이 상태 비저장 서비스는 모든 Service Fabric 클러스터 노드에서 실행됩니다. 서비스는 다음과 같은 역할을 합니다.
    - 노드 에이전트 NTService의 부트스트랩
    - 노드 에이전트 NTService의 모니터링

- **노드 에이전트 NTService**: 이 Windows NT 서비스는 더 높은 수준의 권한(시스템)에서 실행됩니다. 반면, 노드 에이전트 서비스 및 코디네이터 서비스는 하위 수준의 권한(네트워크 서비스)에서 실행됩니다. 서비스는 모든 클러스터 노드에서 다음과 같은 Windows 업데이트 작업을 수행하는 일을 담당합니다.
    - 노드에서 자동 Windows 업데이트를 사용 하지 않도록 설정
    - 사용자가 제공한 정책에 따라 Windows 업데이트를 다운로드 하 고 설치 합니다.
    - Windows 사후 업데이트 설치 후 컴퓨터를 다시 시작 하는 중입니다.
    - 코디네이터 서비스에 Windows 업데이트 결과 업로드
    - 보고 상태는 모든 재시도를 모두 사용한 후 작업이 실패 한 경우 보고 합니다.

> [!NOTE]
> POA는 Service Fabric Repair Manager 서비스를 사용 하 여 노드를 사용 하지 않도록 설정 하거나 사용 하도록 설정 하 고 상태 검사를 수행 합니다. POA에서 만든 복구 작업은 각 노드에 대 한 Windows 업데이트 진행률을 추적 합니다.

## <a name="prerequisites"></a>필수 조건

> [!NOTE]
> 필요한 최소 .NET Framework 버전은 4.6입니다.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Repair Manager 서비스를 사용 하도록 설정 합니다 (아직 실행 중이 아닌 경우).

POA를 사용 하려면 클러스터에서 Repair Manager 서비스를 사용 하도록 설정 해야 합니다.

#### <a name="azure-clusters"></a>Azure 클러스터

실버 내구성 계층의 Azure 클러스터에는 기본적으로 Repair Manager 서비스가 사용 하도록 설정 되어 있습니다. 골드 내구성 계층의 Azure 클러스터에는 해당 클러스터를 만든 시기에 따라 Repair Manager 서비스가 사용 하도록 설정 되어 있을 수도 있고 없을 수도 있습니다. 청동 내구성 계층의 Azure 클러스터에는 기본적으로 Repair Manager 서비스가 사용 하도록 설정 되어 있지 않습니다. 서비스가 이미 사용 하도록 설정 된 경우 Service Fabric Explorer의 시스템 서비스 섹션에서 실행 되는 것을 볼 수 있습니다.

##### <a name="the-azure-portal"></a>Azure 포털
클러스터를 설정할 때 Azure Portal에서 Repair Manager를 사용 하도록 설정할 수 있습니다. 클러스터를 구성 하는 경우 **추가 기능**에서 **Repair Manager 포함** 옵션을 선택 합니다.

![Azure Portal에서 Repair Manager를 사용 하도록 설정 하는 이미지](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure Resource Manager 배포 모델
또는 [Azure Resource Manager 배포 모델](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) 을 사용 하 여 새 및 기존 Service Fabric 클러스터에서 Repair Manager 서비스를 사용 하도록 설정할 수 있습니다. 배포하려는 클러스터에 대한 템플릿을 가져옵니다. 예제 템플릿을 사용하거나 사용자 지정 Azure Resource Manager 배포 모델을 만들 수 있습니다. 

[Azure Resource Manager 배포 모델 템플릿을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)사용 하 여 Repair Manager 서비스를 사용 하도록 설정 하려면 다음을 수행 합니다.

1. *ServiceFabric/클러스터* 리소스에 대해 `apiVersion`가 *2017-07-01-preview* 로 설정 되어 있는지 확인 합니다. 다른 경우 `apiVersion`를 *2017-07-01-preview* 이상으로 업데이트 해야 합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. `fabricSettings` 섹션 뒤에 다음 `addonFeatures` 섹션을 추가 하 여 Repair Manager 서비스를 사용 하도록 설정 합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 이러한 변경 내용으로 클러스터 템플릿을 업데이트 한 후에는 적용 하 고 업데이트를 완료 하도록 합니다. 이제 클러스터에서 실행 중인 Repair Manager 서비스를 볼 수 있습니다. Service Fabric Explorer의 시스템 서비스 섹션에서 *fabric:/system/RepairManagerService* 이라고 합니다. 

### <a name="standalone-on-premises-clusters"></a>독립 실행형 온-프레미스 클러스터

새 또는 기존 Service Fabric 클러스터에서 Repair Manager 서비스를 사용 하도록 설정 하려면 [독립 실행형 Windows 클러스터에 대 한 구성 설정을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)사용할 수 있습니다.

Repair Manager 서비스를 사용 하도록 설정 하려면

1. 다음과 같이 [일반 클러스터 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) 의 `apiVersion` *04-2017* 이상으로 설정 되어 있는지 확인 합니다.

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 다음과 같이 `fabricSettings` 섹션 뒤에 다음 `addonFeatures` 섹션을 추가 하 여 Repair Manager 서비스를 사용 하도록 설정 합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 업데이트 된 클러스터 매니페스트 [새 클러스터 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) 또는 [클러스터 구성 업그레이드](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server)를 사용 하 여 클러스터 매니페스트를 이러한 변경 내용으로 업데이트 합니다. 

   클러스터가 업데이트 된 클러스터 매니페스트로 실행 되 면 클러스터에서 실행 중인 Repair Manager 서비스를 볼 수 있습니다. *Fabric:/system/RepairManagerService*이라고 하며 Service Fabric Explorer의 시스템 서비스 섹션에 있습니다.

### <a name="configure-windows-updates-for-all-nodes"></a>모든 노드에 대 한 Windows 업데이트 구성

자동 Windows 업데이트를 사용 하면 여러 클러스터 노드를 동시에 다시 시작할 수 있으므로 가용성 손실이 발생할 수 있습니다. POA는 기본적으로 각 클러스터 노드에서 자동 Windows 업데이트를 사용 하지 않도록 설정 하려고 시도 합니다. 그러나 설정이 관리자 또는 그룹 정책에서 관리 되는 경우에는 Windows 업데이트 정책을 "다운로드 하기 전에 알림"으로 명시적으로 설정 하는 것이 좋습니다.

## <a name="download-the-application-package"></a>응용 프로그램 패키지 다운로드

응용 프로그램 패키지를 다운로드 하려면 GitHub의 [패치 오케스트레이션 응용 프로그램 릴리스 페이지](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) 로 이동 합니다.

## <a name="configure-poa-behavior"></a>POA 동작 구성

요구 사항에 맞게 POA 동작을 구성할 수 있습니다. 응용 프로그램을 만들거나 업데이트 하는 동안 응용 프로그램 매개 변수를 전달 하 여 기본값을 재정의 합니다. `Start-ServiceFabricApplicationUpgrade` 또는 `New-ServiceFabricApplication` cmdlet에 `ApplicationParameter`를 지정 하 여 응용 프로그램 매개 변수를 제공할 수 있습니다.

| 매개 변수        | 유형                          | 세부 정보 |
|:-|-|-|
|MaxResultsToCache    |장기                              | 캐시 해야 하는 Windows 업데이트 결과의 최대 수입니다. <br><br>기본값은 3000입니다. <br> &nbsp;&nbsp;-노드 수는 20 개입니다. <br> &nbsp;&nbsp;-월별 노드에 대 한 업데이트 수는 5 개입니다. <br> &nbsp;&nbsp;-작업당 결과 수는 10 개입니다. <br> &nbsp;&nbsp;-지난 3 개월 동안의 결과를 저장 해야 합니다. |
|TaskApprovalPolicy   |열거형 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy는 Service Fabric 클러스터 노드에서 Windows 업데이트를 설치하기 위해 코디네이터 서비스에서 사용하는 정책을 나타냅니다.<br><br>허용되는 값은 다음과 같습니다. <br>*Nodewise*: Windows update는 한 번에 하나의 노드에 설치 됩니다. <br> *Upgradedomainwise*: Windows 업데이트는 한 번에 하나의 업데이트 도메인에 설치 됩니다. (대부분의 경우 업데이트 도메인에 속하는 모든 노드에서 Windows 업데이트를 사용할 수 있습니다.)<br><br> 클러스터에 가장 적합 한 정책을 결정 하는 데 도움이 필요 하면 [FAQ](#frequently-asked-questions) 섹션을 참조 하세요.
|LogsDiskQuotaInMB   |장기  <br> (기본값: *1024*)               | 패치 오케스트레이션 앱 로그의 최대 크기 (MB)로, 노드에서 로컬로 유지 될 수 있습니다.
| WUQuery               | 문자열<br>(기본값: *Isinstalled = 0*)                | Windows 업데이트를 가져올 쿼리입니다. 자세한 내용은 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)를 참조하세요.
| InstallWindowsOSOnlyUpdates | *Boolean* <br> (기본값: false)                 | 이 플래그를 사용하여 다운로드하고 설치해야 하는 업데이트를 제어합니다. 다음 값이 허용됩니다. <br>true - Windows 운영 체제 업데이트만 설치합니다.<br>false - 컴퓨터에서 사용 가능한 모든 업데이트를 설치합니다.          |
| WUOperationTimeOutInMinutes | Int <br>(기본값: *90*)                   | Windows 업데이트 작업에 대한 시간 제한을 지정합니다(검색, 다운로드 또는 설치). 지정된 시간 제한 내에 작업이 완료되지 않으면 중단됩니다.       |
| WURescheduleCount     | Int <br> (기본값: *5*)                  | 작업이 영구적으로 실패 하는 경우 서비스에서 Windows 업데이트를 예약 하는 최대 횟수입니다.          |
| WURescheduleTimeInMinutes | Int <br>(기본값: *30*) | 오류가 계속 발생 하는 경우 서비스에서 Windows 업데이트를 예약 하는 간격입니다. |
| WUFrequency           | 쉼표로 구분 된 문자열 (기본값: *매주, 수요일, 7:00:00*)     | Windows 업데이트를 설치 하는 빈도입니다. 형식 및 가능한 값은 다음과 같습니다. <br>&nbsp;&nbsp;-매월: DD, HH: MM: SS (예: *매월, 5, 12:22:32*)<br>필드 DD (일)에 허용 되는 값은 1부터 28 까지의 숫자 이며 "last"입니다. <br> &nbsp;&nbsp;-주별, DAY, HH: MM: SS (예: *매주, 화요일, 12:22:32*)  <br> &nbsp;&nbsp;-매일, HH: MM: SS (예: *매일, 12:22:32*)  <br> &nbsp;&nbsp;-  *없음* 은 Windows 업데이트를 수행 하지 않음을 나타냅니다.  <br><br> 시간은 UTC 단위입니다.|
| AcceptWindowsUpdateEula | 부울 <br>(기본값: *true*) | 이 플래그를 설정하면 애플리케이션이 컴퓨터의 소유자를 대신하여 Windows 업데이트에 대한 최종 사용자 사용권 계약에 동의합니다.              |

> [!TIP]
> Windows 업데이트를 즉시 수행 하려면 응용 프로그램 배포 시간을 기준으로 `WUFrequency`를 설정 합니다. 예를 들어 5노드 테스트 클러스터가 있고 약 5PM UTC에 앱을 배포할 계획이라고 가정할 수 있습니다. 응용 프로그램 업그레이드 또는 배포에 30 분이 소요 된다고 가정 하면 WUFrequency을 *매일, 17:30:00*로 설정 합니다.

## <a name="deploy-poa"></a>POA 배포

1. 모든 필수 구성 요소 단계를 완료하여 클러스터를 준비합니다.
1. 다른 Service Fabric 앱과 마찬가지로 POA를 배포 합니다. PowerShell을 사용 하 여 배포 하려면 [powershell을 사용 하 여 응용 프로그램 배포 및 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)를 참조 하세요.
1. 배포 시 애플리케이션을 구성하려면 `ApplicationParameter`를 `New-ServiceFabricApplication` cmdlet에 전달합니다. 편의를 위해 Deploy.ps1 스크립트가 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

    - `Connect-ServiceFabricCluster`를 사용하여 Service Fabric 클러스터에 연결합니다.
    - 적절한 `ApplicationParameter` 값을 사용하여 PowerShell 스크립트 Deploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 및 응용 프로그램 폴더 *PatchOrchestrationApplication* 를 동일한 디렉터리에 유지 합니다.

## <a name="upgrade-poa"></a>업그레이드 POA

PowerShell을 사용 하 여 POA 버전을 업그레이드 하려면 [powershell을 사용 하 여 응용 프로그램 업그레이드 Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)의 지침을 따르세요.

## <a name="remove-poa"></a>POA 제거

응용 프로그램을 제거 하려면 [PowerShell을 사용 하 여 응용 프로그램 배포 및 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)의 지침을 따르세요.

편의를 위해 응용 프로그램과 함께 배포 취소 ps1 스크립트를 제공 했습니다. 스크립트를 사용하려면 다음을 수행합니다.

  - ```Connect-ServiceFabricCluster```를 사용하여 Service Fabric 클러스터에 연결합니다.
  - PowerShell 스크립트 Undeploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 및 응용 프로그램 폴더 *PatchOrchestrationApplication* 를 동일한 디렉터리에 유지 합니다.

## <a name="view-the-windows-update-results"></a>Windows 업데이트 결과 보기

POA는 사용자에 게 기록 결과를 표시 하는 REST Api를 노출 합니다. 결과 JSON의 예는 다음과 같습니다.

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

JSON 필드는 다음 표에 설명 되어 있습니다.

필드 | 값 | 세부 정보
-- | -- | --
OperationResult | 0 - 성공<br> 1 - 성공하였으나 오류 발생<br> 2 - 실패<br> 3 - 중단<br> 4 - 시간 제한으로 중단 | 일반적으로 하나 이상의 업데이트를 설치 하는 작업을 포함 하는 전체 작업의 결과를 나타냅니다.
ResultCode | OperationResult와 동일 | 이 필드는 개별 업데이트에 대 한 설치 작업의 결과를 나타냅니다.
OperationType | 1 - 설치<br> 0-검색 및 다운로드| 기본적으로 설치는 결과에 표시 되는 유일한 OperationType입니다.
WindowsUpdateQuery | 기본값은 “IsInstalled = 0”입니다. | 업데이트를 검색 하는 데 사용 된 Windows 업데이트 쿼리입니다. 자세한 내용은 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)를 참조 하세요.
RebootRequired | true - 다시 부팅 필요<br> false - 다시 부팅 필요 없음 | 업데이트 설치를 완료 하는 데 다시 부팅이 필요한 지 여부를 나타냅니다.
OperationStartTime | DateTime | 작업 (다운로드/설치)이 시작 된 시간을 나타냅니다.
OperationTime | DateTime | 작업 (다운로드/설치)이 완료 된 시간을 나타냅니다.
HResult | 0-성공<br> 기타-오류| UpdateID "7392acaf-6a85-427c-8a8d-058c25beb0d6"을 사용 하 여 Windows 업데이트가 실패 한 이유를 나타냅니다.

업데이트가 아직 예약되어 있지 않으면 결과 JSON은 비어 있습니다.

클러스터에 로그인 하 Windows 업데이트 결과를 쿼리 합니다. 코디네이터 서비스의 기본 주소에 대 한 복제본 IP 주소를 확인 하 고 브라우저에서 다음 URL을 엽니다. http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

코디네이터 서비스의 REST 엔드포인트에는 동적 포트가 있습니다. 정확한 URL을 확인 하려면 Service Fabric Explorer를 참조 하세요. 예를 들어 *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* 에서 결과를 사용할 수 있습니다.

![REST 엔드포인트의 이미지](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

클러스터에서 역방향 프록시를 사용 하는 경우 클러스터 외부에서 URL에도 액세스할 수 있습니다.

적중 해야 하는 엔드포인트은 *http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*입니다.

클러스터에서 역방향 프록시를 사용 하도록 설정 하려면 [Azure의 역방향 프록시 Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)에 있는 지침을 따르세요. 

> 
> [!WARNING]
> 역방향 프록시가 구성 된 후에는 HTTP 엔드포인트을 노출 하는 클러스터의 모든 마이크로 서비스를 클러스터 외부에서 주소를 지정할 수 있습니다.

## <a name="diagnostics-and-health-events"></a>진단 및 상태 이벤트

이 섹션에서는 Service Fabric 클러스터에서 POA를 통해 패치 업데이트 관련 문제를 디버그 하거나 진단 하는 방법에 대해 설명 합니다.

> [!NOTE]
> 다음과 같은 여러 가지 호출 된 자체 진단 기능을 제공 하기 위해 1.4.0 이상 버전을 설치 해야 합니다.

노드 에이전트 NTService는 노드에 업데이트를 설치 하기 위한 [복구 작업](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) 을 만듭니다. 그런 다음 작업 승인 정책에 따라 코디네이터 서비스에서 각 작업을 준비 합니다. 마지막으로, Repair Manager에서 준비 된 작업을 승인 하며, 클러스터가 비정상 상태인 경우에는 작업을 승인 하지 않습니다. 

노드에서 업데이트가 진행 되는 방식을 이해 하는 데 도움이 되도록 다음 단계를 수행해 보겠습니다.

1. NodeAgentNTService는 모든 노드에서 실행 되며 예약 된 시간에 사용 가능한 Windows 업데이트를 찾습니다. 업데이트를 사용할 수 있는 경우 해당 업데이트를 노드에서 다운로드 합니다.

1. 업데이트를 다운로드 한 후 노드 에이전트 NTService는 *POS___\<unique_id >* 이름으로 노드에 대 한 해당 복구 작업을 만듭니다. [ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) cmdlet을 사용 하거나 노드 세부 정보 섹션에서 SFX를 사용 하 여 이러한 복구 작업을 볼 수 있습니다. 복구 작업을 만든 후에는 [ *요청* 된 상태로](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)신속 하 게 이동 합니다.

1. 코디네이터 서비스는 주기적으로 *요청* 된 상태에서 복구 작업을 찾은 다음 TaskApprovalPolicy에 따라 상태를 *준비* 하도록 업데이트 합니다. TaskApprovalPolicy가 NodeWise로 구성 된 경우에는 현재 준비, *승인 됨*, *실행* *중*또는 *복원* 중인 다른 복구 작업이 없는 경우에만 노드에 해당 하는 복구 작업이 준비 됩니다. 

   마찬가지로 UpgradeWise TaskApprovalPolicy의 경우 동일한 업데이트 도메인에 속하는 노드에 대해서만 위의 상태에 있는 태스크가 있습니다. 복구 작업이 상태 *준비* 로 이동 된 후에는 해당 하는 Service Fabric 노드가 *다시 시작*되도록 설정 된 상태로 [사용 하지 않도록](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) 설정 됩니다.

   1\.4.0 나중에 ClusterPatchingStatus 속성을 사용 하 여 이벤트를 게시 하 여 패치 중인 노드를 표시 합니다. 업데이트는 다음 이미지와 같이 _poanode_0에 설치 됩니다.

    [클러스터 패치 상태의 ![이미지](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. 노드를 사용 하지 않도록 설정 하면 복구 작업이 *실행* 중 상태로 이동 합니다. 
   
   > [!NOTE]
   > *비활성화* 상태에서 중단 된 노드는 클러스터에서 패치 작업을 중단 하는 새 복구 작업을 차단할 수 있습니다.

1. 복구 작업이 *실행* 중 상태 이면 해당 노드에 대 한 패치 설치가 시작 됩니다. 패치를 설치 하 고 나면 패치에 따라 노드가 다시 시작 되거나 다시 시작 되지 않을 수 있습니다. 그런 다음 복구 작업을 *복원* 상태로 이동 하 여 노드를 다시 합니다. 그런 다음 복구 작업이 완료로 표시 됩니다.

   POA 버전 1.4.0 이상에서는 WUOperationStatus\<NodeName > 속성을 사용 하 여 NodeAgentService에서 상태 이벤트를 확인 하 여 업데이트 상태를 찾을 수 있습니다. 다음 이미지의 강조 표시 된 섹션에서는 *poanode_0* 및 *poanode_2*노드의 Windows 업데이트 상태를 보여 줍니다.

   [Windows 업데이트 작업 상태의 ![이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [Windows 업데이트 작업 상태의 ![이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   PowerShell을 사용 하 여 세부 정보를 가져올 수도 있습니다. 이렇게 하려면 클러스터에 연결 하 고 [ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)을 사용 하 여 복구 작업의 상태를 가져옵니다. 
   
   다음 예제에서 "POS__poanode_2_125f2969-933c-4774-ebdf85e79f15" 작업은 *Downloadcomplete* 상태에 있습니다. 즉, *poanode_2* 노드에 업데이트가 다운로드 되었으며, 작업을 *실행* 중 상태로 이동할 때 설치를 시도 합니다.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   문제가 계속 발생 하면 VM (가상 머신)에 로그인 하 고 Windows 이벤트 로그를 사용 하 여 해당 vm에 대해 알아보세요. 앞에서 언급 한 복구 작업은 다음 executor 하위 작업에만 존재할 수 있습니다.

      ExecutorSubState 방법 | Description
    -- | -- 
      없음 = 1 |  는 노드에서 진행 중인 작업이 없음을 의미 합니다. 상태가 전환 중일 수 있습니다.
      DownloadCompleted = 2 | 다운로드 작업이 성공, 부분 실패 또는 실패와 함께 완료 되었음을 의미 합니다.
      승인 된 설치 = 3 | 다운로드 작업을 이전에 완료 했으며 Repair Manager 설치를 승인한 것을 의미 합니다.
      설치 Inprogress = 4 | 복구 작업의 실행 상태에 해당 합니다.
      설치 완료 됨 = 5 | 설치 완료 성공, 부분 성공 또는 실패를 의미 합니다.
      RestartRequested=6 | 패치 설치가 완료 되었으며 노드에 보류 중인 다시 시작 작업이 있음을 의미 합니다.
      RestartNotNeeded=7 |  패치 설치가 완료 된 후 다시 시작이 필요 하지 않음을 의미 합니다.
      RestartCompleted = 8 | 다시 시작이 성공적으로 완료 되었음을 의미 합니다.
      OperationCompleted = 9 | Windows 업데이트 작업이 성공적으로 완료 되었습니다.
      OperationAborted=10 | Windows 업데이트 작업이 중단 되었음을 나타냅니다.

1. POA 버전 1.4.0 이상에서 노드 업데이트 시도가 완료 되 면, 다음에 Windows 업데이트를 다운로드 하 고 설치 하는 시도가 시작 될 때 알리도록 "WUOperationStatus-[NodeName]" 속성이 포함 된 이벤트가 NodeAgentService에 게시 됩니다. 다음 그림에 표시 됩니다.

     [Windows 업데이트 작업 상태의 ![이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>진단 로그

패치 오케스트레이션 응용 프로그램 로그는 Service Fabric 런타임 로그의 일부로 수집 됩니다.

선택한 진단 도구나 파이프라인을 사용 하 여 로그를 캡처할 수 있습니다. POA는 다음과 같은 고정 된 공급자 Id를 사용 하 여 [이벤트 소스](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)를 통해 이벤트를 기록 합니다.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>상태 보고서

또한 POA는 다음과 같은 시나리오에서 노드 에이전트 서비스 또는 코디네이터 서비스에 대 한 상태 보고서를 게시 합니다.

* 노드 에이전트 NTService 중단

   노드에서 노드 에이전트 NTService가 중단되면 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다.

* Repair Manager 서비스를 사용할 수 없습니다.

   클러스터에 Repair Manager 서비스가 없는 경우 코디네이터 서비스에 대해 경고 수준 상태 보고서가 생성 됩니다.

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

**Q: POA가 실행 중일 때 내 클러스터가 오류 상태로 표시 되는 이유는 무엇 인가요?**

A: 설치 프로세스가 진행 되는 동안 POA는 노드를 사용 하지 않도록 설정 하거나 다시 시작 하 여 일시적으로 비정상 클러스터를 발생 시킬 수 있습니다.

응용 프로그램 정책에 따라 패치 작업 중에 하나의 노드가 다운 *되거나* 전체 업데이트 도메인은 한 번에 모두 다운 될 수 있습니다.

Windows 업데이트 설치가 끝날 때까지 노드는 다시 시작 후에 했다가 다시 설정할 됩니다.

다음 예제에서는 두 노드가 중단되고 MaxPercentageUnhealthyNodes 정책이 위반되었으므로 클러스터가 일시적으로 오류 상태가 되었습니다. 패치 작업을 시작할 수 있을 때까지 오류는 일시적인 오류입니다.

![비정상 클러스터의 이미지](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

문제가 계속되는 경우 문제 해결 섹션을 참조하세요.

**Q: POA가 경고 상태 이면 어떻게 해야 하나요?**

A: 응용 프로그램에 대해 게시 된 상태 보고서가 근본 원인을 표시 하는지 확인 합니다. 일반적으로 경고에는 문제에 대한 세부 정보가 포함되어 있습니다. 일시적인 문제인 경우 응용 프로그램은 자동으로 복구 될 것으로 예상 됩니다.

**Q: 클러스터가 비정상이 고 긴급 한 운영 체제 업데이트를 수행 해야 하는 경우 어떻게 해야 하나요?**

A: POA는 클러스터가 비정상 상태인 동안 업데이트를 설치 하지 않습니다. 클러스터를 정상 상태로 전환 하 여 POA 워크플로 차단을 해제 해 보세요.

**Q: 내 클러스터에 대해 TaskApprovalPolicy를 "NodeWise" 또는 "UpgradeDomainWise"로 설정 해야 하나요?**

A: "Upgradedomainstststststststststststststststup" 설정은 업데이트 도메인에 속하는 모든 노드를 병렬로 패치 하 여 전체 클러스터 복구 속도를 이 과정에서 전체 업데이트 도메인에 속하는 노드는 사용할 수 없습니다 ( [ *비활성화* 상태](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

이와 대조적으로 "NodeWise" 설정은 한 번에 하나의 노드만 패치 하며,이는 전체 클러스터 패치가 더 오래 걸릴 수 있음을 의미 합니다. 그러나 패치를 처리 하는 동안에는 최대 하나의 노드만 사용할 수 없게 됩니다 ( *비활성화* 상태).

클러스터가 패치 주기 동안 N-1 개의 업데이트 도메인에서 실행 되도록 허용할 수 있는 경우 (여기서 N은 클러스터에 있는 총 업데이트 도메인 수) 정책을 "UpgradeDomainWise"로 설정할 수 있습니다. 그렇지 않으면 "NodeWise"로 설정 합니다.

**Q: 노드를 패치 하는 데 소요 되는 시간은 얼마 인가요?**

A: 노드 패치는 시간 (예: windows [누적 업데이트](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update))에서 몇 분 (예: [windows Defender 정의 업데이트](https://www.microsoft.com/en-us/wdsi/definitions))까지 걸릴 수 있습니다. 노드를 패치 하는 데 필요한 시간은 주로 다음에 따라 달라 집니다. 
 - 업데이트 크기입니다.
 - 패치 창에 적용 해야 하는 업데이트의 수입니다.
 - 업데이트를 설치 하는 데 걸리는 시간으로, 노드를 다시 부팅 하 고 (필요한 경우) 다시 부팅 후 설치 단계를 완료 합니다.
 - VM 또는 컴퓨터의 성능 및 네트워크 상태입니다.

**Q: 전체 클러스터를 패치 하는 데 얼마나 걸립니까?**

A: 전체 클러스터를 패치 하는 데 필요한 시간은 다음에 따라 달라 집니다.

- 노드를 패치 하는 데 필요한 시간입니다.

- 코디네이터 서비스 정책 기본 정책 "NodeWise"은 한 번에 하나의 노드만 패치 하 고 "UpgradeDomainWise"를 사용 하는 것 보다 속도가 느립니다. 

   예를 들어 노드가 패치를 적용 하는 데 ~ 1 시간을 사용 하는 경우, 각각 4 개의 노드를 포함 하는 5 개의 업데이트 도메인을 포함 하는 20 개 노드 (동일한 유형의 노드) 클러스터에 패치를 적용 하려면
    - "NodeWise": ~ 20 시간
    - "UpgradeDomainWise": ~ 5 시간

- 클러스터 로드입니다. 각 패치 작업을 수행 하려면 고객 워크 로드를 클러스터의 다른 사용 가능한 노드로 재배치 해야 합니다. 패치가 적용 되는 노드는이 시간 동안 [ *비활성화* 상태](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) 입니다. 클러스터가 거의 최대 부하를 실행 하는 경우 비활성화 프로세스는 더 오래 걸립니다. 따라서 전반적인 패치 프로세스는 이러한 과도 한 상황에서 속도가 느려지는 것 처럼 보일 수 있습니다.

- 패치 중 클러스터 상태 오류입니다. [클러스터의 상태가](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) [저하](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) 되 면 패치 프로세스가 중단 됩니다. 이 문제는 전체 클러스터를 패치 하는 데 필요한 전체 시간에 추가 됩니다.

**Q: REST API Windows 업데이트를 통해 가져온 Windows 업데이트 결과에 일부 업데이트가 표시 되는 이유는 무엇 인가요?**

A: 일부 제품 업데이트는 자체 업데이트 또는 패치 기록에만 표시 됩니다. 예를 들어 windows Defender 업데이트는 Windows Server 2016의 Windows 업데이트 기록에 표시 되거나 표시 되지 않을 수 있습니다.

**Q: 내 개발 클러스터 (단일 노드 클러스터)를 패치 하는 데 사용할 수 있나요?**

A: 아니요, POA는 단일 노드 클러스터를 패치 하는 데 사용할 수 없습니다. 이러한 제한은 [Service Fabric 시스템 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) 또는 기타 고객 응용 프로그램에서 가동 중지 시간이 발생 하기 때문에 의도 된 것입니다. 따라서 복구 작업 패치는 Repair Manager에서 승인 되지 않습니다.

**Q: Linux에서 클러스터 노드를 어떻게 할까요? 패치 하는 방법**

A: Linux의 오케스트레이션 업데이트에 대해 알아보려면 [Azure 가상 머신 확장 집합 자동 OS 이미지 업그레이드](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)를 참조 하세요.

**Q: 업데이트 주기가 오래 걸리는 이유는 무엇 인가요?**

A: 결과 JSON에 대 한 쿼리를 실행 하 고, 모든 노드에 대 한 업데이트 주기를 입력 한 다음 OperationStartTime 및 Operationstarttime (Operationstarttime)을 사용 하 여 모든 노드에서 업데이트 설치에 소요 된 시간을 확인할 수 있습니다. 

업데이트가 수행 되지 않는 기간이 긴 경우 클러스터는 오류 상태에 있을 수 있으므로 POA 복구 작업을 승인할 수 Repair Manager. 모든 노드에서 업데이트 설치에 오랜 시간이 걸리는 경우이 노드가 잠시 동안 업데이트 되지 않았을 수 있습니다. 많은 업데이트가 설치 보류 중 이어서 지연이 발생할 수 있습니다. 

상태를 *비활성화* 하는 중이기 때문에 노드 패치가 차단 될 수도 있습니다. 이 문제는 일반적으로 노드를 사용 하지 않도록 설정 하면 쿼럼 또는 데이터 손실이 발생할 수 있기 때문에 발생 합니다.

**Q: POA가 패치를 적용할 때 노드를 사용 하지 않도록 설정 해야 하는 이유는 무엇 인가요?**

A: POA는 *다시 시작* 의도를 사용 하 여 노드를 사용 하지 않도록 설정 하 여 노드에서 실행 중인 모든 Service Fabric 서비스를 중지 하거나 다시 할당 합니다. POA는 응용 프로그램이 새로운 Dll과 이전 Dll을 혼합 하 여 사용 하지 않도록 하기 위해이 작업을 수행 하므로 노드를 사용 하지 않도록 설정 하지 않고 다시 패치 하지 않는 것이 좋습니다.

## <a name="disclaimers"></a>고지 사항

- POA는 사용자를 대신 하 여 Windows 업데이트에 대 한 최종 사용자 사용권 계약을 수락 합니다. 필요에 따라 애플리케이션 구성에서 설정을 해제할 수 있습니다.

- POA는 사용량 및 성능을 추적 하기 위해 원격 분석을 수집 합니다. 애플리케이션의 원격 분석은 Service Fabric 런타임의 원격 분석 설정을 따릅니다(기본적으로 설정됨).

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 패치 노드 문제에 대 한 가능한 문제 해결 솔루션을 제공 합니다.

### <a name="a-node-is-not-coming-back-to-up-state"></a>노드가 활성 상태로 전환되지 않음

* 노드는 다음과 같은 이유로 *비활성화* 상태에서 멈출 수 있습니다.

  - 안전 검사가 보류 중입니다. 이 문제를 해결하려면 정상 상태에서 충분히 노드를 사용할 수 있는지 확인합니다.

* 노드는 다음과 같은 이유로 *비활성화* 상태에서 멈출 수 있습니다.

  - 수동으로 사용 하지 않도록 설정 되었습니다.
  - 진행 중인 Azure 인프라 작업으로 인해 사용 하지 않도록 설정 되었습니다.
  - 이는 노드를 패치 하는 데 일시적으로 사용 하지 않도록 설정 되었습니다.

* 노드는 다음과 같은 이유로 중단 상태에 멈춰 있을 수 있습니다.

  - 중단 된 상태에서 수동으로 배치 되었습니다.
  - 다시 시작 하는 중입니다 (POA에 의해 트리거될 수 있음).
  - VM 또는 컴퓨터가 잘못 되었거나 네트워크 연결에 문제가 있는 것 같습니다.

### <a name="updates-were-skipped-on-some-nodes"></a>일부 노드에서 업데이트를 건너뛰었습니다.

POA는 다시 예약 정책에 따라 Windows 업데이트를 설치 하려고 시도 합니다. 이 서비스는 노드를 복구하고 애플리케이션 정책에 따라 업데이트를 건너뛰려고 합니다.

이러한 경우에 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다. Windows 업데이트 결과에는 가능한 오류 이유가 포함 됩니다.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>업데이트가 설치 되는 동안 클러스터의 상태가 오류로 이동 합니다.

잘못 된 Windows 업데이트는 특정 노드 또는 업데이트 도메인에서 응용 프로그램 또는 클러스터의 상태를 되돌릴 수 있습니다. POA 중단는 클러스터가 다시 정상 상태가 될 때까지 후속 Windows 업데이트 작업을 수행 합니다.

관리자는 Windows 업데이트으로 인해 응용 프로그램 또는 클러스터가 비정상 상태가 된 이유를 확인 해야 합니다.

## <a name="poa-release-notes"></a>POA 릴리스 정보

>[!NOTE]
> POA 버전 1.4.0 이상에서는 GitHub의 [패치 오케스트레이션 응용 프로그램 릴리스 페이지](https://github.com/microsoft/Service-Fabric-POA/releases/) 에서 릴리스 정보 및 릴리스를 찾을 수 있습니다.

### <a name="version-110"></a>버전 1.1.0
- 공개 릴리스

### <a name="version-111"></a>버전 1.1.1
- NodeAgentNTService의 설치를 방지하는 NodeAgentService의 SetupEntryPoint에서 버그 수정

### <a name="version-120"></a>버전 1.2.0

- 시스템 다시 시작 워크플로 관련 버그 수정
- 복구 중 상태 확인이 예상 대로 작동하지 않는 문제로 인해 RM 작업 생성의 버그 수정
- Windows 서비스 POANodeSvc 시작 모드가 자동에서 지연-자동으로 변경 되었습니다.

### <a name="version-121"></a>버전 1.2.1

- 클러스터 축소 워크플로의 버그 수정입니다. 존재하지 않는 노드에 속하는 POA 복구 태스크에 대해 가비지 수집 논리가 도입되었습니다.

### <a name="version-122"></a>버전 1.2.2

- 기타 버그가 수정되었습니다.
- 이제 이진 파일이 서명됩니다.
- 애플리케이션에 대한 sfpkg 링크가 추가되었습니다.

### <a name="version-130"></a>버전 1.3.0

- 이제 InstallWindowsOSOnlyUpdates를 False로 설정하면 사용 가능한 모든 업데이트가 설치됩니다.
- 자동 업데이트를 사용하지 않도록 설정하는 논리가 변경되었습니다. 이로 인해 Server 2016 이상에서 자동 업데이트가 사용하지 않도록 설정되지 않는 버그가 수정됩니다.
- 고급 사용 사례에 대 한 POA의 마이크로 서비스에 대 한 매개 변수가 있는 배치 제약 조건입니다.

### <a name="version-131"></a>버전 1.3.1
- 자동 업데이트를 사용 하지 않도록 설정 하는 동안 오류가 발생 하 여 POA 1.3.0가 Windows Server 2012 R2 또는 이전 버전에서 작동 하지 않는 재발 문제를 수정 합니다. 
- InstallWindowsOSOnlyUpdates 구성이 항상 True로 선택되는 버그 수정
- InstallWindowsOSOnlyUpdates의 기본값을 False로 변경

### <a name="version-132"></a>버전 1.3.2
- 현재 노드 이름의 하위 집합인 이름을 가진 노드가 있는 경우 노드의 패치 수명 주기에 영향을 주는 문제를 해결 합니다. 이러한 노드의 경우 패치가 누락 되었거나 다시 부팅이 보류 중일 수 있습니다.