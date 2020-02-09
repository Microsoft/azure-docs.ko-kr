---
title: Azure Automation 상태 구성 개요
description: Azure Automation 상태 구성(DSC) 개요, 용어 및 알려진 문제
keywords: PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 787cade13a0636bb25afa1d4043a977f512484f9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850893"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation 상태 구성 개요

Azure Automation 상태 구성은 Azure 서비스로, 클라우드에서 PowerShell DSC(Desired State Configuration) [구성](/powershell/scripting/dsc/configurations/configurations)을 작성, 관리 및 컴파일하고 [DSC 리소스](/powershell/scripting/dsc/resources/resources)를 가져오고 대상 노드에 구성을 할당하는 등 모든 것을 할 수 있습니다.

## <a name="why-use-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 사용하는 이유

Azure Automation 상태 구성은 Azure 외부에서 DSC를 사용하는 것에 비해 몇 가지 장점이 있습니다.

### <a name="built-in-pull-server"></a>기본 제공 끌어오기 서버

Azure Automation 상태 구성은 대상 노드가 구성을 자동으로 수신하고 원하는 상태를 따르며 규정 준수를 다시 보고하도록 [Windows DSC 서비스 기능](/powershell/scripting/dsc/pull-server/pullserver)과 유사한 DSC 끌어오기 서버를 제공합니다. Azure Automation에 있는 기본 제공 끌어오기 서버는 자체 끌어오기 서버를 설정하고 유지 관리할 필요가 없습니다. Azure Automation은 클라우드 또는 온-프레미스에서 가상 또는 실제 Windows 또는 Linux 컴퓨터를 대상으로 할 수 있습니다.

### <a name="management-of-all-your-dsc-artifacts"></a>모든 DSC 아티팩트 관리

Azure Automation 상태 구성은 Azure Automation이 PowerShell 스크립팅에 제공하는 동일한 관리 계층을 [PowerShell DSC(Desired State Configuration)](/powershell/scripting/dsc/overview/overview)에 제공합니다.

Azure 포털 또는 PowerShell에서 DSC 구성, 리소스 및 대상 노드를 모두 관리할 수 있습니다.

![Azure Automation 페이지의 스크린샷](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Azure Monitor 로그로 보고 데이터 가져오기

Azure Automation 상태 구성으로 관리되는 노드는 상세한 보고 상태 데이터를 기본 제공 끌어오기 서버에 보냅니다. 이 데이터를 Log Analytics 작업 영역으로 보내려면 Azure Automation 상태 구성을 구성할 수 있습니다. 상태 구성 상태 데이터를 Log Analytics 작업 영역으로 전송 하는 방법에 대한 자세한 내용은 [Azure Automation 상태 구성 보고 데이터를 Azure Monitor 로그로 전달](automation-dsc-diagnostics.md)을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

DSC (Azure Automation 상태 구성)를 사용 하는 경우 다음 요구 사항을 고려 하세요.

### <a name="operating-system-requirements"></a>운영 체제 요구 사항

Windows를 실행 하는 노드의 경우 지원 되는 버전은 다음과 같습니다.

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) 독립 실행형 제품 SKU는 필요한 상태 구성의 구현을 포함하지 않으므로 PowerShell DSC 또는 Azure Automation 상태 구성으로 관리할 수 없습니다.

Linux를 실행 하는 노드의 경우 다음 distros/버전이 지원 됩니다.

DSC Linux 확장은 [지원 되는 Linux 배포판](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)에 나열 된 모든 linux 배포를 지원 합니다.

### <a name="dsc-requirements"></a>DSC 요구 사항

Azure에서 실행 되는 모든 Windows 노드의 경우, 온 보 딩 중에 [WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) 이 설치 됩니다.  Windows Server 2012 및 Windows 7을 실행 하는 노드의 경우 [WinRM이 사용](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)됩니다.

Azure에서 실행 되는 모든 Linux 노드의 경우 온 보 딩 중에 [linux 용 POWERSHELL DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 가 설치 됩니다.

### <a name="network-planning"></a>개인 네트워크 구성

노드가 개인 네트워크 내에 있는 경우 상태 구성 (DSC)에서 Automation과 통신 하는 데 다음 포트 및 Url이 필요 합니다.

* 포트: 아웃바운드 인터넷 액세스에는 443 TCP 포트만 필요합니다.
* 글로벌 URL: *.azure-automation.net
* US Gov 버지니아의 전역 URL: *.azure-automation.us
* 에이전트 서비스: https://\<workspaceId\>.agentsvc.azure-automation.net

이는 관리 되는 노드가 Azure Automation와 통신할 수 있도록 네트워크 연결을 제공 합니다.
[WaitFor * 리소스](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)와 같이 노드 간에 통신 하는 DSC 리소스를 사용 하는 경우에는 노드 간 트래픽만 허용 해야 합니다.
이러한 네트워크 요구 사항을 이해 하려면 각 DSC 리소스에 대한 설명서를 참조 하세요.

#### <a name="proxy-support"></a>프록시 지원

DSC 에이전트에 대한 프록시 지원은 Windows 버전 1809 이상에서 사용할 수 있습니다.
이 옵션을 구성 하려면 노드를 등록 하는 데 사용 되는 [메타 구성 스크립트](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) 에서 **Proxyurl** 및 **ProxyCredential** 의 값을 설정 합니다.
프록시는 이전 버전의 Windows 용 DSC에서 사용할 수 없습니다.

Linux 노드의 경우 DSC 에이전트는 프록시를 지원 하 고 http_proxy 변수를 활용 하 여 url을 결정 합니다.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure 상태 구성 네트워크 범위 및 네임 스페이스

예외를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 포함합니다.

특정 지역에 대해 정의된 Automation 계정이 있는 경우 해당 지역 데이터 센터에 대한 통신을 제한할 수 있습니다. 다음 표에서는 각 지역에 대한 DNS 레코드를 제공합니다.

| **지역** | **DNS 레코드** |
| --- | --- |
| 미국 중서부 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 미국 동부   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 북유럽 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 인도 중부 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 일본 동부 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 동남부 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 영국 남부 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 버지니아 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

지역 이름 대신 지역 IP 주소 목록을 보려면 Microsoft 다운로드 센터에서 [Azure 데이터 센터 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653) XML 파일을 다운로드하세요.

> [!NOTE]
> Azure 데이터 센터 IP 주소 XML 파일은 Microsoft Azure 데이터 센터에서 사용되는 IP 주소 범위를 나열합니다. 이 파일에는 컴퓨팅, SQL 및 스토리지 범위가 포함되어 있습니다.
>
>업데이트된 파일이 매주 게시됩니다. 이 파일에는 현재 배포된 범위 및 IP 범위에 대해 예정된 변경 내용이 반영됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다.
>
> 새 XML 파일을 매주 다운로드하는 것이 좋습니다. 그런 다음 Azure에서 실행되는 서비스를 올바르게 식별하도록 사이트를 업데이트합니다. Azure ExpressRoute 사용자는 이 파일을 사용해 Azure 공간에 대한 BGP(Border Gateway Protocol) 공지 사항을 매월 첫 주에 업데이트하고 있음에 유의해야 합니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- 노드를 온보드하는 방법에 대한 자세한 내용은 [Azure Automation 상태 구성을 통해 머신을 관리하기 위한 머신 온보딩](automation-dsc-onboarding.md)을 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
