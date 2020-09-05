---
title: AD FS 2.0-Azure Active Directory를 사용 하 여 Azure MFA 서버 사용
description: Azure MFA 및 AD FS 2.0 시작 방법을 설명하는 Azure Multi-Factor Authentication 페이지입니다.
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
ms.openlocfilehash: d5bcb63a325ca6bbf464faf9c5f9934879ccf9a3
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949663"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>AD FS 2.0과 작동하도록 Azure Multi-Factor Authentication 서버 구성

이 문서는 Azure Active Directory와 페더레이션되는 조직에 대해 온-프레미스 또는 클라우드에서 리소스의 보안을 유지하려고 작성되었습니다. 중요한 끝점에 대해 2단계 확인을 트리거할 수 있도록 Azure Multi-factor Authentication 서버를 사용하고 AD FS를 사용하도록 구성하여 리소스를 보호합니다.

이 문서에서는 AD FS 2.0과 함께 Azure Multi-Factor Authentication 서버를 사용하는 방법을 소개합니다. AD FS에 대한 자세한 내용은 [Windows Server 2012 R2 AD FS와 Azure Multi-factor Authentication 서버를 사용하여 클라우드 및 온-프레미스 리소스 보안 유지](howto-mfaserver-adfs-2012.md)를 참조하세요.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 클라우드 기반 MFA를 사용 하는 경우 [Azure Multi-Factor Authentication 및 AD FS를 사용 하 여 클라우드 리소스 보안 유지](howto-mfa-adfs.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="secure-ad-fs-20-with-a-proxy"></a>프록시로 AD FS 2.0 보안 유지

프록시로 AD FS 2.0 보안을 유지하려면 AD FS 프록시 서버에 Azure Multi-Factor Authentication 서버를 설치합니다.

### <a name="configure-iis-authentication"></a>IIS 인증 구성

1. Azure Multi-Factor Authentication 서버의 왼쪽 메뉴에서 **IIS 인증** 아이콘을 클릭 합니다.
2. **양식 기반** 탭을 클릭합니다.
3. **추가**를 클릭합니다.

   ![MFA 서버 IIS 인증 창](./media/howto-mfaserver-adfs-2/setup1.png)

4. 사용자 이름, 암호 및 도메인 변수를 자동으로 검색 하려면 `https://sso.contoso.com/adfs/ls` 양식 기반 웹 사이트 자동 구성 대화 상자 내에서 로그인 URL (예:)을 입력 하 고 **확인**을 클릭 합니다.
5. 모든 사용자를 서버로 가져왔거나 가져올 예정이고 2단계 확인을 적용하는 경우 **Azure Multi-Factor Authentication 사용자 일치 필요** 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 2단계 확인에서 제외할 예정이면 이 확인란을 선택 취소합니다.
6. 페이지 변수를 자동으로 검색할 수 없는 경우 **수동으로 지정...** 을 클릭합니다. 양식 기반 웹 사이트 자동 구성 대화 상자의 단추.
7. [양식 기반 웹 사이트 추가] 대화 상자에서 등록 URL 필드 (예:)에 AD FS 로그인 페이지의 URL을 입력 하 `https://sso.contoso.com/adfs/ls` 고 응용 프로그램 이름 (선택 사항)을 입력 합니다. 애플리케이션 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
8. 요청 형식을 **POST 또는 GET**으로 설정 합니다.
9. Username 변수(ctl00$ContentPlaceHolder1$UsernameTextBox) 및 Password 변수(ctl00$ContentPlaceHolder1$PasswordTextBox)를 입력합니다. 양식 기반 로그인 페이지에 도메인 텍스트 상자가 표시되면 Domain 변수도 입력합니다. 로그인 페이지에서 입력 상자의 이름을 찾으려면 웹 브라우저에서 로그인 페이지로 이동하고 해당 페이지를 마우스 오른쪽 단추로 클릭하여 **소스 보기**를 선택합니다.
10. 모든 사용자를 서버로 가져왔거나 가져올 예정이고 2단계 확인을 적용하는 경우 **Azure Multi-Factor Authentication 사용자 일치 필요** 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 2단계 확인에서 제외할 예정이면 이 확인란을 선택 취소합니다.

    ![MFA 서버에 양식 기반 웹 사이트 추가](./media/howto-mfaserver-adfs-2/manual.png)

11. **고급 ...** 을 클릭 합니다. 고급 설정을 확인합니다. 구성할 수 있는 설정은 다음과 같습니다.

    - 사용자 지정 거부 페이지 파일 선택
    - 쿠키를 사용하는 웹 사이트로 성공한 인증 캐시
    - 기본 자격 증명을 인증하는 방법 선택

12. AD FS 프록시 서버는 도메인에 연결될 가능성이 낮으므로 LDAP를 사용하여 사용자 가져오기 및 사전 인증을 위해 도메인 컨트롤러에 연결할 수 있습니다. 고급 양식 기반 웹 사이트 대화 상자에서 **기본 인증** 탭을 클릭 하 고 사전 인증 인증 유형에 대해 **LDAP 바인딩** 을 선택 합니다.
13. 작업을 완료하면 **확인**을 클릭하여 양식 기반 웹 사이트 추가 대화 상자로 돌아갑니다.
14. **확인** 을 클릭하여 대화 상자를 닫습니다.
15. URL 및 페이지 변수가 검색되거나 입력되면 양식 기반 패널에 웹 사이트 데이터가 표시됩니다.
16. **네이티브 모듈** 탭을 클릭 하 고 서버, AD FS 프록시가 실행 중인 웹 사이트 (예: "기본 웹 사이트") 또는 AD FS 프록시 응용 프로그램 (예: "adfs"의 "ls")을 선택 하 여 원하는 수준에서 IIS 플러그 인을 사용 하도록 설정 합니다.
17. 화면 위쪽에서 **IIS 인증 사용** 상자를 클릭합니다.

이제 IIS 인증이 사용되도록 설정되었습니다.

### <a name="configure-directory-integration"></a>디렉터리 통합 구성

IIS 인증을 활성화했지만 LDAP를 통해 AD(Active Directory)에 대한 사전 인증을 수행하려면 도메인 컨트롤러에 대한 LDAP 연결을 구성해야 합니다.

1. **디렉터리 통합** 아이콘을 클릭합니다.
2. 설정 탭에서 **특정 LDAP 구성 사용** 라디오 단추를 선택 합니다.

   ![특정 LDAP 설정에 대 한 LDAP 설정 구성](./media/howto-mfaserver-adfs-2/ldap1.png)

3. **편집**을 클릭합니다.
4. LDAP 구성 편집 대화 상자에서 AD 도메인 컨트롤러에 연결하는 데 필요한 정보를 필드에 입력합니다. 필드의 설명은 Azure Multi-Factor Authentication 서버 도움말 파일에도 포함되어 있습니다.
5. **테스트** 단추를 클릭 하 여 LDAP 연결을 테스트 합니다.

   ![MFA 서버에서 LDAP 구성 테스트](./media/howto-mfaserver-adfs-2/ldap2.png)

6. LDAP 연결 테스트가 성공한 경우 **확인**을 클릭합니다.

### <a name="configure-company-settings"></a>회사 설정 구성

1. 그런 다음 **회사 설정** 아이콘을 클릭 하 고 **사용자 이름 확인** 탭을 선택 합니다.
2. **사용자 이름 일치에 LDAP 고유 식별자 특성 사용** 라디오 단추를 선택 합니다.
3. 사용자가 "domain\username" 형식으로 사용자 이름을 입력 하는 경우 서버는 LDAP 쿼리를 만들 때 사용자 이름에서 도메인을 제거할 수 있어야 합니다. 이 작업은 레지스트리 설정을 통해 수행할 수 있습니다.
4. 64비트 서버에서 레지스트리 편집기를 열고 HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor로 이동합니다. 32 비트 서버에 있는 경우 경로에서 "Wow6432Node"를 가져옵니다. "UsernameCxz_stripPrefixDomain" 이라는 DWORD 레지스트리 키를 만들고 값을 1로 설정 합니다. 이제 Azure Multi-Factor Authentication을 통해 AD FS 프록시 보안이 유지됩니다.

Active Directory에서 서버로 사용자를 가져왔는지 확인합니다. 해당 위치에서 웹 사이트에 로그인 할 때 2 단계 인증이 필요 하지 않도록 내부 IP 주소를 허용 하려면 [신뢰할 수](#trusted-ips) 있는 ip 섹션을 참조 하세요.

![회사 설정을 구성 하는 레지스트리 편집기](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>프록시 없이 AD FS 2.0 직접

AD FS 프록시를 사용하지 않는 경우 AD FS의 보안을 유지할 수 있습니다. AD FS 서버에 Azure Multi-Factor Authentication 서버를 설치하고 다음 단계에 따라 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버 내에서 왼쪽 메뉴에 있는 **IIS 인증** 아이콘을 클릭 합니다.
2. **HTTP** 탭을 클릭합니다.
3. **추가**를 클릭합니다.
4. 기준 URL 추가 대화 상자에서 기본 URL 필드에 HTTP 인증이 수행 되는 AD FS 웹 사이트의 URL을 입력 합니다 (예: `https://sso.domain.com/adfs/ls/auth/integrated` ). 그런 다음 애플리케이션 이름을 입력합니다(선택 사항). 애플리케이션 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
5. 원하는 경우 유휴 시간 제한 및 최대 세션 시간을 조정합니다.
6. 모든 사용자를 서버로 가져왔거나 가져올 예정이고 2단계 확인을 적용하는 경우 **Azure Multi-Factor Authentication 사용자 일치 필요** 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 2단계 확인에서 제외할 예정이면 이 확인란을 선택 취소합니다.
7. 원하는 경우 쿠키 캐시 상자를 선택합니다.

   ![프록시 없이 AD FS 2.0 직접](./media/howto-mfaserver-adfs-2/noproxy.png)

8. **확인**을 클릭합니다.
9. **네이티브 모듈** 탭을 클릭 하 고 서버, 웹 사이트 (예: "기본 웹 사이트") 또는 AD FS 응용 프로그램 (예: "adfs"의 "ls")을 선택 하 여 원하는 수준에서 IIS 플러그 인을 사용 하도록 설정 합니다.
10. 화면 위쪽에서 **IIS 인증 사용** 상자를 클릭합니다.

이제 Azure Multi-Factor Authentication을 통해 AD FS 보안이 유지됩니다.

Active Directory에서 서버로 사용자를 가져왔는지 확인합니다. 해당 위치에서 웹 사이트에 로그인 할 때 2 단계 인증이 필요 하지 않도록 내부 IP 주소를 허용 하려면 신뢰할 수 있는 ip 섹션을 참조 하세요.

## <a name="trusted-ips"></a>신뢰할 수 있는 IP

신뢰할 수 있는 IP를 사용하면 특정 IP 주소 또는 서브넷에서 시작된 웹 사이트 요청에 대한 Azure Multi-Factor Authentication을 바이패스할 수 있습니다. 예를 들어 사용자가 사무실에서 로그인할 경우 2단계 인증에서 제외하려고 합니다. 이를 위해 사무실 서브넷을 신뢰할 수 있는 IP 항목으로 지정할 수 있습니다.

### <a name="to-configure-trusted-ips"></a>신뢰할 수 있는 IP를 구성하려면

1. IIS 인증 섹션에서 **신뢰할 수 있는 IP** 탭을 클릭합니다.
2. **추가** ...를 클릭 합니다. 클릭합니다.
3. 신뢰할 수 있는 IP 추가 대화 상자가 나타나면 **단일 IP**, **IP 범위** 또는 **서브넷** 라디오 단추를 선택합니다.
4. 허용 해야 하는 IP 주소, IP 주소 범위 또는 서브넷을 입력 합니다. 서브넷을 입력하는 경우 적합한 넷마스크를 선택하고 **확인** 단추를 클릭합니다.

![MFA 서버에 대 한 신뢰할 수 있는 Ip 구성](./media/howto-mfaserver-adfs-2/trusted.png)
