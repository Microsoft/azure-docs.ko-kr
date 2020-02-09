---
title: Azure Multi-Factor Authentication 데이터 상주
description: 사용자와 사용자 및 사용자에 대 한 Azure Multi-Factor Authentication 저장소와 원본 국가에 남아 있는 데이터에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4556ece7faaacb8657a1012344f2263ee84214
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480139"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대 한 데이터 상주 및 고객 데이터

고객 데이터는 Office 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공 하는 주소에 따라 지리적 위치에 Azure AD에 저장 됩니다. 고객 데이터가 저장 되는 위치에 대 한 자세한 내용은 Microsoft 보안 센터의 [어디에 있는 데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

클라우드 기반 Azure Multi-Factor Authentication 및 Azure Multi-Factor Authentication 서버는 몇 가지 개인 데이터와 조직 데이터를 처리 하 고 저장 합니다. 이 문서에서는 데이터가 저장 되는 위치 및 위치를 간략하게 설명 합니다.

다음 Multi-Factor Authentication 활동은 현재 명시 된 경우를 제외 하 고는 현재 미국 데이터 센터에서 비롯 됩니다.

* 전화 통화 또는 SMS를 사용 하는 2 단계 인증은 일반적으로 미국 데이터 센터에서 발생 하 고 전역 공급자에 의해 라우팅됩니다.
    * 유럽 또는 오스트레일리아와 같은 다른 지역의 범용 사용자 인증 요청은 현재 해당 지역의 데이터 센터에서 처리 됩니다. 셀프 서비스 암호 재설정, Azure B2C 이벤트 또는 NPS 확장 또는 AD FS 어댑터를 사용 하는 하이브리드 시나리오와 같은 기타 이벤트는 현재 미국 데이터 센터에서 모두 처리 됩니다.
* Microsoft Authenticator 앱을 사용한 푸시 알림은 미국 데이터 센터에서 시작 됩니다. 또한 장치 공급 업체의 특정 서비스도 다른 지역에서 재생 될 수 있습니다.
* OATH 코드는 일반적으로 미국에서 현재 유효성을 검사 합니다.
    * 또한 유럽 또는 오스트레일리아와 같이 다른 지역에서 시작 되는 범용 사용자 인증 이벤트는 해당 지역의 데이터 센터에서 처리 됩니다. 추가 이벤트는 현재 미국 데이터 센터에서 처리 됩니다.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 저장 된 개인 데이터

개인 데이터는 특정 사용자와 연결 된 사용자 수준 정보입니다. 다음 데이터 저장소에는 개인 정보가 포함 되어 있습니다.

* 차단 된 사용자
* 바이패스 사용자
* 장치 토큰 변경 요청 Microsoft Authenticator
* Multi-Factor Authentication 활동 보고서
* Microsoft Authenticator 활성화

이 정보는 90 일 동안 보존 됩니다.

Azure Multi-Factor Authentication는 사용자 이름, 전화 번호 또는 IP 주소와 같은 개인 데이터를 기록 하지 않지만 사용자에 대 한 Multi-Factor Authentication 시도를 식별 하는 *Userobjectid* 는 있습니다. 로그 데이터는 30 일 동안 저장 됩니다.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Azure B2C 인증, NPS 확장 및 Windows Server 2016 또는 2019 AD FS 어댑터를 제외 하 고 Azure 공용 클라우드의 경우 다음 개인 데이터가 저장 됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | Multi-Factor Authentication 로그     |
| 단방향 SMS                          | Multi-Factor Authentication 로그     |
| 음성 통화                           | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소<br />사기 행위를 보고 한 경우 차단 된 사용자 |
| Microsoft Authenticator 알림 | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소<br />사기 행위를 보고 한 경우 차단 된 사용자<br />장치 토큰 변경 Microsoft Authenticator 때 변경 요청 |

> [!NOTE]
> Multi-Factor Authentication 활동 보고서 데이터 저장소는 인증 요청을 처리 하는 지역과 관계 없이 모든 클라우드의 미국에 저장 됩니다. Microsoft Azure 독일, 21Vianet에서 운영 하는 Microsoft Azure Microsoft 정부 클라우드는 공용 클라우드 지역 데이터 저장소와 별도의 독립적인 데이터 저장소를가지고 있지만이 데이터는 항상 미국에 저장 됩니다.

21Vianet, Azure B2C 인증, NPS 확장 및 Windows Server 2016 또는 2019 AD FS 어댑터에서 작동 하는 Microsoft Azure Government, Microsoft Azure 독일 Microsoft Azure에는 다음 개인 데이터가 저장 됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소 |
| 단방향 SMS                          | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소 |
| 음성 통화                           | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소<br />사기 행위를 보고 한 경우 차단 된 사용자 |
| Microsoft Authenticator 알림 | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소<br />사기 행위를 보고 한 경우 차단 된 사용자<br />장치 토큰 변경 Microsoft Authenticator 때 변경 요청 |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication 서버

Azure Multi-Factor Authentication 서버를 배포 하 고 실행 하는 경우 다음 개인 데이터가 저장 됩니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포에 대 한 Multi-Factor Authentication 서버를 제공 하지 않습니다. 사용자에 게 multi-factor authentication을 요구 하려는 새 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다. 7 월 1 일 이전에 Multi-Factor Authentication 서버를 활성화 한 기존 고객은 최신 버전을 다운로드 하 고, 나중에 업데이트 하 고 활성화 자격 증명을 생성할 수 있습니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소 |
| 단방향 SMS                          | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소 |
| 음성 통화                           | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소<br />사기 행위를 보고 한 경우 차단 된 사용자 |
| Microsoft Authenticator 알림 | Multi-Factor Authentication 로그<br />Multi-Factor Authentication 활동 보고서 데이터 저장소<br />사기 행위를 보고 한 경우 차단 된 사용자<br />장치 토큰 변경 Microsoft Authenticator 때 변경 요청 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에서 저장 한 조직 데이터

조직 데이터는 구성 또는 환경 설치를 노출할 수 있는 테넌트 수준 정보입니다. 다음 Azure Portal Multi-Factor Authentication 페이지에서 테넌트 설정은 수신 전화 인증 요청에 대 한 잠금 임계값 또는 호출자 ID 정보와 같은 조직 데이터를 저장할 수 있습니다.

* 계정 잠금
* 사기 경고
* 알림
* 전화 통화 설정

Azure Multi-Factor Authentication 서버의 경우 다음 Azure Portal 페이지에 조직 데이터가 포함 될 수 있습니다.

* 서버 설정
* 일회성 바이패스
* 캐싱 규칙
* Multi-Factor Authentication 서버 상태

## <a name="log-data-location"></a>로그 데이터 위치

로그 정보가 저장 되는 위치는 처리 되는 지역에 따라 다릅니다. 대부분의 지역에는 네이티브 Azure Multi-Factor Authentication 기능이 있으므로 로그 데이터는 Multi-Factor Authentication 요청을 처리 하는 동일한 지역에 저장 됩니다. 네이티브 Azure Multi-Factor Authentication 지원 하지 않는 지역에서 이러한 서비스는 미국 또는 유럽 지역에서 서비스 되며, 로그 데이터는 Multi-Factor Authentication 요청을 처리 하는 동일한 지역에 저장 됩니다.

일부 핵심 인증 로그 데이터는 미국에만 저장 됩니다. 21Vianet에서 운영 하는 Microsoft Azure 독일 및 Microsoft Azure는 항상 해당 클라우드에 저장 됩니다. Microsoft 정부 클라우드 로그 데이터는 항상 미국에 저장 됩니다.

## <a name="next-steps"></a>다음 단계

클라우드 기반 Azure Multi-Factor Authentication 및 Azure Multi-Factor Authentication 서버에서 수집 하는 사용자 정보에 대 한 자세한 내용은 [azure Multi-Factor Authentication 사용자 데이터 컬렉션](howto-mfa-reporting-datacollection.md)을 참조 하세요.
