---
title: Azure Monitor의 Azure Log Analytics VM 확장 문제 해결 | Microsoft Docs
description: Windows 및 Linux Azure VM에 대한 Log Analytics VM 확장에서 발생하는 가장 일반적인 문제에 대한 증상, 원인 및 해결 방법에 대해 설명합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: 40f0705cfa7f0e9bb45d300a629adebd0cc5be47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341235"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Azure Monitor에서 Log Analytics VM 확장 문제 해결
이 문서는 Microsoft Azure에서 실행되는 Windows 및 Linux 가상 머신에 대한 Log Analytics VM 확장에서 발생할 수 있는 오류를 해결하는 데 도움을 주며, 가능한 해결 방법을 제안합니다.

확장의 상태를 확인하려면 Azure Portal에서 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에 **가상 머신**을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **가상 머신**을 선택합니다.
3. 가상 머신 목록에서 해당 가상 머신을 찾아 선택합니다.
3. 가상 머신에서 **확장**을 클릭합니다.
4. 목록에서 Log Analytics가 사용되도록 설정되어 있는지 여부를 확인합니다.  Linux의 경우 에이전트가 **OMSAgentforLinux**로 표시되고, Windows의 경우에는 에이전트가 **MicrosoftMonitoringAgent**로 표시됩니다.

   ![VM 확장 보기](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. 확장을 클릭하여 세부 정보를 확인합니다. 

   ![VM 확장 세부 정보](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM 확장 문제 해결

*Microsoft Monitoring Agent* VM 확장이 설치되지 않거나 문제를 보고할 경우 다음 절차를 수행하여 문제를 해결할 수 있습니다.

1. [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)의 절차를 통해 Azure VM 에이전트가 올바르게 설치되어 작동하는지 확인합니다.
   * VM 에이전트 로그 파일 `C:\WindowsAzure\logs\WaAppAgent.log`를 검토할 수도 있습니다.
   * 로그가 없는 경우 VM 에이전트가 설치되지 않은 것입니다.
   * [Azure VM 에이전트 설치](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 다음 절차를 통해 Microsoft Monitoring Agent 확장 하트 비트 작업이 실행되고 있는지 확인합니다.
   * 가상 머신에 로그인합니다.
   * 작업 스케줄러를 열고 `update_azureoperationalinsight_agent_heartbeat` 작업을 찾습니다.
   * 작업이 활성화되었고 1분 간격으로 실행되는지 확인합니다.
   * `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`에서 하트비트 로그 파일을 확인합니다.
3. `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`에서 Microsoft Monitoring Agent VM 확장 로그 파일을 검토합니다.
4. 가상 머신이 PowerShell 스크립트를 실행할 수 있는지 확인합니다.
5. C:\Windows\temp 권한이 변경되지 않았는지 확인합니다.
6. 가상 컴퓨터 `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`에 나타나는 PowerShell 창에서 다음을 입력하여Microsoft Monitoring Agent의 상태를 봅니다. 
7. `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`에서 Microsoft Monitoring Agent 설정 로그 파일을 검토합니다.

자세한 내용은 [Windows 확장 문제 해결](../../virtual-machines/extensions/oms-windows.md)을 참조하세요.

## <a name="troubleshooting-linux-vm-extension"></a>Linux VM 확장 문제 해결
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
*Linux용 Log Analytics 에이전트* VM 확장이 설치되지 않거나 보고되지 않으면 다음 단계를 수행하여 문제를 해결할 수 있습니다.

1. 확장 상태가 *알 수 없음*이면 VM 에이전트 로그 파일 `/var/log/waagent.log`를 검토하여 Azure VM 에이전트가 올바르게 설치되어 작동하고 있는지 확인합니다. 
   * 로그가 없는 경우 VM 에이전트가 설치되지 않은 것입니다.
   * [Linux VM에 Azure VM 에이전트 설치](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 그 밖의 비정상 상태에 대해 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 및 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`에서 Linux용 Log Analytics VM 확장 로그 파일을 검토합니다.
3. 확장 상태가 정상이나 데이터가 업로드되지 않는 경우 `/var/opt/microsoft/omsagent/log/omsagent.log`에서 Linux용 Log Analytics 에이전트 로그 파일을 검토합니다.

자세한 내용은 [Linux 확장 문제 해결](../../virtual-machines/extensions/oms-linux.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 외부의 컴퓨터에서 호스트되는 Linux용 Log Analytics 에이전트와 관련된 추가 문제 해결 지침에 대해서는 [Azure Log Analytics Linux 에이전트 문제 해결](agent-linux-troubleshoot.md)을 참조하세요.  
