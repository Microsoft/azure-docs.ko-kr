---
title: 하이브리드 Azure Active Directory 조인 디바이스 문제 해결
description: Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: bc926c385aeee40601c00b3b4ab68065a4260f2f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268777"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>하이브리드 Azure Active Directory 조인 디바이스 문제 해결

이 문서의 내용은 Windows 10 또는 Windows Server 2016를 실행 하는 장치에 적용 됩니다.

다른 Windows 클라이언트의 경우 [하위 수준 장치에 조인 된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)문서를 참조 하세요.

이 문서에서는 다음 시나리오를 지원하도록 [디바이스에 조인된 하이브리드 Azure Active Directory를 구성](hybrid-azuread-join-plan.md)했다고 가정합니다.

- 디바이스 기반 조건부 액세스
- [엔터프라이즈 설정 로밍](./enterprise-state-roaming-overview.md)
- [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

이 문서에서는 잠재적인 문제를 해결 하기 위한 문제 해결 지침을 제공 합니다.

Windows 10 및 Windows Server 2016의 경우 하이브리드 Azure Active Directory 조인은 Windows 10 2015년 11월 업데이트 이상을 지원합니다.

## <a name="troubleshoot-join-failures"></a>조인 오류 문제 해결

### <a name="step-1-retrieve-the-join-status"></a>1단계: 조인 상태 검색

**조인 상태를 검색하려면:**

1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. `dsregcmd /status` 입력

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>2단계: 조인 상태 평가

다음 필드를 검토하고 예상 값을 갖는지 확인합니다.

#### <a name="domainjoined--yes"></a>DomainJoined : YES

이 필드는 디바이스가 온-프레미스 Active Directory에 조인되는지 여부를 나타냅니다. 값이 **아니요**인 경우 디바이스는 하이브리드 Azure AD 조인을 수행할 수 없습니다.

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO

이 필드는 디바이스가 Azure AD에 개인 디바이스로 등록되어 있는지 여부를 나타냅니다(*작업 영역 조인*으로 표시). 이 값은 하이브리드 Azure AD 조인된 도메인에 가입된 컴퓨터에 대해 **아니요**이어야 합니다. 값이 **예**인 경우 하이브리드 Azure AD 조인을 완료하기 전에 회사 또는 학교 계정이 추가되었습니다. 이 경우 Windows 10 버전의 1주년 업데이트(1607)를 사용하는 경우 계정은 무시됩니다.

#### <a name="azureadjoined--yes"></a>AzureAdJoined : YES

이 필드는 장치가 조인 되었는지 여부를 나타냅니다. 장치가 Azure AD 조인 장치 또는 하이브리드 Azure AD 가입 장치인 경우에는 값이 **예** 입니다.
값이 **아니요**인 경우 Azure AD에 대한 조인은 아직 완료되지 않았습니다.

추가 문제 해결을 위해 다음 단계를 진행 합니다.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>3 단계: 조인이 실패 한 단계 및 errorcode 확인

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 이상

조인 상태 출력의 ' 진단 데이터 ' 섹션에서 ' 이전 등록 ' 하위 섹션을 찾습니다. 이 섹션은 장치가 도메인에 가입 되어 있으며 Azure AD 조인을 하이브리드 할 수 없는 경우에만 표시 됩니다.
' 오류 단계 ' 필드는 "클라이언트 ErrorCode"가 조인 작업의 오류 코드를 표시 하는 동안 조인 오류의 단계를 나타냅니다.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>이전 Windows 10 버전

이벤트 뷰어 로그를 사용 하 여 조인 실패에 대 한 단계 및 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 장치 등록** 이벤트 로그를 엽니다. **응용 프로그램 및 서비스 로그**  >  **Microsoft**  >  **Windows**  >  **사용자 장치 등록** 아래에 있음
2. 다음 Eventid 304, 305, 307를 사용 하 여 이벤트를 찾습니다.

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/1.png)

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>4 단계: 아래 목록에서 가능한 원인 및 해결 방법 확인

#### <a name="pre-check-phase"></a>사전 검사 단계

오류의 가능한 원인:

- 장치에서 도메인 컨트롤러에 대 한 시야를 찾을 수 없습니다.
   - 장치는 조직의 내부 네트워크 또는 온-프레미스 AD (Active Directory) 도메인 컨트롤러에 대 한 네트워크 회선이 있는 VPN에 있어야 합니다.

#### <a name="discover-phase"></a>검색 단계

오류의 가능한 원인:

- SCP (서비스 연결 지점) 개체가 잘못 구성 되었거나 DC에서 SCP 개체를 읽을 수 없습니다.
   - 유효한 SCP 개체는 Azure AD에서 확인 된 도메인 이름을 가리키는 AD 포리스트에 있어야 합니다.
   - 자세한 내용은 [서비스 연결 지점 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)섹션에서 찾을 수 있습니다.
- 검색 끝점에서 연결 및 검색 메타 데이터를 가져오지 못했습니다.
   - 장치는 `https://enterpriseregistration.windows.net` 등록 및 권한 부여 끝점을 검색 하기 위해 시스템 컨텍스트에서 액세스할 수 있어야 합니다.
   - 온-프레미스 환경에 아웃 바운드 프록시가 필요한 경우 IT 관리자는 장치의 컴퓨터 계정이 아웃 바운드 프록시를 검색 하 고 자동으로 인증할 수 있는지 확인 해야 합니다.
- 사용자 영역 끝점에 연결 하는 데 실패 하 고 영역 검색을 수행 합니다. (Windows 10 버전 1809 이상에만 해당)
   - 장치는 `https://login.microsoftonline.com` 확인 된 도메인에 대해 영역 검색을 수행 하 고 도메인 유형 (관리/페더레이션)을 결정할 수 있도록 시스템 컨텍스트에서 액세스할 수 있어야 합니다.
   - 온-프레미스 환경에 아웃 바운드 프록시가 필요한 경우 IT 관리자는 장치의 시스템 컨텍스트가 아웃 바운드 프록시를 검색 하 고 자동으로 인증할 수 있는지 확인 해야 합니다.

**일반적인 오류 코드:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - 원인: SCP 개체를 읽고 Azure AD 테 넌 트 정보를 가져올 수 없습니다.
   - 해결 방법: [서비스 연결 지점 구성](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)섹션을 참조 하세요.
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - 이유: 일반 검색에 실패 했습니다. DRS에서 검색 메타 데이터를 가져오지 못했습니다.
   - 해결 방법: 아래에서 하위 오류를 찾아 자세히 조사 합니다.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - 이유: 검색을 수행 하는 동안 작업 시간이 초과 되었습니다.
   - 해결 방법: `https://enterpriseregistration.windows.net` 시스템 컨텍스트에서에 액세스할 수 있는지 확인 합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)섹션을 참조 하세요.
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - 원인: 일반 영역 검색에 실패 했습니다. STS에서 도메인 유형 (관리/페더레이션)을 확인 하지 못했습니다.
   - 해결 방법: 아래에서 하위 오류를 찾아 자세히 조사 합니다.

**일반적인 하위 오류 코드:**

검색 오류 코드에 대 한 하위 오류 코드를 찾으려면 다음 방법 중 하나를 사용 합니다.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 이상

조인 상태 출력의 ' 진단 데이터 ' 섹션에서 ' DRS 검색 테스트 '를 찾습니다. 이 섹션은 장치가 도메인에 가입 되어 있으며 Azure AD 조인을 하이브리드 할 수 없는 경우에만 표시 됩니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>이전 Windows 10 버전

이벤트 뷰어 로그를 사용 하 여 단계를 찾고 조인 오류에 대 한 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 장치 등록** 이벤트 로그를 엽니다. **응용 프로그램 및 서비스 로그**  >  **Microsoft**  >  **Windows**  >  **사용자 장치 등록** 아래에 있음
2. 다음 Eventid 201를 사용 하 여 이벤트를 찾습니다.

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>네트워크 오류

- **WININET_E_CANNOT_CONNECT** (0x80072efd/2147012867)
   - 원인: 서버와의 연결을 설정할 수 없습니다.
   - 해결 방법: 필요한 Microsoft 리소스에 대 한 네트워크 연결을 확인 합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조 하세요.
- **WININET_E_TIMEOUT** (0x80072ee2/2147012894)
   - 이유: 일반 네트워크 시간 제한입니다.
   - 해결 방법: 필요한 Microsoft 리소스에 대 한 네트워크 연결을 확인 합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조 하세요.
- **WININET_E_DECODING_FAILED** (0x80072f8f가/2147012721)
   - 원인: 네트워크 스택이 서버에서 응답을 디코딩할 수 없습니다.
   - 해결 방법: 네트워크 프록시가 서버 응답을 방해 하 고 수정 하지 않는지 확인 합니다.

###### <a name="http-errors"></a>HTTP 오류

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - 원인: SCP 개체가 잘못 된 테 넌 트 ID로 구성 되었습니다. 또는 테 넌 트에서 활성 구독을 찾을 수 없습니다.
   - 해결 방법: SCP 개체가 올바른 Azure AD 테 넌 트 ID 및 활성 구독으로 구성 되어 있는지 또는 테 넌 트에 있는지 확인 합니다.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - 이유: DRS 서버의 HTTP 503.
   - 해결 방법: 서버를 현재 사용할 수 없습니다. 이후 조인 시도는 서버가 다시 온라인 상태가 되 면 성공할 수 있습니다.

###### <a name="other-errors"></a>기타 오류

- **E_INVALIDDATA** (0x8007000d/2147024883)
   - 원인: 서버 응답 JSON을 구문 분석할 수 없습니다. HTTP 200을 HTML 인증 페이지와 함께 반환 하는 프록시로 인해 발생 했을 수 있습니다.
   - 해결 방법: 온-프레미스 환경에 아웃 바운드 프록시가 필요한 경우 IT 관리자는 장치의 시스템 컨텍스트가 아웃 바운드 프록시를 검색 하 고 자동으로 인증할 수 있는지 확인 해야 합니다.

#### <a name="authentication-phase"></a>인증 단계

페더레이션된 도메인 계정에만 적용 됩니다.

실패 이유:

- DRS 리소스에 대 한 액세스 토큰을 자동으로 가져올 수 없습니다.
   - Windows 10 장치는 활성 WS-TRUST 끝점에 Windows 통합 인증을 사용 하 여 페더레이션 서비스에서 인증 토큰을 가져옵니다. 세부 정보: [페더레이션 서비스 구성](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**일반적인 오류 코드:**

이벤트 뷰어 로그를 사용 하 여 오류 코드, 하위 오류 코드, 서버 오류 코드 및 서버 오류 메시지를 찾을 수 있습니다.

1. 이벤트 뷰어에서 **사용자 장치 등록** 이벤트 로그를 엽니다. **응용 프로그램 및 서비스 로그**  >  **Microsoft**  >  **Windows**  >  **사용자 장치 등록** 아래에 있음
2. 다음 eventID 305를 사용 하 여 이벤트를 찾습니다.

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>구성 오류

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - 원인: 인증 프로토콜이 WS-TRUST가 아닙니다.
   - 해결 방법: 온-프레미스 id 공급자는 WS-TRUST를 지원 해야 합니다.
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - 원인: 온-프레미스 페더레이션 서비스에서 XML 응답을 반환 하지 않았습니다.
   - 해결 방법: MEX 끝점이 유효한 XML을 반환 하는지 확인 합니다. 프록시가 방해 하지 않고 비 xml 응답을 반환 하는지 확인 합니다.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - 원인: 사용자 이름/암호 인증에 대 한 끝점을 검색할 수 없습니다.
   - 해결 방법: 온-프레미스 id 공급자 설정을 확인 합니다. WS-TRUST 끝점을 사용 하도록 설정 하 고 MEX 응답에 이러한 올바른 끝점이 포함 되어 있는지 확인 합니다.

##### <a name="network-errors"></a>네트워크 오류

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - 이유: 일반 네트워크 시간 제한입니다.
   - 해결 방법: `https://login.microsoftonline.com` 시스템 컨텍스트에서에 액세스할 수 있는지 확인 합니다. 시스템 컨텍스트에서 온-프레미스 id 공급자에 액세스할 수 있는지 확인 합니다. 자세한 내용은 [네트워크 연결 요구 사항](hybrid-azuread-join-managed-domains.md#prerequisites)을 참조 하세요.
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - 이유: 인증 끝점에 대 한 연결이 중단 되었습니다.
   - 해결 방법: 잠시 후 다시 시도 하거나 안정적인 다른 네트워크 위치에서 조인 해 보세요.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/894947441)
   - 이유: 이전에 SSL (SSL(Secure Sockets Layer))으로 알려진 TLS (전송 계층 보안)를 서버에서 보낸 인증서의 유효성을 검사할 수 없습니다.
   - 해결 방법: 클라이언트 시간 오차를 확인 합니다. 잠시 후 다시 시도 하거나 안정적인 다른 네트워크 위치에서 조인 해 보세요.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/894947587)
   - 이유:에 연결 `https://login.microsoftonline.com` 하지 못했습니다.
   - 해결 방법:에 대 한 네트워크 연결을 확인 `https://login.microsoftonline.com` 합니다.

##### <a name="other-errors"></a>기타 오류

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - 원인: Azure AD에서 온-프레미스 id 공급자의 SAML 토큰을 수락 하지 않았습니다.
   - 해결 방법: 페더레이션 서버 설정을 확인 합니다. 인증 로그에서 서버 오류 코드를 찾습니다.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - 원인: 서버 WS-TRUST 응답이 오류 예외를 보고 했으며 어설션을 가져오지 못했습니다.
   - 해결 방법: 페더레이션 서버 설정을 확인 합니다. 인증 로그에서 서버 오류 코드를 찾습니다.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - 원인: 토큰 끝점에서 액세스 토큰을 가져오려고 할 때 오류가 발생 했습니다.
   - 해결 방법: ADAL 로그에서 기본 오류를 찾습니다.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - 이유: 일반 ADAL 오류
   - 해결 방법: 인증 로그에서 하위 오류 코드 또는 서버 오류 코드를 찾습니다.

#### <a name="join-phase"></a>조인 단계

실패 이유:

등록 유형을 찾고 아래 목록에서 오류 코드를 찾습니다.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 이상

조인 상태 출력의 ' 진단 데이터 ' 섹션에서 ' 이전 등록 ' 하위 섹션을 찾습니다. 이 섹션은 장치가 도메인에 가입 되어 있으며 Azure AD 조인을 하이브리드 할 수 없는 경우에만 표시 됩니다.
' 등록 유형 ' 필드는 수행 되는 조인 유형을 나타냅니다.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>이전 Windows 10 버전

이벤트 뷰어 로그를 사용 하 여 단계를 찾고 조인 오류에 대 한 오류 코드를 찾습니다.

1. 이벤트 뷰어에서 **사용자 장치 등록** 이벤트 로그를 엽니다. **응용 프로그램 및 서비스 로그**  >  **Microsoft**  >  **Windows**  >  **사용자 장치 등록** 아래에 있음
2. 다음 Eventid 204를 사용 하 여 이벤트를 찾습니다.

![실패 로그 이벤트](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>DRS 서버에서 반환 된 HTTP 오류

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - 원인: DRS에서 오류 응답을 받았습니다. 오류 코드: "DirectoryError"
   - 해결 방법: 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조 하십시오.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - 이유: DRS에서 오류 응답을 받았습니다: "AuthenticationError" 및 ErrorSubCode 코드는 "DeviceNotFound"이 아닙니다.
   - 해결 방법: 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조 하십시오.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - 원인: DRS에서 오류 응답을 받았습니다. 오류 코드: "DirectoryError"
   - 해결 방법: 가능한 원인 및 해결 방법에 대해서는 서버 오류 코드를 참조 하십시오.

##### <a name="tpm-errors"></a>TPM 오류

- **NTE_BAD_KEYSET** (0x80090016/2146893802)
   - 원인: TPM 작업이 실패 했거나 잘못 되었습니다.
   - 해결 방법: 잘못 된 sysprep 이미지가 원인일 수 있습니다. Sysprep 이미지가 생성 된 컴퓨터가 Azure AD 조인, 하이브리드 Azure AD 조인 또는 Azure AD가 등록 되지 않았는지 확인 합니다.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - 이유: 일반 TPM 오류입니다.
   - 해결 방법:이 오류가 발생 한 장치에서 TPM을 사용 하지 않도록 설정 합니다. Windows 10 버전 1809 이상에서는 tpm 오류를 자동으로 감지 하 고 TPM을 사용 하지 않고 하이브리드 Azure AD 조인을 완료 합니다.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - 원인: FIPS 모드의 TPM이 현재 지원 되지 않습니다.
   - 해결 방법:이 오류가 발생 한 장치에서 TPM을 사용 하지 않도록 설정 합니다. Windows 1809는 tpm 오류를 자동으로 감지 하 고 TPM을 사용 하지 않고 하이브리드 Azure AD 조인을 완료 합니다.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/2146893775)
   - 이유: TPM이 잠겼습니다.
   - 해결 방법: 일시적인 오류입니다. 쿨 기간 동안 기다립니다. 잠시 후 조인 시도에 성공 합니다. 자세한 내용은 [TPM 기본 사항](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) 문서에서 찾을 수 있습니다.

##### <a name="network-errors"></a>네트워크 오류

- **WININET_E_TIMEOUT** (0x80072ee2/2147012894)
   - 이유: DRS에서 장치를 등록 하려고 시도 하는 일반 네트워크 시간 초과
   - 해결 방법:에 대 한 네트워크 연결을 확인 `https://enterpriseregistration.windows.net` 합니다.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/2147012889)
   - 이유: 서버 이름이 나 주소를 확인할 수 없습니다.
   - 해결 방법:에 대 한 네트워크 연결을 확인 `https://enterpriseregistration.windows.net` 합니다. 호스트 이름에 대 한 DNS 확인이 장치에서 n/w와 정확한 지 확인 합니다.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/2147012866)
   - 이유: 서버와의 연결이 비정상적으로 종료 되었습니다.
   - 해결 방법: 잠시 후 다시 시도 하거나 안정적인 다른 네트워크 위치에서 조인 해 보세요.

##### <a name="federated-join-server-errors"></a>페더레이션된 조인 서버 오류

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 원인 | 해결 방법 |
| --- | --- | --- | --- |
| DirectoryError | 요청이 일시적으로 제한되었습니다. 300 초 후에 시도 하세요. | 오류가 발생 했습니다. 여러 번의 신속한 등록 요청이 있을 수 있습니다. | 쿨 기간 이후에 조인 다시 시도 |

##### <a name="sync-join-server-errors"></a>동기화 조인 서버 오류

| 서버 오류 코드 | 서버 오류 메시지 | 가능한 원인 | 해결 방법 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: 테 넌 트가 <UUID> 없습니다. 이 오류는 테 넌 트에 대 한 활성 구독이 없는 경우에 발생할 수 있습니다. 구독 관리자에 게 문의 하세요. | SCP 개체의 테 넌 트 ID가 잘못 되었습니다. | SCP 개체가 올바른 Azure AD 테 넌 트 ID 및 활성 구독으로 구성 되 고 테 넌 트에 표시 되는지 확인 합니다. |
| DirectoryError | 지정 된 ID의 장치 개체를 찾을 수 없습니다. | 동기화 조인에 필요한 오류입니다. 장치 개체가 AD에서 Azure AD로 동기화 되지 않았습니다. | Azure AD Connect 동기화가 완료 될 때까지 기다렸다가 동기화 완료 후 다음 조인 시도가 문제를 해결 합니다. |
| AuthenticationError | 대상 컴퓨터의 SID를 확인 하는 중입니다. | Azure AD 장치의 인증서가 동기화 조인 중 blob에 서명 하는 데 사용 된 인증서와 일치 하지 않습니다. 이 오류는 일반적으로 동기화가 아직 완료 되지 않았음을 의미 합니다. |  Azure AD Connect 동기화가 완료 될 때까지 기다렸다가 동기화 완료 후 다음 조인 시도가 문제를 해결 합니다. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>5 단계: 로그 및 연락처 Microsoft 지원 수집

에서 Auth.zip 파일을 다운로드 합니다. [https://github.com/CSS-Windows/WindowsDiag/tree/master/ADS/AUTH](https://github.com/CSS-Windows/WindowsDiag/tree/master/ADS/AUTH)

1. 파일의 압축을 풀고 포함 된 파일의 이름을 **start-auth.txt** 하 **고 start-auth** 및 **stop-auth**에 **stop-auth.txt** 합니다.
1. 관리자 권한 명령 프롬프트에서 **start-auth**를 실행 합니다.
1. 스위치 계정을 사용 하 여 문제 사용자의 다른 세션으로 전환 합니다.
1. 문제를 재현합니다.
1. 스위치 계정을 사용 하 여 추적을 실행 하는 관리자 세션으로 다시 전환 합니다.
1. 관리자 권한 명령 프롬프트에서 **stop-auth**를 실행 합니다.
1. Zip을 열고 스크립트가 실행 된 폴더에서 **Authlogs** 폴더를 보냅니다.

## <a name="troubleshoot-post-join-issues"></a>조인 후 문제 해결

### <a name="retrieve-the-join-status"></a>조인 상태 검색

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: YES 및 AzureADPrt: YES

이러한 필드는 사용자가 디바이스에 로그인 시 Azure AD에서 성공적으로 인증되었는지 여부를 나타냅니다.
값이 **아니요**인 경우 다음의 원인 때문일 수 있습니다.

- 등록 시 장치에 연결 된 TPM의 저장소 키가 잘못 되었습니다. 상승 된 상태에서 실행 하는 동안에는 KeySignTest를 확인 하세요.
- 대체 로그인 ID
- HTTP 프록시를 찾을 수 없음

## <a name="known-issues"></a>알려진 문제
- 설정-> 계정-회사 또는 학교에 액세스 > 하는 하이브리드 Azure AD 조인 장치는 모바일 핫스팟 또는 외부 WiFi 네트워크에 연결 된 경우 Azure AD 용과 온-프레미스 AD에 대해 각각 하나씩 두 개의 계정을 표시할 수 있습니다. 이는 UI 문제 이며 기능에 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

[Dsregcmd.exe 명령을 사용 하 여 장치 문제 해결](troubleshoot-device-dsregcmd.md) 계속

질문은 [디바이스 관리 FAQ](faq.md)를 참조하세요.