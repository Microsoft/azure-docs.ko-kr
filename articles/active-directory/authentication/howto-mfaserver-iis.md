---
title: IIS 인증 및 Azure MFA 서버 - Azure Active Directory
description: IIS 인증 및 Azure Multi-Factor Authentication 서버 배포
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
ms.openlocfilehash: 5c072298bf5cce4e22fe50d2474c7abe6b915f74
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919508"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>IIS 웹앱용 Azure Multi-Factor Authentication 서버 구성

Azure MFA(Multi-Factor Authentication) 서버의 IIS 인증 섹션을 사용하여 Microsoft IIS 웹 애플리케이션과의 통합을 위한 IIS 인증을 사용하도록 설정하고 구성할 수 있습니다. Azure MFA 서버는 Azure Multi-Factor Authentication을 추가하기 위해 IIS 웹 서버에 요청하는 사항을 필터링할 수 있는 플러그인을 설치합니다. IIS 플러그인은 양식 기반 인증 및 통합 Windows HTTP 인증을 지원합니다. 신뢰할 수 있는 IP는 2단계 인증에서 내부 IP 주소를 제외하도록 구성할 수도 있습니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.
>
> 클라우드 기반 Azure Multi-Factor Authentication을 사용하는 경우 Azure MFA(Multi-Factor Authentication) 서버에서 제공하는 IIS 플러그 인에 대한 대안이 없습니다. 대신 AD FS(Active Directory Federation Services)가 포함된 WAP(웹 애플리케이션 프록시) 또는 Azure Active Directory의 애플리케이션 프록시를 사용하세요.

![MFA 서버의 IIS 인증](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버에서 양식 기반 IIS 인증 사용

양식 기반 인증을 사용하는 IIS 웹 애플리케이션을 보호하려면 IIS 웹 서버에 Azure Multi-Factor Authentication 서버를 설치하고 다음 절차에 따라 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버에서 왼쪽 메뉴에 있는 IIS 인증 아이콘을 클릭합니다.
2. **양식 기반** 탭을 클릭합니다.
3. **추가**를 클릭합니다.
4. 사용자 이름, 암호 및 도메인 변수를 자동으로 검색하려면 양식 기반 웹 사이트 자동 구성 대화 상자 내에서 로그인 URL(예: `https://localhost/contoso/auth/login.aspx`)을 입력하고 **확인**을 클릭합니다.
5. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 Multi-Factor Authentication을 사용하려는 경우 **Require Azure Multi-Factor Authentication user match**(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다.
6. 페이지 변수를 자동으로 검색할 수 없는 경우 양식 기반 웹 사이트 자동 구성 대화 상자에서 **수동으로 지정**을 클릭합니다.
7. Add Form-Based Website(양식 기반 웹 사이트 추가) 대화 상자에서 Submit URL(URL 제출) 필드에 로그인 페이지의 URL을 입력하고 애플리케이션 이름을 입력합니다(선택 사항). 애플리케이션 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
8. 올바른 요청 양식을 선택합니다. 대부분의 웹 애플리케이션에 대해 **POST 또는 GET**으로 설정됩니다.
9. 사용자 이름 변수, 암호 변수 및 도메인 변수를 입력합니다(로그인 페이지에 나타나는 경우). 입력 상자의 이름을 찾으려면 웹 브라우저에서 로그인 페이지로 이동하고 해당 페이지를 마우스 오른쪽 단추로 클릭하여 **원본 보기**를 선택합니다.
10. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 다단계 인증을 사용하려는 경우 **Require Azure Multi-Factor Authentication user match**(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다.
11. **고급**을 클릭하여 다음을 포함하는 고급 설정을 검토합니다.

    - 사용자 지정 거부 페이지 파일 선택
    - 쿠키를 사용하여 일정 기간 동안 웹 사이트로 성공한 인증 캐시
    - Windows 도메인, LDAP 디렉터리에 대해 기본 자격 증명을 인증할지 여부를 선택합니다. 또는 RADIUS 서버입니다.

12. **확인**을 클릭하여 양식 기반 웹 사이트 추가 대화 상자로 돌아갑니다.
13. **확인**을 클릭합니다.
14. URL 및 페이지 변수가 검색되거나 입력되면 양식 기반 패널에 웹 사이트 데이터가 표시됩니다.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버에서 Windows 통합 인증 사용

통합 Windows HTTP 인증을 사용하는 IIS 웹 애플리케이션을 보호하려면 IIS 웹 서버에 Azure MFA 서버를 설치한 후 다음 단계로 서버를 구성합니다.

1. Azure Multi-Factor Authentication 서버에서 왼쪽 메뉴에 있는 IIS 인증 아이콘을 클릭합니다.
2. **HTTP** 탭을 클릭합니다.
3. **추가**를 클릭합니다.
4. 기준 URL 추가 대화 상자에서 HTTP 인증이 수행되는 웹 사이트의 URL(예: `http://localhost/owa`)을 입력하고 애플리케이션 이름을 제공합니다(선택 사항). 애플리케이션 이름이 Azure Multi-Factor Authentication 보고서에 나타나며 SMS 또는 모바일 앱 인증 메시지 내에 표시될 수 있습니다.
5. 기본값이 충분하지 않으면 유휴 제한 시간 및 최대 세션 시간을 조정합니다.
6. 모든 사용자를 내부 서버로 가져왔거나 가져올 예정이고 Multi-Factor Authentication을 사용하려는 경우 **Require Azure Multi-Factor Authentication user match**(Azure Multi-Factor Authentication 사용자 일치 필요) 확인란을 선택합니다. 많은 수의 사용자를 서버에 아직 가져오지 않았거나 다단계 인증에서 제외할 예정이면 이 확인란을 선택 취소합니다.
7. 원하는 경우 **쿠키 캐시** 상자를 선택합니다.
8. **확인**을 클릭합니다.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버에 대해 IIS 플러그인 사용

양식 기반 또는 HTTP 인증 URL 및 설정을 구성한 후 Azure Multi-Factor Authentication IIS 플러그인을 로드하고 IIS에서 사용하도록 설정할 위치를 선택합니다. 이렇게 하려면 다음 절차를 수행합니다.

1. IIS 6에서 실행하는 경우 **ISAPI** 탭을 클릭합니다. 웹 애플리케이션이 실행되고 있는 웹 사이트(예: 기본 웹 사이트)를 선택하여 해당 사이트에 대해 Azure Multi-Factor Authentication ISAPI 필터 플러그인을 사용하도록 설정합니다.
2. IIS 7 이상에서 실행하는 경우 **네이티브 모듈** 탭을 클릭합니다. 서버, 웹 사이트 또는 애플리케이션을 선택하여 원하는 수준에서 IIS 플러그인을 사용하도록 설정합니다.
3. 화면 위쪽에서 **IIS 인증 사용** 상자를 클릭합니다. 이제 Azure Multi-Factor Authentication이 선택된 IIS 애플리케이션을 보호합니다. 사용자를 서버에 가져왔는지 확인합니다.

## <a name="trusted-ips"></a>신뢰할 수 있는 IP

신뢰할 수 있는 IP를 사용하면 특정 IP 주소 또는 서브넷에서 시작된 웹 사이트 요청에 대한 Azure Multi-Factor Authentication을 바이패스할 수 있습니다. 예를 들어 사무실에서 로그인한 동안에는 사용자를 Azure Multi-Factor Authentication에서 제외시킬 수 있습니다. 이를 위해 사무실 서브넷을 신뢰할 수 있는 IP 항목으로 지정할 수 있습니다. 신뢰할 수 있는 IP를 구성하려면 다음 절차를 사용합니다.

1. IIS 인증 섹션에서 **신뢰할 수 있는 IP** 탭을 클릭합니다.
2. **추가**를 클릭합니다.
3. 신뢰할 수 있는 IP 추가 대화 상자가 나타나면 **단일 IP**, **IP 범위** 또는 **서브넷** 라디오 단추를 선택합니다.
4. 허용되어야 할 IP 주소, IP 주소 범위 또는 서브넷을 입력합니다. 서브넷을 입력하는 경우 해당 네트워크 마스크를 선택하고 **확인**을 클릭합니다.
