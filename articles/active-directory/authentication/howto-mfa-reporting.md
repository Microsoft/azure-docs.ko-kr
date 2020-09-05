---
title: Azure Multi-Factor Authentication의 로그인 이벤트 세부 정보 - Azure Active Directory
description: Azure Multi-Factor Authentication 이벤트의 로그인 활동 및 상태 메시지를 보는 방법에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6bc07dc4a46327981c432cf8982f0c3a646fda0d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068918"
---
# <a name="use-the-sign-ins-report-to-review-azure-multi-factor-authentication-events"></a>로그인 보고서를 사용하여 Azure Multi-Factor Authentication 이벤트 검토

Azure Multi-Factor Authentication 이벤트를 검토하고 이해하려면 Azure AD(Azure Active Directory) 로그인 보고서를 사용하면 됩니다. 이 보고서는 사용자에게 다단계 인증에 대한 메시지가 표시될 때 조건부 액세스 정책이 사용 중인 경우 이벤트에 대한 인증 세부 정보를 표시합니다. 로그인 보고서에 대한 자세한 내용은 [Azure AD의 로그인 활동 보고서 개요](../reports-monitoring/concept-sign-ins.md)를 참조하세요.

이 문서에서는 Azure Portal, MSOnline V1 PowerShell 모듈에서 Azure AD 로그인 보고서를 보는 방법을 보여 줍니다.

## <a name="view-the-azure-ad-sign-ins-report"></a>Azure AD 로그인 보고서 보기

로그인 보고서는 MFA(Multi-Factor Authentication) 사용에 대한 정보가 포함된 사용자 로그인 활동 및 관리되는 애플리케이션에 대한 정보를 제공합니다. MFA 데이터는 MFA가 조직에서 어떻게 작동하는지에 대한 정보를 제공합니다. 이를 통해 다음과 같은 질문에 대답할 수 있습니다.

- 로그인에 MFA가 요구되었나요?
- 사용자가 MFA를 어떻게 완료했나요?
- 사용자가 MFA를 완료할 수 없었던 이유는 무엇인가요?
- MFA에 어려움을 겪은 사용자가 얼마나 되나요?
- MFA 챌린지를 완료할 수 없었던 사용자가 얼마나 되나요?
- 최종 사용자가 겪은 일반적인 MFA 문제는 무엇인가요?

[Azure Portal](https://portal.azure.com)에서 로그인 활동 보고서를 보려면 다음 단계를 완료합니다. [보고 API](../reports-monitoring/concept-reporting-api.md)를 사용하여 데이터를 쿼리할 수도 있습니다.

1. *글로벌 관리자* 권한이 있는 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택한 다음, 왼쪽 메뉴에서 **사용자**를 선택합니다.
1. 왼쪽의 메뉴에 있는 *활동*에서 **로그인**을 선택합니다.
1. 로그인 이벤트 목록이 표시됩니다(상태 포함). 이벤트를 선택하여 자세한 정보를 볼 수 있습니다.

    이벤트 세부 정보의 *인증 세부 정보* 또는 *조건부 액세스* 탭에는 MFA 프롬프트를 트리거한 상태 코드 또는 정책이 표시됩니다.

    [![Azure Portal의 Azure Active Directory 로그인 보고서 예제 스크린샷](media/howto-mfa-reporting/sign-in-report-cropped.png)](media/howto-mfa-reporting/sign-in-report.png#lightbox)

사용할 수 있는 경우 문자 메시지, Microsoft Authenticator 앱 알림 또는 전화 통화와 같은 인증이 표시됩니다.

로그인 이벤트의 *인증 세부 정보* 창에 MFA 요청이 충족 또는 거부되었는지 보여 주는 다음 세부 정보가 표시됩니다.

* MFA가 충족되면 MFA가 어떻게 충족되었는지에 대한 자세한 정보가 이 열에 제공합니다.
   * 클라우드에서 완료
   * 테넌트에 구성된 정책으로 인해 만료
   * 등록 메시지가 표시됨
   * 토큰 클레임으로 충족
   * 외부 공급자가 제공한 클레임으로 충족
   * 강력한 인증으로 충족
   * 실행된 흐름이 Windows 브로커 로그온 흐름이어서 건너뜀
   * 앱 암호로 인해 건너뜀
   * 위치로 인해 건너뜀
   * 등록된 디바이스로 인해 건너뜀
   * 기억된 디바이스로 인해 건너뜀
   * 성공적으로 완료됨

* MFA가 거부된 경우 거부 이유가 이 열에 제공됩니다.
   * 인증 진행 중
   * 중복된 인증 시도
   * 잘못된 코드를 너무 많이 입력함
   * 잘못된 인증
   * 잘못된 모바일 앱 확인 코드
   * 잘못된 구성
   * 전화 통화가 음성 사서함으로 연결됨
   * 전화 번호 형식이 잘못됨
   * 서비스 오류
   * 사용자의 전화에 연결할 수 없음
   * 모바일 앱 알림을 디바이스에 보낼 수 없음
   * 모바일 앱 알림을 보낼 수 없음
   * 사용자가 인증을 거부했음
   * 사용자가 모바일 앱 알림에 응답하지 않음
   * 사용자에게 등록된 인증 방법이 없음
   * 사용자가 잘못된 코드를 입력했음
   * 사용자가 잘못된 PIN을 입력했음
   * 인증을 성공하지 않은 상태에서 사용자가 전화를 끊음
   * 사용자가 차단됨
   * 사용자가 확인 코드를 입력하지 않음
   * 사용자를 찾을 수 없음
   * 확인 코드가 이미 한 번 사용됨

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>MFA에 등록된 사용자에 대한 PowerShell 보고

먼저 [MSOnline V1 PowerShell 모듈](/powershell/azure/active-directory/overview?view=azureadps-1.0)이 설치되어 있는지 확인합니다.

다음에 나오는 PowerShell을 사용하여 MFA에 등록한 사용자를 식별합니다. 이 명령 집합은 비활성화된 사용자를 제외합니다. 이러한 계정은 Azure AD에 인증할 수 없기 때문입니다.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

다음에 나오는 PowerShell을 사용하여 MFA에 등록하지 않은 사용자를 식별합니다. 이 명령 집합은 비활성화된 사용자를 제외합니다. 이러한 계정은 Azure AD에 인증할 수 없기 때문입니다.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

등록된 사용자 및 출력 메서드를 식별합니다.

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>다운로드한 활동 보고서 결과 코드

다음 표는 이전 포털 단계 또는 PowerShell 명령을 통해 다운로드한 활동 보고서 버전을 사용하여 이벤트 문제를 해결하는 데 도움이 될 수 있습니다. 이러한 결과 코드는 Azure Portal에 직접 표시되지 않습니다.

| 통화 결과 | Description | 광범위한 설명 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN이 입력됨 | 사용자가 PIN을 입력했습니다.  인증이 성공하면 사용자가 올바른 PIN을 입력한 것이며,  인증이 거부되면 사용자가 잘못된 PIN을 입력했거나 표준 모드로 설정된 것입니다. |
| SUCCESS_NO_PIN | #만 입력됨 | PIN 모드로 설정된 사용자의 인증이 거부되는 경우 사용자가 PIN을 입력하지 않고 #만 입력했음을 의미합니다.  사용자가 표준 모드로 설정되었고 인증에 성공했다면 사용자가 #만 입력한 것이며, 이는 표준 모드에서 올바른 동작입니다. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | 입력 후 # 단추를 누르지 않음 | 사용자가 #을 입력하지 않았으므로 DTMF 번호를 전송하지 않았습니다.  입력 완료를 나타내는 #이 입력되지 않으면 입력된 다른 번호가 전송되지 않습니다. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 전화 입력 없음 - 시간 초과됨 | 전화를 받았지만 응답이 없었습니다.  이는 일반적으로 음성 사서함으로 연결되었음을 나타냅니다. |
| SUCCESS_PIN_EXPIRED | PIN이 만료된 후 변경 안 됨 | 사용자의 PIN이 만료되어 PIN을 변경하라는 안내를 받았지만 PIN 변경이 성공적으로 완료되지 않았습니다. |
| SUCCESS_USED_CACHE | 캐시가 사용됨 | 구성된 캐시 기간 중 이전에 동일한 사용자 이름에 대한 인증이 성공한 이후로 Multi-Factor Authentication 호출 없이 인증에 성공했습니다. |
| SUCCESS_BYPASSED_AUTH | 인증이 바이패스됨 | 사용자가 시작한 원타임 바이패스를 사용하여 인증에 성공했습니다.  바이패스에 대한 자세한 내용은 바이패스된 사용자 기록 보고서를 참조하세요. |
| SUCCESS_USED_IP_BASED_CACHE | IP 기반 캐시가 사용됨 | 구성된 캐시 기간 중 이전에 동일한 사용자 이름, 인증 유형, 애플리케이션 이름 및 IP에 대한 인증이 성공한 이후로 Multi-Factor Authentication 호출 없이 인증에 성공했습니다. |
| SUCCESS_USED_APP_BASED_CACHE | 앱 기반 캐시가 사용됨 | 구성된 캐시 기간 중 이전에 동일한 사용자 이름, 인증 유형 및 애플리케이션 이름에 대한 인증이 성공한 이후로 Multi-Factor Authentication 호출 없이 인증에 성공했습니다. |
| SUCCESS_INVALID_INPUT | 잘못된 전화 입력 | 전화에서 보낸 응답이 올바르지 않습니다.  이는 팩스 기기나 모뎀의 문제 때문이거나 사용자가 PIN의 일부로 *를 입력했기 때문일 수 있습니다. |
| SUCCESS_USER_BLOCKED | 사용자가 차단됨 | 사용자의 전화 번호가 차단되었습니다.  번호 차단은 인증 통화 동안 사용자가 시작하거나 관리자가 Azure Portal을 통해 시작할 수 있습니다. <br> 참고:   차단된 번호는 사기 행위 경고로 인해 차단된 것일 수도 있습니다. |
| SUCCESS_SMS_AUTHENTICATED | 문자 메시지 인증됨 | 양방향 문자 메시지의 경우 사용자가 OTP(일회성 암호) + PIN을 포함하여 올바르게 회신했습니다. |
| SUCCESS_SMS_SENT | 문자 메시지 전송됨 | 문자 메시지의 경우 OTP(일회성 암호)가 포함된 문자 메시지가 성공적으로 전송되었습니다.  사용자는 애플리케이션에 OTP 또는 OTP + PIN을 입력하여 인증을 완료합니다. |
| SUCCESS_PHONE_APP_AUTHENTICATED | 모바일 앱 인증됨 | 사용자가 모바일 앱을 통해 성공적으로 인증되었습니다. |
| SUCCESS_OATH_CODE_PENDING | OATH 코드가 보류됨 | 사용자가 OATH 코드를 입력하라는 안내를 받았지만 응답하지 않았습니다. |
| SUCCESS_OATH_CODE_VERIFIED | OATH 코드가 확인됨 | 사용자가 OATH 코드를 입력하라는 안내를 받고 올바른 OATH 코드를 입력했습니다. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 대체 OATH 코드가 확인됨 | 기본 Multi-Factor Authentication 방법을 사용한 인증이 거부된 후 사용자가 대체 방식인 OATH 코드를 올바르게 제공했습니다. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 대체 보안 질문이 답변됨 | 기본 Multi-Factor Authentication 방법을 사용한 인증이 거부된 후 사용자가 대체 방식인 보안 질문에 올바르게 답변했습니다. |
| FAILED_PHONE_BUSY | 인증이 이미 진행 중 | Multi-Factor Authentication에서 해당 사용자에 대한 인증을 처리하고 있습니다.  이는 종종 동일한 로그온 중에 여러 인증 요청을 보내는 RADIUS 클라이언트로 인해 발생할 수 있습니다. |
| CONFIG_ISSUE | 전화 연결 불가 | 통화를 시도했지만 전화를 걸 수 없거나 응답이 없습니다.  통화 중, 전화망 폭주(연결 끊김), 서비스되지 않는 번호, 시간 초과 등의 이유로 전화가 걸리지 않을 수 있습니다. |
| FAILED_INVALID_PHONENUMBER | 잘못된 전화 번호 형식 | 전화 번호 형식이 잘못되었습니다.  전화 번호는 숫자여야 하며 국가 코드 + 1(미국 및 캐나다)의 경우 10자리여야 합니다. |
| FAILED_USER_HUNGUP_ON_US | 사용자가 전화 끊음 | 사용자가 전화를 받았지만 단추를 누르지 않고 끊었습니다. |
| FAILED_INVALID_EXTENSION | 잘못된 내선 번호 | 내선 번호에 잘못된 문자가 포함되어 있습니다.  숫자, 쉼표, * 및 #만 허용됩니다.  @ 접미사를 사용할 수도 있습니다. |
| FAILED_FRAUD_CODE_ENTERED | 사기 코드가 입력됨 | 사용자가 전화 통화 시 사기 행위를 보고하여 인증이 거부되고 전화 번호가 차단되었습니다.| 
| FAILED_SERVER_ERROR | 전화를 걸 수 없음 | Multi-Factor Authentication 서비스에서 전화를 걸 수 없습니다. |
| FAILED_SMS_NOT_SENT | 문자 메시지를 보낼 수 없음 | 문자 메시지를 보낼 수 없습니다.  인증이 거부되었습니다. |
| FAILED_SMS_OTP_INCORRECT | 문자 메시지의 OTP가 잘못됨 | 사용자가 수신된 문자 메시지의 OTP(일회성 암호)를 잘못 입력했습니다.  인증이 거부되었습니다. |
| FAILED_SMS_OTP_PIN_INCORRECT | 문자 메시지의 OTP + PIN이 잘못됨 | 사용자가 잘못된 OTP(일회용 암호) 및/또는 잘못된 사용자 PIN을 입력했습니다.  인증이 거부되었습니다. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 문자 메시지 최대 OTP 시도 횟수를 초과함 | 사용자가 최대 OTP(일회용 암호) 시도 횟수를 초과했습니다. |
| FAILED_PHONE_APP_DENIED | 모바일 앱이 거부됨 | 사용자가 모바일 앱에서 거부 단추를 눌러 인증을 거부했습니다. |
| FAILED_PHONE_APP_INVALID_PIN | 모바일 앱의 잘못된 PIN | 사용자가 모바일 앱에서 인증할 때 잘못된 PIN을 입력했습니다. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 모바일 앱 PIN이 변경 안 됨 | 사용자가 모바일 앱에서 필요한 PIN 변경을 완료하지 못했습니다. |
| FAILED_FRAUD_REPORTED | 사기 행위가 보고됨 | 사용자가 모바일 앱에서 사기 행위를 보고했습니다. |
| FAILED_PHONE_APP_NO_RESPONSE | 모바일 앱 응답 없음 | 사용자가 모바일 앱 인증 요청에 응답하지 않았습니다. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 모바일 앱의 모든 디바이스가 차단됨 | 이 사용자의 모바일 앱 디바이스가 더 이상 알림에 응답하지 않으며 차단되었습니다. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 모바일 앱 알림 실패 | 사용자의 디바이스에 있는 모바일 앱으로 알림을 보내려는 동안 오류가 발생했습니다. |
| FAILED_PHONE_APP_INVALID_RESULT | 모바일 앱의 잘못된 결과 | 모바일 앱에서 잘못된 결과를 반환했습니다. |
| FAILED_OATH_CODE_INCORRECT | OATH 코드가 잘못됨 | 사용자가 잘못된 OATH 코드를 입력했습니다.  인증이 거부되었습니다. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH 코드 + PIN이 잘못됨 | 사용자가 잘못된 OATH 코드 및/또는 잘못된 사용자 PIN을 입력했습니다.  인증이 거부되었습니다. |
| FAILED_OATH_CODE_DUPLICATE | 중복된 OATH 코드 | 사용자가 이전에 사용된 OATH 코드를 입력했습니다.  인증이 거부되었습니다. |
| FAILED_OATH_CODE_OLD | OATH 코드가 만료됨 | 사용자가 이전에 사용된 OATH 코드보다 이전의 OATH 코드를 입력했습니다.  인증이 거부되었습니다. |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 코드 결과 제한 시간 초과 | 사용자가 OATH 코드를 입력하는 데 너무 오래 걸려서 Multi-Factor Authentication 시도 제한 시간이 이미 초과되었습니다. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 보안 질문 결과 제한 시간 초과 | 사용자가 보안 질문에 답변하는 데 너무 오래 걸려서 Multi-Factor Authentication 시도 제한 시간이 이미 초과되었습니다. |
| FAILED_AUTH_RESULT_TIMEOUT | 인증 결과 제한 시간 초과 | 사용자가 Multi-Factor Authentication 시도를 완료하는 데 너무 오래 걸렸습니다. |
| FAILED_AUTHENTICATION_THROTTLED | 인증이 제한됨 | 서비스에서 Multi-Factor Authentication 시도를 제한했습니다. |

## <a name="additional-mfa-reports"></a>추가 MFA 보고서

MFA 이벤트에 대한 다음과 같은 추가 정보 및 보고서를 볼 수 있습니다(MFA 서버에 대한 추가 정보 및 보고서 포함).

| 보고서 | 위치 | Description |
|:--- |:--- |:--- |
| 차단된 사용자 기록 | Azure AD > 보안 > MFA > 사용자 차단/차단 해제 | 사용자를 차단 또는 차단 해제하도록 요청한 기록이 표시됩니다. |
| 온-프레미스 구성 요소의 사용량 | Azure AD > 보안 > MFA > 활동 보고서 | NPS 확장, ADFS 및 MFA 서버를 통해 MFA 서버의 전체 사용량에 대한 정보를 제공합니다. |
| 무시된 사용자 기록 | Azure AD > 보안 > MFA > 일회성 바이패스 | 사용자에 대한 MFA를 바이패스하는 MFA 서버 요청 기록을 제공합니다. |
| 서버 상태 | Azure AD > 보안 > MFA > 서버 상태 | 계정에 연결된 MFA 서버의 상태가 표시됩니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 로그인 활동 보고서에 대한 개요를 제공했습니다. 이 보고서에 포함되는 내용과 데이터에 대한 자세한 내용은 [Azure AD의 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md)를 참조하세요.
