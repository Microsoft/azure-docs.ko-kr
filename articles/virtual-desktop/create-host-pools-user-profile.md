---
title: Windows 가상 데스크톱 미리 보기 호스트 풀-Azure에 대 한 사용자 프로필 공유 설정
description: Windows 가상 데스크톱 미리 보기 호스트 풀 FSLogix 프로필 컨테이너를 설정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 0cb4df099faad8ca482fd15cf0bb50504c1528ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883873"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>호스트 풀에 대한 사용자 프로필 공유 설정

Windows 가상 데스크톱 미리 보기 서비스는 권장 되는 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 제공합니다. 될 사용자 프로필 디스크 (UPD) 솔루션을 사용 하 여 사용 되지 않는 이후 버전의 Windows 가상 데스크톱 없는 것이 좋습니다.

이 섹션에서는 호스트 풀에 대 한 FSLogix 프로필 컨테이너 공유를 설정 하는 방법을 알려줍니다. FSLogix에 대 한 일반 설명서에 대 한 참조를 [FSLogix 사이트](https://docs.fslogix.com/)합니다.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>파일 공유로 할 새 가상 머신 만들기

가상 컴퓨터를 만들 때 하거나 동일한 가상 네트워크에 풀 가상 머신과 호스트 또는 호스트 풀 가상 컴퓨터에 연결 되어 있는 가상 네트워크에 배치 해야 합니다. 여러 가지 방법으로 가상 컴퓨터를 만들 수 있습니다.

- [Azure 갤러리 이미지에서 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [관리 되는 이미지에서 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [관리 되지 않는 이미지에서 가상 머신 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

가상 컴퓨터를 만든 후 도메인에 가입 된 다음을 수행 하 여:

1. [가상 머신에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) 가상 머신을 만들 때 제공한 자격 증명을 사용 합니다.
2. 가상 컴퓨터에서 시작 **Control Panel** 선택한 **시스템**입니다.
3. 선택 **컴퓨터 이름**를 선택 **설정을 변경**를 선택한 후 **변경 하는 중...**
4. 선택 **도메인** 가상 네트워크에 Active Directory 도메인을 입력 합니다.
5. 도메인 가입 컴퓨터에 대 한 권한이 있는 도메인 계정으로 인증 합니다.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>사용자 프로필에 대 한 파일 공유로 작동 하도록 가상 머신 준비

사용자 프로필에 대 한 파일 공유로 작동 하도록 가상 컴퓨터를 준비 하는 방법에 대 한 일반 지침은 다음과 같습니다.

1. Windows 가상 데스크톱 Active Directory 사용자를 추가 된 [Active Directory 보안 그룹](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups)합니다. 방금 만든 파일 공유 가상 컴퓨터에 Windows 가상 데스크톱 사용자를 인증 하도록이 보안 그룹 사용 됩니다.
2. [파일 공유 가상 머신에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)합니다.
3. 파일 공유 가상 컴퓨터에 폴더를 만들고 합니다 **C 드라이브** 프로 파일 공유로 사용 되는 합니다.
4. 새 폴더를 마우스 오른쪽 단추로 클릭 한 다음를 선택 합니다 **속성**를 선택 **공유**를 선택 하 고 **고급 공유 하는 중...** .
5. 선택 **이 폴더를 공유**, 선택 **권한...** 를 선택 하 고 **추가...** .
6. Windows 가상 데스크톱 사용자에 추가 된 보안 그룹을 검색 한 다음 해당 그룹에 있는지 **전면적인**합니다.
7. 폴더를 마우스 오른쪽 단추로 클릭는 보안 그룹을 추가한 후을 선택 합니다 **속성**를 선택 **공유**, 아래로 복사 합니다 **네트워크 경로** 나중에 사용할 합니다.

사용 권한에 대 한 자세한 내용은 참조는 [FSLogix 설명서](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers)합니다.

## <a name="configure-the-fslogix-profile-container"></a>FSLogix 프로필 컨테이너 구성

FSLogix 소프트웨어를 사용 하 여 가상 컴퓨터를 구성 하려면 호스트 그룹에 등록 된 각 컴퓨터에서 다음을 수행 합니다.

1. [가상 머신에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) 가상 머신을 만들 때 제공한 자격 증명을 사용 합니다.
2. 인터넷 브라우저를 시작 하 고 이동할 [이 링크](https://go.microsoft.com/fwlink/?linkid=2084562) FSLogix 에이전트를 다운로드 합니다. Windows 가상 데스크톱 공개 미리 보기의 일부로, FSLogix 소프트웨어를 활성화 하려면 라이선스 키를 얻을 수 있습니다. 키는 LicenseKey.txt 파일 FSLogix 에이전트.zip 파일에 포함 합니다.
3. 로 이동 \\ \\Win32\\릴리스 또는 \\ \\X64\\.zip 파일과 실행에 대 한 릴리스 **FSLogixAppsSetup** FSLogix 에이전트를 설치 합니다.
4. 이동할 **Program Files** > **FSLogix** > **앱** 에이전트 설치를 확인 합니다.
5. 시작 메뉴에서 실행 **RegEdit** 관리자 권한으로 합니다. 이동할 **컴퓨터\\HKEY_LOCAL_MACHINE\\소프트웨어\\FSLogix**합니다.
6. 명명 된 키를 만듭니다 **프로필**합니다.
7. 프로필 키에 대해 다음 값을 만듭니다.

| 이름                | Type               | 데이터/값                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | 다중 문자열 값 | "파일 공유에 대 한 네트워크 경로"     |

>[!IMPORTANT]
>Azure에서 Windows Virtual Desktop 환경의 보안을 유지하도록 돕기 위해 VM에서 인바운드 포트 3389를 열지 않는 것이 좋습니다. Windows Virtual Desktop에서는 사용자가 인바운드 포트 3389를 열지 않아도 호스트 풀의 VM에 액세스할 수 있습니다. 문제 해결을 위해 포트 3389를 열어야 하는 경우 [Just-In-Time VM 액세스](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)를 사용하는 것이 좋습니다.