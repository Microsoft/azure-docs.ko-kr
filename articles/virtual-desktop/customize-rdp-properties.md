---
title: PowerShell을 사용 하 여 RDP 속성 사용자 지정-Azure
description: PowerShell cmdlet을 사용 하 여 Windows 가상 데스크톱에 대 한 RDP 속성을 사용자 지정 하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc3a08f383368b189e41bebd204707f2483e77c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018310"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>호스트 풀의 RDP (원격 데스크톱 프로토콜) 속성 사용자 지정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 통해 Windows Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)를 참조하세요.

다중 모니터 환경 및 오디오 리디렉션과 같은 RDP (호스트 풀의 원격 데스크톱 프로토콜) 속성을 사용자 지정 하면 요구 사항에 따라 사용자에 게 최적의 환경을 제공할 수 있습니다. Azure Portal를 사용 하거나 **AzWvdHostPool** cmdlet의 *-customrdpproperty* 매개 변수를 사용 하 여 WINDOWS 가상 데스크톱에서 RDP 속성을 사용자 지정할 수 있습니다.

지원 되는 속성 및 해당 기본값의 전체 목록은 [지원 되는 RDP 파일 설정](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) 을 참조 하세요.

## <a name="default-rdp-file-properties"></a>기본 RDP 파일 속성

RDP 파일에는 기본적으로 다음 속성이 있습니다.

|RDP 속성|바탕 화면|RemoteApp으로|
|---|---|---|
|다중 모니터 모드|사용 안 함|사용|
|드라이브 리디렉션 사용|드라이브, 클립보드, 프린터, COM 포트 및 스마트 카드|드라이브, 클립보드 및 프린터|
|원격 오디오 모드|로컬로 재생|로컬로 재생|

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 PowerShell 모듈을 설정하고 Azure에 로그인하려면 [Windows Virtual Desktop PowerShell 모듈 설정](powershell-module.md)의 지침을 따르세요.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Azure Portal에서 RDP 속성 구성

Azure Portal에서 RDP 속성을 구성 하려면 다음을 수행 합니다.

1. <https://portal.azure.com> 에서 Azure에 로그인합니다.
2. 검색 창에 **windows 가상 데스크톱** 을 입력 합니다.
3. 서비스에서 **Windows 가상 데스크톱** 을 선택 합니다.
4. Windows 가상 데스크톱 페이지의 화면 왼쪽에 있는 메뉴에서 **호스트 풀** 을 선택 합니다.
5. 업데이트 하려는 **호스트 풀의 이름을** 선택 합니다.
6. 화면 왼쪽의 메뉴에서 **RDP 속성** 을 선택 합니다.
7. 원하는 속성을 설정 합니다.
   - 또는 **고급** 탭을 열고 다음 섹션의 PowerShell 예제와 같이 세미콜론으로 구분 된 형식으로 RDP 속성을 추가할 수 있습니다.
8. 완료 되 면 **저장** 을 선택 하 여 변경 내용을 저장 합니다.

다음 섹션에서는 PowerShell에서 사용자 지정 RDP 속성을 수동으로 편집 하는 방법을 설명 합니다.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>단일 사용자 지정 RDP 속성 추가 또는 편집

단일 사용자 지정 RDP 속성을 추가 하거나 편집 하려면 다음 PowerShell cmdlet을 실행 합니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

방금 실행 한 cmdlet이 속성을 업데이트 했는지 확인 하려면 다음 cmdlet을 실행 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

예를 들어 0301HP 라는 호스트 풀의 "audiocapturemode" 속성을 확인 하는 경우이 cmdlet을 입력 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>여러 사용자 지정 RDP 속성 추가 또는 편집

여러 사용자 지정 RDP 속성을 추가 하거나 편집 하려면 사용자 지정 RDP 속성을 세미콜론으로 구분 된 문자열로 제공 하 여 다음 PowerShell cmdlet을 실행 합니다.

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

다음 cmdlet을 실행 하 여 RDP 속성이 추가 되었는지 확인할 수 있습니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

이전 cmdlet 예제에 따라 0301HP 호스트 풀에 여러 RDP 속성을 설정 하는 경우 cmdlet은 다음과 같이 표시 됩니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>모든 사용자 지정 RDP 속성 다시 설정

[단일 사용자 지정 rdp 속성 추가 또는 편집](#add-or-edit-a-single-custom-rdp-property)의 지침에 따라 개별 사용자 지정 rdp 속성을 기본값으로 다시 설정 하거나, 다음 PowerShell cmdlet을 실행 하 여 호스트 풀의 모든 사용자 지정 rdp 속성을 다시 설정할 수 있습니다.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

설정이 성공적으로 제거 되었는지 확인 하려면 다음 cmdlet을 입력 합니다.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>다음 단계

이제 지정 된 호스트 풀의 RDP 속성을 사용자 지정 했으므로 Windows 가상 데스크톱 클라이언트에 로그인 하 여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 방법 가이드에서는 사용자가 선택한 클라이언트를 사용 하 여 세션에 연결 하는 방법을 설명 합니다.

- [Windows Desktop 클라이언트를 사용하여 연결](connect-windows-7-10.md)
- [웹 클라이언트를 사용하여 연결](connect-web.md)
- [Android 클라이언트와 연결](connect-android.md)
- [macOS 클라이언트와 연결](connect-macos.md)
- [iOS 클라이언트와 연결](connect-ios.md)