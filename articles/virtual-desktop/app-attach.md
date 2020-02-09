---
title: Windows 가상 데스크톱 MSIX 앱 연결-Azure
description: Windows 가상 데스크톱에 대 한 MSIX 앱 연결을 설정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
ms.openlocfilehash: 8e8eec8af81832992a27206efcd7b7e7051a83b8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772553"
---
# <a name="set-up-msix-app-attach"></a>MSIX 앱 연결 설정

> [!IMPORTANT]
> MSIX 앱 연결은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 항목에서는 Windows 가상 데스크톱 환경에서 MSIX 앱 연결을 설정 하는 방법을 안내 합니다.

## <a name="requirements"></a>요구 사항

시작 하기 전에 MSIX 앱 연결을 구성 하는 데 필요한 사항은 다음과 같습니다.

- MSIX 앱 연결 Api를 지 원하는 windows 10 버전을 얻으려면 Windows 참가자 포털에 액세스 합니다.
- 작동 하는 Windows 가상 데스크톱 배포. 자세한 내용은 [Windows 가상 데스크톱에서 테넌트 만들기](tenant-setup-azure-active-directory.md)를 참조 하세요.
- MSIX 패키징 도구
- MSIX 패키지가 저장 되는 Windows 가상 데스크톱 배포의 네트워크 공유

## <a name="get-the-os-image"></a>OS 이미지 가져오기

먼저 MSIX 앱에 사용할 OS 이미지를 가져와야 합니다. OS 이미지를 가져오려면:

1. [Windows 참가자 포털](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) 을 열고 로그인 합니다.

     >[!NOTE]
     >Windows Insider portal에 액세스 하려면 Windows Insider program의 구성원 이어야 합니다. Windows 참가자 프로그램에 대해 자세히 알아보려면 [Windows 참가자 설명서](https://docs.microsoft.com/windows-insider/at-home/)를 확인 하세요.

2. **선택 버전** 섹션으로 스크롤하고 **Windows 10 Insider PREVIEW Enterprise (FAST) – Build 19035** 이상을 선택 합니다.

3. **확인**을 선택 하 고 사용 하려는 언어를 선택한 후에 다시 **확인** 을 선택 합니다.
    
     >[!NOTE]
     >현재이 기능을 사용 하 여 테스트 한 언어는 영어 뿐입니다. 다른 언어를 선택할 수 있지만 의도 한 대로 표시 되지 않을 수 있습니다.
    
4. 다운로드 링크가 생성 되 면 **64 비트 다운로드** 를 선택 하 여 로컬 하드 디스크에 저장 합니다.

## <a name="prepare-the-vhd-image-for-azure"></a>Azure에 대 한 VHD 이미지 준비 

시작 하기 전에 마스터 VHD 이미지를 만들어야 합니다. 마스터 VHD 이미지를 아직 만들지 않은 경우에는 [마스터 vhd 이미지 준비 및 사용자 지정](set-up-customize-master-image.md) 으로 이동 하 여 지침을 따르세요. 

마스터 VHD 이미지를 만든 후에는 MSIX 앱 연결 응용 프로그램에 대 한 자동 업데이트를 사용 하지 않도록 설정 해야 합니다. 자동 업데이트를 사용 하지 않도록 설정 하려면 관리자 권한 명령 프롬프트에서 다음 명령을 실행 해야 합니다.

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

그런 다음 Azure에 대 한 VM VHD를 준비 하 고 결과 VHD 디스크를 Azure에 업로드 합니다. 자세히 알아보려면 [마스터 VHD 이미지 준비 및 사용자 지정](set-up-customize-master-image.md)을 참조 하세요.

VHD를 Azure에 업로드 한 후에는 [Azure Marketplace 자습서를 사용 하 여 호스트 풀 만들기](create-host-pools-azure-marketplace.md) 의 지침에 따라이 새 이미지를 기반으로 하는 호스트 풀을 만듭니다.

## <a name="prepare-the-application-for-msix-app-attach"></a>MSIX 앱 연결을 위한 응용 프로그램 준비 

MSIX 패키지가 이미 있는 경우 [Windows 가상 데스크톱 인프라 구성](#configure-windows-virtual-desktop-infrastructure)으로 건너뜁니다. 레거시 응용 프로그램을 테스트 하려면 [VM의 데스크톱 설치 관리자에서 MSIX 패키지 만들기](https://docs.microsoft.com/windows/msix/packaging-tool/create-app-package-msi-vm) 의 지침에 따라 레거시 응용 프로그램을 msix 패키지로 변환 합니다.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>MSIX 용 VHD 또는 VHDX 패키지 생성

패키지는 성능을 최적화 하기 위해 VHD 또는 VHDX 형식입니다. MSIX을 사용 하려면 VHD 또는 VHDX 패키지가 제대로 작동 해야 합니다.

MSIX 용 VHD 또는 VHDX 패키지를 생성 하려면:

1. [Msixmgr 도구를 다운로드](https://aka.ms/msixmgr) 하 고 .zip 폴더를 세션 호스트 VM 내의 폴더에 저장 합니다.

2. Msixmgr tool .zip 폴더의 압축을 풉니다.

3. 소스 MSIX 패키지를 msixmgr 도구의 압축을 푼 동일한 폴더에 넣습니다.

4. PowerShell에서 다음 cmdlet을 실행 하 여 VHD를 만듭니다.

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >VHD의 크기가 확장 된 MSIX을 저장할 만큼 충분히 큰지 확인 합니다. *

5. 다음 cmdlet을 실행 하 여 새로 만든 VHD를 탑재 합니다.

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. 이 cmdlet을 실행 하 여 VHD를 초기화할 수 있습니다.

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. 이 cmdlet을 실행 하 여 새 파티션을 만들 수 있습니다.

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. 이 cmdlet을 실행 하 여 파티션을 포맷 합니다.

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. 탑재 된 VHD에 부모 폴더를 만듭니다. 이 단계는 MSIX 앱 연결에 부모 폴더가 필요 하므로 필수입니다. 부모 폴더의 이름을 원하는 대로 지정할 수 있습니다.

### <a name="expand-msix"></a>MSIX 확장

그런 후에 압축을 풀고 MSIX 이미지를 "확장" 해야 합니다. MSIX 이미지의 압축을 풀려면:

1. 관리자 권한으로 명령 프롬프트를 열고 msixmgr 도구를 다운로드 하 고 압축을 푼 폴더로 이동 합니다.

2. 다음 cmdlet을 실행 하 여 이전 섹션에서 만들고 탑재 한 VHD에 MSIX의 압축을 풉니다.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    다음 메시지는 압축 해제가 완료 되 면 표시 됩니다.

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > 네트워크 내에서 또는 교육용 Microsoft Store의 패키지를 사용 하거나 인터넷에 연결 되지 않은 장치에서 패키지를 사용 하는 경우 스토어에서 패키지 라이선스를 가져온 후 앱을 성공적으로 실행 하기 위해 설치 해야 합니다. [오프 라인에서 패키지 사용](#use-packages-offline)을 참조 하세요.

3. 탑재 된 VHD로 이동 하 여 앱 폴더를 열고 패키지 콘텐츠가 있는지 확인 합니다.

4. VHD를 분리합니다.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Windows 가상 데스크톱 인프라 구성

기본적으로, 단일 MSIX 확장 된 패키지 (이전 섹션에서 만든 VHD)는 읽기 전용 모드에서 Vhd가 연결 될 때 여러 세션 호스트 Vm 간에 공유 될 수 있습니다.

시작 하기 전에 네트워크 공유가 다음과 같은 요구 사항을 충족 하는지 확인 합니다.

- 공유가 SMB와 호환 됩니다.
- 세션 호스트 풀의 일부인 Vm에는 공유에 대 한 NTFS 권한이 있습니다.

### <a name="set-up-an-msix-app-attach-share"></a>MSIX 앱 연결 공유 설정 

Windows 가상 데스크톱 환경에서 네트워크 공유를 만들고 해당 위치에서 패키지를 이동 합니다.

>[!NOTE]
> 네트워크 공유를 만드는 모범 사례는 NTFS 읽기 전용 권한을 사용 하 여 네트워크 공유를 설정 하는 것입니다.

## <a name="install-certificates"></a>인증서 설치

앱이 공개적으로 신뢰할 수 없는 인증서 또는 자체 서명 된 인증서를 사용 하는 경우 설치 하는 방법은 다음과 같습니다.

1. 패키지를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택 합니다.
2. 표시 되는 창에서 **디지털 서명** 탭을 선택 합니다. 다음 그림에 표시 된 것 처럼 탭의 목록에는 하나의 항목만 있어야 합니다. 해당 항목을 선택 하 여 항목을 강조 표시 하 고 **세부 정보**를 선택 합니다.
3. 디지털 서명 정보 창이 표시 되 면 **일반** 탭을 선택 하 고 **인증서 설치**를 선택 합니다.
4. 설치 관리자가 열리면 **로컬 컴퓨터** 를 저장소 위치로 선택 하 고 **다음**을 선택 합니다.
5. 응용 프로그램에서 장치를 변경할 수 있도록 허용할지 묻는 메시지가 표시 되 면 **예**를 선택 합니다.
6. **모든 인증서를 다음 저장소에**저장을 선택한 다음 **찾아보기**를 선택 합니다.
7. 인증서 저장소 선택 창이 표시 되 면 **신뢰할 수 있는 사용자**를 선택 하 고 **확인**을 선택 합니다.
8. **마침**을 선택합니다.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>MSIX 앱 연결을 위한 PowerShell 스크립트 준비

MSIX 앱 연결에는 다음 순서 대로 수행 해야 하는 4 개의 고유한 단계가 있습니다.

1. Stage
2. 등록
3. 취소할
4. 디

각 단계에서 PowerShell 스크립트를 만듭니다. 각 단계에 대 한 샘플 스크립트는 [여기](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)에서 사용할 수 있습니다.

### <a name="stage-the-powershell-script"></a>PowerShell 스크립트 준비

PowerShell 스크립트를 업데이트 하기 전에 VHD의 볼륨에 대 한 볼륨 GUID가 있는지 확인 합니다. 볼륨 GUID를 가져오려면:

1.  스크립트를 실행할 VM 내에서 VHD가 있는 네트워크 공유를 엽니다.

2.  VHD를 마우스 오른쪽 단추로 클릭 하 고 **탑재**를 선택 합니다. 그러면 VHD가 드라이브 문자에 탑재 됩니다.

3.  VHD를 탑재 한 후에는 **파일 탐색기** 창이 열립니다. 부모 폴더를 캡처하고 **$parentFolder** 변수를 업데이트 합니다.

    >[!NOTE]
    >부모 폴더가 표시 되지 않으면 MSIX이 제대로 확장 되지 않았음을 의미 합니다. 이전 섹션을 다시 실행 하 고 다시 시도 하세요.

4.  부모 폴더를 엽니다. 올바르게 확장 된 경우 패키지와 이름이 같은 폴더가 표시 됩니다. 이 폴더의 이름과 일치 하도록 **$packageName** 변수를 업데이트 합니다.

    `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`)을 입력합니다.

5.  명령 프롬프트를 열고 **mountvol**을 입력 합니다. 이 명령은 볼륨과 해당 Guid의 목록을 표시 합니다. 2 단계에서 드라이브 문자가 VHD를 탑재 한 드라이브와 일치 하는 볼륨의 GUID를 복사 합니다.

    예를 들어, mountvol 명령에 대 한이 예제 출력에서 VHD를 C 드라이브에 탑재 한 경우 `C:\`위의 값을 복사 합니다.

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  방금 복사한 볼륨 GUID를 사용 하 여 **$volumeGuid** 변수를 업데이트 합니다.

7. 관리자 PowerShell 프롬프트를 열고 사용자 환경에 적용 되는 변수를 사용 하 여 다음 PowerShell 스크립트를 업데이트 합니다.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell 스크립트 등록

등록 스크립트를 실행 하려면 자리 표시자 값을 사용자 환경에 적용 되는 값으로 대체 하 여 다음 PowerShell cmdlet을 실행 합니다.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>PowerShell 스크립트 등록 취소

이 스크립트의 경우 **$packageName** 자리 표시자를 테스트 중인 패키지의 이름으로 바꿉니다.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>디 스테이지 PowerShell 스크립트

이 스크립트의 경우 **$packageName** 자리 표시자를 테스트 중인 패키지의 이름으로 바꿉니다.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>MSIX 앱 연결 에이전트에 대 한 시뮬레이션 스크립트 설정

스크립트를 만든 후 사용자는 수동으로 실행 하거나 시작, 로그온, 로그 오프 및 종료 스크립트로 자동 실행 되도록 설정할 수 있습니다. 이러한 유형의 스크립트에 대 한 자세한 내용은 [그룹 정책 시작, 종료, 로그온 및 로그 오프 스크립트 사용](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11))을 참조 하세요.

이러한 각 자동 스크립트는 앱 연결 스크립트의 한 단계를 실행 합니다.

- 시작 스크립트는 단계 스크립트를 실행 합니다.
- Logon 스크립트는 register 스크립트를 실행 합니다.
- 로그 오프 스크립트는 등록 취소 스크립트를 실행 합니다.
- 종료 스크립트는 디 스테이지 스크립트를 실행 합니다.

## <a name="use-packages-offline"></a>오프 라인으로 패키지 사용

[회사의 Microsoft Store](https://businessstore.microsoft.com/) 또는 네트워크 내의 [교육용 Microsoft Store](https://educationstore.microsoft.com/) 또는 인터넷에 연결 되지 않은 장치에서 패키지를 사용 하는 경우 앱을 성공적으로 실행 하려면 Microsoft Store에서 패키지 라이선스를 가져온 후 장치에 설치 해야 합니다. 장치가 온라인 상태이 고 비즈니스용 Microsoft Store에 연결할 수 있는 경우 필요한 라이선스를 자동으로 다운로드 해야 하지만, 오프 라인 상태인 경우에는 라이선스를 수동으로 설정 해야 합니다. 

라이선스 파일을 설치 하려면 WMI 브리지 공급자에서 MDM_EnterpriseModernAppManagement_StoreLicenses02_01 클래스를 호출 하는 PowerShell 스크립트를 사용 해야 합니다.  

오프 라인 사용을 위해 라이선스를 설정 하는 방법은 다음과 같습니다. 

1. 비즈니스용 Microsoft Store에서 앱 패키지, 라이선스 및 필수 프레임 워크를 다운로드 합니다. 인코딩된 및 인코딩되지 않은 라이선스 파일이 모두 필요 합니다. 자세한 다운로드 지침은 [여기](https://docs.microsoft.com/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)에서 찾을 수 있습니다.
2. 3 단계에 대 한 스크립트에서 다음 변수를 업데이트 합니다.
      1. `$contentID`은 인코딩되지 않은 라이선스 파일 (.xml)의 ContentID 값입니다. 선택한 텍스트 편집기에서 라이선스 파일을 열 수 있습니다.
      2. `$licenseBlob`은 인코딩된 라이선스 파일 (. bin)의 라이선스 blob에 대 한 전체 문자열입니다. 선택한 텍스트 편집기에서 인코딩된 라이선스 파일을 열 수 있습니다. 
3. 관리자 PowerShell 프롬프트에서 다음 스크립트를 실행 합니다. [준비 스크립트](#stage-the-powershell-script) 의 끝 부분에는 관리자 프롬프트에서 실행 해야 하는 라이선스 설치를 수행 하는 것이 좋습니다.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{‘ContentID’_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>다음 단계

이 기능은 현재 지원 되지 않지만 [Windows 가상 데스크톱 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)커뮤니티에 질문할 수 있습니다.

또한 windows 가상 데스크톱 [피드백 허브](https://aka.ms/MRSFeedbackHub)에서 Windows 가상 데스크톱에 대 한 피드백을 유지 하거나 [msix 앱](https://aka.ms/msixappattachfeedback) 에 대 한 피드백 허브 및 [msix 패키징 도구 피드백](https://aka.ms/msixtoolfeedback)허브에서 msix 앱 및 패키징 도구에 대 한 피드백을 남길 수 있습니다.
