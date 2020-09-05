---
title: PowerShell을 사용하여 Azure에 Windows Server 백업
description: 이 문서에서는 PowerShell을 사용 하 여 Windows Server 또는 Windows 클라이언트에서 Azure Backup를 설정 하 고 백업 및 복구를 관리 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 47c8fc39626d3bca3355c1d1e46f1634327748a8
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892374"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>PowerShell을 사용하여 Windows Server/Windows Client용 Azure 백업 배포 및 관리

이 문서에서는 PowerShell을 사용 하 여 Windows Server 또는 Windows 클라이언트에서 Azure Backup를 설정 하 고 백업 및 복구를 관리 하는 방법을 보여 줍니다.

## <a name="install-azure-powershell"></a>Azure Powershell 설치

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

시작하려면 [최신 PowerShell 릴리스를 설치](/powershell/azure/install-az-ps)합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계는 Recovery Services 자격 증명 모음을 만드는 과정을 안내합니다. Recovery Services 자격 증명 모음은 Backup 자격 증명 모음과 다릅니다.

1. Azure Backup를 처음으로 사용 하는 경우 **AzResourceProvider** cmdlet을 사용 하 여 Azure 복구 서비스 공급자를 구독에 등록 해야 합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services 자격 증명 모음은 Azure Resource Manager 리소스 이므로 리소스 그룹 내에 저장 해야 합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다. 새 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. **New-AzRecoveryServicesVault** cmdlet을 사용하여 새 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정해야 합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. 사용할 스토리지 중복 유형을 지정합니다. [LRS (로컬 중복 저장소)](../storage/common/storage-redundancy.md) 또는 [GRS (지역 중복 저장소](../storage/common/storage-redundancy.md))를 사용할 수 있습니다. 다음 예에서는 *Testvault* 에 대 한 **-BackupStorageRedundancy** 옵션을 **GeoRedundant**로 설정 하는 방법을 보여 줍니다.

   > [!TIP]
   > 많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다. 이러한 이유로 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장 하는 것이 편리 합니다.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

**Get-AzRecoveryServicesVault**를 사용하여 현재 구독의 모든 자격 증명 모음 목록을 볼 수 있습니다. 이 명령을 사용 하 여 새 자격 증명 모음이 만들어졌는지 확인 하거나 구독에서 사용할 수 있는 자격 증명 모음을 확인할 수 있습니다.

**Get-AzRecoveryServicesVault** 명령을 실행하면 구독의 모든 자격 증명 모음이 나열됩니다.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Azure Backup 에이전트 설치

Azure Backup 에이전트를 설치하기 전에 Windows Server에 설치 관리자를 다운로드해 두어야 합니다. 최신 버전의 설치 관리자는 [Microsoft 다운로드 센터](https://aka.ms/azurebackup_agent) 또는 Recovery Services의 자격 증명 모음 대시보드 페이지에서 다운로드할 수 있습니다. 쉽게 액세스할 수 있는 위치(예: `C:\Downloads\*`)에 설치 관리자를 저장합니다.

또는 PowerShell을 사용하여 다운로더를 가져옵니다.

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

에이전트를 설치하려면 승격된 PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
MARSAgentInstaller.exe /q
```

그러면 에이전트가 모두 기본 옵션으로 설치됩니다. 설치는 백그라운드에서 몇 분 정도 소요됩니다. */Nu* 옵션을 지정 하지 않으면 설치 종료 시 **Windows 업데이트** 창이 열리고 업데이트가 있는지 확인할 수 있습니다. 설치되면 설치된 프로그램 목록에 에이전트가 표시됩니다.

설치된 프로그램 목록을 보려면 **제어판** > **프로그램** > **프로그램 및 기능**으로 이동합니다.

![에이전트 설치됨](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>설치 옵션

명령줄을 통해 사용 가능한 모든 옵션을 보려면 다음 명령을 사용합니다.

```powershell
MARSAgentInstaller.exe /?
```

사용 가능한 옵션은 다음과 같습니다.

| 옵션 | 세부 정보 | 기본값 |
| --- | --- | --- |
| /q |자동 설치 |- |
| /p:"위치" |Azure Backup 에이전트의 설치 폴더에 대한 경로입니다. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s:"위치" |Azure Backup 에이전트의 캐시 폴더에 대한 경로입니다. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Microsoft 업데이트에 옵트인 |- |
| /nu |설치가 완료 된 후 업데이트 확인 안 함 |- |
| /d |Microsoft Azure Recovery Services 에이전트를 제거합니다. |- |
| /ph |프록시 호스트 주소 |- |
| /po |프록시 호스트 포트 번호 |- |
| /pu |프록시 호스트 사용자 이름 |- |
| /pw |프록시 암호 |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 Windows Server 또는 Windows 클라이언트 컴퓨터 등록

Recovery Services 자격 증명 모음을 만든 후에, 최신 에이전트 및 보관 자격 증명을 다운로드하여 편리한 위치(예: C:\Downloads)에 저장합니다.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>PS Az 모듈을 사용하여 등록

> [!NOTE]
> 자격 증명 모음 인증서를 생성할 때 발생하는 버그는 Az 3.5.릴리스에서 수정되었습니다. Az 3.5.0 이상 릴리스 버전을 사용하여 자격 증명 모음 인증서를 다운로드합니다.

기본 플랫폼 제한으로 인해 PowerShell의 최신 Az 모듈에서 자격 증명 모음 자격 증명을 다운로드 하려면 자체 서명 된 인증서가 필요 합니다. 다음 예제에서는 자체 서명된 인증서를 제공하고 자격 증명 모음을 다운로드하는 방법을 보여 줍니다.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

Windows Server 또는 Windows 클라이언트 컴퓨터에서, [Start-OBRegistration](/powershell/module/msonlinebackup/start-obregistration) cmdlet을 실행하여 컴퓨터를 자격 증명 모음에 등록합니다.
이 및 백업에 사용 되는 다른 cmdlet은 설치 프로세스의 일부로 MARS AgentInstaller가 추가 된 MSONLINE 모듈에서 가져온 것입니다.

에이전트 설치 관리자가 $Env:P SModulePath 변수를 업데이트 하지 않습니다. 즉, 모듈 자동 로드에 실패합니다. 이 문제를 해결하려면 다음을 수행하면 됩니다.

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

또는 다음과 같이 스크립트에 모듈을 수동으로 로드할 수 있습니다.

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

Online Backup cmdlet을 로드하면 자격 증명 모음을 등록합니다.

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> 자격 증명 모음 자격 증명 파일을 지정 하려면 상대 경로를 사용 하지 마세요. cmdlet 입력 내용은 반드시 절대 경로를 제공해야 합니다.
>
>

## <a name="networking-settings"></a>네트워킹 서비스

Windows 컴퓨터의 인터넷 연결이 프록시 서버를 통하는 경우, 프록시 설정도 에이전트에 제공될 수 있습니다. 이 예제에서는 프록시 서버가 없으므로 프록시와 관련 된 모든 정보를 명시적으로 지웁니다.

대역폭 사용 역시 주의 정해진 요일에 대해 `work hour bandwidth` 및 `non-work hour bandwidth` 옵션으로 제어할 수 있습니다.

프록시 및 대역폭 세부 정보 설정은 [Set-OBMachineSetting](/powershell/module/msonlinebackup/set-obmachinesetting) cmdlet을 사용합니다.

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>암호화 설정

Azure Backup에 전송되는 백업 데이터는 데이터의 기밀성을 보호하기 위해 암호화됩니다. 암호화 암호는 복원 시 데이터를 해독하기 위한 “암호"입니다.

Azure Portal의 **Recovery Services 자격 증명 모음**에서 **설정** > **속성** > **보안 PIN** 아래의 **생성**을 선택하여 보안 PIN을 생성해야 합니다.

>[!NOTE]
> 보안 PIN은 Azure Portal를 통해서만 생성할 수 있습니다.

그런 다음, 명령에서 `generatedPIN`으로 사용합니다.

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> 암호 정보는 설정 되 면 안전 하 고 안전 하 게 유지 합니다. 이 암호 없이는 Azure에서 데이터를 복원할 수 없습니다.
>
>

## <a name="back-up-files-and-folders"></a>파일 및 폴더 백업

Windows 서버 및 클라이언트에서 Azure Backup으로의 모든 백업은 정책에 따라 제어됩니다. 정책은 다음 세 부분으로 구성 됩니다.

1. 백업을 수행하고 서비스와 동기화해야 할 시기를 지정하는 **백업 일정** .
2. Azure에 복구 지점을 보존할 기간을 지정하는 **보존 일정** 입니다.
3. 백업해야 할 항목을 지정하는 **파일 포함/제외 사양** .

이 문서에서는 백업을 자동화하기 때문에 아무것도 구성되지 않은 것으로 가정합니다. 먼저 [New-OBPolicy](/powershell/module/msonlinebackup/new-obpolicy) cmdlet을 사용하여 새로운 백업 정책을 만듭니다.

```powershell
$NewPolicy = New-OBPolicy
```

지금은 정책이 비어 있으며, 포함 또는 제외할 항목, 백업 실행 시기 및 백업이 저장 되는 위치를 정의 하는 데 다른 cmdlet이 필요 합니다.

### <a name="configuring-the-backup-schedule"></a>백업 일정 구성

정책의 3부분 중 첫 번째는 백업 일정으로, [New-OBSchedule](/powershell/module/msonlinebackup/new-obschedule) cmdlet을 사용하여 만듭니다. 백업 일정은 백업을 수행해야 할 시기를 정의합니다. 일정을 만들 때는 2개의 입력 매개 변수를 지정해야 합니다.

* **요일** . 백업을 하루만 실행하거나 해당 주의 모든 요일 또는 그 사이의 날짜를 조합하여 실행할 수 있습니다.
* **시간** . 백업이 트리거되는 시간을 최대 3개까지 서로 다르게 정의할 수 있습니다.

예를 들어, 토요일과 일요일마다 오후 4시에 실행되는 백업 정책을 구성할 수 있습니다.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

백업 일정은 정책과 연결되어야 하며 이 작업은 [Set-OBSchedule](/powershell/module/msonlinebackup/set-obschedule) cmdlet을 사용하여 수행할 수 있습니다.

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>보존 정책 구성

보존 정책은 백업 작업에서 생성된 복구 지점이 유지되는 기간을 정의합니다. 새 보존 정책을 만들 때 [새](/powershell/module/msonlinebackup/new-obretentionpolicy) 보존 정책을 만들 때 백업 복구 지점이 Azure Backup 보존 되는 일 수를 지정할 수 있습니다. 다음 예제에서는 7일의 보존 정책을 설정합니다.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

보존 정책은 cmdlet [Set-OBRetentionPolicy](/powershell/module/msonlinebackup/set-obretentionpolicy)를 사용하여 기본 정책과 연결되어야 합니다.

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="including-and-excluding-files-to-be-backed-up"></a>백업할 파일 포함 및 제외

`OBFileSpec` 개체는 백업에 포함 및 제외시킬 파일을 정의합니다. 이 개체는 컴퓨터에서 보호된 파일 및 폴더를 자세히 살펴보는 규칙의 집합입니다. 필요에 따라 원하는 만큼 파일을 포함 또는 제외시키고 정책과 연결할 수 있습니다. 새 OBFileSpec 개체를 만드는 경우 다음 작업을 수행할 수 있습니다.

* 포함시킬 파일 및 폴더 지정
* 제외시킬 파일 및 폴더 지정
* 폴더의 데이터에 대한 재귀 백업을 지정하거나 지정된 폴더의 최상위 수준 파일만 백업해야 하는지 여부를 지정합니다.

후자는 New-OBFileSpec 명령의 -NonRecursive 플래그를 사용하여 수행됩니다.

아래 예제에서는 C: 및 D: 볼륨을 백업하고 Windows 폴더 및 임시 폴더에 있는 운영 체제 바이너리를 제외시킵니다. 이를 수행하기 위해 [New-OBFileSpec](/powershell/module/msonlinebackup/new-obfilespec) cmdlet을 사용하여 두 개의 파일 사양을 만드는데, 하나는 포함용이고 또 하나는 제외용입니다. 파일 사양을 만들고 나면 [Add-OBFileSpec](/powershell/module/msonlinebackup/add-obfilespec) cmdlet을 사용하여 정책과 연결됩니다.

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>정책 적용

이제 정책 개체가 완료되었으므로 연결된 백업 일정, 보존 정책 및 파일의 포함/제외 목록이 있습니다. 이제는 이 정책을 Azure Backup에 커밋하여 사용할 수 있습니다. 새로 만든 정책을 적용하기 전에 [Remove-OBPolicy](/powershell/module/msonlinebackup/remove-obpolicy) cmdlet을 사용하여 서버에 연결된 기존 백업 정책이 없도록 만듭니다. 정책을 제거하면 확인 메시지가 나타납니다. 확인 메시지를 건너뛰려면 이 cmdlet에서 `-Confirm:$false` 플래그를 사용합니다.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

정책 개체 커밋은 [Set-OBPolicy](/powershell/module/msonlinebackup/set-obpolicy) cmdlet을 사용하여 수행됩니다. 이 작업에서도 확인 메시지가 표시됩니다. 확인 메시지를 건너뛰려면 이 cmdlet에서 `-Confirm:$false` 플래그를 사용합니다.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

[Get-OBPolicy](/powershell/module/msonlinebackup/get-obpolicy) cmdlet을 사용하여 기존 백업 정책에 대한 세부 정보를 볼 수 있습니다. 백업 입정에는 [Get-OBSchedule](/powershell/module/msonlinebackup/get-obschedule) cmdlet, 보존 정책에는 [Get-OBRetentionPolicy](/powershell/module/msonlinebackup/get-obretentionpolicy) cmdlet을 사용하면 더욱 상세하게 정보를 볼 수 있습니다.

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-on-demand-backup"></a>주문형 백업 수행

백업 정책이 설정되면 일정에 따라 백업이 발생합니다. 또한 [Start-OBBackup](/powershell/module/msonlinebackup/start-obbackup) cmdlet을 사용하여 주문형 백업 트리거도 가능합니다.

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="back-up-windows-server-system-state-in-mars-agent"></a>MARS 에이전트에서 Windows Server 시스템 상태 백업

이 섹션에서는 MARS 에이전트에서 시스템 상태를 설정 하는 PowerShell 명령에 대해 설명 합니다.

### <a name="schedule"></a>일정

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>보존

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>일정 및 보존 구성

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>정책 확인

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Azure Backup에서 데이터 복원

이 섹션에서는 Azure Backup에서 데이터 복구를 자동화하는 방법을 단계별로 안내합니다. 다음 단계를 수행하여 작업을 진행합니다.

1. 원본 볼륨 선택
2. 복원할 백업 시점 선택
3. 복원할 항목 지정
4. 복원 프로세스 트리거

### <a name="picking-the-source-volume"></a>원본 볼륨 선택

Azure Backup에서 항목을 복원 하려면 먼저 항목의 소스를 식별 해야 합니다. Windows 서버 또는 Windows 클라이언트의 컨텍스트에서 명령을 실행 중이므로 컴퓨터는 이미 식별된 상태입니다. 원본을 식별하는 다음 단계는 해당 원본이 포함된 볼륨을 식별하는 것입니다. 이 컴퓨터에서 백업 중인 볼륨 또는 원본 목록은 [Get-OBRecoverableSource](/powershell/module/msonlinebackup/get-obrecoverablesource) cmdlet을 실행하여 검색할 수 있습니다. 이 명령은 이 서버/클라이언트에서 백업한 모든 원본의 배열을 반환합니다.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>복원할 백업 시점 선택

[Get-OBRecoverableItem](/powershell/module/msonlinebackup/get-obrecoverableitem) cmdlet을 적절한 매개 변수와 함께 실행하여 백업 시점 목록을 검색합니다. 이 예제에서는 원본 볼륨 *C:* 의 최신 백업 시점을 선택하고 이 시점을 사용하여 특정 파일을 복구합니다.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

개체 `$Rps` 는 백업 시점의 배열입니다. 첫 번째 요소는 가장 최근 시점이고 n 번째 요소는 가장 오래된 시점입니다. 최신 요소를 선택 하려면를 사용 `$Rps[0]` 합니다.

### <a name="specifying-an-item-to-restore"></a>복원할 항목 선택

특정 파일을 복원하려면 루트 볼륨을 기준으로 파일 이름을 지정합니다. 예를 들어 C:\Test\Cat.job을 검색하려면 다음 명령을 실행합니다.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>복원 프로세스 트리거

복원 프로세스를 트리거하려면 먼저 복구 옵션을 지정해야 합니다. 이 작업은 [New-OBRecoveryOption](/powershell/module/msonlinebackup/new-obrecoveryoption) cmdlet을 사용하여 수행할 수 있습니다. 이 예에서는 파일을 *C:\temp*로 복원 하려는 경우를 가정해 보겠습니다. 또한 대상 폴더 *C:\temp*에 이미 있는 파일을 건너뛸지를 가정해 보겠습니다. 이러한 복구 옵션을 만들려면 다음 명령을 사용 합니다.

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

이제 `Get-OBRecoverableItem` cmdlet의 출력에서 선택한 `$Item`에 대해 [Start-OBRecovery](/powershell/module/msonlinebackup/start-obrecovery) 명령을 사용하여 복원 프로세스를 트리거합니다.

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Azure Backup 에이전트 제거

Azure Backup 에이전트 제거는 다음 명령을 사용하여 수행할 수 있습니다.

```powershell
.\MARSAgentInstaller.exe /d /q
```

컴퓨터에서 에이전트 이진 파일을 제거하면 고려해야 할 몇 가지 결과가 발생합니다.

* 컴퓨터에서 파일 필터를 제거하고 변경 내용 추적이 중단됩니다.
* 모든 정책 정보가 컴퓨터에서 제거되지만 정책 정보는 서비스에 계속 저장됩니다.
* 모든 백업 일정이 제거되고 더 이상 백업이 수행되지 않습니다.

그러나 Azure에 저장 된 데이터는 그대로 유지 되며 사용자의 보존 정책 설정에 따라 보존 됩니다. 이전 지점은 시간이 경과하면 자동으로 삭제됩니다.

## <a name="remote-management"></a>원격 관리

Azure Backup 에이전트, 정책, 데이터 원본과 관련된 모든 관리는 PowerShell을 통해 원격으로 수행될 수 있습니다. 원격으로 관리될 컴퓨터는 올바르게 준비되어야 합니다.

기본적으로 WinRM 서비스는 수동 시작으로 구성됩니다. 시작 유형은 반드시 *자동* 으로 설정되어야 하며 서비스가 시작되어야 합니다. WinRM 서비스가 실행되는지 확인하도록 Status 속성의 값은 *Running*이어야 합니다.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell을 원격 작업용으로 구성해야 합니다.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

이제 에이전트 설치부터 시작하여 컴퓨터를 원격으로 관리할 수 있습니다. 예를 들어, 다음 스크립트는 에이전트를 원격 컴퓨터로 복사하고 설치합니다.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>다음 단계

Windows Server/Client용 Azure Backup에 대한 자세한 정보는 다음을 참조하세요.

* [Azure Backup 소개](./backup-overview.md)
* [Windows 서버 백업](backup-windows-with-mars-agent.md)
