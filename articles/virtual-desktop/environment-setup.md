---
title: Windows 가상 데스크톱 미리 보기 환경-Azure
description: 기본 요소는 Windows 가상 데스크톱 미리 보기 환경입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60870322"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Windows 가상 데스크톱 미리 보기 환경

Windows 가상 데스크톱 미리 보기는 가상화 된 데스크톱 및 Remoteapp에 사용자가 손쉽고 안전한 액세스를 제공 하는 서비스입니다. 이 항목에서는 Windows 가상 데스크톱 환경의 일반적인 구조에 대 한 좀을 알려 드립니다.

## <a name="tenants"></a>테 넌 트

Windows 가상 데스크톱 테 넌 트에는 Windows 가상 데스크톱 환경 관리에 대 한 기본 인터페이스입니다. 각 Windows 가상 데스크톱 테 넌 트 환경에 로그인 하는 사용자를 포함 하는 Azure Active Directory와 연결 되어야 합니다. Windows 가상 데스크톱 테 넌 트에서 사용자 워크 로드를 실행 하려면 호스트 풀을 만들기 시작할 수 있습니다.

## <a name="host-pools"></a>호스트 풀

호스트 풀은 가상 데스크톱 Windows 에이전트를 실행할 때 세션 호스트로 Windows 가상 데스크톱에 등록 하는 Azure virtual machines의 컬렉션입니다. 호스트 풀의 모든 호스트 가상 컴퓨터 세션을 기반으로 일관 된 사용자 경험에 대 한 동일한 이미지에서 해야 합니다.

호스트 풀에는 두 가지 유형 중 하나일 수 있습니다.

- 개인 경우 각 세션 호스트는 개별 사용자에 게 할당 됩니다.
- 풀링된 세션 호스트 호스트 풀 내의 앱 그룹 권한이 있는 모든 사용자의 연결을 허용할 수 있습니다 위치 합니다.

각 세션 호스트 수, 얼마나 많은 세션의 부하 분산 동작을 변경 하려면 호스트 그룹 및 사용자의 Windows 가상 데스크톱 세션에 로그인 하는 동안 호스트 풀의 세션 호스트를 수행할 수 있는 추가 속성을 설정할 수 있습니다. 앱 그룹을 통해 사용자에 게 게시 하는 리소스를 제어 합니다.

## <a name="app-groups"></a>앱 그룹

앱 그룹은 호스트 풀의 세션 호스트에 설치 하는 응용 프로그램의 논리적 그룹화입니다. 앱 그룹에는 두 가지 유형 중 하나일 수 있습니다.

- 사용자는 Remoteapp에 액세스 RemoteApp 개별적으로 선택 하 고 앱 그룹에 게시
- 데스크톱, 사용자는 전체 데스크톱에 액세스

데스크톱 앱 그룹 ("데스크톱 응용 프로그램 그룹" 이라는)가 기본적으로 호스트 풀을 만들 때마다 자동으로 만들어집니다. 언제 든 지가 앱 그룹을 제거할 수 있습니다. 그러나 데스크톱 앱 그룹이 존재 하는 동안 호스트 풀에 다른 데스크톱 앱 그룹을 만들 수 없습니다. Remoteapp에 게시 하려면 RemoteApp 앱 그룹을 만들어야 합니다. 다른 작업자 시나리오에 맞게 여러 RemoteApp 앱 그룹을 만들 수 있습니다. RemoteApp 앱 그룹을 다른 Remoteapp 겹치는 포함할 수도 있습니다.

사용자에 게 리소스를 게시할 앱 그룹에 할당 해야 합니다. 사용자가 앱 그룹에 할당할 때 다음 작업을 고려 합니다.

- 데스크톱 앱 그룹과 같은 호스트 그룹에서 RemoteApp 앱 그룹 모두에 사용자를 할당할 수 없습니다.
- 호스트, 동일한 풀에 있는 여러 앱 그룹에 사용자를 지정할 수 및 해당 피드는 앱 그룹을 모두 누적 됩니다.

## <a name="tenant-groups"></a>테 넌 트 그룹

Windows 가상 데스크톱, Windows 가상 데스크톱 테 넌 트 설치 및 구성의 대부분이 이루어지는입니다. Windows 가상 데스크톱 테 넌 트 호스트 풀, 앱 그룹 및 앱 그룹 사용자 할당을 포함합니다. 그러나 특정 해야 하는 경우 한 번에 여러 Windows 가상 데스크톱 테 넌 트를 관리 하는 클라우드 서비스 공급자 (CSP) 또는 호스팅 파트너 인 경우에 특히 있을 수 있습니다. 이러한 상황에서 각 고객의 Windows 가상 데스크톱 테 넌 트를 배치 하 고 액세스를 중앙에서 관리 사용자 지정 Windows 가상 데스크톱 테 넌 트 그룹을 사용할 수 있습니다. 그러나 단일 Windows 가상 데스크톱 테 넌 트에만 관리 하 고 있다면 테 넌 트 그룹 개념이 적용 되지 않습니다 및 작동 하 고 기본 테 넌 트 그룹에 존재 하는 테 넌 트 관리를 할 수 있습니다.

## <a name="end-users"></a>최종 사용자

앱 그룹에 사용자를 할당 한 후 Windows 가상 데스크톱 클라이언트를 사용 하 여 Windows 가상 데스크톱 배포를 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

위임 된 권한과 사용자에 게 역할을 할당 하는 방법에 자세히 알아보려면 [Windows 가상 데스크톱 미리 보기에 대 한 위임 된 액세스](delegated-access-virtual-desktop.md)합니다.

Windows Virtual Desktop 테 넌 트를 설정 하는 방법에 알아보려면 참조 [Windows 가상 데스크톱 미리 보기에서 테 넌 트를 만드는](tenant-setup-azure-active-directory.md)합니다.

Windows 가상 데스크톱에 연결 하는 방법에 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Windows 7 및 Windows 10에서 원격 데스크톱 클라이언트에 연결](connect-windows-7-and-10.md)
- [Windows Virtual Desktop 미리 보기 웹 클라이언트에 연결](connect-web.md)
