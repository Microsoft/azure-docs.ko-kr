---
title: Windows 템플릿 컴퓨터 설정 가이드 | Microsoft Docs
description: Lab Services에서 Windows 템플릿 컴퓨터를 준비 하는 일반적인 단계입니다.  이러한 단계는 Windows 업데이트 일정 설정, OneDrive 설치 및 Office 설치를 포함 합니다.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c52a1212d160adce3a0a0638164833bc2907a856
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515006"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Azure Lab Services에서 Windows 템플릿 컴퓨터를 설정 하는 방법에 대 한 가이드

Azure Lab Services에 대 한 Windows 10 템플릿 컴퓨터를 설정 하는 경우 고려해 야 할 몇 가지 모범 사례 및 팁이 있습니다. 아래 구성 단계는 모두 선택 사항입니다.  그러나 이러한 준비 단계는 학생의 생산성을 높이고, 클래스 시간 중단을 최소화 하 고, 최신 기술을 사용 하 고 있는지 확인 하는 데 도움이 될 수 있습니다.

>[!IMPORTANT]
>이 문서에는 컴퓨터 템플릿 수정 프로세스를 간소화 하는 PowerShell 코드 조각이 포함 되어 있습니다.  표시 되는 모든 PowerShell 스크립트에 대해 Windows PowerShell에서 관리자 권한으로 실행 하는 것이 좋습니다. Windows 10에서는 시작 메뉴를 마우스 오른쪽 단추로 클릭 하 고 "Windows PowerShell (관리)"을 선택 하는 것이 가장 빠른 방법입니다.

## <a name="install-and-configure-onedrive"></a>OneDrive 설치 및 구성

가상 머신을 다시 설정 하는 경우 학생 데이터가 손실 되지 않도록 하려면 데이터를 클라우드로 다시 가져오는 것이 좋습니다.  Microsoft OneDrive는 학생 들이 자신의 데이터를 보호 하도록 도울 수 있습니다.  

### <a name="install-onedrive"></a>OneDrive 설치

OneDrive를 수동으로 다운로드 하 여 설치 하려면 [onedrive](https://onedrive.live.com/about/download/) 또는 비즈니스용 [onedrive](https://onedrive.live.com/about/business/) 다운로드 페이지를 참조 하세요.

다음 PowerShell 스크립트를 사용할 수도 있습니다.  그러면 OneDrive의 최신 버전을 자동으로 다운로드 하 여 설치 합니다.  OneDrive 클라이언트가 설치 되 면 설치 관리자를 실행 합니다.  이 예제에서는 `/allUsers` 스위치를 사용 하 여 컴퓨터의 모든 사용자에 대해 OneDrive를 설치 합니다. 또한 `/silent` 스위치를 사용 하 여 OneDrive를 자동으로 설치 합니다.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive 사용자 지정

[OneDrive에 대해 수행할 수 있는 많은 사용자 지정](https://docs.microsoft.com/onedrive/use-group-policy)이 있습니다. 몇 가지 일반적인 사용자 지정 항목을 살펴보겠습니다.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Windows의 알려진 폴더를 OneDrive로 자동 이동

문서, 다운로드 및 사진과 같은 폴더는 학생 파일을 저장 하는 데 주로 사용 됩니다. 이러한 폴더가 OneDrive에 백업 되도록 하려면 이러한 폴더를 OneDrive로 이동 하는 것이 좋습니다.

Active Directory를 사용 하지 않는 컴퓨터에 있는 경우 사용자가 OneDrive에 인증 되 면 해당 폴더를 OneDrive로 수동으로 이동할 수 있습니다.

1. 파일 탐색기 열기
2. 문서, 다운로드 또는 그림 폴더를 마우스 오른쪽 단추로 클릭 합니다.
3. 속성 > 위치로 이동 합니다.  폴더를 OneDrive 디렉터리의 새 폴더로 이동 합니다.

가상 컴퓨터가 Active Directory에 연결 된 경우 학생에 게 자동으로 알려진 폴더를 OneDrive로 이동 하도록 템플릿 컴퓨터를 설정할 수 있습니다.  

먼저 Office 테넌트 ID를 검색 해야 합니다.  자세한 지침은 [Office 365 테넌트 ID 찾기](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)를 참조 하세요.  다음 PowerShell을 사용 하 여 Office 365 테넌트 ID를 가져올 수도 있습니다.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Office 365 테넌트 ID가 있는 경우 다음 PowerShell을 사용 하 여 OneDrive를 OneDrive로 이동 하도록 설정 합니다.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>요청 시 OneDrive 파일 사용

학생 들이 OneDrive 계정 내에 많은 파일을 포함할 수 있습니다. 컴퓨터의 공간을 절약 하 고 다운로드 시간을 줄일 수 있도록 학생의 OneDrive 계정에 저장 된 모든 파일을 요청 시로 설정 하는 것이 좋습니다.  주문형 파일은 사용자가 파일에 액세스 한 후에만 다운로드 됩니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>OneDrive에 자동으로 사용자 로그인

OneDrive는 로그온 한 사용자의 Windows 자격 증명을 사용 하 여 자동으로 로그인 하도록 설정할 수 있습니다.  자동 로그인은 학생이 Office 365 학교 자격 증명을 사용 하 여 로그인 하는 클래스에 유용 합니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>OneDrive 설정 끝에 표시 되는 자습서를 사용 하지 않도록 설정

이 설정을 사용 하면 OneDrive 설치 종료 시 웹 브라우저에서 자습서를 시작할 수 없습니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>자동으로 다운로드 되는 파일의 최대 크기를 설정 합니다.

이 설정은 onedrive 동기화 클라이언트에 대 한 사용자의 Windows 자격 증명을 사용 하도록 설정 된 OneDrive 파일이 없는 장치에서 자동으로 사용자 로그인과 함께 사용 됩니다. 지정 된 임계값 (MB) 보다 큰 OneDrive를 사용 하는 모든 사용자에 게는 OneDrive 동기화 클라이언트 (cluster.exe)에서 파일을 다운로드 하기 전에 동기화 할 폴더를 선택 하 라는 메시지가 표시 됩니다.  이 예제에서 "1111-2222-3333-4444"은 Office 365 테넌트 ID 이며 0005000는 5gb의 임계값을 설정 합니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Microsoft Office 365 설치 및 구성

### <a name="install-microsoft-office-365"></a>Microsoft Office 365 설치

템플릿 컴퓨터에 Office가 필요한 경우 [ODT (Office 배포 도구)](https://www.microsoft.com/download/details.aspx?id=49117 )를 통해 office를 설치 하는 것이 좋습니다. [Office 365 클라이언트 구성 서비스](https://config.office.com/) 를 사용 하 여 재사용 가능한 구성 파일을 만들어 office에서 필요한 기능 및 업데이트 빈도를 선택 해야 합니다.

1. [Office 365 클라이언트 구성 서비스로](https://config.office.com/) 이동 하 여 사용자 고유의 구성 파일을 다운로드 합니다.
2. [Office 배포 도구](https://www.microsoft.com/download/details.aspx?id=49117)를 다운로드 합니다.  다운로드 한 파일은 `setup.exe`됩니다.
3. `setup.exe /download configuration.xml`를 실행 하 여 Office 구성 요소를 다운로드 합니다.
4. `setup.exe /configure configuration.xml`를 실행 하 여 Office 구성 요소를 설치 합니다.

### <a name="change-the-microsoft-office-365-update-channel"></a>Microsoft Office 365 업데이트 채널 변경

Office 구성 도구를 사용 하 여 Office에서 업데이트를 수신 하는 빈도를 설정할 수 있습니다. 그러나 설치 후 Office에서 업데이트를 수신 하는 빈도를 수정 해야 하는 경우에는 업데이트 채널 URL을 변경할 수 있습니다. 업데이트 채널 URL 주소 [는 조직의 장치에 대 한 Office 365 ProPlus 업데이트 채널 변경](https://docs.microsoft.com/deployoffice/change-update-channels)에서 찾을 수 있습니다. 아래 예제에서는 월별 업데이트 채널을 사용 하도록 Office 365을 설정 하는 방법을 보여 줍니다.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>업데이트 설치 및 구성

### <a name="install-the-latest-windows-updates"></a>최신 Windows 업데이트 설치

템플릿 VM을 게시 하기 전에 보안을 위해 템플릿 컴퓨터에 최신 Microsoft 업데이트를 설치 하는 것이 좋습니다.  또한 예기치 않은 시간에 업데이트가 실행 될 때 학생이 작업에서 중단 되지 않도록 방지할 수 있습니다.

1. 시작 메뉴에서 **설정** 시작
2. **업데이트** & 보안을 클릭 합니다.
3. **업데이트 확인을 클릭 합니다** .
4. 업데이트를 다운로드 하 고 설치 합니다.

PowerShell을 사용 하 여 템플릿 컴퓨터를 업데이트할 수도 있습니다.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>일부 업데이트의 경우 컴퓨터를 다시 시작 해야 할 수 있습니다.  다시 부팅 해야 하는 경우 메시지가 표시 됩니다.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Microsoft Store 앱에 대 한 최신 업데이트 설치

모든 Microsoft Store 앱을 최신 버전으로 업데이트 하는 것이 좋습니다.  Microsoft Store에서 응용 프로그램을 수동으로 업데이트 하는 지침은 다음과 같습니다.  

1. **Microsoft Store** 응용 프로그램을 시작 합니다.
2. 응용 프로그램의 위쪽 모퉁이에 있는 사용자 사진 옆에 있는 줄임표 (...)를 클릭 합니다.
3. 드롭다운 메뉴에서 **다운로드** 및 업데이트를 선택 합니다.
4. **업데이트 가져오기** 단추를 클릭 합니다.

Powershell을 사용 하 여 이미 설치 된 Microsoft Store 응용 프로그램을 업데이트할 수도 있습니다.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>자동 Windows 업데이트 중지

Windows를 최신 버전으로 업데이트 한 후 Windows 업데이트를 중지 하는 것을 고려할 수 있습니다.  자동 업데이트는 예약 된 클래스 시간에 방해가 될 수 있습니다.  코스가 더 오래 실행 되는 경우 학생에 게 수동으로 업데이트를 확인 하거나 예약 된 클래스 시간 외의 시간에 자동 업데이트를 설정 하도록 요청 하는 것이 좋습니다.  Windows 업데이트에 대 한 사용자 지정 옵션에 대 한 자세한 내용은 [추가 Windows 업데이트 설정 관리](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)를 참조 하세요.

다음 PowerShell 스크립트를 사용 하 여 자동 Windows 업데이트를 중지할 수 있습니다.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>외국 언어 팩 설치

가상 컴퓨터에 추가 언어가 설치 되어 있어야 하는 경우 Microsoft Store를 통해 추가할 수 있습니다.

1. 시작 Microsoft Store
2. "언어 팩" 검색
3. 설치할 언어 선택

이미 템플릿 VM에 로그온 한 경우 ["언어 팩 설치" 바로 가기를](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) 사용 하 여 적절 한 설정 페이지로 직접 이동 합니다.

## <a name="remove-unneeded-built-in-apps"></a>불필요 한 기본 제공 앱 제거

Windows 10에는 특정 클래스에 필요 하지 않을 수 있는 다양 한 기본 제공 응용 프로그램이 함께 제공 됩니다. 학생용 컴퓨터 이미지를 간소화 하기 위해 템플릿 컴퓨터에서 일부 응용 프로그램을 제거 하는 것이 좋습니다.  설치 된 응용 프로그램의 목록을 보려면 PowerShell `Get-AppxPackage` cmdlet을 사용 합니다.  아래 예제에서는 제거할 수 있는 설치 된 모든 응용 프로그램을 보여 줍니다.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

응용 프로그램을 제거 하려면 제거-Appx cmdlet을 사용 합니다.  아래 예제에서는 XBox와 관련 된 모든 항목을 제거 하는 방법을 보여 줍니다.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>일반적인 교육 관련 응용 프로그램 설치

Windows 스토어 앱을 통해 일반적으로 학습에 사용 되는 다른 앱을 설치 합니다. 제안에는 [Microsoft 화이트 보드 앱](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft 팀](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Minecraft 교육용 버전](https://education.minecraft.net/)등의 응용 프로그램이 포함 됩니다. 이러한 응용 프로그램은 Windows 스토어를 통하거나 템플릿 VM의 해당 웹 사이트를 통해 수동으로 설치 해야 합니다.

## <a name="conclusion"></a>결론

이 문서에는 효과적인 클래스를 위해 Windows 템플릿 VM을 준비 하는 선택적 단계가 나와 있습니다.  이러한 단계에는 OneDrive 설치 및 Office 365 설치, Windows 용 업데이트 설치 및 Microsoft Store 앱에 대 한 업데이트 설치가 포함 됩니다.  또한 클래스에 가장 적합 한 일정으로 업데이트를 설정 하는 방법을 설명 했습니다.  
