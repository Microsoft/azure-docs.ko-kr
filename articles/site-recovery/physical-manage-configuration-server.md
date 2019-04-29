---
title: Azure Site Recovery를 사용한 온-프레미스 물리적 서버와 Azure 간 재해 복구를 위한 구성 서버 관리 | Microsoft Docs
description: 이 문서에서는 물리적 서버와 Azure 간 재해 복구를 위한 Azure Site Recovery 구성 서버를 관리하는 방법을 설명합니다.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: 10bec01a3b90776c8dd8c32a74ba7754264da131
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119733"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>물리적 서버 재해 복구용 구성 서버 관리

Azure에 대한 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용할 경우 온-프레미스 구성 서버를 설정합니다. 구성 서버는 온-프레미스 컴퓨터와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다. 이 문서에서는 배포된 구성 서버를 관리하기 위한 일반적인 작업을 요약합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

아래 표에는 온-프레미스 구성 서버 컴퓨터 배포를 위한 필수 구성 요소가 요약되어 있습니다.

| **구성 요소** | **요구 사항** |
| --- |---|
| CPU 코어| 8 |
| RAM | 16GB|
| 디스크 수 | 3, OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용) 포함 |
| 사용 가능한 디스크 공간(프로세스 서버 캐시) | 600GB
| 사용 가능한 디스크 공간(보존 디스크) | 600GB|
| 운영 체제  | Windows Server 2012 R2 <br> Windows Server 2016 |
| 운영 체제 로케일 | 영어(미국)|
| VMware vSphere PowerCLI 버전 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V |
| 그룹 정책| 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세히 알아보기](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - 기존의 기본 웹 사이트 없음 <br> - [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용  <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/응용 프로그램 없음<br>|
| NIC 유형 | VMXNET3(VMware VM으로 배포될 경우) |
| IP 주소 유형 | 공용 |
| 인터넷 액세스 | 서버에서 다음 URL에 액세스해야 합니다. <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi(스케일 아웃 프로세스 서버에는 필요하지 않음) <br> - time.nist.gov <br> - time.windows.com |
| 포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송)|

## <a name="download-the-latest-installation-file"></a>최신 설치 파일 다운로드

구성 서버 설치 파일의 최신 버전은 Site Recovery 포털에서 사용할 수 있습니다. 또한 [Microsoft 다운로드 센터](https://aka.ms/unifiedsetup)에서 직접 다운로드할 수 있습니다.

1. Azure Portal에 로그온하고 Recovery Services 자격 증명 모음으로 이동합니다.
2. **Site Recovery 인프라** > **구성 서버**(VMware 및 물리적 컴퓨터용 아래)로 이동합니다.
3. **+ 서버** 단추를 클릭합니다.
4. **서버 추가** 페이지에서 다운로드 단추를 클릭하여 등록 키를 다운로드합니다. 구성 서버 설치 동안 Azure Site Recovery 서비스에 등록하기 위해 이 키가 필요합니다.
5. **Microsoft Azure Site Recovery 통합 설치 다운로드** 링크를 클릭하여 구성 서버의 최신 버전을 다운로드합니다.

   ![다운로드 페이지](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>서버 설치 및 등록

1. 통합 설치 프로그램 설치 파일을 실행합니다.
2. **시작하기 전에**에서 **구성 서버 및 프로세스 서버 설치**를 선택합니다.

    ![시작하기 전에](./media/physical-manage-configuration-server/combined-wiz1.png)

3. **타사 소프트웨어 라이선스**에서 **동의함**을 클릭하고 MySQL을 다운로드 및 설치합니다.
4. **인터넷 설정**에서 구성 서버에서 실행 중인 공급자가 인터넷을 통해 Azure Site Recovery에 연결하는 방법을 지정합니다. 필수 URL을 허용했는지 확인합니다.

    - 현재 컴퓨터에 설정된 프록시를 사용하여 연결하려면 **프록시 서버를 사용하여 Azure Site Recovery에 연결**을 선택합니다.
    - 공급자를 직접 연결하려면 **프록시 서버 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
    - 기존 프록시에 인증이 필요하거나 공급자 연결에 사용자 지정 프록시를 사용하려면 **사용자 지정 프록시 설정으로 연결**을 선택하고 주소, 포트 및 자격 증명을 지정합니다.
     ![방화벽](./media/physical-manage-configuration-server/combined-wiz4.png)
6. **필수 조건 확인**에서 설치 프로그램은 설치가 실행될 수 있는지 확인합니다. **글로벌 시간 동기화 확인**에 대한 경고가 표시되면 시스템 시계의 시간(**날짜 및 시간** 설정)이 표준 시간대와 같은지 확인합니다.

    ![필수 조건](./media/physical-manage-configuration-server/combined-wiz5.png)
7. **MySQL 구성**에서 설치된 MySQL 서버 인스턴스에 로그온하기 위한 자격 증명을 만듭니다.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. **환경 세부 정보**에서 VMware VM을 복제 여부를 선택합니다. 복제할 경우 설치 프로그램에서 PowerCLI 6.0이 설치되어 있는지 확인합니다.
9. **설치 위치**에서 이진 파일을 설치하고 캐시를 저장할 위치를 선택합니다. 최소 5GB의 디스크 공간이 있는 드라이브를 선택해야 하지만 600GB 이상의 사용 가능한 공간이 있는 캐시 드라이브를 선택하는 것이 좋습니다.

    ![설치 위치](./media/physical-manage-configuration-server/combined-wiz8.png)
10. **네트워크 선택**, 먼저 원본 컴퓨터에서 모바일 서비스의 검색 및 푸시 설치에 대 한 기본 제공 프로세스 서버를 사용 하는 NIC를 선택 하 고 선택한 다음 연결을 위한 구성 서버를 사용 하는 NIC azure. 9443 포트는 복제 트래픽을 보내고 받는 데 사용되는 기본 포트이지만, 환경의 요구 사항에 맞게 이 포트 번호를 수정할 수 있습니다. 9443 포트 외에도 웹 서버에서 복제 작업을 조정하기 위해 사용하는 443 포트를 엽니다. 복제 트래픽을 보내거나 받는 데 443 포트를 사용하면 안 됩니다.

    ![네트워크 선택](./media/physical-manage-configuration-server/combined-wiz9.png)


11. **요약**에서 정보를 검토하고 **설치**를 클릭합니다. 설치가 완료되면 암호가 생성됩니다. 복제를 사용하도록 설정할 때 필요하므로 암호를 복사하고 안전한 위치에 보관합니다.


등록이 완료되면 자격 증명 모음의 **설정** > **서버** 블레이드에 서버가 표시됩니다.


## <a name="install-from-the-command-line"></a>명령줄에서 설치

설치 파일을 다음과 같이 실행합니다.

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>샘플 사용
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>매개 변수

|매개 변수 이름| Type | 설명| 값|
|-|-|-|-|
| /ServerMode|필수|구성 서버와 프로세스 서버를 모두 설치할지 또는 프로세스 서버만 설치할지 여부를 지정합니다.|CS<br>PS|
|/InstallLocation|필수|구성 요소가 설치되는 폴더입니다.| 컴퓨터의 모든 폴더|
|/MySQLCredsFilePath|필수|MySQL 서버 자격 증명이 저장되는 파일 경로입니다.|파일은 아래에 지정된 형식이어야 합니다.|
|/VaultCredsFilePath|필수|자격 증명 모음 자격 증명 파일의 경로입니다.|유효한 파일 경로|
|/EnvType|필수|보호하려는 환경 유형입니다. |VMware<br>NonVMware|
|/PSIP|필수|복제 데이터 전송에 사용할 NIC의 IP 주소입니다.| 모든 유효한 IP 주소|
|/CSIP|필수|구성 서버가 수신 대기하는 NIC의 IP 주소입니다.| 모든 유효한 IP 주소|
|/PassphraseFilePath|필수|암호 파일의 위치에 대한 전체 경로입니다.|유효한 파일 경로|
|/BypassProxy|옵션|구성 서버가 프록시 없이 Azure에 연결되도록 지정합니다.|이렇게 하려면 Venu에서 이 값을 가져옵니다.|
|/ProxySettingsFilePath|옵션|프록시 설정(인증이 필요한 기본 프록시 또는 사용자 지정 프록시)입니다.|파일은 아래에 지정된 형식이어야 합니다.|
|DataTransferSecurePort|옵션|복제 데이터에 사용할 PSIP의 포트 번호입니다.| 유효한 포트 번호(기본값: 9433)|
|/SkipSpaceCheck|옵션|캐시 디스크의 공간 확인을 건너뜁니다.| |
|/AcceptThirdpartyEULA|필수|플래그는 타사 EULA에 대한 동의를 의미합니다.| |
|/ShowThirdpartyEULA|옵션|타사 EULA를 표시합니다. 입력으로 제공되는 경우 다른 모든 매개 변수가 무시됩니다.| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>MYSQLCredsFilePath에 대한 파일 입력 만들기

MySQLCredsFilePath 매개 변수는 입력으로 파일을 사용합니다. 다음 형식을 사용하여 파일을 만들고 입력 MySQLCredsFilePath 매개 변수로 전달합니다.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>ProxySettingsFilePath에 대한 파일 입력 만들기
ProxySettingsFilePath 매개 변수는 입력으로 파일을 사용합니다. 다음 형식을 사용하여 파일을 만들고 입력 ProxySettingsFilePath 매개 변수로 전달합니다.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>프록시 설정 수정

구성 서버 컴퓨터에 대한 프록시 설정을 다음과 같이 수정할 수 있습니다.

1. 구성 서버에 로그온합니다.
2. 데스크톱에서 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
3. **자격 증명 모음 등록** 탭을 클릭합니다.
4. 포털에서 새 자격 증명 모음 등록 파일을 다운로드하고 도구에 대한 입력으로 제공합니다.

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. 새 프록시 세부 정보를 제공하고 **등록** 단추를 클릭합니다.
6. 관리자 PowerShell 명령 창을 엽니다.
7. 다음 명령 실행:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > 이 구성 서버에 추가 프로세스 서버가 연결된 경우 배포의 [모든 확장 프로세스 서버에서 프록시 설정을 수정](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server)해야 합니다.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>동일한 자격 증명 모음에 구성 서버 다시 등록
1. 구성 서버에 로그인 합니다.
2. 데스크톱에서 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
3. **자격 증명 모음 등록** 탭을 클릭합니다.
4. 포털에서 새 등록 파일을 다운로드하고 도구에 대한 입력으로 제공합니다.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. 프록시 서버 세부 정보를 제공하고 **등록** 단추를 클릭합니다.  
6. 관리자 PowerShell 명령 창을 엽니다.
7. 다음 명령을 실행합니다.

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > 여러 프로세스 서버가 있는 경우 [다시 등록](vmware-azure-manage-process-server.md#reregister-a-process-server)해야 합니다.

## <a name="register-a-configuration-server-with-a-different-vault"></a>다른 자격 증명 모음에 구성 서버 등록

> [!WARNING]
> 다음 단계에서는 현재 자격 증명 모음으로부터 구성 서버를 분리하고 구성 서버에서 보호된 모든 가상 머신의 복제를 중지합니다.

1. 구성 서버 컴퓨터에 로그온합니다.
2. 관리자 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. 데스크톱에서 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
4. **자격 증명 모음 등록** 탭을 클릭합니다.
5. 포털에서 새 등록 파일을 다운로드하고 도구에 대한 입력으로 제공합니다.
6. 프록시 서버 세부 정보를 제공하고 **등록** 단추를 클릭합니다.  
7. 관리자 PowerShell 명령 창을 엽니다.
8. 다음 명령을 실행합니다.
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>구성 서버 업그레이드

구성 서버를 업데이트하려면 업데이트 롤업을 실행합니다. 업데이트는 N-4 버전까지 적용할 수 있습니다. 예를 들면 다음과 같습니다.

- 9.7, 9.8, 9.9 또는 9.10을 실행 중인 경우 9.11로 바로 업그레이드할 수 있습니다.
- 9.6 이하를 실행 중이고 9.11로 업그레이드하려는 경우 먼저 9.7 버전으로 업그레이드한 후 9.11로 업그레이드해야 합니다.

모든 버전의 구성 서버로 업그레이드할 수 있는 업데이트 롤업 링크가 [wiki 업데이트 페이지](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)에 제공됩니다.

다음과 같이 서버를 업그레이드합니다.

1. 업데이트 설치 관리자 파일을 구성 서버에 다운로드합니다.
2. 두 번 클릭하여 설치 관리자를 실행합니다.
3. 설치 관리자는 컴퓨터에서 실행 중인 현재 버전을 검색합니다.
4. **확인**을 클릭하여 확인하고 업그레이드를 실행합니다. 


## <a name="delete-or-unregister-a-configuration-server"></a>구성 서버 삭제 또는 등록 취소

> [!WARNING]
> 구성 서버의 서비스 해제를 시작하기 전에 다음 사항을 확인하세요.
> 1. 이 구성 서버 아래의 모든 가상 머신에 대한 [보호를 사용하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)합니다.
> 2. 구성 서버에서 모든 복제 정책을 [연결 해제](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) 및 [삭제](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)합니다.
> 3. 구성 서버에 연결된 모든 vCenter 서버/vSphere 호스트를 [삭제](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)합니다.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Azure Portal에서 구성 서버 삭제
1. Azure Portal의 자격 증명 모음 메뉴에서 **Site Recovery 인프라** > **구성 서버**로 이동합니다.
2. 서비스를 해제하려는 구성 서버를 클릭합니다.
3. 구성 서버의 세부 정보 페이지에서 **삭제** 단추를 클릭합니다.
4. **예**를 클릭하여 서버 삭제를 확인합니다.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>구성 서버 및 해당 종속성 제거
> [!TIP]
>   Azure Site Recovery에서 구성 서버를 다시 사용하려는 경우에는 4단계로 직접 건너뛸 수 있습니다.

1. 관리자 권한으로 구성 서버에 로그온합니다.
2. 제어판 > 프로그램 > 프로그램 제거 열기
3. 다음 순서로 프로그램을 제거합니다.
   * Microsoft Azure Recovery Services 에이전트
   * Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버
   * Microsoft Azure Site Recovery 공급자
   * Microsoft Azure Site Recovery 구성 서버/프로세스 서버
   * Microsoft Azure Site Recovery 구성 서버 종속성
   * MySQL Server 5.5
4. 관리자 명령 프롬프트에서 다음 명령을 실행합니다.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>구성 서버 삭제 또는 등록 취소(PowerShell)

1. Azure PowerShell 모듈 [설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. 다음 명령을 사용하여 Azure 계정에 로그인
    
    `Connect-AzAccount`
3. 자격 증명 모음이 있는 구독 선택

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  자격 증명 모음 컨텍스트 설정
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. 구성 서버 선택

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 구성 서버 삭제

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> 합니다 **-강제로** 제거/삭제 구성 서버의 강제 제거-AzSiteRecoveryFabric에서 옵션을 사용할 수 있습니다.

## <a name="renew-ssl-certificates"></a>SSL 인증서 갱신
구성 서버에는 기본 제공 웹 서버가 있습니다. 이 서버는 모바일 서비스, 프로세스 서버 및 마스터 대상 서버의 작업을 오케스트레이션합니다. 웹 서버는 SSL 인증서를 사용하여 클라이언트를 인증합니다. 인증서는 3년 후에 만료되며 언제든지 갱신할 수 있습니다.

### <a name="check-expiry"></a>만료 확인

2016년 5월 이전의 구성 서버 배포의 경우, 인증서 만료가 1년으로 설정되었습니다. 인증서가 곧 만료될 예정인 경우 다음이 발생합니다.

- 만료 날짜가 2개월 이내로 남은 경우 서비스는 포털을 통해 및 전자 메일로 알림을 전송하기 시작합니다(Azure Site Recovery 알림을 구독한 경우).
- 알림 배너가 자격 증명 모음 리소스 페이지에 나타납니다. 자세한 내용을 보려면 배너를 클릭합니다.
- **지금 업그레이드** 단추가 표시되면 작업 환경의 일부 구성 요소가 아직 9.4.xxxx.x 이상 버전으로 업그레이드되지 않았음을 의미합니다. 인증서를 갱신하기 전에 구성 요소를 업그레이드합니다. 이전 버전에서는 갱신할 수 없습니다.

### <a name="renew-the-certificate"></a>인증서 갱신

1. 자격 증명 모음에서 **Site Recovery 인프라** > **구성 서버**를 열고 필요한 구성 서버를 클릭합니다.
2. 만료 날짜가 **구성 서버 상태** 아래에 표시됩니다.
3. **인증서 갱신**을 클릭합니다. 




## <a name="common-issues"></a>일반적인 문제
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>다음 단계

Azure에 대한 [물리적 서버](tutorial-physical-to-azure.md)의 재해 복구를 설정하기 위한 자습서를 검토하세요.

