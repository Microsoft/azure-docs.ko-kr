---
title: PowerShell 함수를 사용 하 여 원격 온-프레미스 리소스 관리
description: PowerShell 함수 앱을 온-프레미스 리소스에 연결 하 여 온-프레미스 리소스를 원격으로 관리 하는 데 사용할 수 있는 Azure Relay에서 하이브리드 연결를 구성 하는 방법에 대해 알아봅니다.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226930"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Azure Functions 및 App Service 하이브리드 연결에서 PowerShell을 사용 하 여 하이브리드 환경 관리

Azure App Service 하이브리드 연결 기능을 사용 하면 다른 네트워크의 리소스에 액세스할 수 있습니다. 이 기능에 대한 자세한 내용은 [하이브리드 연결](../app-service/app-service-hybrid-connections.md) 설명서에서 확인할 수 있습니다. 이 문서에서는이 기능을 사용 하 여 온-프레미스 서버를 대상으로 하는 PowerShell 함수를 실행 하는 방법을 설명 합니다. 그런 다음이 서버를 사용 하 여 Azure PowerShell 함수에서 온-프레미스 환경의 모든 리소스를 관리할 수 있습니다.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>PowerShell 원격에 대한 온-프레미스 서버 구성

다음 스크립트는 PowerShell 원격을 사용 하도록 설정 하 고 새 방화벽 규칙 및 WinRM https 수신기를 만듭니다. 테스트 목적으로 자체 서명 된 인증서가 사용 됩니다. 프로덕션 환경에서는 서명 된 인증서를 사용 하는 것이 좋습니다.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>포털에서 PowerShell 함수 앱 만들기

App Service 하이브리드 연결 기능은 Basic, Standard 및 격리 요금제 에서만 사용할 수 있습니다. PowerShell을 사용 하 여 함수 앱을 만들 때 이러한 계획 중 하나를 만들거나 선택 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽에 있는 메뉴에서 **+ 리소스 만들기** 를 선택 하 고 **함수 앱**을 선택 합니다.

1. **호스팅 계획**의 경우 **App Service 계획**을 선택한 다음 **App Service 계획/위치**를 선택 합니다.

1. **새로 만들기**를 선택 하 고, **App Service 계획** 이름을 입력 하 고, 사용자의 함수 액세스에 가까운 [지역](https://azure.microsoft.com/regions/) 또는 다른 서비스에 대한 **위치** 를 선택 하 고 **가격 책정 계층**을 선택 합니다.

1. S1 표준 요금제를 선택 하 고 **적용**을 선택 합니다.

1. **확인** 을 선택 하 여 계획을 만든 다음, 다음 스크린샷 바로 뒤의 표에 지정 된 대로 나머지 **함수 앱** 설정을 구성 합니다.

    ![PowerShell Core 함수 앱](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.  | 
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **리소스 그룹** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. 제안 된 값을 사용할 수도 있습니다. |
    | **OS** | 기본 설정 OS | Windows를 선택 합니다. |
    | **런타임 스택** | 기본 설정 언어 | PowerShell Core를 선택 합니다. |
    | **Storage** |  전역적으로 고유한 이름 |  함수 앱에서 사용하는 스토리지 계정을 만듭니다. Storage 계정 이름은 3 자에서 24 자 사이 여야 하며 숫자와 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다.
    | **Application Insights** | 기본값 | 가장 가까운 지원 영역에 동일한 *앱 이름*의 Application Insight 리소스를 만듭니다. 이 설정을 확장 하 여 **새 리소스 이름을** 변경 하거나 데이터를 저장 하려는 [Azure geography](https://azure.microsoft.com/global-infrastructure/geographies/) 지역에서 다른 **위치** 를 선택할 수 있습니다. |

1. 설정의 유효성을 검사 한 후 **만들기**를 선택 합니다.

1. 포털의 오른쪽 위 모퉁이에 있는 **알림** 아이콘을 선택 하 고 "배포 했습니다." 메시지가 나올 때까지 기다립니다.

1. **리소스로 이동**을 선택하여 함수 앱을 봅니다. **대시보드에 고정**을 선택할 수도 있습니다. 고정하면 대시보드에서 이 함수 앱 리소스로 쉽게 돌아올 수 있습니다.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>함수 앱에 대한 하이브리드 연결 만들기

하이브리드 연결은 함수 앱의 네트워킹 섹션에서 구성 됩니다.

1. 함수 앱에서 **플랫폼 기능** 탭을 선택 하 고 **네트워킹**을 선택 합니다. 
   플랫폼 네트워킹](./media/functions-hybrid-powershell/app-overview-platform-networking.png)에 대한 ![앱 개요  
1. **하이브리드 연결 엔드포인트 구성**을 선택 합니다.
   ![네트워킹](./media/functions-hybrid-powershell/select-network-feature.png)  
1. **하이브리드 연결 추가**를 선택 합니다.
   하이브리드 연결을](./media/functions-hybrid-powershell/hybrid-connection-overview.png) ![  
1. 다음 스크린샷 후와 같이 하이브리드 연결에 대한 정보를 입력 합니다. **엔드포인트 호스트** 설정을 온-프레미스 서버의 호스트 이름과 일치 시키고 나중에 원격 명령을 실행 하는 경우 서버를 쉽게 기억할 수 있도록 하는 옵션이 있습니다. 포트는 이전에 서버에 정의 된 기본 Windows 원격 관리 서비스 포트와 일치 합니다.
  하이브리드 연결 추가 ![](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **하이브리드 연결 이름**: ContosoHybridOnPremisesServer
    
    **엔드포인트 호스트**: finance1
    
    **엔드포인트 포트**: 5986
    
    **Servicebus 네임 스페이스**: 새로 만들기
    
    **위치**: 사용 가능한 위치를 선택 합니다.
    
    **이름**: contosopowershellhybrid

5. **확인** 을 선택 하 여 하이브리드 연결을 만듭니다.

## <a name="download-and-install-the-hybrid-connection"></a>하이브리드 연결 다운로드 및 설치

1. **연결 관리자 다운로드** 를 선택 하 여 .msi 파일을 컴퓨터에 로컬로 저장 합니다.
설치 관리자를 다운로드 ![](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. 로컬 컴퓨터의 .msi 파일을 온-프레미스 서버에 복사 합니다.
1. 하이브리드 연결 관리자 설치 관리자를 실행 하 여 온-프레미스 서버에 서비스를 설치 합니다.
하이브리드 연결을 설치 ![](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. 포털에서 하이브리드 연결을 열고 게이트웨이 연결 문자열을 클립보드에 복사 합니다.
하이브리드 연결 문자열 복사 ![](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. 온-프레미스 서버에서 하이브리드 연결 관리자 UI를 엽니다.
하이브리드 연결 UI를 열 ![](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. **수동으로 입력** 단추를 선택 하 고 연결 문자열을 클립보드에 붙여 넣습니다.
연결 붙여넣기를 ![](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. 연결 된 것으로 표시 되지 않는 경우 PowerShell에서 하이브리드 연결 관리자를 다시 시작 합니다.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>관리자 계정의 암호에 대한 앱 설정 만들기

1. 함수 앱에서 **플랫폼 기능** 탭을 선택 합니다.
1. **일반 설정**에서 **구성**을 선택 합니다.
플랫폼 구성 ![선택](./media/functions-hybrid-powershell/select-configuration.png)  
1. **새 응용 프로그램 설정** 을 확장 하 여 암호에 대한 새 설정을 만듭니다.
1. _ContosoUserPassword_설정의 이름을로 설정 하 고 암호를 입력 합니다.
1. **확인** 을 선택 하 고 저장을 선택 하 여 함수 응용 프로그램에 암호를 저장 합니다.
암호에 대한 앱 설정 추가 ![](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>테스트할 함수 http 트리거를 만듭니다.

1. 함수 앱에서 새 HTTP 트리거 함수를 만듭니다.
새 HTTP 트리거를 만들 ![](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. 템플릿에서 PowerShell 코드를 다음 코드로 바꿉니다.

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. **저장** 및 **실행** 을 선택 하 여 함수를 테스트 합니다.
함수 앱을 테스트 ![](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>온-프레미스에서 다른 시스템 관리

연결 된 온-프레미스 서버를 사용 하 여 로컬 환경의 다른 서버 및 관리 시스템에 연결할 수 있습니다. 이렇게 하면 PowerShell 함수를 사용 하 여 Azure에서 데이터 센터 작업을 관리할 수 있습니다. 다음 스크립트는 제공 된 자격 증명으로 실행 되는 PowerShell 구성 세션을 등록 합니다. 이러한 자격 증명은 원격 서버의 관리자를 위한 자격 증명 이어야 합니다. 그런 다음이 구성을 사용 하 여 로컬 서버 또는 데이터 센터의 다른 엔드포인트에 액세스할 수 있습니다.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

이 스크립트에서 다음 변수를 사용자 환경에서 해당 하는 값으로 바꿉니다.
* $HybridEndpoint
* $RemoteServer

위의 두 시나리오에서는 Azure Functions 및 하이브리드 연결에서 PowerShell을 사용 하 여 온-프레미스 환경에 연결 하 고 관리할 수 있습니다. [함수에서](./functions-reference-powershell.md) [하이브리드 연결](../app-service/app-service-hybrid-connections.md) 및 PowerShell에 대해 자세히 알아보는 것이 좋습니다.

Azure [virtual network](./functions-create-vnet.md) 를 사용 하 여 Azure Functions 통해 온-프레미스 환경에 연결할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [PowerShell 함수 사용에 대한 자세한 정보](functions-reference-powershell.md)
