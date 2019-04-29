---
title: 여러 Azure 가상 머신에 대한 업데이트 관리
description: 이 문서에서는 Azure 가상 머신에 대한 업데이트를 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 16fe2d23fdd07f8f150cc010b0a1d232c761c77f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300100"
---
# <a name="manage-updates-for-multiple-machines"></a>여러 컴퓨터의 업데이트 관리

업데이트 관리 솔루션을 사용하여 Windows 및 Linux 가상 머신의 업데이트 및 패치를 관리할 수 있습니다. [Azure Automation](automation-offering-get-started.md) 계정에서 다음을 수행할 수 있습니다.

- 가상 머신 등록
- 사용 가능한 업데이트의 상태 평가
- 필수 업데이트의 설치 예약
- 배포 결과를 검토하여 업데이트 관리를 사용하는 모든 가상 머신에 업데이트가 성공적으로 적용되었는지 확인

## <a name="prerequisites"></a>필수 조건

업데이트 관리를 사용하려면 다음이 필요합니다.

- 지원되는 운영 체제 중 하나가 설치된 가상 머신 또는 컴퓨터

## <a name="supported-operating-systems"></a>지원되는 운영 체제

업데이트 관리를 지원하는 운영 체제는 다음과 같습니다.

|운영 체제  |메모  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | 업데이트 평가만 지원합니다.         |
|Windows Server 2008 R2 SP1 이상     |Windows PowerShell 4.0 이상이 필요합니다. ([WMF 4.0 다운로드](https://www.microsoft.com/download/details.aspx?id=40855))</br> Windows PowerShell 5.1은 안정성 개선을 위해 필요합니다. ([WMF 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04 LTS(x86/x64)      |Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.         |

> [!NOTE]
> Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 방지하려면 자동 업데이트를 사용하지 않도록 Unattended-Upgrade 패키지를 다시 구성합니다. 자세한 내용은 [Ubuntu Server의 자동 업데이트 항목 가이드](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)를 참조하세요.

Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.

이 솔루션은 여러 Azure Log Analytics 작업 영역에 보고하도록 구성된 Linux용 Log Analytics 에이전트를 지원하지 않습니다.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Azure 가상 머신에 대한 업데이트 관리 사용

Azure Portal에서 Automation 계정을 연 후 **업데이트 관리**를 선택합니다.

**Azure VM 추가**를 선택합니다.

![Azure VM 추가 탭](./media/manage-update-multi/update-onboard-vm.png)

등록할 가상 머신을 선택합니다. 

**업데이트 관리 사용** 아래에서 **사용**을 선택하여 가상 머신을 온보딩합니다.

![업데이트 관리 사용 대화 상자](./media/manage-update-multi/update-enable.png)

온보딩이 완료되면 가상 머신에 대한 업데이트 관리를 사용합니다.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>비 Azure 가상 머신 및 컴퓨터에 대한 업데이트 관리 사용

비 Azure Windows 가상 머신 및 컴퓨터에 대 한 업데이트 관리를 사용 하는 방법에 알아보려면 참조 [Azure에서 Azure Monitor 서비스에 연결 하는 Windows 컴퓨터](../log-analytics/log-analytics-windows-agent.md)합니다.

비 Azure Linux 가상 머신 및 컴퓨터에 대 한 업데이트 관리를 사용 하는 방법에 알아보려면 참조 [Azure Monitor 로그에 Linux 컴퓨터 연결](../log-analytics/log-analytics-agent-linux.md)합니다.

## <a name="view-computers-attached-to-your-automation-account"></a>Automation 계정에 연결된 컴퓨터 보기

컴퓨터에 대한 업데이트 관리를 사용하도록 설정한 후에 **컴퓨터**를 선택하여 컴퓨터 정보를 볼 수 있습니다. 컴퓨터에 대해 *컴퓨터 이름*, *준수 상태*, *환경*, *OS 유형*, *설치된 중요 및 보안 업데이트*, *설치된 기타 업데이트* 및 *업데이트 에이전트 준비* 정보를 확인할 수 있습니다.

  ![컴퓨터 보기 탭](./media/manage-update-multi/update-computers-tab.png)

최근에 업데이트 관리가 설정된 컴퓨터는 아직 평가를 받지 않았을 수도 있습니다. 이러한 컴퓨터의 준수 상태는 **평가되지 않음**입니다. 다음은 규정 준수 상태의 가능한 값 목록입니다.

- **준수**: 중요 또는 보안 업데이트가 누락되지 않은 컴퓨터입니다.

- **비준수**: 중요 또는 보안 업데이트가 하나 이상 누락된 컴퓨터입니다.

- **평가되지 않음**: 예상 기간 내에 컴퓨터의 업데이트 평가 데이터를 받지 못했습니다. Linux 컴퓨터의 경우, 예상 시간 범위는 지난 3시간입니다. Windows 컴퓨터의 경우, 예상 시간 범위는 지난 12시간입니다.

에이전트의 상태를 보려면 **업데이트 에이전트 준비** 열에서 링크를 선택합니다. 이 옵션을 선택하면 **Hybrid Worker** 창이 열리고 Hybrid Worker의 상태가 표시됩니다. 다음 그림에서는 확대된 시간 동안 업데이트 관리에 연결되지 않은 에이전트의 예제를 보여 줍니다.

![컴퓨터 보기 탭](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>업데이트 평가 보기

업데이트 관리를 사용하도록 설정하면 **업데이트 관리** 창이 열립니다. **누락된 업데이트** 탭에서 누락된 업데이트 목록을 볼 수 있습니다.

## <a name="collect-data"></a>데이터 수집

가상 머신 및 컴퓨터에 설치된 에이전트에서 업데이트에 대한 데이터를 수집합니다. 그런 후 에이전트는 해당 데이터를 Azure 업데이트 관리로 보냅니다.

### <a name="supported-agents"></a>지원되는 에이전트

다음 표에서는 솔루션이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |예 |업데이트 관리에서 Windows 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 필요한 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |업데이트 관리에서 Linux 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필요한 업데이트를 설치하기 시작합니다. |
| Operations Manager 관리 그룹 |예 |업데이트 관리에서 연결된 관리 그룹의 에이전트로부터 시스템 업데이트에 대한 정보를 수집합니다. |
| Azure Storage 계정 |아닙니다. |Azure Storage는 시스템 업데이트에 대한 정보를 포함하지 않습니다. |

### <a name="collection-frequency"></a>수집 빈도

컴퓨터에 업데이트 준수 검사가 완료 되 면 에이전트를 대량으로 Azure Monitor 로그의 정보를 전달 합니다. Windows 컴퓨터는 기본적으로 12시간마다 준수 검사가 실행됩니다.

검사 일정 외에도, MMA가 다시 시작되면 업데이트 설치 전과 업데이트 설치 후 15분 이내에 업데이트 준수 검사가 시작됩니다.

Linux 컴퓨터에서 준수 검사는 기본적으로 3시간마다 수행됩니다. MMA 에이전트가 다시 시작되면 15분 이내에 준수 검사가 시작됩니다.

관리되는 컴퓨터의 업데이트 데이터를 대시보드에 표시하는 데 30분에서 6시간이 걸릴 수 있습니다.

## <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

하나 이상의 가상 머신에 대해 새 업데이트 배포를 예약하려면 **업데이트 관리** 아래에서 **업데이트 배포 예약**을 선택합니다.

**새 배포 업데이트** 창에서 다음 정보를 지정합니다.

- **이름**: 업데이트 배포를 식별하는 고유 이름을 제공합니다.
- **운영 체제**: **Windows** 또는 **Linux**를 선택합니다.
- **업데이트할 그룹(미리 보기)**: 구독, 리소스 그룹, 위치 및 태그의 조합을 기반으로 쿼리를 정의하여 배포에 포함할 Azure VM의 동적 그룹을 빌드합니다. 자세한 내용은 [동적 그룹](automation-update-management.md#using-dynamic-groups)을 참조하세요.
- **업데이트할 머신**: 저장된 검색, 가져온 그룹 또는 머신을 선택하여 업데이트할 머신을 선택합니다. **머신**을 선택한 경우 머신의 준비는 **업데이트 에이전트 준비** 열에 표시됩니다. 업데이트 배포를 예약하기 전에 컴퓨터의 상태를 확인할 수 있습니다. Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../azure-monitor/platform/computer-groups.md)을 참조하세요.

  ![새 업데이트 배포 창](./media/manage-update-multi/update-select-computers.png)

- **업데이트 분류**: 업데이트 배포에 포함할 소프트웨어 종류를 선택합니다. 분류 형식에 대한 설명은 [업데이트 분류](automation-update-management.md#update-classifications)를 참조하세요. 분류 형식은 다음과 같습니다.
  - 중요 업데이트
  - 보안 업데이트
  - 업데이트 롤업
  - 기능 팩
  - 서비스 팩
  - 정의 업데이트
  - 도구
  - 업데이트

- **포함/제외할 업데이트** - **포함/제외** 페이지가 열립니다. 포함 또는 제외할 업데이트는 별도의 탭에 있습니다. 포함이 처리되는 방식에 대한 자세한 내용은 [포함 동작](automation-update-management.md#inclusion-behavior)을 참조하세요.

- **일정 설정**: 현재 시간부터 30분 이후인 기본 날짜 및 시간을 그대로 적용할 수 있습니다. 다른 시간을 지정할 수도 있습니다.

   배포가 한 번만 수행될지 아니면 되풀이 일정으로 수행될지를 지정할 수도 있습니다. 되풀이 일정을 설정하려면 **되풀이**에서 **되풀이**를 선택합니다.

   ![일정 설정 대화 상자](./media/manage-update-multi/update-set-schedule.png)

- **사전 스크립트 + 사후 스크립트**: 배포 전후에 실행할 스크립트를 선택합니다. 자세한 내용은 [사전 및 사후 스크립트 관리](pre-post-scripts.md)를 참조하세요.
- **유지 관리 기간(분)**: 업데이트 배포가 수행될 기간을 지정합니다. 이 설정을 통해 정해진 서비스 기간 내에 변경 내용을 수행할 수 있습니다.

- **다시 부팅 컨트롤** - 이 설정은 업데이트 배포에 대해 다시 부팅을 처리하는 방법을 결정합니다.

   |옵션|설명|
   |---|---|
   |필요한 경우 다시 부팅| **(기본값)** 필요한 경우 유지 관리 기간이 허용되면 다시 부팅이 시작됩니다.|
   |항상 다시 부팅|필요 여부에 관계없이 다시 부팅이 시작됩니다. |
   |다시 부팅 안 함|다시 부팅이 필요한지 여부와 관계없이 다시 부팅이 무시됩니다.|
   |다시 부팅만 - 업데이트 설치 안 함|이 옵션은 업데이트 설치를 무시하고 다시 부팅만 시작합니다.|

일정 구성을 완료한 후에 **만들기** 단추를 선택하여 상태 대시보드로 돌아갑니다. **예약됨** 표에는 만든 배포 일정이 표시됩니다.

> [!NOTE]
> 업데이트 관리는 자사 업데이트 배포와 미리 다운로드 패치를 지원합니다. 이를 위해 패치 대상 시스템을 변경해야 합니다. 시스템에서 이러한 설정을 구성하는 방법은 [자사 및 미리 다운로드 지원](automation-update-management.md#firstparty-predownload)을 참조하세요.

## <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작되면 **업데이트 관리**의 **업데이트 배포** 탭에서 해당 배포에 대한 상태를 볼 수 있습니다.

현재 실행 중인 배포 상태를 **진행 중**에서 확인할 수 있습니다. 배포가 성공적으로 완료된 후에는 상태가 **성공**으로 변경됩니다.

배포에서 하나 이상의 업데이트가 실패하면 상태는 **부분적으로 실패**로 나타납니다.

![업데이트 배포 상태](./media/manage-update-multi/update-view-results.png)

업데이트 배포의 대시보드를 확인하려면 완료된 배포 업데이트를 선택합니다.

**업데이트 결과** 창은 가상 머신의 총 업데이트 수 및 배포 결과를 보여 줍니다. 오른쪽의 표에서는 각 업데이트에 대한 자세한 분석 및 설치 결과를 보여 줍니다. 설치 결과는 다음 값 중 하나일 수 있습니다.

- **시도 안 함**: 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
- **성공**: 업데이트했습니다.
- **실패**: 업데이트하지 못했습니다.

배포를 통해 생성된 모든 로그 항목을 보려면 **모든 로그**를 선택합니다.

대상 가상 머신에 대한 업데이트 배포를 관리하는 Runbook의 작업 스트림을 보려면 출력 타일을 선택합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 로그, 출력 및 오류를 비롯해 업데이트 관리에 대한 자세한 정보를 알아보려면 [Azure에서 업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md)을 참조하세요.

