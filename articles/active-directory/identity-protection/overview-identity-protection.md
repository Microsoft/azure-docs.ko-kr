---
title: Azure Active Directory Identity Protection이란?
description: Azure AD Identity Protection을 통한 위험의 감지, 수정, 조사 및 분석
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f6ec9c1fb5ae359ca88b48dea97c6a00c0f2b40
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815184"
---
# <a name="what-is-identity-protection"></a>Identity Protection이란?

Identity Protection은 조직에서 다음과 같은 세 가지 주요 작업을 수행할 수 있는 도구입니다.

- ID 기반 위험의 감지 및 수정 자동화
- 포털에서 데이터를 사용하는 위험 조사
- 심도있는 조사를 위해 타사 유틸리티로 위험 감지 데이터 내보내기

Identity Protection은 사용자 보호를 위해 Microsoft가 Azure AD를 사용하는 조직, Microsoft 계정의 소비자 공간 및 Xbox를 이용한 게임 등에서 사용자 위치로부터 습득한 학습을 사용합니다. Microsoft는 하루 6조 5000억 개의 신호를 분석하여 고객을 위협으로부터 보호합니다.

Identity Protection에서 생성되어 전달되는 신호는 액세스 결정을 내리기 위해 조건부 액세스와 같은 도구에 추가적으로 전달되거나, 조직의 시행 정책에 따라 추가 조사를 위해 SIEM(보안 정보 및 이벤트 관리) 도구로 다시 전달될 수 있습니다.

## <a name="why-is-automation-important"></a>자동화가 중요한 이유는 무엇인가요?

Microsoft의 ID 보안 및 보호 팀을 이끄는 Alex Weinert는 [자신의 2018년 10월 블로그 포스트](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843)에서 많은 이벤트를 다룰 때 자동화가 중요한 이유를 설명했습니다.

> Microsoft의 기계 학습 및 휴리스틱 시스템은 매일 8억 개 이상의 개별 계정에서 180억 개의 로그인 시도에 대한 위험 점수를 제공하며, 그 중 3억 개는 악의적 사용자(범죄자, 해커 등과 같은 엔터티)에 의해 식별 가능하게 이루어집니다.
>
> 작년 Ignite에서는 Microsoft의 ID 신분 시스템에 대한 상위 3가지 공격에 대해 말했습니다. 다음은 이러한 공격들의 최신 볼륨입니다.
>   
>   - **보안 위반 재생**: 2018년 5월 4.6BN 공격이 감지됨
>   - **암호 스프레이**: 2018년 4월 350k
>   - **피싱**: 정확히 수치화하기 어렵지만, 2018년 3월에 23M 위험 이벤트를 확인했으며, 그 중 상당수는 피싱 관련 사건입니다.

## <a name="risk-detection-and-remediation"></a>위험 감지 및 수정

Identity Protection은 다음과 같은 분류로 위험을 식별합니다.

| 위험 탐지 유형 | Description |
| --- | --- |
| 비정상적 이동 | 사용자의 최근 로그인을 기준으로 비정상적인 위치에서 로그인합니다. |
| 익명 IP 주소 | 익명 IP 주소에서 로그인(예: Tor 브라우저, 익명성 도구 VPN) |
| 일반적이지 않은 로그인 속성 | 지정된 사용자에게 최근 확인되지 않은 속성으로 로그인합니다. |
| 맬웨어 연결 IP 주소 | 맬웨어 연결 IP 주소에서 로그인합니다. |
| 유출된 자격 증명 | 이 위험 감지는 사용자의 유효한 자격 증명이 유출되었음을 나타냅니다. |
| 암호 스프레이 | 여러 사용자 이름이 통합된 무차별 암호 대입 공격 방식으로 공통 암호를 사용하여 공격 받고 있음을 나타냅니다. |
| Azure AD 위협 인텔리전스 | Microsoft의 내부 및 외부 위협 인텔리전스 소스가 알려진 공격 패턴을 식별했습니다. |

이러한 위험에 대한 더 자세한 내용 및 계산되는 방법/시기는 [위험이란?](concept-identity-protection-risks.md) 문서에서 확인할 수 있습니다.

위험 신호는 사용자에게 Azure Multi-Factor Authentication 수행, 셀프 서비스 암호 재설정을 사용하여 암호 다시 설정, 관리자가 조치를 취할 때까지 차단 등의 수정 작업을 트리거할 수 있습니다.

## <a name="risk-investigation"></a>위험 조사

관리자는 필요한 경우 탐지를 검토하고 수동 작업을 수행할 수 있습니다. 관리자가 Identity Protection의 조사에 사용하는 세 가지 주요 보고서는 다음과 같습니다.

- 위험한 사용자
- 위험한 로그인
- 위험 탐지

자세한 내용은 [방법: 위험 조사](howto-identity-protection-investigate-risk.md) 문서를 참조하세요.

### <a name="risk-levels"></a>위험 수준

ID 보호는 위험을 낮음, 중간 및 높음의 세 가지 계층으로 분류합니다. 

Microsoft는 위험을 계산하는 방법에 대한 구체적인 정보를 제공하지 않지만 각 수준에는 사용자 또는 로그인이 손상되었다는 확신을 더 많이 제공합니다. 예를 들어 사용자에 대한 익숙하지 않은 로그인 속성의 한 인스턴스와 같은 것은 다른 사용자의 자격 증명 유출만큼 위협적이지 않을 수 있습니다.

## <a name="exporting-risk-data"></a>위험 데이터 내보내기

Identity Protection의 데이터는 보관 및 추가 조사와 상관 관계를 위해 다른 도구로 내보낼 수 있습니다. 조직에서는 Microsoft Graph 기반 API를 사용하여 SIEM 등의 도구에서 추가로 처리할 수 있도록 이 데이터를 수집할 수 있습니다. Identity Protection API에 액세스하는 방법에 대한 정보는 [Azure Active Directory Identity Protection 및 Microsoft Graph 시작](howto-identity-protection-graph-api.md) 문서에서 확인할 수 있습니다.

Identity Protection 정보를 Azure Sentinel과 통합하는 방법에 대한 정보는 [Azure AD Identity Protection에서 정보 연결](../../sentinel/connect-azure-ad-identity-protection.md) 문서에서 확인할 수 있습니다.

## <a name="permissions"></a>사용 권한

Identity Protection을 사용하려면 사용자가 보안 읽기 권한자, 보안 운영자, 보안 관리자, 글로벌 독자 또는 글로벌 관리자여야 액세스할 수 있습니다.

| 역할 | 가능한 작업 | 수행할 수 없음 |
| --- | --- | --- |
| 전역 관리자 | ID 보호에 대한 완전한 액세스 |   |
| 보안 관리자 | ID 보호에 대한 완전한 액세스 | 사용자 암호 다시 설정 |
| 보안 운영자 | 모든 Identity Protection 보고서 및 개요 블레이드 보기 <br><br> 사용자 위험 해제, 안전 로그인 확인, 손상 확인 | 정책 구성 또는 변경 <br><br> 사용자 암호 다시 설정 <br><br> 경고 구성 |
| 보안 판독기 | 모든 Identity Protection 보고서 및 개요 블레이드 보기 | 정책 구성 또는 변경 <br><br> 사용자 암호 다시 설정 <br><br> 경고 구성 <br><br> 검색에 대한 피드백 제공 |

현재 보안 운영자 역할은 위험 로그인 보고서에 액세스할 수 없습니다.

조건부 액세스 관리자는 로그인 위험을 조건으로 고려하는 정책을 만들 수도 있습니다. 자세한 내용은 [조건부 액세스: 조건](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) 문서에서 찾을 수 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| 기능 | 세부 정보 | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Free / Office 365 앱 |
| --- | --- | --- | --- | --- |
| 위험 정책 | 사용자 위험 정책(ID 보호를 통해) | 예 | 아니요 | 예 |
| 위험 정책 | 로그인 위험 정책(ID 보호 또는 조건부 액세스를 통해) | 예 | 아니요 | 예 |
| 보안 보고서 | 개요 | 예 | 아니요 | 예 |
| 보안 보고서 | 위험한 사용자 | 모든 권한 | 제한적 정보 제공 | 제한적 정보 제공 |
| 보안 보고서 | 위험한 로그인 | 모든 권한 | 제한적 정보 제공 | 제한적 정보 제공 |
| 보안 보고서 | 위험 탐지 | 모든 권한 | 제한적 정보 제공 | 예 |
| 공지 | 위험에 처한 사용자가 알림을 감지함 | 예 | 아니요 | 예 |
| 공지 | 주 단위 요약 | 예 | 아니요 | 예 |
| | MFA 등록 정책 | 예 | 아니요 | 아니요 |

## <a name="next-steps"></a>다음 단계

- [보안 개요](concept-identity-protection-security-overview.md)

- [위험이란?](concept-identity-protection-risks.md)

- [위험을 완화하는 데 사용할 수 있는 정책](concept-identity-protection-policies.md)
