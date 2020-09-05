---
title: LDAP 인증 및 Azure MFA 서버-Azure Active Directory
description: LDAP 인증 및 Azure Multi-Factor Authentication 서버 배포.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f15fcd2d8e42d56deb50ee7eaae7472dae6bf6c
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949493"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP 인증 및 Azure Multi-Factor Authentication 서버

기본적으로 Azure Multi-Factor Authentication 서버는 Active Directory에서 사용자를 가져오거나 동기화하도록 구성됩니다. 그러나 ADAM 디렉터리 또는 특정 Active Directory 도메인 컨트롤러 같은 다른 LDAP 디렉터리에 바인딩하도록 구성할 수 있습니다. LDAP를 통해 디렉터리에 연결된 경우 Azure Multi-Factor Authentication 서버는 LDAP 프록시의 역할인 인증을 수행할 수 있습니다. 또한 RADIUS 대상으로서 LDAP 바인딩 사용, IIS 인증을 사용한 사용자의 사전 인증 또는 Azure MFA 사용자 포털에서 기본 인증이 가능합니다.

Azure Multi-Factor Authentication을 LDAP 프록시로 사용하려면 Azure Multi-Factor Authentication 서버를 LDAP 클라이언트(예: VPN 어플라이언스, 애플리케이션)와 LDAP 디렉터리 서버 사이에 삽입합니다. Azure Multi-Factor Authentication 서버가 클라이언트 서버 및 LDAP 디렉터리와 모두 통신할 수 있도록 구성되어야 합니다. 이 구성에서 Azure Multi-Factor Authentication 서버는 클라이언트 서버 및 애플리케이션의 LDAP 요청을 수락하고 대상 LDAP 디렉터리 서버에 전달하여 기본 자격 증명의 유효성을 검사합니다. LDAP 디렉터리가 기본 자격 증명의 유효성을 검사하면 Azure Multi-Factor Authentication은 두 번째 ID 검증을 수행하고 다시 LDAP 클라이언트로 응답을 보냅니다. LDAP 서버 인증 및 2단계 검증이 모두 성공해야만 전체 인증에 성공합니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="configure-ldap-authentication"></a>LDAP 인증 구성

LDAP 인증을 구성하려면 Windows 서버에 Azure Multi-Factor Authentication 서버를 설치합니다. 이렇게 하려면 다음 절차를 수행합니다.

### <a name="add-an-ldap-client"></a>LDAP 클라이언트를 추가합니다.

1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 LDAP 인증 아이콘을 선택합니다.
2. **LDAP 인증 사용** 확인란을 선택합니다.

   ![MFA 서버의 LDAP 인증](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. LDAP 요청을 수신 하기 위해 Azure Multi-Factor Authentication LDAP 서비스가 비표준 포트에 바인딩해야 하는 경우 클라이언트 탭에서 TCP 포트 및 SSL (TLS) 포트를 변경 합니다.
4. 클라이언트에서 Azure Multi-Factor Authentication 서버로 LDAPS를 사용 하려는 경우에는 TLS/SSL 인증서를 MFA 서버와 동일한 서버에 설치 해야 합니다. SSL (TLS) 인증서 상자 옆에 있는 **찾아보기** 를 클릭 하 고 보안 연결에 사용할 인증서를 선택 합니다.
5. **추가**를 클릭합니다.
6. [LDAP 클라이언트 추가] 대화 상자에서 서버 및 애플리케이션 이름(선택 사항)을 인증하는 애플리케이션의 IP 주소, 서버 및 애플리케이션을 입력합니다. 애플리케이션 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
7. 모든 사용자를 서버로 가져왔거나 가져올 예정이고 2단계 확인을 적용하는 경우 **Azure Multi-Factor Authentication 사용자 일치 필요** 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 2단계 확인에서 제외할 예정이면 이 확인란을 선택 취소합니다. 이 기능에 대한 자세한 내용은 MFA 서버 도움말 파일을 참조하세요.

LDAP 클라이언트를 더 추가하려면 이 단계를 반복합니다.

### <a name="configure-the-ldap-directory-connection"></a>LDAP 디렉터리 연결 구성

Azure Multi-Factor Authentication이 LDAP 인증을 받도록 구성된 경우 해당 인증을 LDAP 디렉터리로 프록시해야 합니다. 따라서 대상 탭에는 LDAP 대상을 사용하기 위해 단일한 회색 옵션만 표시됩니다.

> [!NOTE]
> 디렉터리 통합은 Active Directory Domain Services 이외의 디렉터리에서 작동 하지 않을 수 있습니다.

1. LDAP 디렉터리 연결을 구성하려면 **디렉터리 통합** 아이콘을 클릭합니다.
2. 설정 탭에서 **특정 LDAP 구성 사용** 라디오 단추를 선택 합니다.
3. **편집 ...** 을 선택 합니다.
4. Edit LDAP Configuration(LDAP 구성 편집) 대화 상자에서 LDAP 디렉터리 연결에 필요한 정보를 필드에 입력합니다. 필드의 설명은 Azure Multi-Factor Authentication 서버 도움말 파일에도 포함되어 있습니다.

    ![디렉터리 통합 LDAP 구성](./media/howto-mfaserver-dir-ldap/ldap.png)

5. **테스트** 단추를 클릭 하 여 LDAP 연결을 테스트 합니다.
6. LDAP 연결 테스트가 성공한 경우 **확인** 단추를 클릭 합니다.
7. **필터** 탭을 클릭 합니다. 서버는 Active Directory에서 컨테이너, 보안 그룹 및 사용자를 로드 하도록 미리 구성 되어 있습니다. 다른 LDAP 디렉터리에 바인딩하는 경우 표시되는 필터를 편집해야 합니다. 필터에 대한 자세한 내용은 **도움말** 링크를 클릭합니다.
8. **특성** 탭을 클릭 합니다. 서버는 Active Directory의 특성을 매핑하도록 미리 구성 되어 있습니다.
9. 다른 LDAP 디렉터리에 바인딩하거나 미리 구성된 특성 매핑을 변경하려는 경우 **편집...** 을 클릭합니다.
10. Edit Attributes(특성 편집) 대화 상자에서 디렉터리에 대한 LDAP 특성 매핑을 수정합니다. 각 필드 옆에 있는 **…** 단추를 클릭하면 특성 이름을 입력하거나 선택할 수 있습니다. 특성에 대한 자세한 내용은 **도움말** 링크를 클릭합니다.
11. **확인** 단추를 클릭합니다.
12. **회사 설정** 아이콘을 클릭하고 **사용자 이름 확인** 탭을 선택합니다.
13. 도메인에 가입된 서버에서 Active Directory에 연결하는 경우 **사용자 이름과 일치하는 Windows 보안 식별자(SID) 사용** 라디오 단추를 선택한 상태로 그대로 둡니다. 그렇지 않으면 **사용자 이름과 일치하는 LDAP 고유 식별자 특성 사용** 라디오 단추를 선택합니다.

**사용자 이름과 일치하는 LDAP 고유 식별자 특성 사용** 라디오 단추를 선택한 경우 Azure Multi-factor Authentication 서버에서는 각 사용자 이름을 LDAP 디렉터리의 고유 식별자로 결정하려고 합니다. LDAP 검색은 디렉터리 통합-> 특성 탭에 정의 된 사용자 이름 특성에 대해 수행 됩니다. 사용자가 인증 하는 경우 사용자 이름은 LDAP 디렉터리의 고유 식별자로 확인 됩니다. Azure Multi-Factor Authentication 데이터 파일에서 사용자를 일치시키기 위해 고유 식별자를 사용합니다. 이를 통해 대/소문자 구분 비교 및 길고 짧은 사용자 이름 형식 사용이 가능합니다.

이 단계를 완료한 후에 MFA 서버는 구성된 클라이언트의 LDAP 액세스 요청에 대해 구성된 포트에서 수신하고 인증하기 위해 LDAP 디렉터리로 해당 요청의 프록시 역할을 담당합니다.

## <a name="configure-ldap-client"></a>LDAP 클라이언트 구성

LDAP 클라이언트를 구성하려면 다음 지침을 사용합니다.

* 어플라이언스, 서버 또는 애플리케이션이 마치 LDAP 디렉터리인 것처럼 LDAP를 통해 Azure Multi-Factor Authentication 서버를 인증하도록 구성합니다. 일반적으로 LDAP 디렉터리에 직접 연결하기 위해 사용하는 설정과 동일한 설정을 사용합니다. 단 Azure Multi-Factor Authentication 서버에 사용할 서버 이름 또는 IP 주소는 그렇지 않습니다.
* Ldap 디렉터리를 사용 하 여 사용자 자격 증명의 유효성을 검사할 시간이 있도록 LDAP 제한 시간을 30-60 초로 구성 하 고, 두 번째 단계 인증을 수행 하 고, 응답을 수신 하 고, LDAP 액세스 요청에 응답 합니다.
* LDAPS를 사용 하는 경우 LDAP 쿼리를 만드는 어플라이언스 또는 서버는 Azure Multi-Factor Authentication 서버에 설치 된 TLS/SSL 인증서를 신뢰 해야 합니다.
