---
title: Azure Automation 상태 구성 개요
description: Azure Automation 상태 구성(DSC) 개요, 용어 및 알려진 문제
keywords: PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da746d80e3ae1fa5cc02683a8bb0ff0402722b8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61071479"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation 상태 구성 개요

Azure Automation 상태 구성은 Azure 서비스로, 클라우드에서 PowerShell DSC(Desired State Configuration) [구성](/powershell/dsc/configurations)을 작성, 관리 및 컴파일하고 [DSC 리소스](/powershell/dsc/resources)를 가져오고 대상 노드에 구성을 할당하는 등 모든 것을 할 수 있습니다.

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 사용하는 이유

Azure Automation 상태 구성은 Azure 외부에서 DSC를 사용하는 것에 비해 몇 가지 장점이 있습니다.

### <a name="built-in-pull-server"></a>기본 제공 끌어오기 서버

Azure Automation 상태 구성은 대상 노드가 구성을 자동으로 수신하고 원하는 상태를 따르며 규정 준수를 다시 보고하도록 [Windows DSC 서비스 기능](/powershell/dsc/pullserver)과 유사한 DSC 끌어오기 서버를 제공합니다. Azure Automation에 있는 기본 제공 끌어오기 서버는 자체 끌어오기 서버를 설정하고 유지 관리할 필요가 없습니다. Azure Automation은 클라우드 또는 온-프레미스에서 가상 또는 실제 Windows 또는 Linux 컴퓨터를 대상으로 할 수 있습니다.

### <a name="management-of-all-your-dsc-artifacts"></a>모든 DSC 아티팩트 관리

Azure Automation 상태 구성은 Azure Automation이 PowerShell 스크립팅에 제공하는 동일한 관리 계층을 [PowerShell DSC(Desired State Configuration)](/powershell/dsc/overview)에 제공합니다.

Azure 포털 또는 PowerShell에서 DSC 구성, 리소스 및 대상 노드를 모두 관리할 수 있습니다.

![Azure Automation 페이지의 스크린샷](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Azure Monitor 로그로 보고 데이터 가져오기

Azure Automation 상태 구성으로 관리되는 노드는 상세한 보고 상태 데이터를 기본 제공 끌어오기 서버에 보냅니다. 이 데이터를 Log Analytics 작업 영역으로 보내려면 Azure Automation 상태 구성을 구성할 수 있습니다. Log Analytics 작업 영역에 상태 구성 상태 데이터를 보내는 방법에 알아보려면 참조 [앞으로 Azure Automation 상태 구성 보고 데이터를 Azure Monitor 로그](automation-dsc-diagnostics.md)합니다.

## <a name="prerequisites"></a>필수 조건

Azure Automation 상태 구성 (DSC)를 사용 하는 경우 다음 요구 사항을 고려 하십시오.

### <a name="operating-system-requirements"></a>운영 체제 요구 사항

Windows를 실행 하는 노드를 다음 버전 지원 됩니다.

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- 윈도우 10
- Windows 8.1
- Windows 7

Linux를 실행 하는 노드를 다음 배포판/버전 지원 됩니다.

DSC Linux 확장은 모든 Linux 배포판을 지원 [azure 보증](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) 제외:

배포 | 버전
-|-
Debian  | 모든 버전
Ubuntu  | 18.04

### <a name="dsc-requirements"></a>DSC의 요구 사항

Azure에서 실행 되는 모든 Windows 노드에 대해 [WMF 5.1](https://docs.microsoft.com/powershell/wmf/5.1/install-configure) 온 보 딩 하는 동안 설치 됩니다.  Windows Server 2012 및 Windows 7을 실행 하는 노드에 대 한 [WinRM을 사용할](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency)합니다.

Azure에서 실행 되는 모든 Linux 노드에 대 한 [Linux 용 PowerShell DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 온 보 딩 하는 동안 설치 됩니다.

### <a name="network-planning"></a>개인 네트워크를 구성 합니다.

노드에 개인 네트워크 내에 있는 경우 다음 포트와 Url이에 대 한 상태 구성 (DSC) Automation과 통신 하려면 필요 합니다.

* 포트: 아웃바운드 인터넷 액세스에는 443 TCP 포트만 필요합니다.
* 글로벌 URL: *.azure-automation.net
* US Gov 버지니아의 전역 URL: *.azure-automation.us
* 에이전트 서비스: https://\<workspaceId\>.agentsvc.azure-automation.net

예외를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 포함합니다.

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터에 대한 통신을 제한할 수 있습니다. 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 미국 정부 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) XML 파일을 다운로드하세요.

> [!NOTE]
> Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 계산, SQL 및 스토리지 범위가 포함되어 있습니다.
>
>업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다.
>
> 새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 이 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

## <a name="introduction-video"></a>소개 비디오

읽기보다 비디오를 시청하기가 편하신가요? Azure Automation 상태 구성이 처음 발표된 2015년 5월 당시의 비디오를 다음에서 확인하세요.

> [!NOTE]
> 이 비디오에 언급된 개념 및 수명 주기는 정확하지만 이 비디오가 기록된 이후에 Azure Automation 상태 구성은 많이 발전되었습니다. 일반적으로 사용할 수 있고 Azure 포털의 UI는 보다 강력해졌으며 더 많은 기능을 제공하고 있습니다.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- 노드를 온보드하는 방법에 대한 자세한 내용은 [Azure Automation 상태 구성을 통해 머신을 관리하기 위한 머신 온보딩](automation-dsc-onboarding.md)을 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
