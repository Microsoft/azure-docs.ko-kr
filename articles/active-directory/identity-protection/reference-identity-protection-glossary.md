---
title: Azure Active Directory ID 보호 용어집
description: Azure Active Directory ID 보호 용어집
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a17f52e519a84c58c9ed5b75259cc0b96d0a175
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949177"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory ID 보호 용어집

### <a name="at-risk-user"></a>위험(사용자)
하나 이상의 활성 위험 검색이 있는 사용자입니다. 

### <a name="atypical-sign-in-location"></a>비정상적인 로그인 위치
특정 사용자, 비슷한 사용자 또는 테넌트에 일반적이지 않은 지리적 위치에서 시도하는 로그인입니다.

### <a name="azure-ad-identity-protection"></a>Azure AD ID 보호
조직의 id에 영향을 주는 위험 검색 및 잠재적 취약성에 대 한 통합 보기를 제공 하는 Azure Active Directory의 보안 모듈입니다.

### <a name="conditional-access"></a>조건부 액세스
리소스에 대한 액세스를 보호하는 정책입니다. 조건부 액세스 규칙은 Azure Active Directory에 저장 되 고 Azure AD에서 리소스에 대 한 액세스 권한을 부여 하기 전에 평가 됩니다.  예제 규칙은 사용자 위치, 디바이스 상태 또는 사용자 인증 방법에 따라 액세스를 제한하게 됩니다.

### <a name="credentials"></a>자격 증명
로컬 및 네트워크 리소스에 대한 액세스 권한을 얻는 데 사용되는 ID 및 ID의 증명을 포함하는 정보입니다. 자격 증명의 예는 사용자 이름 및 암호, 스마트 카드 및 인증서가 있습니다.

### <a name="event"></a>이벤트
Azure Active Directory에서 작업의 기록입니다.

### <a name="false-positive-risk-detection"></a>거짓-긍정 (위험 검색)
Id 보호 사용자가 수동으로 설정한 위험 검색 상태 이며, 위험 검색이 조사 되었고 위험 검색으로 잘못 플래그가 지정 되었음을 나타냅니다.

### <a name="identity"></a>ID
암호 또는 인증서와 같은 조건에 따라 인증이라는 방법으로 확인되어야 하는 개인 및 엔터티입니다.

### <a name="identity-risk-detection"></a>Id 위험 검색
Id 보호에 의해 비정상으로 플래그가 지정 된 Azure AD 이벤트 이며 id가 손상 되었음을 나타낼 수 있습니다.

### <a name="ignored-risk-detection"></a>무시 됨 (위험 검색)
Id 보호 사용자가 수동으로 설정한 위험 검색 상태 이며,이는 수정 작업을 수행 하지 않고 위험 검색이 닫 혔 음을 나타냅니다.

### <a name="impossible-travel-from-atypical-locations"></a>비정상적 위치에서 불가능한 이동
동일한 사용자에 대 한 두 개의 로그인이 검색 된 경우 (하나 이상 비정상적인 로그인 위치에서 발생 하는 경우), 로그인 간의 시간이 이러한 위치 사이에서 실제로 이동 하는 데 소요 되는 최소 시간 보다 짧은 경우에 트리거되는 위험 검색입니다.  

### <a name="investigation"></a>조사
수정 또는 완화 단계가 필요한 지 여부를 결정 하기 위해 작업, 로그 및 기타 관련 정보를 검토 하 고, id가 손상 되었는지 여부를 파악 하 고, 손상 된 id가 사용 되는 방식을 이해 하는 프로세스입니다.

### <a name="leaked-credentials"></a>유출된 자격 증명
현재 사용자 자격 증명 (사용자 이름 및 암호)이 사용자의 연구원에 의해 진한 웹에 공개적으로 게시 된 경우 발생 하는 위험 검색입니다.

### <a name="mitigation"></a>완화 방법
ID 또는 디바이스를 안전한 상태로 복원하지 않고 공격자의 능력을 제한하거나 제거하여 공격에 노출된 ID 또는 디바이스를 악용하는 작업입니다. 완화는 id 또는 장치와 연결 된 이전 위험 검색을 해결 하지 않습니다.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
인증서 등 사용자가 가진 정보를 포함하는 두 개 이상의 인증 방법이 필요한 인증 방법입니다. 사용자 이름, 암호 또는 암호 문구 등 사용자가 아는 정보 혹은 지문 등 물리적 특성 및 개인 서명 등 개인 특성일 수 있습니다.

### <a name="offline-detection"></a>오프라인 검색
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 사후 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="policy-condition"></a>정책 조건
엔터티(그룹, 사용자, 앱, 디바이스 플랫폼, 디바이스 상태, IP 범위, 클라이언트 형식)를 정의하는 보안 정책의 일부는 정책에 포함되었거나 제외됩니다.

### <a name="policy-rule"></a>정책 규칙
정책을 트리거하는 환경 및 정책이 트리거될 때 수행 되는 작업을 설명 하는 보안 정책의 일부입니다.

### <a name="prevention"></a>방지
손상이 우려되거나 손상된 ID 또는 디바이스를 남용하여 조직에 대한 손상을 방지하는 동작입니다. 방지 작업은 장치나 id를 보호 하지 않으며 이전 위험 검색을 해결 하지 않습니다.

### <a name="privileged-user"></a>권한 있는(사용자)
위험 검색 시 전역 관리자, 대금 청구 관리자, 서비스 관리자, 사용자 관리자, 암호 관리자 등 Azure Active Directory에 있는 하나 이상의 리소스에 대 한 영구 또는 임시 관리 권한이 있는 사용자입니다. 

### <a name="real-time"></a>실시간
실시간 탐지를 참조하세요.

### <a name="real-time-detection"></a>실시간 탐지
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 이벤트를 진행하기 전에 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="remediated-risk-detection"></a>재구성 됨 (위험 검색)
Id 보호에 의해 자동으로 설정 되는 위험 검색 상태는이 유형의 위험 검색에 대 한 표준 수정 작업을 사용 하 여 위험 검색이 재구성 되었음을 나타냅니다. 예를 들어 사용자 암호를 재설정 하면 이전 암호가 손상 되었음을 나타내는 많은 위험 검색이 자동으로 재구성 됩니다.

### <a name="remediation"></a>수정
이전에 손상이 우려되거나 손상된 ID 또는 디바이스를 보호하는 작업입니다. 수정 작업은 id 또는 장치를 안전한 상태로 복원 하 고 id 또는 장치와 연결 된 이전 위험 검색을 확인 합니다.

### <a name="resolved-risk-detection"></a>해결 됨 (위험 검색)
Id 보호 사용자가 수동으로 설정 하는 위험 검색 상태 이며, 사용자가 Id 보호 외부에서 적절 한 수정 작업을 수행 하 고 위험 검색을 종결 된 것으로 간주 해야 함을 나타냅니다.

### <a name="risk-detection-status"></a>위험 검색 상태
이벤트가 활성 상태 인지 여부 및 닫힌 경우 닫은 이유를 나타내는 위험 검색의 속성입니다.

### <a name="risk-detection-type"></a>위험 탐지 유형
이벤트를 위험한 것으로 간주 하는 변칙 유형을 나타내는 위험 검색의 범주입니다.

### <a name="risk-level-risk-detection"></a>위험 수준 (위험 검색)
Id 보호 사용자가 조직에 대 한 위험을 줄이기 위해 수행 하는 작업의 우선 순위를 정하는 데 도움이 되는 위험 검색의 심각도 (높음, 중간 또는 낮음)입니다. 

### <a name="risk-level-sign-in"></a>위험 수준(로그인)
사용자의 ID를 사용하려는 다른 사용자라는 특정 로그인에 대한 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-user-compromise"></a>위험 수준(사용자 손상)
ID가 손상된 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-vulnerability"></a>위험 수준(취약점)
ID 보호 사용자가 조직에 대한 위험을 줄이기 위해 수행하는 동작을 우선하도록 하는 취약점의 심각도를 표시(높음, 중간 또는 낮음)합니다.

### <a name="secure-identity"></a>보안(ID)
잠재적으로 노출된 ID를 비교할 수 없는 상태로 복원하기 위해 이미지로 다시 설치한 암호 변경 또는 컴퓨터와 같은 수정 작업을 수행합니다.

### <a name="security-policy"></a>보안 정책
정책 규칙 및 조건의 컬렉션입니다. 사용자, 그룹, 앱, 디바이스, 디바이스 플랫폼, 디바이스 상태, IP 범위 및 Auth2.0 클라이언트 유형과 같은 엔터티에 정책을 적용할 수 있습니다. 정책을 사용하는 경우 정책에 포함된 엔터티가 리소스에 토큰을 발급할 때마다 평가됩니다.

### <a name="sign-in-v"></a>로그인(v)
Azure Active Directory의 ID를 인증하려면.

### <a name="sign-in-n"></a>로그인(n)
Azure Active Directory에서 ID를 인증하는 프로세스 또는 동작이며 이 작업을 캡처하는 이벤트입니다.

### <a name="sign-in-from-anonymous-ip-address"></a>익명 IP 주소에서 로그인
익명 프록시 IP 주소로 식별 된 IP 주소에서 성공적으로 로그인 한 후에 트리거되는 위험 검색입니다.

### <a name="sign-in-from-infected-device"></a>감염된 디바이스에서 로그인
로그인이 IP 주소에서 시작 될 때 트리거되는 위험 검색은 하나 이상의 손상 된 장치에서 사용 되는 것으로 알려져 있으며,이는 봇 서버와의 통신을 적극적으로 시도 합니다.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>의심스러운 동작으로 IP 주소에서 로그인
짧은 기간 동안 여러 사용자 계정에 대해 실패 한 로그인 시도 횟수가 많은 IP 주소에서 성공적으로 로그인 한 후에 트리거되는 위험 검색입니다.

### <a name="sign-in-from-unfamiliar-location"></a>알 수 없는 위치에서 로그인
사용자가 새 위치 (IP, 위도/경도 및 ASN)에서 성공적으로 로그인 할 때 트리거되는 위험 검색입니다.

### <a name="sign-in-risk"></a>로그인 위험
위험 수준(로그인)을 참조하세요.

### <a name="sign-in-risk-policy"></a>로그인 위험 정책
특정 로그인에 대 한 위험을 평가 하 고 미리 정의 된 조건 및 규칙에 따라 완화를 적용 하는 조건부 액세스 정책입니다.

### <a name="user-compromise-risk"></a>사용자 손상 위험
위험 수준(사용자 손상)을 참조하세요

### <a name="user-risk"></a>사용자 위험
위험 수준(사용자 손상)을 참조하세요.

### <a name="user-risk-policy"></a>사용자 위험 정책
로그인을 고려 하 고 미리 정의 된 조건 및 규칙에 따라 완화를 적용 하는 조건부 액세스 정책입니다.

### <a name="users-flagged-for-risk"></a>위험에 대한 플래그가 지정된 사용자
위험 검색을 포함 하는 사용자 (활성 또는 재구성 됨)

### <a name="vulnerability"></a>취약점
디렉터리가 악용 또는 위협에 취약하게 만드는 Azure Active Directory의 구성 또는 조건입니다.

## <a name="see-also"></a>참조

- [Azure Active Directory ID 보호](./overview-identity-protection.md)