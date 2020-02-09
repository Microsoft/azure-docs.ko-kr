---
title: Azure AD 암호 보호-Azure Active Directory
description: Azure AD 암호 보호를 사용 하 여 온-프레미스 Active Directory에서 weak 암호 금지
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848649"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Windows Server Active Directory에 Azure AD 암호 보호 강제 적용

Azure AD 암호 보호는 조직의 암호 정책을 향상 시키는 기능입니다. 암호 보호의 온-프레미스 배포는 Azure AD에 저장 된 전역 및 사용자 지정 금지 된 암호 목록을 모두 사용 합니다. Azure AD가 클라우드 기반 변경에 대해 수행 하는 것 처럼 온-프레미스에서 동일한 검사를 수행 합니다. 이러한 검사는 암호 변경 및 암호 다시 설정 시나리오에서 수행 됩니다.

## <a name="design-principles"></a>디자인 원칙

Azure AD 암호 보호는 다음과 같은 원칙을 염두에 두어야 합니다.

* 도메인 컨트롤러는 인터넷과 직접 통신할 필요가 없습니다.
* 새 네트워크 포트가 도메인 컨트롤러에서 열려 있지 않습니다.
* Active Directory 스키마 변경이 필요하지 않습니다. 이 소프트웨어는 기존 Active Directory **컨테이너** 및 **serviceConnectionPoint** 스키마 개체를 사용 합니다.
* 최소 Active Directory 도메인 또는 포리스트 기능 수준 (DFL/FFL)이 필요 하지 않습니다.
* 소프트웨어는 보호 하는 Active Directory 도메인에서 계정을 만들거나 요구 하지 않습니다.
* 사용자 일반 텍스트 암호는 암호 유효성 검사 작업 중 또는 다른 시간에 도메인 컨트롤러를 떠나지 않습니다.
* 이 소프트웨어는 다른 Azure AD 기능에 종속 되지 않습니다. 예를 들어 Azure AD 암호 해시 동기화는 관련이 없으며 Azure AD 암호 보호가 작동 하기 위해 필요 하지 않습니다.
* 증분 배포를 지원 하지만 암호 정책은 도메인 컨트롤러 에이전트 (DC 에이전트)가 설치 된 위치에만 적용 됩니다. 자세한 내용은 다음 항목을 참조 하세요.

## <a name="incremental-deployment"></a>증분 배포

Azure AD 암호 보호는 Active Directory 도메인의 도메인 컨트롤러에 대 한 증분 배포를 지원 하지만,이에 대 한 의미 및 장단점을 이해 하는 것이 중요 합니다.

Azure AD 암호 보호 DC 에이전트 소프트웨어는 도메인 컨트롤러에 설치 된 경우와 해당 도메인 컨트롤러에 전송 된 암호 변경에만 암호의 유효성을 검사할 수 있습니다. 사용자 암호 변경을 처리 하기 위해 Windows 클라이언트 컴퓨터에서 선택할 도메인 컨트롤러를 제어할 수 없습니다. 일관 된 동작 및 유니버설 암호 보호 보안 적용을 보장 하기 위해 도메인의 모든 도메인 컨트롤러에 DC 에이전트 소프트웨어를 설치 해야 합니다.

대부분의 조직에서는 전체 배포를 수행 하기 전에 도메인 컨트롤러의 하위 집합에 대 한 Azure AD 암호 보호를 신중 하 게 테스트 하려고 합니다. Azure AD 암호 보호는 부분 배포를 지원 합니다. 즉, 지정 된 DC의 DC 에이전트 소프트웨어는 도메인의 다른 Dc에 DC 에이전트 소프트웨어가 설치 되어 있지 않은 경우에도 암호의 유효성을 검사 합니다. 이 형식의 부분 배포는 안전 하지 않으며 테스트 목적으로는 사용 하지 않는 것이 좋습니다.

## <a name="architectural-diagram"></a>아키텍처 다이어그램

온-프레미스 Active Directory 환경에서 Azure AD 암호 보호를 배포 하기 전에 기본적인 디자인 및 기능 개념을 이해 하는 것이 중요 합니다. 다음 다이어그램에서는 암호 보호 구성 요소가 함께 작동 하는 방법을 보여 줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 암호 보호 프록시 서비스는 현재 Active Directory 포리스트의 도메인에 조인된 모든 머신에서 실행됩니다. 기본 용도는 도메인 컨트롤러에서 Azure AD로 암호 정책 다운로드 요청을 전달 하는 것입니다. 그런 다음 Azure AD에서 도메인 컨트롤러로 응답을 반환 합니다.
* DC 에이전트의 암호 필터 DLL은 운영 체제에서 사용자 암호 유효성 검사 요청을 받습니다. 도메인 컨트롤러에서 로컬로 실행 되는 DC 에이전트 서비스로 전달 합니다.
* 암호 보호의 DC 에이전트 서비스는 DC 에이전트의 암호 필터 DLL에서 암호 유효성 검사 요청을 받습니다. 현재 (로컬에서 사용 가능한) 암호 정책을 사용 하 여 처리 하 고 결과를 *pass* 또는 *fail*으로 반환 합니다.

## <a name="how-password-protection-works"></a>암호 보호 작동 방법

각 Azure AD 암호 보호 프록시 서비스 인스턴스는 Active Directory에서 **serviceConnectionPoint** 개체를 만들어 포리스트의 도메인 컨트롤러에 자신을 알립니다.

암호 보호를 위한 각 DC 에이전트 서비스는 또한 Active Directory에 **serviceConnectionPoint** 개체를 만듭니다. 이 개체는 주로 보고 및 진단에 사용 됩니다.

DC 에이전트 서비스는 Azure AD에서 새 암호 정책의 다운로드를 시작 하는 일을 담당 합니다. 첫 번째 단계는 프록시 **serviceConnectionPoint** 개체에 대해 포리스트를 쿼리하여 Azure AD 암호 보호 프록시 서비스를 찾는 것입니다. 사용 가능한 프록시 서비스가 있으면 DC 에이전트는 프록시 서비스에 암호 정책 다운로드 요청을 보냅니다. 그러면 프록시 서비스가 Azure AD로 요청을 보냅니다. 그러면 프록시 서비스가 DC 에이전트 서비스에 대 한 응답을 반환 합니다.

DC 에이전트 서비스가 Azure AD에서 새 암호 정책을 받은 후 서비스는 해당 도메인 *sysvol* 폴더 공유의 루트에 있는 전용 폴더에 정책을 저장 합니다. 또한 DC 에이전트 서비스는 새 정책이 도메인의 다른 DC 에이전트 서비스에서 복제 되는 경우이 폴더를 모니터링 합니다.

DC 에이전트 서비스는 서비스를 시작할 때 항상 새 정책을 요청 합니다. DC 에이전트 서비스가 시작 된 후에는 현재 로컬로 사용 가능한 정책의 기간을 매시간 확인 합니다. 정책이 1 시간 보다 오래 된 경우 DC 에이전트는 앞에서 설명한 대로 프록시 서비스를 통해 Azure AD에서 새 정책을 요청 합니다. 현재 정책이 1 시간 보다 오래 되지 않은 경우 DC 에이전트는 해당 정책을 계속 사용 합니다.

Azure AD 암호 보호 암호 정책이 다운로드 될 때마다 해당 정책은 테넌트에만 적용 됩니다. 즉, 암호 정책은 항상 Microsoft 전역 금지 된 암호 목록과 테넌트 당 사용자 지정 금지 된 암호 목록을 조합한 것입니다.

DC 에이전트는 RPC over TCP를 통해 프록시 서비스와 통신 합니다. 프록시 서비스는 구성에 따라 동적 또는 정적 RPC 포트에서 이러한 호출을 수신 합니다.

DC 에이전트는 네트워크에서 사용 가능한 포트에서 수신 대기 하지 않습니다.

프록시 서비스는 DC 에이전트 서비스를 호출 하지 않습니다.

프록시 서비스는 상태 비저장입니다. Azure에서 다운로드 한 정책 또는 다른 상태는 캐시 하지 않습니다.

DC 에이전트 서비스는 항상 가장 최근의 로컬 사용 가능한 암호 정책을 사용 하 여 사용자의 암호를 평가 합니다. 로컬 DC에서 사용할 수 있는 암호 정책이 없으면 암호가 자동으로 허용 됩니다. 이 경우 관리자에 게 경고 하는 이벤트 메시지가 기록 됩니다.

Azure AD 암호 보호는 실시간 정책 응용 프로그램 엔진이 아닙니다. Azure AD에서 암호 정책 구성이 변경 되는 시간과 해당 변경 내용이 모든 도메인 컨트롤러에서 적용 되는 시점 사이에 지연이 있을 수 있습니다.

Azure AD 암호 보호는 교체가 아닌 기존 Active Directory 암호 정책에 대 한 보완 역할을 합니다. 여기에는 설치할 수 있는 타사 암호 필터 dll이 포함 됩니다. Active Directory 암호를 수락 하기 전에 항상 모든 암호 유효성 검사 구성 요소에 동의 해야 합니다.

## <a name="foresttenant-binding-for-password-protection"></a>암호 보호를 위한 포리스트/테넌트 바인딩

Active Directory 포리스트에 Azure AD 암호 보호를 배포 하려면 Azure AD에 해당 포리스트를 등록 해야 합니다. 또한 배포 된 각 프록시 서비스는 Azure AD에 등록 되어야 합니다. 이러한 포리스트 및 프록시 등록은 등록 중에 사용 되는 자격 증명으로 암시적으로 식별 되는 특정 Azure AD 테넌트와 연결 됩니다.

포리스트 내의 Active Directory 포리스트와 모든 배포 된 프록시 서비스는 동일한 테넌트에 등록 되어야 합니다. 다른 Azure AD 테넌트에 등록 되는 Active Directory 포리스트 또는 해당 포리스트의 프록시 서비스는 지원 되지 않습니다. 잘못 구성 된 배포의 증상으로는 암호 정책을 다운로드할 수 없습니다.

## <a name="download"></a>다운로드

[Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 Azure AD 암호 보호에 필요한 두 개의 에이전트 설치 관리자를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
