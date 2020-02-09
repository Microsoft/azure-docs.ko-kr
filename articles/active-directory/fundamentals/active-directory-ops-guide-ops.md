---
title: Azure Active Directory 일반 작업 가이드 참조
description: 이 작업 참조 가이드에서는 일반 작업을 보호 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422953"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 일반 작업 가이드 참조

[AZURE ad 작업 참조 가이드](active-directory-ops-guide-intro.md) 의이 섹션에서는 Azure Active Directory (azure ad)의 일반 작업을 최적화 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 따라 최신 이지만 시간이 지남에 따라 변경 될 수 있습니다. 조직에서는 Microsoft 제품 및 서비스가 시간이 지남에 따라 발전 함에 따라 운영 사례를 지속적으로 평가 해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>키 작업에 소유자 할당

Azure Active Directory를 관리 하려면 롤아웃 프로젝트에 포함 되지 않을 수 있는 주요 운영 작업 및 프로세스를 지속적으로 실행 해야 합니다. 환경 최적화를 위해 이러한 작업을 설정 하는 것도 중요 합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| Id 보안 점수에 대 한 향상 된 드라이브 | InfoSec 운영 팀 |
| Azure AD Connect 서버 유지 관리 | IAM 운영 팀 |
| 정기적으로 IdFix 보고서 실행 및 심사 | IAM 운영 팀 |
| 동기화 및 AD FS에 대 한 Azure AD Connect Health 경고 심사 | IAM 운영 팀 |
| Azure AD Connect Health 사용 하지 않는 경우 고객은 해당 프로세스와 도구를 사용 하 여 사용자 지정 인프라를 모니터링 합니다. | IAM 운영 팀 |
| AD FS 사용 하지 않는 경우 고객은 해당 프로세스와 도구를 사용 하 여 사용자 지정 인프라를 모니터링 합니다. | IAM 운영 팀 |
| 하이브리드 로그 모니터링: Azure AD 앱 프록시 커넥터 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 통과 인증 에이전트 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 비밀 번호 쓰기 저장 서비스 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: 온-프레미스 암호 보호 게이트웨이 | IAM 운영 팀 |
| 하이브리드 로그 모니터링: Azure MFA NPS 확장 (해당 하는 경우) | IAM 운영 팀 |

목록을 검토할 때 소유자가 없는 작업의 소유자를 할당 하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대 한 소유권을 조정 해야 할 수 있습니다.

#### <a name="owners-recommended-reading"></a>소유자 권장 읽기

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>하이브리드 관리

### <a name="recent-versions-of-on-premises-components"></a>최신 버전의 온-프레미스 구성 요소

온-프레미스 구성 요소의 최신 버전을 제공 하면 고객에 게 최신 보안 업데이트, 성능 향상 및 환경을 더욱 간소화 하는 데 도움이 되는 기능을 제공 합니다. 대부분의 구성 요소에는 자동 업그레이드 설정이 있습니다 .이 설정은 업그레이드 프로세스를 자동화 합니다.

이러한 구성 요소는 다음과 같습니다.

- Azure AD Connect
- Azure AD 애플리케이션 프록시 커넥터
- Azure AD 통과 인증 에이전트
- 에이전트 Azure AD Connect Health

설정 하지 않은 경우 이러한 구성 요소를 업그레이드 하는 프로세스를 정의 하 고 가능한 한 자동 업그레이드 기능을 사용 해야 합니다. 6 개월 이상 지난 구성 요소가 있는 경우 가능한 한 빨리 업그레이드 해야 합니다.

#### <a name="hybrid-management-recommended-reading"></a>하이브리드 관리 권장 읽기

- [Azure AD Connect: 자동 업그레이드](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Azure AD 응용 프로그램 프록시 커넥터 이해 | 자동 업데이트](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health 경고 기준

조직에서는 Azure AD Connect 및 AD FS의 모니터링 및 보고를 위해 [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) 를 배포 해야 합니다. Azure AD Connect 및 AD FS는 수명 주기 관리 및 인증을 중단 하 여 중단 될 수 있는 중요 한 구성 요소입니다. Azure AD Connect Health은 온-프레미스 id 인프라를 모니터링 하 고 통찰력을 얻을 수 있으므로 환경의 안정성을 보장 합니다.

![Azure AD Connect Heath 아키텍처](./media/active-directory-ops-guide/active-directory-ops-img16.png)

사용자 환경의 상태를 모니터링할 때 심각도가 높은 경고를 즉시 해결 해야 합니다.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health 권장 읽기

- [Azure AD Connect Health Agent 설치](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>온-프레미스 에이전트 로그

하이브리드 시나리오를 활성화 하려면 일부 id 및 액세스 관리 서비스에 온-프레미스 에이전트가 필요 합니다. 예를 들어 암호 재설정, PTA (통과 인증), Azure AD 응용 프로그램 프록시 및 Azure MFA NPS 확장이 있습니다. 운영 팀에서는 System Center Operations Manager 또는 SIEM과 같은 솔루션을 사용 하 여 구성 요소 에이전트 로그를 보관 하 고 분석 하 여 이러한 구성 요소의 상태를 모니터링 하는 것이 중요 합니다. Infosec 운영 팀 또는 지원 센터에서 오류 패턴 문제를 해결 하는 방법을 이해 하는 것도 중요 합니다.

#### <a name="on-premises-agents-logs-recommended-reading"></a>온-프레미스 에이전트에서 권장 읽기를 기록 합니다.

- [애플리케이션 프록시 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [셀프 서비스 암호 재설정 문제 해결-Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Azure AD 애플리케이션 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: 통과 인증 문제 해결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Azure MFA NPS 확장에 대 한 오류 코드 문제 해결](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>온-프레미스 에이전트 관리

모범 사례를 채택 하면 온-프레미스 에이전트의 최적 작업에 도움이 될 수 있습니다. 다음 모범 사례를 고려 합니다.

- 프록시 응용 프로그램에 액세스할 때 단일 실패 지점이 발생 하지 않도록 하 여 원활한 부하 분산 및 고가용성을 제공 하기 위해 커넥터 그룹당 여러 Azure AD 응용 프로그램 프록시 커넥터를 사용 하는 것이 좋습니다. 현재 프로덕션의 응용 프로그램을 처리 하는 커넥터 그룹에 커넥터가 하나 이상 있는 경우 중복성을 위해 두 개 이상의 커넥터를 배포 해야 합니다.
- 디버깅을 위해 앱 프록시 커넥터 그룹을 만들고 사용 하는 것은 시나리오 문제를 해결 하 고 새로운 온-프레미스 응용 프로그램을 온 보 딩 할 때 유용할 수 있습니다. 또한 커넥터 컴퓨터에 Message Analyzer 및 Fiddler와 같은 네트워킹 도구를 설치 하는 것이 좋습니다.
- 여러 통과 인증 에이전트는 인증 흐름 중에 단일 실패 지점을 방지 하 여 원활한 부하 분산 및 고가용성을 제공 하는 것이 좋습니다. 중복성을 위해 통과 인증 에이전트를 두 개 이상 배포 해야 합니다.

#### <a name="on-premises-agents-management-recommended-reading"></a>온-프레미스 에이전트 관리 권장 읽기

- [Azure AD 애플리케이션 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD 통과 인증-빠른 시작](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>규모에 맞게 관리

### <a name="identity-secure-score"></a>ID 보안 점수

[Id 보안 점수](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) 는 조직의 보안 상태를 수치로 측정 하 여 제공 합니다. 보고 되는 검색 결과를 지속적으로 검토 하 고 해결 하는 것은 핵심 이며 가장 높은 점수를 발생 시킬 수 있습니다. 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

![보안 점수](./media/active-directory-ops-guide/active-directory-ops-img17.png)

현재 조직에 Id 보안 점수 변경을 모니터링할 프로그램이 없는 경우 계획을 구현 하 고 소유자를 할당 하 여 개선 작업을 모니터링 하 고 구동 하는 것이 좋습니다. 조직은 가능한 한 빨리 30 보다 큰 점수를 사용 하 여 개선 작업을 수정 해야 합니다.

### <a name="notifications"></a>알림

Microsoft는 관리자에 게 전자 메일 통신을 보내 서비스의 다양 한 변경 내용, 필요한 구성 업데이트 및 관리자 개입이 필요한 오류를 알립니다. 모든 알림을 승인 하 고 작업할 수 있는 적절 한 팀 구성원에 게 알림이 전송 되도록 고객은 알림 전자 메일 주소를 설정 하는 것이 중요 합니다. [Office 365 메시지 센터](https://docs.microsoft.com/office365/admin/manage/message-center) 에 여러 받는 사람을 추가 하 고 메일 그룹 또는 공유 사서함에 알림 (Azure AD Connect Health 알림 포함)을 보내도록 요청 하는 것이 좋습니다. 전자 메일 주소를 가진 전역 관리자 계정이 하나만 있는 경우 전자 메일을 사용할 수 있는 계정을 두 개 이상 구성 해야 합니다.

Azure AD에서 사용 되는 두 개의 "보낸 사람" 주소: <o365mc@email2.microsoft.com>는 Office 365 메시지 센터 알림을 보냅니다. 및 <azure-noreply@microsoft.com>와 관련 된 알림을 보냅니다.

- [Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD ID 보호](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [엔터프라이즈 앱 만료 인증서 알림](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- 엔터프라이즈 앱 서비스 프로비저닝 알림

다음 표를 참조 하 여 전송 되는 알림 유형 및 확인할 위치를 알아봅니다.

| 알림 원본 | 전송 된 항목 | 확인할 위치 |
|:-|:-|:-|
| 기술 담당자 | 동기화 오류 | Azure Portal-속성 블레이드 |
| Office 365 메시지 센터 | Id 서비스 및 O365 백 엔드 서비스의 인시던트 및 성능 저하 알림 | Office 포털 |
| Id 보호 주간 다이제스트 | Id 보호 다이제스트 | Azure AD ID 보호 블레이드 |
| Azure AD Connect Health | 경고 알림 | Azure Portal-Azure AD Connect Health 블레이드 |
| 엔터프라이즈 응용 프로그램 알림 | 인증서가 만료 되 고 프로 비전 오류가 발생 하는 경우 알림 | Azure Portal-엔터프라이즈 응용 프로그램 블레이드 (각 앱에 고유한 전자 메일 주소 설정 포함) |

#### <a name="notifications-recommended-reading"></a>알림 권장 읽기

- [조직의 주소, 기술 연락처 및 기타-Office 365 변경](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>운영 노출 영역

### <a name="ad-fs-lockdown"></a>AD FS 잠금

 [Azure ad 스마트 잠금의](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)azure ad 혜택을 직접 인증 하도록 응용 프로그램을 구성 하는 조직 Windows Server 2012 r 2에서 AD FS를 사용 하는 경우 AD FS [엑스트라넷 잠금 보호](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)를 구현 합니다. Windows Server 2016 이상에서 AD FS를 사용 하는 경우 [엑스트라넷 스마트 잠금을](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)구현 합니다. 최소한, 내부 Active Directory에 대 한 무차별 암호 대입 공격의 위험을 포함 하도록 엑스트라넷 잠금을 설정 하는 것이 좋습니다. 그러나 Windows 2016 이상에서 AD FS 경우 [암호 스프레이](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) 공격을 완화 하는 데 도움이 되는 엑스트라넷 스마트 잠금도 사용 하도록 설정 해야 합니다.

AD FS Azure AD 페더레이션에만 사용 되는 경우 공격 노출 영역을 최소화 하기 위해 해제할 수 있는 몇 가지 엔드포인트이 있습니다. 예를 들어 AD FS Azure AD에만 사용 되는 경우 **usernamemixed** 및 **windowstransport**에 대해 사용 하도록 설정 된 엔드포인트이 아닌 ws-trust 엔드포인트을 사용 하지 않도록 설정 해야 합니다.

### <a name="access-to-machines-with-on-premises-identity-components"></a>온-프레미스 id 구성 요소가 있는 컴퓨터에 액세스

조직은 온-프레미스 도메인과 동일한 방식으로 온-프레미스 하이브리드 구성 요소를 사용 하 여 컴퓨터에 대 한 액세스를 잠가야 합니다. 예를 들어 백업 운영자 또는 Hyper-v 관리자는 Azure AD Connect 서버에 로그인 하 여 규칙을 변경할 수 없습니다.

Active Directory 관리 계층 모델은 환경 (계층 0)의 모든 제어와 공격자가 자주 손상 시키는 높은 위험 수준의 워크스테이션 자산 사이에서 버퍼 영역 집합을 사용 하 여 id 시스템을 보호 하도록 설계 되었습니다. ![계층 모델의 세 가지 계층을 보여 주는 다이어그램](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[계층 모델](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 은 세 가지 수준으로 구성 되며 표준 사용자 계정이 아닌 관리 계정만 포함 합니다.

- **계층 0** -환경에서 엔터프라이즈 Id를 직접 제어 합니다. 계층 0에는 Active Directory 포리스트, 도메인 또는 도메인 컨트롤러와 그 속의 모든 자산에 대한 직접 또는 간접 관리 권한을 가진 계정, 그룹 및 기타 자산이 포함됩니다. 모든 계층 0 자산은 서로를 효과적으로 제어하므로 보안 민감도가 동일합니다.
- **계층 1** 엔터프라이즈 서버 및 응용 프로그램에 대 한 제어 권한입니다. 계층 1 자산에는 서버 운영 체제, 클라우드 서비스 및 엔터프라이즈 애플리케이션이 포함됩니다. 계층 1 관리자 계정은 이러한 자산에서 호스트되는 상당한 양의 비즈니스 가치에 대한 관리 권한이 있습니다. 일반적인 역할 예로는 모든 엔터프라이즈 서비스에 영향을 줄 수 있는 기능으로 이러한 운영 체제를 유지 관리하는 서버 관리자가 있습니다.
- **계층 2** 사용자 워크스테이션 및 장치를 제어 합니다. 계층 2 관리자 계정은 사용자 워크스테이션 및 디바이스에서 호스트되는 상당한 양의 비즈니스 가치에 대한 관리 권한이 있습니다. 예를 들어 지원 센터 및 컴퓨터 지원 관리자가 여기에 포함됩니다. 이들은 거의 모든 사용자 데이터의 무결성에 영향을 줄 수 있기 때문입니다.

도메인 컨트롤러에 대해 수행 하는 것과 동일한 방식으로 Azure AD Connect, AD FS 및 SQL services와 같은 온-프레미스 id 구성 요소에 대 한 액세스를 잠급니다.

## <a name="summary"></a>요약

보안 Id 인프라에는 7 가지 측면이 있습니다. 이 목록을 통해 Azure Active Directory (Azure AD)에 대 한 작업을 최적화 하기 위해 수행 해야 하는 작업을 찾을 수 있습니다.

- 키 작업에 소유자를 할당 합니다.
- 온-프레미스 하이브리드 구성 요소에 대 한 업그레이드 프로세스를 자동화 합니다.
- Azure AD Connect 및 AD FS의 모니터링 및 보고를 위해 Azure AD Connect Health를 배포 합니다.
- System Center Operations Manager 또는 SIEM 솔루션을 사용 하 여 구성 요소 에이전트 로그를 보관 하 고 분석 하 여 온-프레미스 하이브리드 구성 요소의 상태를 모니터링 합니다.
- Id 보안 점수를 사용 하 여 보안 상태를 측정 하 여 보안 향상을 구현 합니다.
- AD FS를 잠급니다.
- 온-프레미스 id 구성 요소를 사용 하 여 컴퓨터에 대 한 액세스를 잠급니다.

## <a name="next-steps"></a>다음 단계

배포 하지 않은 모든 기능에 대 한 구현 세부 정보는 [AZURE AD 배포 계획](active-directory-deployment-plans.md) 을 참조 하세요.
