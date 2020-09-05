---
title: 관리자 작업으로 역할 위임-Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 ID 작업을 위임하는 역할
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 05/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 692b20bf301bbd7bf03ea1af5527e08d947b50da
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798366"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Azure Active Directory에서 관리 작업별 관리자 역할

이 문서에서는 Azure AD(Azure Active Directory)에서 최소 권한 역할을 할당하여 사용자의 관리자 권한을 제한하는 데 필요한 정보를 찾을 수 있습니다. 관리자 작업은 각 작업을 수행하는 데 필요한 기능 영역 및 최소 권한 역할, 그리고 작업을 수행할 수 있는 비 글로벌 관리자 역할에 따라 구성되는 것을 알 수 있습니다.

## <a name="application-proxy"></a>애플리케이션 프록시

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
애플리케이션 프록시 앱 구성 | 애플리케이션 관리자 | 
커넥터 그룹 속성 구성 | 애플리케이션 관리자 | 
모든 사용자에 대한 기능이 비활성화되면 애플리케이션 등록 만들기 | 애플리케이션 개발자 | 클라우드 애플리케이션 관리자, 애플리케이션 관리자
커넥터 그룹 만들기 | 애플리케이션 관리자 | 
커넥터 그룹 삭제 | 애플리케이션 관리자 | 
애플리케이션 프록시 사용 안 함 | 애플리케이션 관리자 | 
커넥터 서비스 다운로드 | 애플리케이션 관리자 | 
모든 구성 읽기 | 애플리케이션 관리자 | 

## <a name="external-identitiesb2c"></a>외부 Id/B2C

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
Azure AD B2C 디렉터리 만들기 | 모든 비 게스트 사용자([설명서 참조](../fundamentals/users-default-permissions.md)) | 
B2C 애플리케이션 만들기 | 전역 관리자 | 
엔터프라이즈 애플리케이션 만들기 | 클라우드 애플리케이션 관리자 | 애플리케이션 관리자
B2C 정책 만들기, 읽기, 업데이트 및 삭제 | B2C IEF 정책 관리자 | 
ID 공급자 만들기, 읽기, 업데이트 및 삭제 | 외부 ID 공급자 관리자 | 
암호 재설정 사용자 흐름 만들기, 읽기, 업데이트 및 삭제 | 외부 ID 사용자 흐름 관리자 | 
프로필 편집 사용자 흐름 만들기, 읽기, 업데이트 및 삭제 | 외부 ID 사용자 흐름 관리자 | 
로그인 사용자 흐름 만들기, 읽기, 업데이트 및 삭제 | 외부 ID 사용자 흐름 관리자 | 
등록 사용자 흐름 만들기, 읽기, 업데이트 및 삭제 |외부 ID 사용자 흐름 관리자 | 
사용자 특정 만들기, 읽기, 업데이트 및 삭제 | 외부 ID 사용자 흐름 특성 관리자 | 
사용자 만들기, 읽기, 업데이트 및 삭제 | 사용자 관리자
모든 구성 읽기 | 글로벌 읽기 권한자 | 
B2C 감사 로그 읽기 | 전역 판독기 ([설명서 참조](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C 전역 판독기에는 Azure AD 전역 관리자와 같은 권한이 없습니다. 전역 관리자 권한이 Azure AD B2C 경우 Azure AD 디렉터리가 아니라 Azure AD B2C 디렉터리에 있는지 확인 합니다.

## <a name="company-branding"></a>회사 브랜딩

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
회사 브랜딩 구성 | 전역 관리자 | 
모든 구성 읽기 | 디렉터리 읽기 권한자 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md))

## <a name="company-properties"></a>회사 속성

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
회사 속성 구성 | 전역 관리자 | 

## <a name="connect"></a>연결

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
통과 인증 | 전역 관리자  | 
모든 구성 읽기 | 글로벌 읽기 권한자 | 전역 관리자  |
원활한 Single Sign-On | 전역 관리자  | 

## <a name="cloud-provisioning"></a>클라우드 프로 비전

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
통과 인증 | 하이브리드 ID 관리자  | 
모든 구성 읽기 | 글로벌 읽기 권한자 | 하이브리드 ID 관리자  |
원활한 Single Sign-On | 하이브리드 ID 관리자  | 

## <a name="connect-health"></a>Connect Health

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
서비스 추가 또는 삭제 | 소유자([설명서 참조](../hybrid/how-to-connect-health-operations.md)) | 
동기화 오류 수정 적용 | 기여자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 소유자
알림 구성 | 기여자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 소유자
설정 구성 | 소유자([설명서 참조](../hybrid/how-to-connect-health-operations.md)) | 
동기화 알림 구성 | 기여자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 소유자
ADFS 보안 보고서 읽기 | 보안 Reader | 기여자, 소유자
모든 구성 읽기 | 읽기 권한자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 기여자, 소유자
동기화 오류 읽기 | 읽기 권한자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 기여자, 소유자
동기화 서비스 읽기 | 읽기 권한자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 기여자, 소유자
메트릭 및 경고 보기 | 읽기 권한자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 기여자, 소유자
메트릭 및 경고 보기 | 읽기 권한자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 기여자, 소유자
동기화 서비스 메트릭 및 경고 보기 | 읽기 권한자([설명서 참조](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | 기여자, 소유자

## <a name="custom-domain-names"></a>사용자 지정 도메인 이름

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
도메인 관리 | 전역 관리자 | 
모든 구성 읽기 | 디렉터리 읽기 권한자 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md))

## <a name="domain-services"></a>Domain Services

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
Azure AD Domain Services 인스턴스 만들기 | 전역 관리자 | 
모든 Azure AD Domain Services 작업 수행 | Azure AD DC 관리자 그룹([설명서 참조](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
모든 구성 읽기 | AD DS 서비스를 포함하는 Azure 구독에 대한 읽기 권한자 | 

## <a name="devices"></a>디바이스

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
디바이스 사용 안 함 | 클라우드 디바이스 관리자 | 
디바이스 사용 | 클라우드 디바이스 관리자 | 
기본 구성 읽기 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md)) | 
BitLocker 키 읽기 | 보안 Reader | 암호 관리자, 보안 관리자

## <a name="enterprise-applications"></a>Enterprise 애플리케이션

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
모든 위임된 권한에 동의 | 클라우드 애플리케이션 관리자 | 애플리케이션 관리자
Microsoft Graph를 포함 하지 않는 응용 프로그램 권한에 동의 | 클라우드 애플리케이션 관리자 | 애플리케이션 관리자
Microsoft Graph에 대 한 응용 프로그램 사용 권한에 동의 | 권한 있는 역할 관리자 | 
자체 데이터에 액세스하는 애플리케이션에 동의 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md)) | 
엔터프라이즈 애플리케이션 만들기 | 클라우드 애플리케이션 관리자 | 애플리케이션 관리자
애플리케이션 프록시 관리 | 애플리케이션 관리자 | 
사용자 설정 관리 | 전역 관리자 | 
그룹 또는 앱의 액세스 검토 읽기 | 보안 Reader | 보안 관리자, 사용자 관리자
모든 구성 읽기 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md)) | 
엔터프라이즈 애플리케이션 할당 업데이트 | 엔터프라이즈 애플리케이션 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 클라우드 애플리케이션 관리자, 애플리케이션 관리자
엔터프라이즈 애플리케이션 소유자 업데이트 | 엔터프라이즈 애플리케이션 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 클라우드 애플리케이션 관리자, 애플리케이션 관리자
엔터프라이즈 애플리케이션 속성 업데이트 | 엔터프라이즈 애플리케이션 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 클라우드 애플리케이션 관리자, 애플리케이션 관리자
엔터프라이즈 애플리케이션 프로비전 업데이트 | 엔터프라이즈 애플리케이션 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 클라우드 애플리케이션 관리자, 애플리케이션 관리자
엔터프라이즈 애플리케이션 셀프 서비스 업데이트 | 엔터프라이즈 애플리케이션 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 클라우드 애플리케이션 관리자, 애플리케이션 관리자
Single Sign-On 속성 업데이트 | 엔터프라이즈 애플리케이션 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 클라우드 애플리케이션 관리자, 애플리케이션 관리자

## <a name="entitlement-management"></a>권한 관리
Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
카탈로그에 리소스 추가 | 사용자 관리자 | 권한 관리를 사용 하 여이 작업을 카탈로그 소유자에 게 위임할 수 있습니다 ([설명서 참조](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)).
카탈로그에 SharePoint Online 사이트 추가 | 전역 관리자


## <a name="groups"></a>그룹

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
라이선스 할당 | 사용자 관리자 | 
그룹 만들기 | 그룹 관리자 | 사용자 관리자
그룹 또는 앱의 액세스 검토 만들기, 업데이트 또는 삭제 | 사용자 관리자 | 
그룹 만료 관리 | 사용자 관리자 | 
그룹 설정 관리 | 그룹 관리자 | 사용자 관리자 | 
모든 구성 읽기(숨겨진 멤버 자격 제외) | 디렉터리 읽기 권한자 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md))
숨겨진 멤버 자격 읽기 | 그룹 멤버 | 그룹 소유자, 암호 관리자, Exchange 관리자, SharePoint 관리자, 팀 관리자, 사용자 관리자
숨겨진 구성원이 있는 그룹의 멤버 자격 읽기 | 기술 지원팀 관리자 | 사용자 관리자, 팀 관리자
라이선스 해지 | 라이선스 관리자 | 사용자 관리자
그룹 멤버 자격 업데이트 | 그룹 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 사용자 관리자
그룹 소유자 업데이트 | 그룹 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 사용자 관리자
그룹 속성 업데이트 | 그룹 소유자([설명서 참조](../fundamentals/users-default-permissions.md)) | 사용자 관리자
그룹 삭제 | 그룹 관리자 | 사용자 관리자

## <a name="identity-protection"></a>ID 보호

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
경고 알림 구성| 보안 관리자 | 
MFA 정책을 구성하고 설정/해제| 보안 관리자 | 
로그인 위험 정책을 구성하고 설정/해제| 보안 관리자 | 
사용자 위험 정책을 구성하고 설정/해제 | 보안 관리자 | 
주간 다이제스트 구성 | 보안 관리자| 
모든 위험 검색 해제 | 보안 관리자 | 
취약점 수정 또는 해결 | 보안 관리자 | 
모든 구성 읽기 | 보안 Reader | 
모든 위험 검색 읽기 | 보안 Reader | 
취약점 읽기 | 보안 Reader | 

## <a name="licenses"></a>라이선스

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
라이선스 할당 | 라이선스 관리자 | 사용자 관리자
모든 구성 읽기 | 디렉터리 읽기 권한자 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md))
라이선스 해지 | 라이선스 관리자 | 사용자 관리자
구독 체험 또는 구입 | 대금 청구 관리자 | 


## <a name="monitoring---audit-logs"></a>모니터링 - 감사 로그

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
감사 로그 읽기 | 보고서 읽기 권한자 | 보안 읽기 권한자, 보안 관리자

## <a name="monitoring---sign-ins"></a>모니터링 - 로그인

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
로그인 로그 읽기 | 보고서 읽기 권한자 | 보안 읽기 권한자, 보안 관리자

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
선택한 사용자가 생성한 모든 기존 앱 암호 삭제 | 전역 관리자 | 
MFA 사용 | 전역 관리자 | 
MFA 사용 | 전역 관리자 | 
MFA 서비스 설정 관리 | 전역 관리자 | 
연락처 메서드를 다시 제공하도록 선택된 사용자 요구 | 인증 관리자 | 
모든 저장된 디바이스에서 다단계 인증 복원  | 인증 관리자 | 

## <a name="mfa-server"></a>MFA 서버

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
사용자 차단/차단 해제 | 전역 관리자 | 
계정 잠금 구성 | 전역 관리자 | 
캐시 규칙 구성 | 전역 관리자 | 
사기 행위 경고 구성 | 전역 관리자
알림 구성 | 전역 관리자 | 
일회성 바이패스 구성 | 전역 관리자 | 
전화 통화 설정 구성 | 전역 관리자 | 
공급자 구성 | 전역 관리자 | 
서버 설정 구성 | 전역 관리자 | 
활동 보고서 읽기 | 글로벌 읽기 권한자 | 
모든 구성 읽기 | 글로벌 읽기 권한자 | 
서버 상태 읽기 | 글로벌 읽기 권한자 |  

## <a name="organizational-relationships"></a>조직 관계

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
ID 공급자 관리 | 외부 ID 공급자 관리자 | 
설정 관리 | 전역 관리자 | 
사용 약관 관리 | 전역 관리자 | 
모든 구성 읽기 | 글로벌 읽기 권한자 | 

## <a name="password-reset"></a>암호 재설정

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
인증 방법 구성 | 전역 관리자 |
사용자 지정 구성 | 전역 관리자 |
알림 구성 | 전역 관리자 |
온-프레미스 통합 구성 | 전역 관리자 |
암호 재설정 속성 구성 | 사용자 관리자 | 전역 관리자
등록 구성 | 전역 관리자 |
모든 구성 읽기 | 보안 관리자 | 사용자 관리자 |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
역할에 사용자 할당 | 권한 있는 역할 관리자 | 
역할 설정 구성 | 권한 있는 역할 관리자 | 
감사 활동 보기 | 보안 판독기 | 
역할 멤버 자격 보기 | 보안 판독기 | 

## <a name="roles-and-administrators"></a>역할 및 관리자

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
역할 할당 관리 | 권한 있는 역할 관리자 | 
Azure AD 역할의 액세스 검토 읽기  | 보안 Reader | 보안 관리자, 권한 있는 역할 관리자
모든 구성 읽기 | 기본 사용자 역할([설명서 참조](../fundamentals/users-default-permissions.md)) | 

## <a name="security---authentication-methods"></a>보안 - 인증 방법

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
인증 방법 구성 | 전역 관리자 | 
암호 보호 구성 | 보안 관리자
스마트 잠금 구성 | 보안 관리자
모든 구성 읽기 | 글로벌 읽기 권한자 | 

## <a name="security---conditional-access"></a>보안-조건부 액세스

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
MFA에서 신뢰할 수 있는 IP 주소 구성 | 조건부 액세스 관리자 | 
사용자 지정 컨트롤 만들기 | 조건부 액세스 관리자 | 보안 관리자
명명된 위치 만들기 | 조건부 액세스 관리자 | 보안 관리자
정책 만들기 | 조건부 액세스 관리자 | 보안 관리자
사용 약관 만들기 | 조건부 액세스 관리자 | 보안 관리자
VPN 연결 인증서 만들기 | 조건부 액세스 관리자 | 보안 관리자
클래식 정책 삭제 | 조건부 액세스 관리자 | 보안 관리자
사용 약관 삭제 | 조건부 액세스 관리자 | 보안 관리자
VPN 연결 인증서 삭제 | 조건부 액세스 관리자 | 보안 관리자
클래식 정책 사용 안 함 | 조건부 액세스 관리자 | 보안 관리자
사용자 지정 컨트롤 관리 | 조건부 액세스 관리자 | 보안 관리자
명명된 위치 관리 | 조건부 액세스 관리자 | 보안 관리자
사용 약관 관리 | 조건부 액세스 관리자 | 보안 관리자
모든 구성 읽기 | 보안 판독기 | 보안 관리자
명명된 위치 읽기 | 보안 판독기 | 조건부 액세스 관리자, 보안 관리자

## <a name="security---identity-security-score"></a>보안 - ID 보안 점수

Task | 최소 권한 역할 | 추가 역할 | 
---- | --------------------- | ----------------
모든 구성 읽기 | 보안 판독기 | 보안 관리자
보안 점수 읽기 | 보안 판독기 | 보안 관리자
이벤트 상태 업데이트 | 보안 관리자 | 

## <a name="security---risky-sign-ins"></a>보안 - 위험한 로그인

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
모든 구성 읽기 | 보안 Reader | 
위험한 로그인 읽기 | 보안 Reader | 

## <a name="security---users-flagged-for-risk"></a>보안 - 위험 플래그가 지정된 사용자

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
모든 이벤트 해제 | 보안 관리자 | 
모든 구성 읽기 | 보안 Reader | 
위험 플래그가 지정된 사용자 읽기 | 보안 Reader | 

## <a name="users"></a>사용자

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
디렉터리 역할에 사용자 추가 | 권한 있는 역할 관리자 | 
그룹에 사용자 추가 | 사용자 관리자 | 
라이선스 할당 | 라이선스 관리자 | 사용자 관리자
게스트 사용자 만들기 | 게스트 초대자 | 사용자 관리자
사용자 만들기 | 사용자 관리자 | 
사용자 삭제 | 사용자 관리자 | 
제한된 관리자의 새로 고침 토큰 무효화(설명서 참조) | 사용자 관리자 | 
비 관리자의 새로 고침 토큰 무효화(설명서 참조) | 암호 관리자 | 사용자 관리자
권한 있는 관리자의 새로 고침 토큰 무효화(설명서 참조) | 권한 있는 인증 관리자 | 
기본 구성 읽기 | 기본 사용자 역할 ([설명서 참조](../fundamentals/users-default-permissions.md) ) | 
제한된 관리자의 암호 재설정(설명서 참조) | 사용자 관리자 | 
비 관리자의 암호 재설정(설명서 참조) | 암호 관리자 | 사용자 관리자
권한 있는 관리자의 암호 재설정 | 권한 있는 인증 관리자 | 
라이선스 해지 | 라이선스 관리자 | 사용자 관리자
사용자 계정 이름을 제외한 모든 속성 업데이트 | 사용자 관리자 | 
제한된 관리자의 사용자 계정 이름 업데이트(설명서 참조) | 사용자 관리자 | 
권한 있는 관리자의 사용자 계정 이름 속성 업데이트(설명서 참조) | 전역 관리자 | 
사용자 설정 업데이트 | 전역 관리자 | 


## <a name="support"></a>지원

Task | 최소 권한 역할 | 추가 역할
---- | --------------------- | ----------------
지원 티켓 제출 | 서비스 관리자 | 응용 프로그램 관리자, Azure Information Protection 관리자, 대금 청구 관리자, 클라우드 응용 프로그램 관리자, 준수 관리자, Dynamics 365 관리자, 데스크톱 분석 관리자, Exchange 관리자, 암호 관리자, Intune 관리자, 비즈니스용 Skype 관리자, Power BI 관리자, 권한 있는 인증 관리자, SharePoint 관리자, 팀 통신 관리자, 팀 관리자, 사용자 관리자, 작업 공간 분석 관리자

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리자 역할을 할당 하거나 제거 하는 방법](directory-manage-roles-portal.md)
* [Azure AD 관리자 역할 참조](directory-assign-admin-roles.md)