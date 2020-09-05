---
title: Azure AD에서 지속적인 액세스 평가
description: Azure AD에서 지속적인 액세스 평가로 사용자 상태 변경에 더 빠르게 응답
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39736f0a369064e1a825ba3f6975a01c5e9ecc40
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147516"
---
# <a name="continuous-access-evaluation"></a>지속적인 액세스 평가

토큰 만료 및 새로 고침은 업계의 표준 메커니즘입니다. Outlook과 같은 클라이언트 응용 프로그램이 Exchange Online과 같은 서비스에 연결 하는 경우 API 요청은 OAuth 2.0 액세스 토큰을 사용 하 여 인증 됩니다. 기본적으로 해당 액세스 토큰은 1 시간 동안 유효 하며 만료 되 면 클라이언트는 새로 고침을 위해 Azure AD로 다시 리디렉션됩니다. 새로 고침 기간을 사용 하면 사용자 액세스를 위한 정책을 다시 평가할 수 있습니다. 예: 조건부 액세스 정책으로 인해 또는 사용자가 디렉터리에서 사용 하지 않도록 설정 되었기 때문에 토큰을 새로 고치지 않도록 선택할 수 있습니다. 

고객은 네트워크 위치 또는 자격 증명 도난 등 사용자에 대 한 조건이 변경 되는 시기와 해당 변경에 관련 된 정책을 적용할 수 있는 시간 사이의 지연에 대 한 문제를 나타냅니다. 토큰 수명이 감소 하는 "무딘 개체" 접근 방식으로 실험, 위험을 제거 하지 않고 사용자 환경 및 안정성을 저하 시킬 수 있습니다.

정책 위반이 나 보안 문제에 대 한 시기 적절 한 응답에는 Azure AD와 같은 토큰 발급자와 Exchange Online과 같은 신뢰 당사자 간의 "대화"가 필요 합니다. 이 양방향 대화는 두 가지 중요 한 기능을 제공 합니다. 신뢰 당사자는 새 위치에서 들어오는 클라이언트와 같이 항목이 변경 된 경우를 확인할 수 있으며 토큰 발급자에 게 알립니다. 또한 토큰 발급자에 게 계정 손상, 비활성화 또는 기타 문제로 인해 지정 된 사용자에 대 한 토큰을 중지 하도록 신뢰 당사자에 게 지시 하는 방법을 제공 합니다. 이 대화에 대 한 메커니즘은 연속 액세스 평가 (CAE)입니다. 목표는 거의 실시간으로 응답 하기 위한 것 이지만, 경우에 따라 최대 15 분의 대기 시간은 이벤트 전파 시간으로 인해 관찰 될 수 있습니다.

연속 액세스 평가의 초기 구현은 Exchange, 팀 및 SharePoint Online을 중심으로 합니다. 

### <a name="key-benefits"></a>주요 이점

- 사용자 종료 또는 암호 변경/다시 설정: 사용자 세션 해지가 거의 실시간으로 적용 됩니다.
- 네트워크 위치 변경: 조건부 액세스 위치 정책은 거의 실시간으로 적용 됩니다.
- 신뢰할 수 있는 네트워크 외부의 컴퓨터로 토큰 내보내기는 조건부 액세스 위치 정책으로 방지할 수 있습니다.

## <a name="scenarios"></a>시나리오 

연속 액세스 평가, 중요 이벤트 평가 및 조건부 액세스 정책 평가를 구성 하는 두 가지 시나리오가 있습니다.

### <a name="critical-event-evaluation"></a>중요 이벤트 평가

연속 액세스 평가는 Exchange Online, SharePoint Online 및 팀과 같은 서비스를 통해 Azure AD의 중요 한 이벤트를 구독 하 여 이러한 이벤트를 평가 하 고 거의 실시간으로 적용할 수 있도록 하 여 구현 됩니다. 중요 이벤트 평가는 모든 테 넌 트에서 사용할 수 있도록 조건부 액세스 정책을 사용 하지 않습니다. 현재 평가 되는 이벤트는 다음과 같습니다.

- 사용자 계정이 삭제 되었거나 사용 하지 않도록 설정 됨
- 사용자 암호 변경 또는 다시 설정
- 사용자에 대해 multi-factor authentication을 사용 하도록 설정
- 관리자가 사용자에 대 한 모든 새로 고침 토큰을 명시적으로 해지
- Azure AD ID 보호에서 검색 된 승격 된 사용자 위험

이 프로세스를 통해 사용자는 조직 SharePoint Online 파일, 전자 메일, 일정, 작업 및 이러한 중요 한 이벤트 중 한 분 내에 O365 클라이언트 앱의 팀에 대 한 액세스 권한을 잃게 됩니다. 

### <a name="conditional-access-policy-evaluation-preview"></a>조건부 액세스 정책 평가 (미리 보기)

Exchange 및 SharePoint는 키 조건부 액세스 정책을 동기화 할 수 있으므로 서비스 자체 내에서 평가할 수 있습니다.

이 프로세스를 통해 사용자는 네트워크 위치를 변경한 후 즉시 O365 클라이언트 앱 또는 SharePoint Online에서 조직 파일, 전자 메일, 일정 또는 작업에 액세스할 수 없게 됩니다.

> [!NOTE]
> 일부 앱 및 리소스 공급자 조합은 지원 되지 않습니다. 아래 표를 참조 하세요. Office는 Word, Excel 및 PowerPoint를 의미 합니다.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| SharePoint Online | 지원됨 | 지원됨 | 지원되지 않음 | 지원되지 않음 | 지원됨 |
| Exchange Online | 지원됨 | 지원됨 | 지원됨 | 지원됨 | 지원됨 |

| | Office web apps | Office Win32 앱 | IOS 용 Office | Android 용 Office | Mac용 Office |
| :--- | :---: | :---: | :---: | :---: | :---: |
| SharePoint Online | 지원됨 | 지원됨 | 지원되지 않음 | 지원됨 | 지원됨 |
| Exchange Online | 지원됨 | 지원됨 | 지원되지 않음 | 지원됨 | 지원됨 |

### <a name="client-side-claim-challenge"></a>클라이언트 쪽 클레임 챌린지

연속 액세스를 평가 하기 전에 클라이언트는 만료 되지 않은 경우 항상 캐시에서 액세스 토큰을 재생 하려고 시도 합니다. CAE를 사용 하는 경우 리소스 공급자가 만료 되지 않은 경우에도 토큰을 거부할 수 있는 새로운 사례를 소개 합니다. 캐시 된 토큰이 만료 되지 않았더라도 클라이언트에 캐시를 사용 하지 않도록 알리기 위해 **클레임 챌린지** 라는 메커니즘을 도입 하 여 토큰이 거부 되었으며 Azure AD에서 새 액세스 토큰을 발급 해야 함을 표시 합니다. CAE에는 클레임 챌린지를 이해 하기 위해 클라이언트 업데이트가 필요 합니다. 아래 응용 프로그램의 최신 버전은 다음과 같은 클레임 챌린지를 지원 합니다.

- Outlook 창
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Windows 용 팀 (팀 리소스에만 해당)
- 팀 iOS (팀 리소스에만 해당)
- 팀 Android (팀 리소스에만 해당)
- 팀 Mac (팀 리소스에만 해당)
- Word/Excel/Windows 용 PowerPoint
- IOS 용 Word/Excel/PowerPoint
- Android 용 Word/Excel/PowerPoint
- Mac 용 Word/Excel/PowerPoint

### <a name="token-lifetime"></a>토큰 수명

위험 및 정책이 실시간으로 평가 되므로 지속적인 액세스 평가 인식 세션을 협상 하는 클라이언트는 기존 정적 액세스 토큰 수명 정책 대신 CAE를 사용 합니다. 즉, 구성 가능한 토큰 수명 정책이 CAE 인식 세션을 협상 하는 CAE 지원 클라이언트에 더 이상 적용 되지 않습니다.

토큰 수명은 CAE 세션에서 수명이 긴 기간 (최대 28 시간)으로 증가 합니다. 해지는 임의의 기간 뿐만 아니라 중요 한 이벤트 및 정책 평가에 의해 결정 됩니다. 이러한 변경으로 인해 보안 상태에 영향을 주지 않고 응용 프로그램의 안정성이 향상 됩니다. 

CAE 지원 클라이언트를 사용 하지 않는 경우 [구성 가능한 토큰 수명 (CTL)](../develop/active-directory-configurable-token-lifetimes.md) 미리 보기 기능을 사용 하 여 액세스 토큰 수명을 구성 하지 않으면 기본 액세스 토큰 수명은 1 시간으로 유지 됩니다.

## <a name="example-flows"></a>예제 흐름

### <a name="user-revocation-event-flow"></a>사용자 해지 이벤트 흐름:

![사용자 해지 이벤트 흐름](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. CAE 지원 클라이언트는 일부 리소스에 대 한 액세스 토큰을 요청 하는 자격 증명 또는 새로 고침 토큰을 Azure AD에 제공 합니다.
1. 액세스 토큰은 클라이언트에 대 한 다른 아티팩트와 함께 반환 됩니다.
1. 관리자는 [사용자에 대 한 모든 새로 고침 토큰을](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)명시적으로 해지 합니다. Azure AD에서 리소스 공급자로 해지 이벤트가 전송 됩니다.
1. 액세스 토큰이 리소스 공급자에 게 표시 됩니다. 리소스 공급자는 토큰의 유효성을 평가 하 고 사용자에 대 한 해지 이벤트가 있는지 여부를 확인 합니다. 리소스 공급자는이 정보를 사용 하 여 리소스에 대 한 액세스 권한을 부여 하도록 결정 합니다.
1. 이 경우 리소스 공급자는 액세스를 거부 하 고 401 + 클레임 챌린지를 다시 클라이언트에 보냅니다.
1. CAE 지원 클라이언트는 401 + 클레임 챌린지를 이해 합니다. 캐시를 우회 하 고 1 단계로 돌아가서 클레임 챌린지와 함께 새로 고침 토큰을 Azure AD로 다시 보냅니다. 그러면 Azure AD는 모든 조건을 다시 평가 하 고이 경우 사용자에 게 다시 인증 하 라는 메시지를 표시 합니다.

### <a name="user-condition-change-flow-public-preview"></a>사용자 조건 변경 흐름 (공개 미리 보기):

다음 예제에서는 조건부 액세스 관리자가 특정 IP 범위 에서만 액세스할 수 있도록 위치 기반 조건부 액세스 정책을 구성 했습니다.

![사용자 조건 이벤트 흐름](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. CAE 지원 클라이언트는 일부 리소스에 대 한 액세스 토큰을 요청 하는 자격 증명 또는 새로 고침 토큰을 Azure AD에 제공 합니다.
1. Azure AD는 모든 조건부 액세스 정책을 평가 하 여 사용자와 클라이언트가 조건을 충족 하는지 여부를 확인 합니다.
1. 액세스 토큰은 클라이언트에 대 한 다른 아티팩트와 함께 반환 됩니다.
1. 사용자가 허용 된 IP 범위 밖으로 이동
1. 클라이언트는 허용 되는 IP 범위 외부에서 리소스 공급자에 대 한 액세스 토큰을 제공 합니다.
1. 리소스 공급자는 토큰의 유효성을 평가 하 고 Azure AD에서 동기화 된 위치 정책을 확인 합니다.
1. 이 경우 리소스 공급자는 액세스를 거부 하 고 401 + 클레임 챌린지는 허용 되는 IP 범위에서 제공 되지 않으므로 클라이언트에 다시 보냅니다.
1. CAE 지원 클라이언트는 401 + 클레임 챌린지를 이해 합니다. 캐시를 우회 하 고 1 단계로 돌아가서 클레임 챌린지와 함께 새로 고침 토큰을 Azure AD로 다시 보냅니다. Azure AD는 모든 조건을 다시 평가이 경우 액세스를 거부 합니다.

## <a name="enable-or-disable-cae-preview"></a>CAE 사용 또는 사용 안 함 (미리 보기)

1. 조건부 액세스 관리자, 보안 관리자 또는 전역 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory**  >  **보안**  >  **연속 액세스 평가**로 이동 합니다.
1. **미리 보기 사용**을 선택 합니다.

이 페이지에서 필요에 따라 미리 보기에 적용 될 사용자 및 그룹을 제한할 수 있습니다.

![Azure Portal에서 CAE 미리 보기 사용](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>문제 해결

### <a name="ip-address-configuration"></a>IP 주소 구성

Id 공급자 및 리소스 공급자는 서로 다른 IP 주소를 볼 수 있습니다. 이러한 불일치는 조직의 네트워크 프록시 구현 또는 id 공급자와 리소스 공급자 간의 잘못 된 IPv4/IPv6 구성으로 인해 발생할 수 있습니다. 예를 들면 다음과 같습니다.

- Id 공급자는 클라이언트에서 하나의 IP 주소를 확인 합니다.
- 리소스 공급자는 프록시를 통과 한 후 클라이언트와 다른 IP 주소를 확인 합니다.
- Id 공급자가 확인 하는 IP 주소는 정책에서 허용 되는 IP 범위의 일부 이지만 리소스 공급자의 IP 주소는 그렇지 않습니다.

무한 루프를 방지 하기 위해 사용자 환경에이 시나리오가 있는 경우 Azure AD는 1 시간 CAE 토큰을 발급 하며 클라이언트 위치 변경을 적용 하지 않습니다. 이 경우에도 클라이언트 위치 변경 이벤트 외의 [다른 이벤트](#critical-event-evaluation) 를 평가 하 고 있으므로 기존 1 시간 토큰에 비해 보안이 향상 됩니다.

> [!IMPORTANT]
> 연속 액세스 평가를 위한 위치를 구성 하는 경우 [IP 기반 조건부 액세스 위치 조건만](../conditional-access/location-condition.md)사용 합니다. Azure Multi-Factor Authentication의 서비스 설정 페이지에서 사용할 수 있는 국가 위치 조건 또는 신뢰할 수 있는 ip 기능을 사용 하지 마세요.

### <a name="office-and-web-account-manager-settings"></a>Office 및 웹 계정 관리자 설정

| Office 업데이트 채널 | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| 반기 엔터프라이즈 채널 | Enabled 또는 1로 설정 하면 CAE가 지원 되지 않습니다. | Enabled 또는 1로 설정 하면 CAE가 지원 되지 않습니다. |
| 현재 채널 <br> 또는 <br> 월별 엔터프라이즈 채널 | CAE는 설정에 상관 없이 지원 됩니다. | CAE는 설정에 상관 없이 지원 됩니다. |

Office 업데이트 채널에 대 한 자세한 내용은 [Microsoft 365 앱의 업데이트 채널 개요](https://docs.microsoft.com/deployoffice/overview-update-channels)를 참조 하세요. 조직에서는 WAM (웹 계정 관리자)를 사용 하지 않도록 설정 하는 것이 좋습니다.

### <a name="policy-change-timing"></a>정책 변경 타이밍

Azure AD와 리소스 공급자 간의 복제 지연이 발생할 수 있으므로 관리자가 수행한 정책 변경 내용은 Exchange Online에 적용 되는 데 최대 2 시간이 걸릴 수 있습니다.

예: 관리자가 11:00 AM에 전자 메일에 액세스 하지 못하도록 차단 하는 정책을 추가 합니다. 이전에 해당 IP 범위에서 가져온 사용자는 1:00 PM까지 계속 해 서 메일에 액세스할 수 있습니다.

### <a name="coauthoring-in-office-apps"></a>Office 앱의 공동 작성

여러 사용자가 동시에 동일한 문서에서 공동 작업 하는 경우 사용자 해지 또는 정책 변경 이벤트에 따라 CAE에 의해 사용자의 문서에 대 한 사용자 액세스 권한이 즉시 취소 되지 않을 수 있습니다. 이 경우 사용자는 문서를 닫거나, Word, Excel 또는 PowerPoint를 닫거나, 10 시간이 지난 후에 완전히 액세스를 중단 합니다.

이 시간을 줄이기 위해 sharepoint 관리자는 sharepoint online [에서 네트워크 위치 정책을 구성](/sharepoint/control-access-based-on-network-location)하 여 sharepoint Online 및 비즈니스용 OneDrive에 저장 된 문서에 대 한 공동 작성 세션의 최대 수명을 줄일 수 있습니다. 이 구성이 변경 되 면 공동 작성 세션의 최대 수명은 15 분으로 줄어들고 SharePoint Online PowerShell 명령 "Set-spotenant – IPAddressWACTokenLifetime"를 사용 하 여 더 세부적으로 조정할 수 있습니다.

### <a name="enable-after-a-user-is-disabled"></a>사용자를 사용 하지 않도록 설정한 후 사용

사용 하지 않도록 설정한 후 사용자를 사용 하도록 설정 하는 경우 계정을 사용 하려면 약간의 대기 시간이 있습니다. SPO 및 팀은 15 분 지연 됩니다. EXO의 지연 시간은 35-40 분입니다.

## <a name="faqs"></a>FAQ

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE가 로그인 빈도로 어떻게 작동 하나요?

로그인 빈도는 CAE를 사용 하거나 사용 하지 않고 적용 됩니다.

## <a name="next-steps"></a>다음 단계

[연속 액세스 평가 발표](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
