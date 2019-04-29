---
title: 업데이트 관리, 변경 내용 추적 및 인벤토리 온보딩 오류 문제 해결
description: 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션에 관한 온보딩 오류 문제를 해결하는 방법을 알아봅니다.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: eaafee304f606ae4d511a6cea1824c26db838635
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119132"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>솔루션을 온보딩할 때 발생하는 오류 문제 해결

업데이트 관리나 변경 내용 추적 및 인벤토리와 같은 솔루션을 온보딩할 때 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이것을 해결하는 방법을 설명합니다.

## <a name="general-errors"></a>일반 오류

### <a name="missing-write-permissions"></a>시나리오: 온보딩이 실패하고 솔루션을 사용할 수 없다는 메시지가 표시됨

#### <a name="issue"></a>문제

하려고 할 때 온 보 딩 솔루션에 가상 머신을 다음 메시지 중 하나가 표시 됩니다.

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>원인

이 오류는 작업 영역에서 가상 컴퓨터 또는 사용자에 대 한 잘못 되었거나 누락 된 사용 권한으로 발생 합니다.

#### <a name="resolution"></a>해결 방법

가상 머신 온보딩을 위한 권한이 올바른지 확인합니다. [가상 머신을 온보딩하는 데 필요한 권한](../automation-role-based-access-control.md#onboarding)을 검토한 후에 솔루션을 다시 온보딩해 보세요. 오류가 발생 하는 경우 `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, 했는지를 `Microsoft.OperationalInsights/workspaces/read` 권한을 VM이 작업 영역에 등록 하는 경우를 찾을 수 있습니다.

### <a name="computer-group-query-format-error"></a>시나리오: ComputerGroupQueryFormatError

#### <a name="issue"></a>문제

이 오류 코드는 솔루션을 대상으로 사용되는 저장된 검색 컴퓨터 그룹 쿼리 형식이 올바르지 않음을 의미합니다. 

#### <a name="cause"></a>원인

사용자가 쿼리를 변경했거나 시스템에서 변경되었을 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 솔루션에 대한 쿼리를 삭제하고, 솔루션을 다시 온보딩하여 쿼리를 다시 만들 수 있습니다. 쿼리를 작업 영역 내의 **저장된 검색**에서 찾을 수 있습니다. 쿼리의 이름은 **MicrosoftDefaultComputerGroup**이고, 쿼리의 범주는 이 쿼리와 연결된 솔루션의 이름입니다. 여러 솔루션이 사용되도록 설정되면 **MicrosoftDefaultComputerGroup**이 **저장된 검색** 아래에 여러 번 표시됩니다.

### <a name="policy-violation"></a>시나리오: PolicyViolation

#### <a name="issue"></a>문제

이 오류 코드는 하나 이상의 정책 위반으로 인해 배포가 실패했음을 의미합니다.

#### <a name="cause"></a>원인 

작업이 완료될 수 없는 위치에 정책이 있습니다.

#### <a name="resolution"></a>해결 방법

솔루션을 성공적으로 배포하려면 표시된 정책을 변경하는 것이 좋습니다. 정의할 수 있는 정책 유형이 다양하기 때문에 필요한 특정 변경 사항은 위반된 정책에 따라 달라집니다. 예를 들어 리소스 그룹 내 특정 유형의 리소스 콘텐츠를 변경할 수 있는 권한을 거부한 리소스 그룹에 정책이 정의된 경우, 예를 들면 다음과 같은 조치를 취할 수 있습니다.

* 정책을 모두 제거합니다.
* 다른 리소스 그룹에 등록을 시도합니다.
* 정책을 수정합니다. 예를 들면 다음과 같이 수정합니다.
  * 정책의 대상을 특정 리소스(예: 특정 Automation 계정)로 다시 지정합니다.
  * 정책이 거부하도록 구성된 리소스 집합을 수정합니다.

Azure portal의 오른쪽 위 모서리에서 알림을 확인 또는 선택한 automation 계정에 포함 된 리소스 그룹으로 이동 **배포** 아래에서 **설정** 장애를 보려면 배포 합니다. Azure Policy에 대해 자세히 알아보려면 [Azure Policy 개요](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)를 참조하세요.

## <a name="mma-extension-failures"></a>MMA 확장 오류

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

솔루션을 배포하는 경우 다양한 관련 리소스가 배포됩니다. Microsoft Monitoring Agent 확장이나 Linux용 Log Analytics 에이전트는 이러한 리소스 중 하나입니다. 이진 파일의 다운로드 이후 조정 하기 위해 구성 된 Log Analytics 작업 영역을 사용 하 여 통신을 담당 하는 가상 컴퓨터의 게스트 에이전트에서 설치 하는 가상 머신 확장 및 기타 파일을 여러분이 솔루션 실행이 시작 되 면 온 보 딩이에 따라 다릅니다.
일반적으로 알림 허브에 나타나는 알림을 통해 MMA 또는 Linux용 Log Analytics 에이전트 설치 실패를 먼저 인식하게 됩니다. 알림을 클릭하면 특정 실패에 대한 자세한 정보가 제공됩니다. 리소스 그룹 리소스로 이동한 다음, 여기에 포함된 배포 요소로 이동하면 배포 실패에 대한 자세한 내용이 제공됩니다.
MMA나 Linux용 Log Analytics 에이전트 설치는 다양한 이유로 인해 실패할 수 있으며 이러한 실패를 처리하는 단계는 문제에 따라 달라집니다. 구체적인 문제 해결 단계가 이어집니다.

다음 섹션에서는 온 보 딩 하는 오류를 발생 시킬 MMA 확장 배포의 경우에서 발생할 수 있는 다양 한 문제를 설명 합니다.

### <a name="webclient-exception"></a>시나리오: WebClient 요청 중 예외가 발생했습니다.

가상 머신의 MMA 확장이 외부 리소스와 통신할 수 없으며 배포가 실패합니다.

#### <a name="issue"></a>문제

다음은 반환되는 오류 메시지의 예입니다.

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>원인

이러한 오류의 잠재적인 원인:

* 특정 포트만 허용 하는 VM에서 구성 된 프록시가 있습니다.

* 방화벽 설정이 필요한 포트 및 주소에 대한 액세스를 차단했습니다.

#### <a name="resolution"></a>해결 방법

통신을 위해 적절한 포트와 주소가 열려 있는지 확인합니다. 포트와 주소 목록은 [네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

### <a name="transient-environment-issue"></a>시나리오: 일시적인 환경 문제로 인해 설치가 실패 했습니다.

다른 설치 또는 설치를 차단 하는 작업으로 인해 배포 하는 동안 Microsoft Monitoring Agent 확장을 설치 하지 못했습니다.

#### <a name="issue"></a>문제

다음은 반환될 수 있는 오류 메시지의 예입니다.

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>원인

이러한 오류의 잠재적인 원인:

* 다른 설치 프로그램이 실행 중입니다.
* 시스템은 템플릿 배포 하는 동안 다시 부팅 트리거됩니다.

#### <a name="resolution"></a>해결 방법

이 오류는 본질적으로 일시적인 문제입니다. 배포를 다시 시도하여 확장 프로그램을 설치하십시오.

### <a name="installation-timeout"></a>시나리오: 설치 시간 제한

MMA 확장 설치가 시간 제한으로 인해 완료 되지 않았습니다.

#### <a name="issue"></a>문제

다음 예제에서는 반환 될 수 있는 오류 메시지입니다.

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>원인

이 오류가 발생 하기 때문에 설치 하는 동안 부하가 사용 되는 가상 컴퓨터.

### <a name="resolution"></a>해결 방법

VM의 부하가 낮을 때 MMA 확장을 설치합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
