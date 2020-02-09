---
title: Dsregcmd.exe 명령을 사용 하 여 문제 해결-Azure Active Directory
description: Dsregcmd.exe의 출력을 사용 하 여 Azure AD의 장치 상태 이해
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb7fed7cf5f38f9f7677126aff92492ccacd6e12
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75707947"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Dsregcmd.exe 명령을 사용 하 여 장치 문제 해결

Dsregcmd.exe/status 유틸리티는 도메인 사용자 계정으로 실행 해야 합니다.

## <a name="device-state"></a>디바이스 상태

이 섹션에는 장치 연결 상태 매개 변수가 나열 됩니다. 다음 표에서는 다양 한 조인 상태에 있는 장치에 대 한 조건을 나열 합니다.

| AzureAdJoined | EnterpriseJoined 됨 | DomainJoined | 디바이스 상태 |
| ---   | ---   | ---   | ---   |
| YES | 아니요 | 아니요 | Azure AD 조인 됨 |
| 아니요 | 아니요 | YES | 도메인에 가입된 경우 |
| YES | 아니요 | YES | 하이브리드 AD 조인 됨 |
| 아니요 | YES | YES | 온-프레미스 DRS 조인 됨 |

> [!NOTE]
> "사용자 상태" 섹션에 Workplace Join (Azure AD 등록 됨) 상태가 표시 됩니다.

- **AzureAdJoined:** -장치가 Azure AD에 가입 되어 있는 경우 "예"로 설정 합니다. 그렇지 않으면 "NO"입니다.
- **Enterprisejoined 됨:** 장치가 온-프레미스 DRS에 가입 되어 있는 경우 "예"로 설정 합니다. 장치는 EnterpriseJoined 되 고 AzureAdJoined 수 없습니다.
- **Domainjoined:** -장치가 도메인 (AD)에 가입 되어 있는 경우 "예"로 설정 합니다.
- **DomainName:** -장치가 도메인에 가입 되어 있는 경우 도메인의 이름으로 설정 합니다.

### <a name="sample-device-state-output"></a>샘플 장치 상태 출력

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>디바이스 세부 정보

장치가 Azure AD에 가입 된 경우 또는 하이브리드 Azure AD에 가입 된 경우에만 표시 됩니다 (Azure AD가 등록 되지 않음). 이 섹션에는 클라우드에 저장 된 장치 식별 정보가 나열 됩니다.

- **DeviceId:** -Azure AD 테넌트에서 장치의 고유 ID
- **지문:** -장치 인증서의 지문 
- **DeviceCertificateValidity:** -장치 인증서의 유효성을 검사 합니다.
- **Keycontainerid:** -장치 인증서와 연결 된 장치 개인 키의 ContainerId
- **Keyprovider:** -Keyprovider (하드웨어/소프트웨어) 장치 개인 키를 저장 하는 데 사용 됩니다.
- **Tpmprotected:** -"예"를 입력 하면 장치 개인 키가 하드웨어 TPM에 저장 됩니다.

### <a name="sample-device-details-output"></a>샘플 장치 세부 정보 출력

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>테넌트 세부 정보

장치가 Azure AD에 가입 된 경우 또는 하이브리드 Azure AD에 가입 된 경우에만 표시 됩니다 (Azure AD가 등록 되지 않음). 이 섹션에서는 장치가 Azure AD에 가입 된 경우 일반적인 테넌트 세부 정보를 나열 합니다.

> [!NOTE]
> 이 섹션에서 MDM Url이 비어 있으면 MDM이 구성 되지 않았거나 현재 사용자가 MDM 등록 범위에 있지 않음을 나타냅니다. Azure AD의 모바일 설정을 확인 하 여 MDM 구성을 검토 합니다.

> [!NOTE]
> MDM Url이 표시 되는 경우에도 장치가 MDM에서 관리 되는 것을 의미 하지는 않습니다. 장치 자체를 관리 하지 않는 경우에도 테넌트에 자동 등록에 대 한 MDM 구성이 있으면 정보가 표시 됩니다. 

### <a name="sample-tenant-details-output"></a>샘플 테넌트 세부 정보 출력

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>사용자 상태

이 섹션에는 현재 장치에 로그인 한 사용자에 대 한 다양 한 특성의 상태가 나열 됩니다.

> [!NOTE]
> 유효한 상태를 검색 하려면 명령을 사용자 컨텍스트에서 실행 해야 합니다.

- **NgcSet:** -현재 로그온 한 사용자에 대해 Windows Hello 키가 설정 된 경우 "예"로 설정 합니다.
- **NgcKeyId:** -현재 로그온 한 사용자에 대해 설정 된 Windows Hello 키의 ID입니다.
- **Canreset:** -사용자가 Windows Hello 키를 다시 설정할 수 있는지 여부를 나타냅니다. 
- **가능한 값은 다음과 같습니다.** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive 또는 Unknown (오류가 발생 한 경우). 
- **WorkplaceJoined:** -Azure AD 등록 된 계정이 현재 ntuser.man 파일 컨텍스트의 장치에 추가 된 경우 "예"로 설정 합니다.
- **WamDefaultSet:** -로그인 한 사용자에 대해 WAM default WebAccount가 생성 되 면 "예"로 설정 합니다. Dsreg/status를 관리자 컨텍스트에서 실행 하는 경우이 필드에 오류가 표시 될 수 있습니다. 
- **WamDefaultAuthority:** -Azure AD의 "조직"으로 설정 합니다.
- **WamDefaultId:** -Azure AD “https://login.microsoft.com” 의 경우-항상 ""입니다.
- **고 wamdefaultguid:** -기본 WAM WebAccount에 대 한 WAM 공급자의 (Azure AD/MICROSOFT 계정) GUID입니다. 

### <a name="sample-user-state-output"></a>샘플 사용자 상태 출력

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO 상태

이 섹션은 Azure AD 등록 장치에 대해 무시할 수 있습니다.

> [!NOTE]
> 해당 사용자에 대 한 유효한 상태를 검색 하려면 명령을 사용자 컨텍스트에서 실행 해야 합니다.

- **AzureAdPrt:** -로그온 한 사용자의 장치에 PRT가 있는 경우 "예"로 설정 합니다.
- **AzureAdPrtUpdateTime:** -PRT가 마지막으로 업데이트 된 UTC 시간으로 설정 합니다.
- **AzureAdPrtExpiryTime:** -갱신 되지 않은 경우 PRT가 만료 되는 시간 (UTC)으로 설정 합니다.
- **AzureAdPrtAuthority:** -Azure AD 기관 URL
- **Enterpriseprt:** -장치에 온-프레미스 ADFS의 PRT가 있는 경우 "예"로 설정 합니다. 하이브리드 Azure AD 가입 장치의 경우 장치는 Azure AD와 온-프레미스 AD의 PRT를 동시에 가질 수 있습니다. 온-프레미스에 가입 된 장치에는 Enterprise PRT만 있습니다.
- **EnterprisePrtUpdateTime:** -Enterprise PRT가 마지막으로 업데이트 된 UTC 시간으로 설정 합니다.
- **EnterprisePrtExpiryTime:** -갱신 되지 않은 경우 PRT가 만료 되는 시간 (UTC)으로 설정 합니다.
- **EnterprisePrtAuthority:** -ADFS 기관 URL

### <a name="sample-sso-state-output"></a>샘플 SSO 상태 출력

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>진단 데이터

### <a name="pre-join-diagnostics"></a>사전 조인 진단

이 섹션은 장치가 도메인에 가입 되어 있으며 Azure AD 조인을 하이브리드 할 수 없는 경우에만 표시 됩니다.

이 섹션에서는 조인 실패를 진단 하는 데 도움이 되는 다양 한 테스트를 수행 합니다. 또한이 섹션에는 이전 (?)의 세부 정보가 포함 되어 있습니다. 이 정보에는 오류 단계, 오류 코드, 서버 요청 ID, 서버 응답 http 상태, 서버 응답 오류 메시지가 포함 됩니다.

- **사용자 컨텍스트:** 진단이 실행 되는 컨텍스트입니다. 가능한 값: 시스템, 관리자 권한 없는 사용자, 관리자 권한 

   > [!NOTE]
   > 실제 조인은 시스템 컨텍스트에서 수행 되므로 시스템 컨텍스트에서 진단 실행은 실제 조인 시나리오와 가장 가깝습니다. 시스템 컨텍스트에서 진단을 실행 하려면 관리자 권한 명령 프롬프트에서 dsregcmd.exe/status 명령을 실행 해야 합니다.

- **클라이언트 시간:** 시스템 시간 (UTC)입니다.
- **AD 연결 테스트:** -테스트는 도메인 컨트롤러에 대 한 연결 테스트를 수행 합니다. 이 테스트에 오류가 발생 하면 사전 검사 단계에서 조인 오류가 발생할 수 있습니다.
- **AD 구성 테스트:** -테스트는 SCP 개체가 온-프레미스 AD 포리스트에서 올바르게 구성 되었는지 여부를 읽고 확인 합니다. 이 테스트의 오류는 검색 단계에서 오류 코드 0x801c001d와 함께 조인 오류가 발생할 수 있습니다.
- **DRS 검색 테스트:** -테스트는 검색 메타 데이터 엔드포인트에서 DRS 엔드포인트을 가져오고 사용자 영역 요청을 수행 합니다. 이 테스트의 오류는 검색 단계에서 조인 오류가 발생할 수 있습니다.
- **Drs 연결 테스트:** -테스트는 DRS 엔드포인트에 대 한 기본 연결 테스트를 수행 합니다.
- **토큰 획득 테스트:** -테스트 사용자 테넌트가 페더레이션 된 경우 Azure AD 인증 토큰을 가져오려고 시도 합니다. 이 테스트에서 오류가 발생 하면 인증 단계에서 조인 오류가 발생할 수 있습니다. 키를 사용 하 여 명시적으로 사용 하지 않도록 설정 된 경우를 제외 하 고 인증 실패 동기화 조인은 대체 (fallback)로 시도 됩니다.
- **Sync로 대체-조인:** -대체가 인증 실패와의 동기화를 방지 하기 위해 레지스트리 키가 없는 경우 "사용"으로 설정 합니다. 이 옵션은 Windows 10 1803 이상에서 사용할 수 있습니다.
- **이전 등록:** -이전 조인 시도가 발생 한 시간입니다. 실패 한 조인 시도만 로깅됩니다.
- **오류 단계:** -중단 된 조인의 단계입니다. 가능한 값은 사전 검사, 검색, 인증, 조인입니다.
- **클라이언트 ErrorCode:** -클라이언트 오류 코드가 반환 되었습니다 (HRESULT).
- **서버 ErrorCode:** -서버에 요청을 보내고 서버에서 오류 코드와 함께 응답 한 경우 서버 오류 코드입니다. 
- **서버 메시지:** -오류 코드와 함께 반환 되는 서버 메시지입니다.
- **Https 상태:** -서버에서 반환 된 Http 상태입니다.
- **요청 ID:** -서버에 전송 되는 클라이언트 requestId입니다. 서버 쪽 로그와의 상관 관계를 파악 하는 데 유용 합니다.

### <a name="sample-pre-join-diagnostics-output"></a>샘플 사전 조인 진단 출력

다음 예제에서는 검색 오류로 인해 진단 테스트가 실패 하는 것을 보여 줍니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

다음 예에서는 진단 테스트가 통과 하지만 동기화 조인에 필요한 디렉터리 오류로 인해 등록 시도가 실패 한 경우를 보여 줍니다. Azure AD Connect 동기화 작업이 완료 되 면 장치를 조인할 수 있습니다.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

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

### <a name="post-join-diagnostics"></a>사후 조인 진단

이 섹션에는 클라우드에 연결 된 장치에서 수행 된 온전성 검사의 출력이 표시 됩니다.

- **AadRecoveryEnabled:** -"YES" 인 경우 장치에 저장 된 키를 사용할 수 없으며 장치가 복구 하도록 표시 됩니다. 다음에 로그인 하면 복구 흐름이 트리거되고 장치를 다시 등록 합니다.
- **Keysigntest:** -"통과" 장치 키의 상태가 정상입니다. KeySignTest가 실패 하면 장치는 일반적으로 복구를 위해 표시 됩니다. 다음에 로그인 하면 복구 흐름이 트리거되고 장치를 다시 등록 합니다. 하이브리드 Azure AD 조인 장치의 경우 복구는 자동입니다. Azure AD 조인 또는 Azure AD가 등록 되는 동안 장치는 필요한 경우 장치를 복구 하 고 다시 등록 하기 위해 사용자 인증을 요청 합니다. **KeySignTest에는 상승 된 권한이 필요 합니다.**

#### <a name="sample-post-join-diagnostics-output"></a>샘플 사후 조인 진단 출력

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC 필수 구성 요소 확인

이 섹션에서는 WHFB (비즈니스용 Windows Hello)를 프로 비전 하는 필수 검사를 수행 합니다. 

> [!NOTE]
> 사용자가 이미 WHFB을 구성 했으면 dsregcmd.exe/status에서 NGC 필수 구성 요소 확인 세부 정보를 볼 수 없습니다.

- **Isdevicejoined:** -장치가 Azure AD에 가입 되어 있는 경우 "예"로 설정 합니다.
- **IsUserAzureAD:** -로그인 한 사용자가 Azure AD에 있는 경우 "예"로 설정 합니다.
- **Policyenabled:** -WHFB 정책이 장치에서 사용 되는 경우 "예"로 설정 합니다.
- **Postlogonenabled:** -WHFB 등록이 플랫폼에 의해 기본적으로 트리거되는 경우 "YES"로 설정 합니다. "아니요"로 설정 된 경우 비즈니스용 Windows Hello 등록이 사용자 지정 메커니즘에 의해 트리거됨을 나타냅니다.
- **Deviceeligible:** -장치가 WHFB를 사용 하 여 등록 하기 위한 하드웨어 요구 사항을 충족 하는 경우 "예"로 설정 합니다.
- **Sessionisnotremote:** -현재 사용자가 원격이 아닌 장치에 직접 로그인 한 경우 "예"로 설정 합니다.
- **Certenrollment:** -WHFB 인증서 신뢰 배포와 관련 하 여 WHFB에 대 한 인증서 등록 기관을 나타냅니다. WHFB policy의 소스가 그룹 정책 경우 "등록 기관"으로 설정 하 고, 원본이 MDM 인 경우 "모바일 장치 관리"로 설정 합니다. 그렇지 않으면 "none"입니다.
- **AdfsRefreshToken:** -WHFB 인증서 신뢰 배포에만 적용 됩니다. CertEnrollment "등록 기관" 인 경우에만 표시 됩니다. 장치에 사용자에 대 한 엔터프라이즈 PRT가 있는지 여부를 나타냅니다.
- **AdfsRaIsReady:** -WHFB 인증서 신뢰 배포에만 적용 됩니다.  CertEnrollment "등록 기관" 인 경우에만 표시 됩니다. ADFS가 WHFB을 지원 하 *고* 로그온 인증서 템플릿을 사용할 수 있는 경우 "예"로 설정 합니다.
- **LogonCertTemplateReady:** -WHFB 인증서 신뢰 배포에만 적용 됩니다. CertEnrollment "등록 기관" 인 경우에만 표시 됩니다. 로그온 인증서 템플릿의 상태가 유효 하 고 ADFS RA 문제를 해결 하는 데 도움이 되는 경우 "예"로 설정 합니다.
- **PreReqResult:** -모든 WHFB 필수 구성 요소 평가의 결과를 제공 합니다. 사용자가 다음에 로그인 할 때 WHFB 등록을 사후 로그온 작업으로 시작 하는 경우 "프로 비전"으로 설정 합니다.

### <a name="sample-ngc-prerequisite-check-output"></a>샘플 NGC 필수 구성 요소 검사 출력

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>다음 단계

질문은 [디바이스 관리 FAQ](faq.md)를 참조하세요.
