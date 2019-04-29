---
title: StorSimple 디바이스에 원격으로 연결 | Microsoft Docs
description: 원격 관리를 위해 디바이스를 구성하는 방법 및 HTTP 또는 HTTPS를 통해 StorSimple용 Windows PowerShell에 연결하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bec60f4c56c98e9b910b50e858656a2e5554b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631880"
---
# <a name="connect-remotely-to-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 디바이스에 원격으로 연결

## <a name="overview"></a>개요

Windows PowerShell을 통해 디바이스에 원격으로 연결할 수 있습니다. 이러한 방식으로 연결하면 메뉴가 표시되지 않습니다. 디바이스의 직렬 콘솔을 사용하여 연결하는 경우에만 메뉴가 표시됩니다. Windows PowerShell 원격을 사용하여 특정 Runspace에 연결합니다. 표시 언어를 지정할 수도 있습니다.

Windows PowerShell 원격을 사용하여 디바이스를 관리하는 방법에 대한 자세한 내용은 [StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스 관리](storsimple-8000-windows-powershell-administration.md)를 참조하세요.

이 자습서에서는 원격 관리를 위해 디바이스를 구성하는 방법 및 StorSimple용 Windows PowerShell에 연결하는 방법을 설명합니다. HTTP 또는 HTTPS를 사용하여 Windows PowerShell을 통해 원격으로 연결할 수 있습니다. 그러나 StorSimple용 Windows PowerShell에 연결하는 방법을 결정하는 경우 다음 정보를 고려하세요.

* 디바이스 직렬 콘솔에 직접 연결하는 것은 안전하지만 네트워크 스위치를 통해 직렬 콘솔에 연결하는 것은 안전하지 않습니다. 네트워크 스위치를 통해 디바이스 직렬 콘솔에 연결할 때는 보안 위험에 주의하세요.
* HTTP 세션을 통해 연결하는 경우 네트워크에서 직렬 콘솔을 통해 연결하는 것보다 보안이 강화될 수 있습니다. 가장 안전한 방법은 아니지만 신뢰할 수 있는 네트워크에서는 적합합니다.
* 가장 안전하고 권장되는 옵션은 자체 서명된 인증서를 사용하여 HTTPS 세션을 통해 연결하는 것입니다.

Windows PowerShell 인터페이스에 원격으로 연결할 수 있습니다. 그러나 Windows PowerShell 인터페이스를 통한 StorSimple 디바이스에 대한 원격 액세스는 기본적으로 사용되지 않습니다. 먼저, 디바이스에서 원격 관리를 사용하도록 설정한 다음 디바이스에 액세스하는 데 사용되는 클라이언트에서 사용하도록 설정해야 합니다.

이 문서에 설명된 단계는 Windows Server 2012 R2를 실행하는 호스트 시스템에서 수행되었습니다.

## <a name="connect-through-http"></a>HTTP를 통해 연결

HTTP 세션을 통해 StorSimple용 Windows PowerShell에 연결하는 경우 StorSimple 디바이스의 직렬 콘솔을 통해 연결하는 것보다 보안이 강화됩니다. 가장 안전한 방법은 아니지만 신뢰할 수 있는 네트워크에서는 적합합니다.

Azure Portal 또는 직렬 콘솔을 사용하여 원격 관리를 구성할 수 있습니다. 다음 절차에서 선택합니다.

* Azure Portal을 사용하여 HTTP를 통한 원격 관리 사용
* [직렬 콘솔을 사용하여 HTTP를 통한 원격 관리 사용](#use-the-serial-console-to-enable-remote-management-over-http)

원격 관리를 사용하도록 설정한 후 다음 절차에 따라 원격 연결을 위해 클라이언트를 준비합니다.

* [원격 연결을 위해 클라이언트 준비](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-portal-to-enable-remote-management-over-http"></a>Azure Portal을 사용하여 HTTP를 통한 원격 관리 사용

Azure Portal에서 다음 단계를 수행하여 HTTP를 통한 원격 관리를 사용할 수 있습니다.

#### <a name="to-enable-remote-management-through-the-azure-portal"></a>Azure Portal을 통해 원격 관리를 사용하도록 설정하려면

1. StorSimple 디바이스 관리자 서비스로 이동합니다. **디바이스**를 선택하고 원격 관리용으로 구성하려는 디바이스를 클릭하여 선택합니다. **디바이스 설정 &gt; 보안**으로 이동합니다.
2. **보안 설정** 블레이드에서 **원격 관리**를 클릭합니다.
3. **원격 관리** 블레이드에서 **원격 관리 사용**을 **예**로 설정합니다.
4. 이제 HTTP를 사용하여 연결하도록 선택할 수 있습니다. 기본값은 HTTPS를 통한 연결입니다. HTTP가 선택되었는지 확인합니다.
   
   > [!NOTE]
   > HTTP를 통한 연결은 신뢰할 수 있는 네트워크에서만 허용됩니다.
   
5. **저장**을 클릭하고 확인하라는 메시지가 표시되면 **예**를 선택합니다.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>직렬 콘솔을 사용하여 HTTP를 통한 원격 관리 사용
원격 관리를 사용하도록 설정하려면 디바이스 직렬 콘솔에서 다음 단계를 따르세요.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>디바이스 직렬 콘솔을 통해 원격 관리를 사용하도록 설정하려면
1. 직렬 콘솔 메뉴에서 옵션 1을 선택합니다. 디바이스의 직렬 콘솔을 사용하는 방법에 대한 자세한 내용은 [디바이스 직렬 콘솔을 통해 StorSimple용 Windows PowerShell에 연결](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)을 참조하세요.
2. 프롬프트에 다음을 입력합니다. `Enable-HcsRemoteManagement –AllowHttp`
3. HTTP를 사용하여 디바이스에 연결하는 경우의 보안 취약점에 대한 알림이 표시됩니다. 메시지가 표시되면 **Y**를 입력하여 확인합니다.
4. 다음을 입력하여 HTTP를 사용할 수 있는지 확인합니다. `Get-HcsSystem`
5. **RemoteManagementMode** 필드에 **HttpsAndHttpEnabled**가 표시되는지 확인합니다. 다음 그림은 PuTTY에서 이러한 설정을 보여 줍니다.
   
     ![직렬 HTTPS 및 HTTP 사용](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>원격 연결을 위해 클라이언트 준비
원격 관리를 사용하도록 설정하려면 클라이언트에서 다음 단계를 따르세요.

#### <a name="to-prepare-the-client-for-remote-connection"></a>원격 연결을 위해 클라이언트를 준비하려면
1. 관리자 권한으로 Windows PowerShell 세션을 시작합니다. Windows 10 클라이언트를 사용하는 경우, 기본적으로 Windows 원격 관리 서비스가 수동으로 설정되어 있습니다. 다음을 입력하여 서비스를 시작해야 할 수 있습니다.

    `Start-Service WinRM`
    
2. 다음 명령을 입력하여 클라이언트의 신뢰할 수 있는 호스트 목록에 StorSimple 디바이스의 IP 주소를 추가합니다.
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
     &lt;*device_ip*&gt;를 디바이스의 IP 주소로 대체합니다. 예를 들면 다음과 같습니다. 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. 다음 명령을 입력하여 변수에 디바이스 자격 증명을 저장합니다. 
   
    ```
    $cred = Get-Credential
    ```
    
4. 나타나는 대화 상자에서 다음을 수행합니다.
   
   1. *device_ip\SSAdmin* 형식으로 사용자 이름을 입력합니다.
   2. 설정 마법사를 사용하여 디바이스를 구성할 때 설정한 디바이스 관리자 암호를 입력합니다. 기본 암호는 *Password1*입니다.
5. 다음 명령을 입력하여 디바이스에서 Windows PowerShell 세션을 시작합니다.
   
     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`
   
   > [!NOTE]
   > StorSimple 가상 디바이스에 사용할 Windows PowerShell 세션을 만들려면 `–Port` 매개 변수를 추가하고 Remoting for StorSimple Virtual Appliance에서 구성한 공용 포트를 지정합니다.
   
   
이제 디바이스에 대한 활성 원격 Windows PowerShell 세션이 있습니다.
   
![HTTP를 사용한 PowerShell 원격](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>HTTPS를 통해 연결

HTTPS 세션을 통해 StorSimple용 Windows PowerShell에 연결하는 것은 Microsoft Azure StorSimple 디바이스에 원격으로 연결하는 가장 안전하고 권장되는 방법입니다. 다음 절차에서는 HTTPS를 사용 여 StorSimple용 Windows PowerShell에 연결할 수 있도록 직렬 콘솔과 클라이언트 컴퓨터를 설정하는 방법을 설명합니다.

Azure Portal 또는 직렬 콘솔을 사용하여 원격 관리를 구성할 수 있습니다. 다음 절차에서 선택합니다.

* Azure Portal을 사용하여 HTTPS를 통한 원격 관리 사용
* [직렬 콘솔을 사용하여 HTTPS를 통한 원격 관리 사용](#use-the-serial-console-to-enable-remote-management-over-https)

원격 관리를 사용하도록 설정한 후 다음 절차에 따라 원격 관리를 위해 호스트를 준비하고 원격 호스트에서 디바이스에 연결합니다.

* [원격 관리를 위해 호스트 준비](#prepare-the-host-for-remote-management)
* [원격 호스트에서 디바이스에 연결](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-portal-to-enable-remote-management-over-https"></a>Azure Portal을 사용하여 HTTPS를 통한 원격 관리 사용

Azure Portal에서 다음 단계를 수행하여 HTTPS를 통한 원격 관리를 사용할 수 있습니다.

#### <a name="to-enable-remote-management-over-https-from-the-azure-portal"></a>Azure Portal에서 HTTPS를 통한 원격 관리를 사용하도록 설정하려면

1. StorSimple 디바이스 관리자 서비스로 이동합니다. **디바이스**를 선택하고 원격 관리용으로 구성하려는 디바이스를 클릭하여 선택합니다. **디바이스 설정 &gt; 보안**으로 이동합니다.
2. **보안 설정** 블레이드에서 **원격 관리**를 클릭합니다.
3. **원격 관리 사용**을 **예**로 설정합니다.
4. 이제 HTTPS를 사용하여 연결하도록 선택할 수 있습니다. 기본값은 HTTPS를 통한 연결입니다. HTTPS가 선택되었는지 확인합니다.
5. ...를 클릭한 후 **원격 관리 인증서 다운로드**를 클릭합니다. 이 파일을 저장할 위치를 지정합니다. 디바이스에 연결하는 데 사용할 클라이언트 또는 호스트 컴퓨터에 이 인증서를 설치해야 합니다.
6. **저장**을 클릭하고 확인하라는 메시지가 표시되면 **예**를 클릭합니다.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>직렬 콘솔을 사용하여 HTTPS를 통한 원격 관리 사용

원격 관리를 사용하도록 설정하려면 디바이스 직렬 콘솔에서 다음 단계를 따르세요.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>디바이스 직렬 콘솔을 통해 원격 관리를 사용하도록 설정하려면
1. 직렬 콘솔 메뉴에서 옵션 1을 선택합니다. 디바이스의 직렬 콘솔을 사용하는 방법에 대한 자세한 내용은 [디바이스 직렬 콘솔을 통해 StorSimple용 Windows PowerShell에 연결](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)을 참조하세요.
2. 프롬프트에 다음을 입력합니다.
   
     `Enable-HcsRemoteManagement`
   
    이제 디바이스에서 HTTPS를 사용할 수 있습니다.
3. 다음을 입력하여 HTTPS를 사용할 수 있는지 확인합니다. 
   
     `Get-HcsSystem`
   
    **RemoteManagementMode** 필드에 **HttpsEnabled**가 표시되는지 확인합니다. 다음 그림은 PuTTY에서 이러한 설정을 보여 줍니다.
   
     ![직렬 HTTPS 사용](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)
4. `Get-HcsSystem`의 출력에서 디바이스의 일련 번호를 복사하고 나중에 사용하기 위해 저장합니다.
   
   > [!NOTE]
   > 일련 번호는 인증서의 CN 이름에 매핑됩니다.
   
5. 다음을 입력하여 원격 관리 인증서를 가져옵니다. 
   
     `Get-HcsRemoteManagementCert`
   
    다음과 유사한 인증서가 나타납니다.
   
    ![원격 관리 인증서 가져오기](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)
6. **-----BEGIN CERTIFICATE-----** 에서 **-----END CERTIFICATE-----** 까지 인증서 정보를 메모장 등의 텍스트 편집기에 복사하고 .cer 파일로 저장합니다. 호스트를 준비할 때 이 파일을 원격 호스트에 복사합니다.
   
   > [!NOTE]
   > 새 인증서를 생성하려면 `Set-HcsRemoteManagementCert` cmdlet을 사용합니다.
   
### <a name="prepare-the-host-for-remote-management"></a>원격 관리를 위해 호스트 준비

HTTPS 세션을 사용하는 원격 연결을 위해 호스트 컴퓨터를 준비하려면 다음 절차를 따르세요.

* [클라이언트 또는 원격 호스트의 루트 저장소로 .cer 파일을 가져옵니다](#to-import-the-certificate-on-the-remote-host).
* [원격 호스트의 호스트 파일에 디바이스 일련 번호를 추가합니다](#to-add-device-serial-numbers-to-the-remote-host).

아래에서는 앞서 나온 각 절차에 대해 설명합니다.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>원격 호스트에서 인증서를 가져오려면
1. .cer 파일을 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 선택합니다. 인증서 가져오기 마법사가 시작됩니다.
   
    ![인증서 가져오기 마법사 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)
2. **저장소 위치**에 대해 **로컬 컴퓨터**를 선택하고 **다음**을 클릭합니다.
3. **모든 인증서를 다음 저장소에 저장**을 선택하고 **찾아보기**를 클릭합니다. 원격 호스트의 루트 저장소로 이동한 후 **다음**을 클릭합니다.
   
    ![인증서 가져오기 마법사 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)
4. **Finish**를 클릭합니다. 가져오기에 성공했음을 알리는 메시지가 나타납니다.
   
    ![인증서 가져오기 마법사 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>원격 호스트에 디바이스 일련 번호를 추가하려면
1. 관리자 권한으로 메모장을 시작하고 \Windows\System32\Drivers\etc에 있는 호스트 파일을 엽니다.
2. 다음 3개 항목을 호스트 파일에 추가합니다. 3개 항목은 **DATA 0 IP 주소**, **컨트롤러 0 고정 IP 주소** 및 **컨트롤러 1 고정 IP 주소**입니다.
3. 이전에 저장한 디바이스 일련 번호를 입력합니다. 다음 그림과 같이 이 일련 번호를 IP 주소에 매핑합니다. 컨트롤러 0과 컨트롤러 1의 경우 일련 번호(CN 이름)의 끝에 **Controller0** 및 **Controller1**을 추가합니다.
   
    ![hosts 파일에 CN 이름 추가](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)
4. 호스트 파일을 저장합니다.

### <a name="connect-to-the-device-from-the-remote-host"></a>원격 호스트에서 디바이스에 연결

Windows PowerShell 및 SSL을 사용하여 원격 호스트 또는 클라이언트에서 디바이스의 SSAdmin 세션에 들어갑니다. SSAdmin 세션은 디바이스의 [직렬 콘솔](storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) 메뉴에 있는 옵션 1에 매핑됩니다.

원격 Windows PowerShell 연결을 설정하려는 컴퓨터에서 다음 절차를 따르세요.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Windows PowerShell 및 SSL을 사용하여 디바이스의 SSAdmin 세션에 들어가려면
1. 관리자 권한으로 Windows PowerShell 세션을 시작합니다. Windows 10 클라이언트를 사용하는 경우, 기본적으로 Windows 원격 관리 서비스가 수동으로 설정되어 있습니다. 다음을 입력하여 서비스를 시작해야 할 수 있습니다.

    `Start-Service WinRM`

2. 다음을 입력하여 클라이언트의 신뢰할 수 있는 호스트에 디바이스 IP 주소를 추가합니다.
   
     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`
   
    여기서 &lt;*device_ip*&gt;는 디바이스의 IP 주소입니다. 예를 들면 다음과 같습니다. 
   
     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`
3. 새 자격 증명을 만들려면 다음을 입력합니다.
   
     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`
   
    여기서 &lt;*대상 디바이스의 IP*&gt;는 디바이스에 대한 DATA 0의 IP 주소(예: hosts 파일의 이전 그림에 표시된 **10.126.173.90**)입니다. 또한 디바이스에 대한 관리자 암호를 제공합니다.
4. 다음을 입력하여 세션을 만듭니다.
   
     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`
   
    cmdlet의 ComputerName 매개 변수의 경우 &lt;*대상 디바이스의 일련 번호*&gt;를 제공합니다. 이 일련 번호는 원격 호스트에서 hosts 파일에 있는 DATA 0의 IP 주소(예: 다음 그림에 표시된 **SHX0991003G44MT** )에 매핑되었습니다.
5. 형식:
   
     `Enter-PSSession $session`
6. 몇 분 정도 기다리면 HTTPS over SSL을 통해 디바이스에 연결됩니다. 디바이스에 연결되었음을 나타내는 메시지가 표시됩니다.
   
    ![HTTPS 및 SSL을 사용한 PowerShell 원격](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>다음 단계

* [Windows PowerShell을 사용하여 StorSimple 디바이스를 관리하는 방법](storsimple-8000-windows-powershell-administration.md)을 자세히 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리하는 방법](storsimple-8000-manager-service-administration.md)에 대해 자세히 알아봅니다.

