---
title: 'VPN Gateway: VPN 클라이언트 문제 해결-Azure AD 인증'
description: Azure AD 인증 클라이언트 VPN Gateway P2S 문제 해결
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151963"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Azure AD 인증 VPN 클라이언트 문제 해결

이 문서는 지점 및 사이트 간 VPN을 사용 하 여 가상 네트워크에 연결 하 고 인증을 Azure Active Directory VPN 클라이언트 문제를 해결 하는 데 도움이 됩니다.

## <a name="status"></a>상태 로그 보기

오류 메시지에 대 한 상태 로그를 봅니다.

![logs](./media/troubleshoot-ad-vpn-client/1.png)

1. 클라이언트 창의 오른쪽 아래에 있는 화살표 아이콘을 클릭 하 여 **상태 로그**를 표시 합니다.
2. 문제를 나타낼 수 있는 오류에 대 한 로그를 확인 합니다.
3. 오류 메시지는 빨강으로 표시 됩니다.

## <a name="clear"></a>로그인 정보 지우기

로그인 정보를 지웁니다.

![로그인](./media/troubleshoot-ad-vpn-client/2.png)

1. ...를 선택 합니다. 문제를 해결 하려는 프로필 옆에 있습니다. **구성-저장 된 계정 > 선택 취소**합니다.
2. **저장**을 선택합니다.
3. 연결을 시도합니다.
4. 그래도 연결에 실패 하는 경우 다음 섹션을 계속 진행 합니다.

## <a name="diagnostics"></a>진단 실행

VPN 클라이언트에서 진단을 실행 합니다.

![진단](./media/troubleshoot-ad-vpn-client/3.png)

1. **Schemas** 속성에서 진단을 실행 하려는 프로필 옆에 있습니다. **진단 > 진단 실행**을 선택 합니다.
2. 클라이언트는 일련의 테스트를 실행 하 고 테스트 결과를 표시 합니다.

   * 인터넷 액세스 – 클라이언트가 인터넷에 연결 되어 있는지 확인 합니다.
   * 클라이언트 자격 증명 – Azure Active Directory 인증 엔드포인트에 연결할 수 있는지 확인 합니다.
   * 서버 확인 가능-DNS 서버에 연결 하 여 구성 된 VPN 서버의 IP 주소를 확인 합니다.
   * 서버에 연결할 수 있음 – VPN 서버가 응답 하는지 확인 합니다.
3. 테스트가 실패 하는 경우 네트워크 관리자에 게 문의 하 여 문제를 해결 하십시오.
4. 다음 섹션에서는 필요한 경우 로그를 수집 하는 방법을 보여 줍니다.

## <a name="logfiles"></a>클라이언트 로그 파일 수집

VPN 클라이언트 로그 파일을 수집 합니다. 로그 파일은 선택한 방법을 통해 지원/관리자에 게 보낼 수 있습니다. 예를 들어 전자 메일입니다.

1. "..."를 클릭 합니다. 진단을 실행 하려는 프로필 옆에 있습니다. **진단-로그 디렉터리 표시 >** 선택 합니다.

   ![로그 표시](./media/troubleshoot-ad-vpn-client/4.png)
2. 로그 파일이 포함 된 폴더에 대 한 Windows 탐색기가 열립니다.

   ![파일 보기](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [AZURE AD 인증을 사용 하는 P2S OPEN VPN 연결에 대 한 Azure Active Directory 테넌트 만들기](openvpn-azure-ad-tenant.md)를 참조 하세요.