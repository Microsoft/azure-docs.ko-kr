---
title: Windows 가상 데스크톱 환경-Azure
description: Windows 가상 데스크톱 환경의 기본 요소
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 23bf9be8e3e5f1c52546faa9ed5171c140eba59a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676616"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Virtual Desktop 환경

Windows 가상 데스크톱은 사용자에 게 가상화 된 데스크톱 및 Remoteapp에 대 한 액세스를 쉽고 안전 하 게 제공 하는 서비스입니다. 이 항목에서는 Windows 가상 데스크톱 환경의 일반적인 구조에 대해 자세히 설명 합니다.

## <a name="tenants"></a>테넌트

Windows 가상 데스크톱 테넌트는 Windows 가상 데스크톱 환경을 관리 하기 위한 기본 인터페이스입니다. 각 Windows 가상 데스크톱 테넌트는 환경에 로그인 할 사용자를 포함 하는 Azure Active Directory 연결 되어야 합니다. Windows 가상 데스크톱 테넌트에서 사용자의 워크 로드를 실행 하는 호스트 풀 만들기를 시작할 수 있습니다.

## <a name="host-pools"></a>호스트 풀

호스트 풀은 windows 가상 데스크톱 에이전트를 실행할 때 Windows 가상 데스크톱에 세션 호스트로 등록 하는 Azure 가상 컴퓨터의 컬렉션입니다. 일관 된 사용자 환경을 위해 호스트 풀의 모든 세션 호스트 가상 머신은 동일한 이미지에서 원본 이어야 합니다.

호스트 풀은 다음 두 가지 유형 중 하나일 수 있습니다.

- 개인-각 세션 호스트가 개별 사용자에 게 할당 됩니다.
- 풀링된-세션 호스트가 호스트 풀 내의 앱 그룹에 대해 권한이 부여 된 모든 사용자의 연결을 허용할 수 있습니다.

호스트 풀에서 추가 속성을 설정 하 여 부하 분산 동작, 각 세션 호스트에서 수행할 수 있는 세션 수, 사용자가 Windows 가상 데스크톱 세션에 로그인 한 동안 호스트 풀의 호스트를 세션에 대해 수행할 수 있는 작업을 변경할 수 있습니다. 앱 그룹을 통해 사용자에 게 게시 된 리소스를 제어할 수 있습니다.

## <a name="app-groups"></a>앱 그룹

앱 그룹은 호스트 풀의 세션 호스트에 설치 된 응용 프로그램의 논리적 그룹입니다. 앱 그룹은 다음 두 가지 유형 중 하나일 수 있습니다.

- 사용자가 앱 그룹에 개별적으로 선택 하 고 게시 하는 Remoteapp에 액세스 하는 RemoteApp
- 사용자가 전체 데스크톱에 액세스 하는 데스크톱

기본적으로 "데스크톱 응용 프로그램 그룹" 이라는 데스크톱 앱 그룹은 호스트 풀을 만들 때마다 자동으로 생성 됩니다. 언제 든 지이 앱 그룹을 제거할 수 있습니다. 그러나 데스크톱 응용 프로그램 그룹이 있는 동안에는 호스트 풀에서 다른 데스크톱 앱 그룹을 만들 수 없습니다. Remoteapp을 게시 하려면 RemoteApp 앱 그룹을 만들어야 합니다. 여러 가지 작업자 시나리오를 수용 하기 위해 여러 RemoteApp 앱 그룹을 만들 수 있습니다. 다른 RemoteApp 앱 그룹에는 겹치는 Remoteapp 포함 될 수도 있습니다.

사용자에 게 리소스를 게시 하려면 앱 그룹에 리소스를 할당 해야 합니다. 앱 그룹에 사용자를 할당 하는 경우 다음 사항을 고려 하세요.

- 사용자는 동일한 호스트 풀의 데스크톱 앱 그룹과 RemoteApp 앱 그룹 모두에 할당할 수 없습니다.
- 사용자는 동일한 호스트 풀 내의 여러 앱 그룹에 할당 될 수 있으며, 피드는 두 앱 그룹의 누적 됩니다.

## <a name="tenant-groups"></a>테넌트 그룹

Windows 가상 데스크톱에서 Windows 가상 데스크톱 테넌트는 대부분의 설정 및 구성이 발생 합니다. Windows 가상 데스크톱 테넌트에는 호스트 풀, 앱 그룹 및 앱 그룹 사용자 할당이 포함 됩니다. 그러나 한 번에 여러 Windows 가상 데스크톱 테넌트를 관리 해야 하는 특정 상황이 있을 수 있습니다. 특히, CSP (클라우드 서비스 공급자) 또는 호스팅 파트너인 경우에 한 합니다. 이러한 경우 사용자 지정 Windows 가상 데스크톱 테넌트 그룹을 사용 하 여 각 고객의 Windows 가상 데스크톱 테넌트를 저장 하 고 중앙에서 액세스를 관리할 수 있습니다. 그러나 단일 Windows 가상 데스크톱 테넌트만 관리 하는 경우에는 테넌트 그룹 개념이 적용 되지 않으며 기본 테넌트 그룹에 있는 테넌트를 계속 운영 및 관리할 수 있습니다.

## <a name="end-users"></a>최종 사용자

사용자를 앱 그룹에 할당 한 후에는 Windows 가상 데스크톱 클라이언트를 사용 하 여 Windows 가상 데스크톱 배포에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

위임 된 액세스에 대 한 자세한 내용과 [Windows 가상 데스크톱의 위임 된 액세스](delegated-access-virtual-desktop.md)에서 사용자에 게 역할을 할당 하는 방법에 대해 알아보세요.

Windows 가상 데스크톱 테넌트를 설정 하는 방법을 알아보려면 [Windows 가상 데스크톱에서 테넌트 만들기](tenant-setup-azure-active-directory.md)를 참조 하세요.

Windows 가상 데스크톱에 연결 하는 방법을 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Windows 10 또는 Windows 7에서 연결](connect-windows-7-and-10.md)
- [웹 브라우저에서 연결](connect-web.md)
