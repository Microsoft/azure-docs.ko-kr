---
title: Azure Log Analytics에 Windows 컴퓨터 연결 | Microsoft Docs
description: 이 문서에서는 MMA(Microsoft Monitoring Agent)를 사용하여 다른 클라우드 또는 온-프레미스에 호스트된 Windows 컴퓨터를 Log Analytics에 연결하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: c7031e54c354392379fee83dbf2a777ba726c5e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777375"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Azure Monitor에 Windows 컴퓨터 연결

(Microsoft 모니터링 에이전트 (MMA)으로) Log Analytics 에이전트를 배포 하 고 구성 해야 모니터링 하 고 가상 머신 또는 로컬 데이터 센터 또는 Azure Monitor를 사용 하 여 다른 클라우드 환경에서 물리적 컴퓨터를 관리 하려면 하나 이상의 Log Analytics 작업 영역에 보고 합니다. 또한 에이전트는 Azure Automation에 대한 Hybrid Runbook Worker 역할을 지원합니다.  

모니터링된 Windows 컴퓨터에서 에이전트는 Microsoft Monitoring Agent 서비스로 나열됩니다. Microsoft Monitoring Agent 서비스는 로그 파일과 Windows 이벤트 로그의 이벤트, 성능 데이터 및 기타 원격 분석을 수집합니다. 에이전트를에 보고 하는 Azure Monitor와 통신할 수 없는 경우에 에이전트는 계속 실행 및 모니터링 되는 컴퓨터의 디스크에 수집 된 데이터를 큐입니다. 연결이 복원되면 Microsoft Monitoring Agent 서비스는 수집된 데이터를 서비스에 보냅니다.

에이전트는 다음 방법 중 하나를 사용하여 설치할 수 있습니다. 대부분의 설치에서는 이러한 방법의 조합을 사용하여 적절하게 다양한 컴퓨터 집합을 설치합니다.  각 방법에 대한 자세한 내용은 문서의 뒷부분에 제공됩니다.

* 수동 설치. 설치 프로그램은 설치 마법사를 사용하여 컴퓨터에서 실행하거나, 명령줄에서 실행하거나, 기존 소프트웨어 배포 도구를 사용하여 배포할 수 있습니다.
* Azure Automation DSC(Desired State Configuration). 환경에 이미 배포된 Windows 컴퓨터용 스크립트를 사용하여 Azure Automation에서 DSC 사용.  
* PowerShell 스크립트.
* Azure Stack에서 Windows 온-프레미스를 실행하는 가상 머신의 Resource Manager 템플릿.  

>[!NOTE]
>Azure Security Center (ASC) Microsoft Monitoring Agent (Log Analytics Windows 에이전트 라고도 함)에 따라 달라 집니다 및는 설치 하 고 해당 배포의 일부로 Log Analytics 작업 영역에 보고 하도록 구성 합니다. ASC는 구독의 모든 Vm에 Log Analytics Windows 에이전트의 자동 설치를 사용 하도록 설정 하 고 특정 작업 영역에 보고 하도록 구성 하는 자동 프로 비전 옵션을 포함 합니다. 이 옵션에 대 한 자세한 내용은 참조 하세요. [Log Analytics 에이전트의 자동 프로 비전 사용](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-microsoft-monitoring-agent-)합니다.
>

지원되는 구성을 이해하려면 [지원되는 Windows 운영 체제](log-analytics-agent.md#supported-windows-operating-systems) 및 [네트워크 방화벽 구성](log-analytics-agent.md#network-firewall-requirements)을 검토합니다.

## <a name="obtain-workspace-id-and-key"></a>작업 영역 ID 및 키 가져오기
Windows용 Log Analytics 에이전트를 설치하려면 Log Analytics 작업 영역에 대한 작업 영역 ID 및 키가 필요합니다.  이 정보는 에이전트를 구성 하 고 Azure Monitor에서 Azure 상용 및 미국 정부 클라우드를 사용 하 여 성공적으로 통신할 수 있는지 확인 하려면 각 설치 방법에서 설치 하는 동안 필요 합니다.  

1. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. Log Analytics 작업 영역 목록에서 에이전트가 보고할 작업 영역을 선택합니다.
3. **고급 설정**을 선택합니다.<br><br> ![Log Analytics 고급 설정](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. **연결된 원본**을 선택한 다음 **Windows 서버**를 선택합니다.   
5. **작업 영역 ID** 및 **기본 키**를 복사한 후 즐겨찾는 편집기에 붙여넣습니다.    
   
## <a name="configure-agent-to-use-tls-12"></a>TLS 1.2를 사용하도록 에이전트 구성
Windows 에이전트와 Log Analytics 서비스 간의 통신에 [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) 프로토콜을 사용하도록 구성하려면 아래 단계에 따라 가상 머신에 에이전트를 설치하기 전이나 나중에 활성화하면 됩니다.   

1. 다음 레지스트리 하위 키를 찾습니다. **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. **프로토콜** 아래에서 TLS 1.2에 대한 하위 키를 만듭니다. **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. 이전에 만든 TLS 1.2 프로토콜 버전 하위 키 아래에 **클라이언트** 하위 키를 만듭니다. 예를 들어 다음과 같습니다. **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**
4. 다음 아래에 DWORD 값을 만듭니다. **HKLM\System\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**

    * **사용** [값 = 1]
    * **DisabledByDefault** [값 = 0]  

보안 암호화를 지원하도록(기본적으로 사용하지 않도록 설정됨) .NET Framework 4.6 이상을 구성합니다. [강력한 암호화](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto)는 TLS 1.2와 같은 강화된 보안 네트워크 프로토콜을 더 사용하며 안전하지 않은 프로토콜을 차단합니다. 

1. 다음 레지스트리 하위 키를 찾습니다. **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. 이 하위 키 아래에 값 **1**을 사용하는 DWORD 값 **SchUseStrongCrypto**를 만듭니다.  
3. 다음 레지스트리 하위 키를 찾습니다. **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. 이 하위 키 아래에 값 **1**을 사용하는 DWORD 값 **SchUseStrongCrypto**를 만듭니다. 
5. 시스템을 다시 시작하여 설정을 적용합니다. 

## <a name="install-the-agent-using-setup-wizard"></a>설치 마법사를 사용하여 에이전트 설치
다음 단계를 설치 하 고 컴퓨터에 에이전트 설치 마법사를 사용 하 여 Azure 및 Azure Government 클라우드에서 Log Analytics 에이전트를 구성 합니다. System Center Operations Manager 관리 그룹에도 보고하도록 에이전트를 구성하는 방법을 알아보려면 [deploy the Operations Manager agent with the Agent Setup Wizard](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)(에이전트 설치 마법사를 사용하여 Operations Manager 에이전트 배포)를 참조하세요.

1. Log Analytics 작업 영역에서 이전에 이동해 온 **Windows 서버** 페이지에서 Windows 운영 체제의 프로세서 아키텍처에 따라 적절한 **Windows 에이전트 다운로드** 버전을 선택하여 다운로드합니다.   
2. 설치를 실행하여 컴퓨터에 에이전트를 설치합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다.
3. **사용 조건** 페이지에서 라이선스를 읽고 **동의함**을 클릭합니다.
4. **대상 폴더** 페이지에서 기본 설치 폴더를 변경 또는 유지하고 **다음**을 클릭합니다.
5. **에이전트 설치 옵션** 페이지에서 Azure Log Analytics에 에이전트를 연결하도록 선택한 다음, **다음**을 클릭합니다.   
6. **Azure Log Analytics** 페이지에서 다음을 수행합니다.
   1. 앞에서 복사한 **작업 영역 ID** 및 **작업 영역 키(기본 키)** 를 붙여넣습니다.  컴퓨터가 Azure Government 클라우드에서 Log Analytics 작업 영역에 보고해야 하는 경우 **Azure Cloud** 드롭다운 목록에서 **Azure 미국 정부**를 선택합니다.  
   2. 컴퓨터가 프록시 서버를 통해 Log Analytics 서비스와 통신해야 하는 경우 **고급**을 클릭하고 프록시 서버의 URL 및 포트 번호를 제공합니다.  프록시 서버에 인증이 필요한 경우 사용자 이름과 암호를 입력하여 프록시 서버로 인증한 후 **다음**을 클릭합니다.  
7. 필요한 구성 설정 제공을 완료한 후 **다음**을 클릭합니다.<br><br> ![작업 영역 ID 및 기본 키 붙여넣기](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. **설치 준비** 페이지에서 선택 항목을 검토한 다음 **설치**를 클릭합니다.
9. **구성 완료** 페이지에서 **마침**을 클릭합니다.

완료되면 **제어판**에 **Microsoft Monitoring Agent**가 나타납니다. Log Analytics에 대한 보고를 확인하려면 [Log Analytics에 대한 에이전트 연결 확인](#verify-agent-connectivity-to-log-analytics)을 검토하세요. 

## <a name="install-the-agent-using-the-command-line"></a>명령줄을 사용하여 에이전트 설치
다운로드한 에이전트용 파일은 자체 포함 설치 패키지입니다.  에이전트용 설치 프로그램과 지원 파일이 패키지에 포함되어 있으며 다음 예제에 나온 대로 명령줄을 사용하여 제대로 설치하려면 이러한 프로그램과 파일을 추출해야 합니다.    

>[!NOTE]
>에이전트를 업그레이드하려는 경우 Log Analytics 스크립팅 API를 사용해야 합니다. 자세한 내용은 [Windows 및 Linux용 Log Analytics 에이전트 관리 및 유지 관리](agent-manage.md) 항목을 참조하세요.

다음 표에서는 자동화 DSC를 사용하여 배포하는 경우를 포함하여 에이전트용 설치 프로그램에서 지원하는 특정 매개 변수를 집중적으로 살펴봅니다.

|MMA 관련 옵션                   |메모         |
|---------------------------------------|--------------|
| NOAPM=1                               | 선택적 매개 변수. .NET 애플리케이션 성능 모니터링 없이 에이전트를 설치합니다.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = 작업 영역에 보고하도록 에이전트 구성                |
|OPINSIGHTS_WORKSPACE_ID                | 추가할 작업 영역의 작업 영역 ID(guid)                    |
|OPINSIGHTS_WORKSPACE_KEY               | 작업 영역에서 처음 인증하는 데 사용되는 작업 영역 키 |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | 작업 영역이 있는 클라우드 환경 지정 <br> 0 = Azure 상용 클라우드(기본값) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | 사용할 프록시의 URI |
|OPINSIGHTS_PROXY_USERNAME               | 인증된 프록시에 액세스할 사용자 이름 |
|OPINSIGHTS_PROXY_PASSWORD               | 인증된 프록시에 액세스할 암호 |

1. 에이전트 설치 파일을 추출하려면 관리자 권한 명령 프롬프트에서 `MMASetup-<platform>.exe /c`를 실행합니다. 그러면 파일을 추출할 경로를 묻은 메시지가 표시됩니다.  `MMASetup-<platform>.exe /c /t:<Full Path>` 인수를 전달하여 경로를 지정할 수도 있습니다.  
2. 에이전트를 자동으로 설치하고 Azure 상용 클라우드에 보고하도록 구성하려면 설치 프로그램 파일을 추출한 폴더에서 다음을 입력합니다. 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   또는 Azure 미국 정부 클라우드에 보고하도록 에이전트를 구성하려면 다음을 입력합니다. 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace ID> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Azure Automation에서 DSC를 사용하여 에이전트 설치

다음 스크립트 예제를 사용하여 Azure Automation DSC를 사용하여 에이전트를 설치합니다.   Automation 계정이 없는 경우 자동화 DSC를 사용하기 전에 필요한 Automation 계정을 만드는 데 필요한 요구 사항과 단계를 이해하기 위해 [Azure Automation 시작](/azure/automation/)을 참조하세요.  자동화 DSC에 익숙하지 않은 경우 [자동화 DSC 시작](../../automation/automation-dsc-getting-started.md)을 참조하세요.

다음 예제에서는 `URI` 값으로 식별되는 64비트 에이전트를 설치합니다. 또한 URI 값을 바꿔 32비트 버전을 사용할 수 있습니다. 두 버전에 대한 URL

- Windows 64비트 에이전트 - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32비트 에이전트 - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>이 절차 및 스크립트 예제는 Windows 컴퓨터에 이미 배포된 에이전트의 업그레이드를 지원하지 않습니다.

32비트 및 64비트 버전의 에이전트 패키지에는 서로 다른 제품 코드가 있으며 릴리스된 새 버전에도 고유한 값이 있습니다.  제품 코드는 애플리케이션 또는 제품의 보안 주체 ID이며 Windows Installer **ProductCode** 속성으로 표시되는 GUID입니다.  **MMAgent.ps1** 스크립트의 `ProductId` 값은 32비트 또는 64비트 에이전트 설치 관리자 패키지의 제품 코드와 일치해야 합니다.

에이전트 설치 패키지에서 제품 코드를 직접 검색하려면 Windows 소프트웨어 개발 키트의 구성 요소인 [Windows Installer 개발자용 Windows SDK 구성 요소](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx)에서 Orca.exe를 사용하거나 MVP(Microsoft Valuable Professional)에서 작성된 [예제 스크립트](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)에 따라 PowerShell을 사용할 수 있습니다.  두 가지 방법에서 먼저 **MOMagent.msi** 파일을 MMASetup 설치 패키지에서 추출해야 합니다.  이는 앞서 첫 번째 단계의 [명령줄을 사용하여 에이전트 설치](#install-the-agent-using-the-command-line) 섹션에 나와 있습니다.  

1. [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration)에서 xPSDesiredStateConfiguration DSC 모듈을 Azure Automation으로 가져옵니다.  
2.  *OPSINSIGHTS_WS_ID* 및 *OPSINSIGHTS_WS_KEY*의 Azure Automation 변수 자산을 만듭니다. *OPSINSIGHTS_WS_ID*를 Log Analytics 작업 영역 ID에 설정하고 *OPSINSIGHTS_WS_KEY*를 작업 영역의 기본 키에 설정합니다.
3.  스크립트를 복사하여 MMAgent.ps1로 저장합니다.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. 앞에서 권장된 메서드를 사용하여 스크립트의 `ProductId` 값을 최신 버전의 에이전트 설치 패키지에서 추출한 제품 코드로 업데이트합니다. 
5. Automation 계정으로 [MMAgent.ps1 구성 스크립트를 가져옵니다](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation). 
5. 구성에 [Windows 컴퓨터 또는 노드를 할당](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration)합니다. 15분 이내에 노드가 구성을 점검하고 에이전트가 노드로 푸시됩니다.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Log Analytics에 대한 에이전트 연결 확인

에이전트 설치가 완료되면 두 가지 방법으로 성공적으로 연결되었는지 확인하고 보고할 수 있습니다.  

**제어판**의 컴퓨터에서 **Microsoft Monitoring Agent** 항목을 찾습니다.  이 항목을 선택하면 **Azure Log Analytics** 탭에서 에이전트가 다음과 같은 메시지를 표시합니다. **Microsoft Monitoring Agent가 Microsoft Operations Management Suite 서비스에 연결되었습니다.**<br><br> ![Log Analytics에 대한 MMA 연결 상태](media/agent-windows/log-analytics-mma-laworkspace-status.png)

또한 Azure portal에서 간단한 로그 쿼리를 수행할 수 있습니다.  

1. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 입력 **Azure Monitor**합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 선택 **Azure Monitor**합니다.  
2. 선택 **로그** 메뉴에서. 
2. 로그 창의 쿼리 필드 형식:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

반환된 검색 결과에는 에이전트가 연결되고 서비스에 보고 중임을 나타내는 컴퓨터에 대한 하트비트 레코드가 표시됩니다.   

## <a name="next-steps"></a>다음 단계

[Windows 및 Linux용 Log Analytics 에이전트 관리 및 유지 관리](agent-manage.md)를 검토하여 컴퓨터에서 배포 수명 주기 중에 에이전트를 관리하는 방법을 알아봅니다.  
