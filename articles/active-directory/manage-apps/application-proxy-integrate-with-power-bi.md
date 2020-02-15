---
title: Azure AD 응용 프로그램 프록시를 사용 하 여 Power BI에 대 한 원격 액세스 사용
description: 온-프레미스 Power BI를 Azure AD 응용 프로그램 프록시와 통합 하는 방법에 대 한 기본 사항을 다룹니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111580"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용 하 여 Power BI Mobile에 대 한 원격 액세스 사용

이 문서에서는 Azure AD 응용 프로그램 프록시를 사용 하 여 Power BI mobile 앱이 Power BI Report Server (PBIRS) 및 SQL Server Reporting Services (SSRS) 2016 이상에 연결 하도록 설정 하는 방법을 설명 합니다. 이러한 통합을 통해 회사 네트워크를 벗어난 사용자는 Power BI 모바일 앱에서 해당 Power BI 보고서에 액세스 하 고 Azure AD 인증으로 보호할 수 있습니다. 이 보호에는 조건부 액세스 및 multi-factor authentication과 같은 [보안 이점이](application-proxy-security.md#security-benefits) 포함 됩니다.  

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 이미 보고서 서비스를 배포 하 고 [응용 프로그램 프록시를 사용 하도록 설정](application-proxy-add-on-premises-application.md)했다고 가정 합니다.

- 응용 프로그램 프록시를 사용 하도록 설정 하려면 Windows 서버에 커넥터를 설치 하 고 커넥터에서 Azure AD 서비스와 통신할 수 있도록 [필수 구성 요소](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 를 완료 해야 합니다.  
- Power BI를 게시 하는 경우 동일한 내부 및 외부 도메인을 사용 하는 것이 좋습니다. 사용자 지정 도메인에 대해 자세히 알아보려면 [응용 프로그램 프록시에서 사용자 지정 도메인 작업](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)을 참조 하세요.
- 이 통합은 **Power BI Mobile iOS 및 Android** 응용 프로그램에 사용할 수 있습니다.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1 단계: KCD (Kerberos 제한 위임) 구성

Windows 인증을 사용하는 온-프레미스 애플리케이션에 대해 Kerberos 인증 프로토콜 및 KCD(Kerberos 제한 위임)라는 기능을 사용하여 SSO(Single Sign-On)를 획득할 수 있습니다. 구성 된 경우 KCD를 사용 하면 사용자가 Windows에 직접 로그인 하지 않았더라도 응용 프로그램 프록시 커넥터에서 사용자에 대 한 Windows 토큰을 얻을 수 있습니다. KCD에 대 한 자세한 내용은 응용 프로그램 프록시를 사용 하 여 앱에 대 한 [Kerberos 제한 위임 개요](https://technet.microsoft.com/library/jj553400.aspx) 및 [Single Sign-On에 대 한 kerberos 제한 위임](application-proxy-configure-single-sign-on-with-kcd.md)을 참조 하세요.

Reporting Services 쪽에서 구성 하는 것은 많지 않습니다. 적절 한 Kerberos 인증을 수행할 수 있도록 유효한 SPN (서비스 사용자 이름)이 있어야 합니다. 또한 Reporting Services 서버에서 Negotiate 인증을 사용할 수 있는지 확인 합니다.

Reporting services 용 KCD를 설정 하려면 다음 단계를 계속 진행 합니다.

### <a name="configure-the-service-principal-name-spn"></a>SPN (서비스 사용자 이름) 구성

SPN은 Kerberos 인증을 사용 하는 서비스에 대 한 고유 식별자입니다. 보고서 서버에 대 한 적절 한 HTTP SPN이 있는지 확인 해야 합니다. 보고서 서버에 대 한 적절 한 SPN (서비스 사용자 이름)을 구성 하는 방법에 대 한 자세한 내용은 [보고서 서버에 대 한 spn (서비스 사용자 이름) 등록](https://msdn.microsoft.com/library/cc281382.aspx)을 참조 하세요.
-L 옵션과 함께 Setspn 명령을 실행하여 SPN이 추가되었는지 확인할 수 있습니다. 이 명령에 대해 자세히 알아보려면 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)을 참조하세요.

### <a name="enable-negotiate-authentication"></a>협상 인증 사용

보고서 서버에서 Kerberos 인증을 사용 하도록 설정 하려면 보고서 서버의 인증 유형을 RSWindowsNegotiate으로 구성 합니다. Rsreportserver.config 파일을 사용 하 여이 설정을 구성 합니다.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

자세한 내용은 [Reporting Services 구성 파일 수정](https://msdn.microsoft.com/library/bb630448.aspx) 및 [보고서 서버에서 Windows 인증 구성](https://msdn.microsoft.com/library/cc281253.aspx)을 참조 하세요.

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Reporting Services 응용 프로그램 풀 계정에 추가 된 SPN에 대 한 위임용으로 커넥터를 신뢰할 수 있는지 확인 합니다.
Azure AD 응용 프로그램 프록시 서비스에서 사용자 id를 Reporting Services 응용 프로그램 풀 계정에 위임할 수 있도록 KCD를 구성 합니다. 애플리케이션 프록시 커넥터에서 Azure AD에서 인증된 사용자에 대한 Kerberos 티켓을 검색할 수 있도록 하여 KCD를 구성합니다. 그런 다음이 서버는 컨텍스트를 대상 응용 프로그램에 전달 하거나이 경우 Reporting Services 합니다.

KCD를 구성 하려면 각 커넥터 컴퓨터에 대해 다음 단계를 반복 합니다.

1. 도메인 관리자로 도메인 컨트롤러에 로그인 하 고 **Active Directory 사용자 및 컴퓨터**를 엽니다.
2. 커넥터가 실행 중인 컴퓨터를 찾습니다.  
3. 컴퓨터를 두 번 클릭 하 고 **위임** 탭을 선택 합니다.
4. 위임 설정을 **지정 된 서비스에 대 한 위임용 으로만이 컴퓨터 트러스트**로 설정 합니다. 그런 다음 **모든 인증 프로토콜 사용**을 선택합니다.
5. **추가**를 선택 하 고 **사용자 또는 컴퓨터**를 선택 합니다.
6. Reporting Services 하는 데 사용 하 고 있는 서비스 계정을 입력 합니다. Reporting Services 구성 내에서 SPN을 추가한 계정입니다.
7. **확인**을 클릭합니다. 변경 내용을 저장 하려면 **확인** 을 다시 클릭 합니다.

자세한 내용은 [응용 프로그램 프록시를 사용 하 여 앱에 Single Sign-On에 대 한 Kerberos 제한 위임](application-proxy-configure-single-sign-on-with-kcd.md)을 참조 하세요.

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>2 단계: Azure AD 응용 프로그램 프록시를 통해 보고서 서비스 게시

이제 Azure AD 응용 프로그램 프록시를 구성할 준비가 되었습니다.

1. 다음 설정을 사용 하 여 응용 프로그램 프록시를 통해 보고서 서비스를 게시 합니다. 응용 프로그램 프록시를 통해 응용 프로그램을 게시 하는 방법에 대 한 단계별 지침은 [Azure AD 응용 프로그램 프록시를 사용 하 여 응용 프로그램 게시](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조 하세요.
   - **내부 url**: 커넥터가 회사 네트워크에서 연결할 수 있는 보고서 서버의 URL을 입력 합니다. 커넥터가 설치 된 서버에서이 URL에 연결할 수 있는지 확인 합니다. 응용 프로그램 프록시를 통해 게시 된 하위 경로 문제를 방지 하기 위해 `https://servername/`와 같은 최상위 도메인을 사용 하는 것이 가장 좋습니다. 예를 들어 `https://servername/` `https://servername/reports/` 또는 `https://servername/reportserver/`사용 하지 않습니다.
     > [!NOTE]
     > 보고서 서버에 대 한 보안 HTTPS 연결을 사용 하는 것이 좋습니다. 방법에 대 한 자세한 내용은 [기본 모드 보고서 서버에서 SSL 연결 구성](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) 을 참조 하세요.
   - **외부 url**: Power BI 모바일 앱이 연결할 공용 url을 입력 합니다. 예를 들어 사용자 지정 도메인을 사용 하는 경우 `https://reports.contoso.com` 것 처럼 보일 수 있습니다. 사용자 지정 도메인을 사용 하려면 해당 도메인에 대 한 인증서를 업로드 하 고, 응용 프로그램의 기본 msappproxy.net 도메인에 대 한 DNS 레코드를 가리킵니다. 자세한 단계는 [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)을 참조 하세요.

   - **사전 인증 방법**: Azure Active Directory

2. 앱이 게시된 후에는 다음 단계에 따라 Single Sign-On 설정을 구성합니다.

   a. 포털의 애플리케이션 페이지에서 **Single Sign-On**을 선택합니다.

   b. **Single Sign-on 모드**의 경우 **Windows 통합 인증**을 선택 합니다.

   다. **내부 애플리케이션 SPN**을 이전에 설정한 값으로 설정합니다.  

   d. 커넥터에 대한 **위임된 로그인 ID**를 선택하여 사용자를 대신하여 사용합니다. 자세한 내용은 [다른 온-프레미스 및 클라우드 id로 작업](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)을 참조 하세요.

   e. **저장**을 클릭하여 변경 내용을 저장합니다.

응용 프로그램 설정을 완료 하려면 [ **사용자 및 그룹** ] 섹션으로 이동 하 여이 응용 프로그램에 액세스할 사용자를 할당 합니다.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>3 단계: 응용 프로그램에 대 한 회신 URI 수정

Power BI 모바일 앱에서 연결 하 여 보고서 서비스에 액세스 하려면 2 단계에서 자동으로 만들어진 응용 프로그램 등록을 구성 해야 합니다. 

1. Azure Active Directory **개요** 페이지에서 **앱 등록**를 선택 합니다.
2. **모든 응용 프로그램** 탭 아래에서 2 단계에서 만든 응용 프로그램을 검색 합니다.
3. 응용 프로그램을 선택한 다음 **인증**을 선택 합니다.
4. 사용 중인 플랫폼에 따라 다음 리디렉션 Uri를 추가 합니다.

   **IOS**Power BI Mobile에 대 한 앱을 구성 하는 경우 공용 클라이언트 형식의 다음 리디렉션 Uri (모바일 & 데스크톱)를 추가 합니다.
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   **Android**Power BI Mobile에 대 한 앱을 구성 하는 경우 공용 클라이언트 형식 (모바일 & 데스크톱)의 다음 리디렉션 uri를 추가 합니다.
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 응용 프로그램이 올바르게 작동 하려면 리디렉션 Uri를 추가 해야 합니다. IOS 및 Android Power BI Mobile 모두에 대해 앱을 구성 하는 경우, iOS: `urn:ietf:wg:oauth:2.0:oob`에 대해 구성 된 리디렉션 Uri 목록에 공용 클라이언트 (모바일 & 데스크톱) 형식의 다음 리디렉션 URI를 추가 합니다.

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>4 단계: Power BI Mobile 앱에서 연결

1. Power BI 모바일 앱에서 Reporting Services 인스턴스에 연결 합니다. 이렇게 하려면 응용 프로그램 프록시를 통해 게시 한 응용 프로그램에 대 한 **외부 URL** 을 입력 합니다.

   ![외부 URL을 사용 하 여 모바일 앱 Power BI](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. **연결**을 선택합니다. Azure Active Directory 로그인 페이지로 이동 합니다.

3. 사용자에 대 한 올바른 자격 증명을 입력 하 고 **로그인**을 선택 합니다. Reporting Services 서버의 요소가 표시 됩니다.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>5 단계: 관리 되는 장치에 대 한 Intune 정책 구성 (선택 사항)

Microsoft Intune를 사용 하 여 회사의 직원이 사용 하는 클라이언트 앱을 관리할 수 있습니다. Intune을 사용 하면 데이터 암호화 및 추가 액세스 요구 사항과 같은 기능을 사용할 수 있습니다. Intune을 통한 앱 관리에 대해 자세히 알아보려면 Intune 앱 관리를 참조 하세요. Power BI 모바일 응용 프로그램이 Intune 정책에서 작동 하도록 설정 하려면 다음 단계를 사용 합니다.

1. **Azure Active Directory** 한 다음 **앱 등록**으로 이동 합니다.
2. Native client 응용 프로그램을 등록할 때 3 단계에서 구성 된 응용 프로그램을 선택 합니다.
3. 응용 프로그램 페이지에서 **API 권한**을 선택 합니다.
4. **권한 추가를**클릭 합니다. 
5. **내 조직에서 사용 하는 api**에서 "Microsoft 모바일 응용 프로그램 관리"를 검색 하 고 선택 합니다.
6. 응용 프로그램에 **DeviceManagementManagedApps** 사용 권한 추가
7. **관리자 동의 부여** 를 클릭 하 여 응용 프로그램에 대 한 사용 권한을 부여 합니다.
8. [앱 보호 정책을 만들고 할당 하는 방법을](https://docs.microsoft.com/intune/app-protection-policies)참조 하 여 원하는 Intune 정책을 구성 합니다.

## <a name="troubleshooting"></a>문제 해결

몇 분 넘게 보고서를 로드 한 후 응용 프로그램에서 오류 페이지를 반환 하는 경우 제한 시간 설정을 변경 해야 할 수 있습니다. 기본적으로 응용 프로그램 프록시는 요청에 응답 하는 데 최대 85 초를 소요 하는 응용 프로그램을 지원 합니다. 이 설정을 180 초로 늘리려면 응용 프로그램의 앱 프록시 설정 페이지에서 백 엔드 시간 제한을 **길게** 선택 합니다. 빠르고 신뢰할 수 있는 보고서를 만드는 방법에 대 한 팁은 [Power BI 보고서 모범 사례](https://docs.microsoft.com/power-bi/power-bi-reports-performance)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [네이티브 클라이언트 응용 프로그램을 사용 하 여 프록시 응용 프로그램과 상호 작용](application-proxy-configure-native-client-application.md)
- [Power BI 모바일 앱에서 온-프레미스 보고서 서버 보고서 및 Kpi 보기](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
