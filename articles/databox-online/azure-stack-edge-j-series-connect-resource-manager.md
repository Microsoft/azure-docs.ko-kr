---
title: Azure Stack Edge GPU 장치에서 Azure Resource Manager에 연결
description: Azure PowerShell를 사용 하 여 Azure Stack에 지 GPU에서 실행 되는 Azure Resource Manager에 연결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: cf57d81c2ef56662abbd529a5de90e03c00e091a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269814"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-device"></a>Azure Stack에 지 장치에서 Azure Resource Manager에 연결

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure Resource Manager는 Azure 구독에서 리소스를 만들고, 업데이트 하 고, 삭제할 수 있는 관리 계층을 제공 합니다. Azure Stack Edge 장치는 로컬 구독에서 Vm을 만들고, 업데이트 하 고, 삭제 하는 동일한 Azure Resource Manager Api를 지원 합니다. 이 지원을 통해 클라우드와 일관 된 방식으로 장치를 관리할 수 있습니다. 

이 자습서에서는 Azure PowerShell를 사용 하 여 Azure Resource Manager를 통해 Azure Stack Edge 장치에서 로컬 Api에 연결 하는 방법을 설명 합니다.

## <a name="about-azure-resource-manager"></a>Azure Resource Manager 정보

Azure Resource Manager은 Azure Stack Edge 장치 API를 호출 하 고 Vm 만들기, 업데이트 및 삭제와 같은 작업을 수행 하는 일관 된 관리 계층을 제공 합니다. Azure Resource Manager의 아키텍처는 다음 다이어그램에 자세히 설명 되어 있습니다.

![Azure Resource Manager 다이어그램](media/azure-stack-edge-j-series-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-device"></a>Azure Stack Edge 장치의 끝점

다음 표에서는 장치에 노출 되는 다양 한 끝점, 지원 되는 프로토콜 및 해당 끝점에 액세스 하는 포트를 요약 하 여 설명 합니다. 이 문서 전체에서 이러한 끝점에 대 한 참조를 찾을 수 있습니다.

| # | 엔드포인트 | 지원되는 프로토콜 | 사용 되는 포트 | 사용 대상 |
| --- | --- | --- | --- | --- |
| 1. | Azure 리소스 관리자 | https | 443 | 자동화를 위해 Azure Resource Manager에 연결 하려면 |
| 2. | 보안 토큰 서비스 | https | 443 | 액세스 및 새로 고침 토큰을 통해 인증 하려면 |
| 3. | Blob | https | 443 | REST를 통해 Blob storage에 연결 하려면 |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Azure Resource Manager 워크플로에 연결

Azure Resource Manager를 사용 하 여 장치의 로컬 Api에 연결 하는 프로세스에는 다음 단계가 필요 합니다.

| 단계 # | 이 단계를 수행 합니다. | .. 이 위치에 있습니다. |
| --- | --- | --- |
| 1. | [Azure Stack Edge 장치 구성](#step-1-configure-azure-stack-edge-device) | 로컬 웹 UI |
| 2. | [인증서 만들기 및 설치](#step-2-create-and-install-certificates) | Windows 클라이언트/로컬 웹 UI |
| 3. | [필수 구성 요소 검토 및 구성](#step-3-install-powershell-on-the-client) | Windows 클라이언트 |
| 4. | [클라이언트에서 Azure PowerShell 설정](#step-4-set-up-azure-powershell-on-the-client) | Windows 클라이언트 |
| 5. | [끝점 이름 확인에 대 한 호스트 파일 수정](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows 클라이언트 또는 DNS 서버 |
| 6. | [끝점 이름이 확인 되었는지 확인 합니다.](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows 클라이언트 |
| 7. | [Azure PowerShell cmdlet을 사용 하 여 Azure Resource Manager에 대 한 연결 확인](#step-7-set-azure-resource-manager-environment) | Windows 클라이언트 |

다음 섹션에서는 Azure Resource Manager 연결에서 위의 각 단계에 대해 자세히 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 Azure Resource Manager을 통해 장치에 연결 하는 데 사용 되는 클라이언트가 TLS 1.2을 사용 하는지 확인 합니다. 자세한 내용은 [Windows 클라이언트에서 TLS 1.2 구성 Azure Stack Edge 장치에 액세스](azure-stack-edge-j-series-configure-tls-settings.md)를 참조 하세요.

## <a name="step-1-configure-azure-stack-edge-device"></a>1 단계: Edge 장치 Azure Stack 구성 

Azure Stack Edge 장치의 로컬 웹 UI에서 다음 단계를 수행 합니다.

1. Azure Stack Edge 장치에 대 한 네트워크 설정을 완료 합니다. 

    ![로컬 웹 UI "네트워크 설정" 페이지](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    장치 IP 주소를 기록해 둡니다. 이 IP는 나중에 사용 합니다.

2. **장치 페이지에서** 장치 이름 및 DNS 도메인을 구성 합니다. 나중에 사용할 수 있도록 장치 이름 및 DNS 도메인을 기록해 둡니다.

    ![로컬 웹 UI "장치" 페이지](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > 장치 이름, DNS 도메인은 노출 되는 끝점을 구성 하는 데 사용 됩니다.
    > 로컬 웹 UI의 **장치** 페이지에서 Azure Resource Manager 및 Blob 끝점을 사용 합니다.


## <a name="step-2-create-and-install-certificates"></a>2 단계: 인증서 만들기 및 설치

인증서를 통해 통신이 신뢰 되는지 확인 합니다. Azure Stack Edge 장치에서 자체 서명 된 어플라이언스, blob 및 Azure Resource Manager 인증서가 자동으로 생성 됩니다. 필요에 따라 서명 된 blob 및 Azure Resource Manager 인증서를 가져올 수 있습니다.

자신의 서명 된 인증서를 가져오는 경우 인증서의 해당 서명 체인도 필요 합니다. 서명 체인, Azure Resource Manager 및 장치의 blob 인증서의 경우 클라이언트 컴퓨터에 해당 인증서를 사용 하 여 장치를 인증 하 고 통신 해야 합니다.

Azure Resource Manager에 연결 하려면 서명 체인 및 끝점 인증서를 만들거나 가져오고, Windows 클라이언트에서 이러한 인증서를 가져오고, 마지막으로 장치에 이러한 인증서를 업로드 해야 합니다.

### <a name="create-certificates-optional"></a>인증서 만들기 (선택 사항)

테스트 및 개발용 으로만 사용 되는 경우 Windows PowerShell을 사용 하 여 로컬 시스템에서 인증서를 만들 수 있습니다. 클라이언트에 대 한 인증서를 만드는 동안 다음 지침을 따르세요.

1. 먼저 서명 체인에 대 한 루트 인증서를 만들어야 합니다. 자세한 내용은 [서명 체인 인증서를 만드는](azure-stack-edge-j-series-manage-certificates.md#create-signing-chain-certificate)단계를 참조 하세요.

2. 그런 다음 blob에 대 한 끝점 인증서를 만들고 Azure Resource Manager 수 있습니다. 로컬 웹 UI의 **장치** 페이지에서 이러한 끝점을 가져올 수 있습니다. [끝점 인증서를 만드는](azure-stack-edge-j-series-manage-certificates.md#create-signed-endpoint-certificates)단계를 참조 하세요.

3. 이러한 모든 인증서의 경우 주체 이름 및 주체 대체 이름이 다음 지침을 준수 하는지 확인 합니다.

    |유형 |주체 이름 (SN)  |SAN (주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |Azure 리소스 관리자|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |두 끝점 모두에 대 한 다중 SAN 단일 인증서|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

인증서에 대 한 자세한 내용은 [인증서를 관리](azure-stack-edge-j-series-manage-certificates.md)하는 방법을 참조 하세요.

### <a name="upload-certificates-on-the-device"></a>장치에서 인증서 업로드

이전 단계에서 만든 인증서는 클라이언트의 개인 저장소에 저장 됩니다. 이러한 인증서는 클라이언트에서 적절 한 형식 파일로 내보내야 합니다. 그러면 장치에 업로드할 수 있습니다.

1. 루트 인증서는 *.cer* 파일 확장명을 가진 DER 형식 파일로 내보내야 합니다. 자세한 단계는 인증서를 [.cer 형식 파일로 내보내기](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-der-format)를 참조 하세요.

2. 끝점 인증서는 개인 키를 사용 하 여 *.pfx* 파일로 내보내야 합니다. 자세한 단계는 [개인 키를 사용 하 여 인증서를 .pfx 파일로 내보내기](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)를 참조 하세요.

3. 그런 다음 로컬 웹 UI의 **인증서** 페이지에서 **+ 인증서 추가** 옵션을 사용 하 여 장치에서 루트 및 끝점 인증서를 업로드 합니다. 인증서를 업로드 하려면 [인증서 업로드](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)의 단계를 따릅니다.


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Azure PowerShell를 실행 하는 클라이언트에서 인증서 가져오기

Azure Resource Manager Api를 호출 하는 Windows 클라이언트는 장치와의 트러스트를 설정 해야 합니다. 이를 위해 이전 단계에서 만든 인증서를 Windows 클라이언트에서 적절 한 인증서 저장소로 가져와야 합니다.

1. 이제 *.cer* 확장명을 사용 하 여 DER 형식으로 내보낸 루트 인증서를 클라이언트 시스템의 신뢰할 수 있는 루트 인증 기관에서 가져와야 합니다. 자세한 단계는 [신뢰할 수 있는 루트 인증 기관 저장소로 인증서 가져오기](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format) 를 참조 하세요.

2. *.Pfx* 로 내보낸 끝점 인증서를 *.cer*파일로 내보내야 합니다. 이 *.cer* 은 시스템의 **개인** 인증서 저장소에서 가져옵니다. 자세한 단계는 [개인 저장소로 인증서 가져오기](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format)를 참조 하세요.

## <a name="step-3-install-powershell-on-the-client"></a>3 단계: 클라이언트에 PowerShell 설치 

Windows 클라이언트는 다음 필수 구성 요소를 충족 해야 합니다.

1. PowerShell 버전 5.0을 실행 합니다. PowerShell 버전 5.0 이상이 있어야 합니다. 시스템의 PowerShell 버전을 확인 하려면 다음 cmdlet을 실행 합니다.

    ```powershell
    $PSVersionTable.PSVersion
    ```

    **주** 버전을 비교 하 고 5.0 이상 인지 확인 합니다.

    만료된 버전을 사용하는 경우 [기존 Windows PowerShell 업그레이드](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)를 참조합니다.

    \'Powershell 5.0이 없으면 [Windows powershell 설치](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6)를 수행 합니다.

    샘플 출력은 다음과 같습니다.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. PowerShell 갤러리에 액세스할 수 있습니다.

    관리자 권한으로 PowerShell을 실행합니다. PSGallery이 리포지토리로 등록 되어 있는지 확인 합니다.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    샘플 출력은 다음과 같습니다.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
리포지토리를 신뢰할 수 없거나 추가 정보가 필요한 경우 [PowerShell 갤러리 접근성 유효성 검사](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?view=azs-1908#2-validate-the-powershell-gallery-accessibility)를 참조 하세요.

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>4 단계: 클라이언트에서 Azure PowerShell 설정 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. 장치에서 작동 하는 Azure PowerShell 모듈을 클라이언트에 설치 합니다.

    a. PowerShell을 관리자 권한으로 실행합니다. PowerShell 갤러리에 대 한 액세스 권한이 필요 합니다. 


    b. PowerShell 갤러리에서 필요한 Azure PowerShell 모듈을 설치 하려면 다음 명령을 실행 합니다.

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    설치가 끝날 때 Azure RM 모듈 버전 2.5.0가 실행 되 고 있는지 확인 합니다. 
    필요한 버전과 일치 하지 않는 기존 버전의 Azure RM 모듈이 있는 경우 다음 명령을 사용 하 여 제거 합니다.

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    이제 필요한 버전을 다시 설치 해야 합니다.
   

AzureRM version 2.5.0 모듈이 성공적으로 설치 되었음을 나타내는 샘플 출력이 아래에 표시 되어 있습니다.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>5 단계: 끝점 이름 확인에 대 한 호스트 파일 수정 

이제 장치의 로컬 웹 UI에서 정의한 Azure 일관 된 VIP를 다음에 추가 합니다.

- 클라이언트의 호스트 파일 또는
- DNS 서버 구성

> [!IMPORTANT]
> 끝점 이름 확인에 대 한 DNS 서버 구성을 수정 하는 것이 좋습니다.

장치에 연결 하는 데 사용 하는 Windows 클라이언트에서 다음 단계를 수행 합니다.

1. 관리자 권한으로 **메모장** 을 시작한 다음 C:\Windows\System32\Drivers\etc.에 있는 **hosts** 파일을 엽니다.

    ![Windows 탐색기 호스트 파일](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)

2. **호스트** 파일에 다음 항목을 추가 하 여 장치에 대 한 적절 한 값으로 바꿉니다. 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > 호스트 파일의 항목은 이후 단계에서 Azure Resource Manager에 연결 하기 위해 제공 된 것과 정확히 일치 해야 합니다. 여기에 DNS 도메인 항목이 모두 소문자로 되어 있는지 확인 합니다.

    이전 단계에서 로컬 웹 UI의 장치 IP를 저장 했습니다.

    로그인 \<appliance name\> 입니다.\<DNS domain\> entry는 STS (보안 토큰 서비스)의 끝점입니다. STS는 보안 토큰의 생성, 유효성 검사, 갱신 및 취소를 담당 합니다. 보안 토큰 서비스는 장치와 클라이언트 간의 연속 통신에 사용 되는 액세스 토큰과 새로 고침 토큰을 만드는 데 사용 됩니다.

3. 참조를 위해 다음 이미지를 사용합니다. **호스트** 파일을 저장합니다.

    ![메모장에서 파일을 호스팅합니다.](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>6 단계: 클라이언트에서 끝점 이름 확인 확인

끝점 이름이 Azure 일치 VIP에 연결 하는 데 사용 하는 클라이언트에서 확인 되는지 확인 합니다.

1. ping.exe 명령줄 유틸리티를 사용 하 여 끝점 이름이 확인 되었는지 확인할 수 있습니다. IP 주소를 지정 하는 경우 ping 명령은 다시 추적 하는 컴퓨터의 TCP/IP 호스트 이름을 반환 합니다 \' .

    `-a`아래 예제에 표시 된 것 처럼 스위치를 명령줄에 추가 합니다. 호스트 이름이 returnable 인 경우에도 회신에 잠재적으로 중요 한 정보가 반환 됩니다.

    ![명령 프롬프트에서 Ping](media/azure-stack-edge-j-series-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>7 단계: Azure Resource Manager 환경 설정

Azure Resource Manager 환경을 설정 하 고 장치가 Azure Resource Manager를 통한 클라이언트 통신에 제대로 작동 하는지 확인 합니다. 이를 확인 하려면 다음 단계를 수행 합니다.


1. Cmdlet을 사용 `Add-AzureRmEnvironment` 하 여 Azure Resource Manager를 통한 통신이 정상적으로 작동 하 고 API 호출이 Azure Resource Manager-443 전용 포트를 통과 하는지 확인 합니다.

    `Add-AzureRmEnvironment`Cmdlet은 Azure Resource Manager cmdlet이 Azure Resource Manager의 새 인스턴스와 연결할 수 있도록 끝점과 메타 데이터를 추가 합니다. 


    > [!IMPORTANT]
    > 다음 cmdlet에 제공 하는 Azure Resource Manager 끝점 URL은 대/소문자를 구분 합니다. 끝점 URL이 모두 소문자 이며 호스트 파일에서 제공한 것과 일치 하는지 확인 합니다. Case가 일치 하지 않으면 오류가 표시 됩니다.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    샘플 출력은 다음과 같습니다.
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. 환경을 Azure Stack Edge로 설정 하 고 Azure Resource Manager 호출에 사용할 포트를 443으로 설정 합니다. 다음 두 가지 방법으로 환경을 정의 합니다.

    - 환경을 설정합니다. 다음 명령을 입력합니다.

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    자세한 내용은 [get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0)를 참조 하세요.

    - 실행 하는 모든 cmdlet에 대해 인라인으로 환경을 정의 합니다. 이렇게 하면 모든 API 호출이 올바른 환경을 통과 하 게 됩니다. 기본적으로 호출은 Azure public을 통과 하지만 이러한 호출은 Edge 장치 Azure Stack에 대해 설정한 환경을 통과 하 게 하려고 합니다.

    - [AzureRM 환경을 전환 하는 방법](#switch-environments)에 대 한 자세한 내용을 참조 하세요.

2. 로컬 장치 Api를 호출 하 여 Azure Resource Manager에 대 한 연결을 인증 합니다. 

    1. 이러한 자격 증명은 로컬 컴퓨터 계정에 대 한 것 이며 API 액세스에만 사용 됩니다.

    2. `login-AzureRMAccount`또는 명령을 통해 연결할 수 있습니다 `Connect-AzureRMAccount` . 

        1. 로그인 하려면 다음 명령을 입력 합니다. 이 인스턴스의 테 넌 트 ID는 하드 코드 된 c0257de7-538f-415c-993a-1b87a031879d입니다. 다음 사용자 이름 및 암호를 사용 합니다.

            - **사용자 이름**  -  *EdgeArmUser*

            - **암호**  -  [Azure Resource Manager에 대 한 암호를 설정](azure-stack-edge-j-series-set-azure-resource-manager-password.md) 하 고이 암호를 사용 하 여 로그인 합니다. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Cmdlet을 사용 하 여 로그인 할 수도 `login-AzureRmAccount` 있습니다. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -TenantId c0257de7-538f-415c-993a-1b87a031879d 

            명령의 샘플 출력은 다음과 같습니다. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> Azure Resource Manager에 대 한 연결은 1.5 시간 마다 또는 Azure Stack에 지 장치가 다시 시작 될 때 만료 됩니다. 이 경우 실행 하는 모든 cmdlet은 Azure에 더 이상 연결 되지 않은 오류 메시지를 결과에 반환 합니다. 다시 로그인 해야 합니다.

## <a name="switch-environments"></a>환경 전환

`Disconnect-AzureRmAccount`명령을 실행 하 여 다른로 전환 `AzureRmEnvironment` 합니다. 

를 사용 `Set-AzureRmEnvironment` 하지 않고 및를 사용 하는 경우 `Login-AzureRmAccount` `Disconnect-AzureRmAccount` 환경은 실제로 전환 되지 않습니다.  

다음 예에서는 두 환경 간에 전환 하는 방법을 보여 `AzDBE1` 줍니다 `AzDBE2` .

먼저 클라이언트의 모든 기존 환경을 나열 합니다.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

다음으로 Azure Resource Manager를 통해 현재 연결 된 환경을 가져옵니다.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

이제 다른 환경으로 전환 하기 전에 현재 환경과의 연결을 끊어야 합니다.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

다른 환경에 로그인 합니다. 샘플 출력은 다음과 같습니다.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

이 cmdlet을 실행 하 여 연결 된 환경을 확인할 수 있습니다.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
이제 원하는 환경으로 전환 했습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack Edge 장치에 vm을 배포](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)합니다.
