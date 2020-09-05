---
title: Azure AD B2C에 대 한 모범 사례
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C (Azure AD B2C)로 작업할 때 고려해 야 할 권장 사항 및 모범 사례입니다.
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 6fee96354760e448de132f88918e9839c6220af3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254494"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 대 한 권장 사항 및 모범 사례

다음 모범 사례 및 권장 사항은 Azure Active Directory (Azure AD) B2C를 기존 또는 새 응용 프로그램 환경에 통합 하는 기본적인 몇 가지 측면을 다룹니다.

## <a name="fundamentals"></a>기본 사항

| 모범 사례 | 설명 |
|--|--|
| 대부분의 시나리오에서 사용자 흐름을 선택 합니다. | Azure AD B2C의 Id 경험 프레임 워크는 서비스의 핵심 수준입니다. 정책은 가입, 로그인 또는 프로필 편집과 같은 ID 환경을 완벽하게 설명합니다. 가장 일반적인 ID 작업을 설정하는 데 도움을 주기 위해 Azure AD B2C 포털은 사용자 흐름이라는 미리 정의되고 구성 가능한 정책을 포함합니다. 사용자 흐름을 사용 하면 몇 번의 클릭 만으로 훌륭한 사용자 환경을 몇 분 내에 만들 수 있습니다. [사용자 흐름과 사용자 지정 정책을 사용 하는 경우를 알아봅니다](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| 앱 등록 | 보안이 설정 된 모든 응용 프로그램 (웹, 네이티브) 및 API는 Azure AD B2C에 등록 되어야 합니다. 앱이 iOS 및 Android의 웹 버전과 네이티브 버전을 모두 사용 하는 경우 동일한 클라이언트 ID를 사용 하 여 Azure AD B2C에서 하나의 응용 프로그램으로 등록할 수 있습니다. [OIDC, SAML, web 및 네이티브 앱을 등록](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications)하는 방법에 대해 알아봅니다. [Azure AD B2C에서 사용할 수 있는 응용 프로그램 유형에](https://docs.microsoft.com/azure/active-directory-b2c/application-types)대해 자세히 알아보세요. |
| 월간 활성 사용자 요금 청구로 이동 | Azure AD B2C 월간 활성 인증에서 MAU (월간 활성 사용자) 청구로 이동 했습니다. 대부분의 고객은이 모델을 비용 효율적으로 찾을 수 있습니다. [월간 활성 사용자 요금 청구에 대해 자세히 알아보세요](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>계획 및 설계

응용 프로그램 및 서비스 아키텍처를 정의 하 고, 현재 시스템을 인벤토리 하 고, Azure AD B2C로 마이그레이션을 계획 합니다.

| 모범 사례 | 설명 |
|--|--|
| 종단 간 솔루션 설계 | Azure AD B2C 통합을 계획할 때 모든 응용 프로그램의 종속성을 포함 합니다. 현재 환경에 있는 모든 서비스 및 제품을 고려 하거나, Azure Functions, CRM (고객 관계 관리) 시스템, Azure API Management 게이트웨이 및 저장소 서비스와 같은 솔루션에 추가 해야 할 수도 있습니다. 모든 서비스에 대 한 보안 및 확장성을 고려 합니다. |
| 사용자 환경 문서화 | 고객이 응용 프로그램에서 경험할 수 있는 모든 사용자 경험에 대해 자세히 설명 합니다. 응용 프로그램의 id 및 프로필 측면과 상호 작용할 때 발생할 수 있는 모든 화면 및 분기 흐름을 포함 합니다. 계획에 유용성, 접근성 및 지역화를 포함 합니다. |
| 올바른 인증 프로토콜 선택 |  다양 한 응용 프로그램 시나리오 및 권장 되는 인증 흐름을 분석 하려면 [시나리오 및 지원 되는 인증 흐름](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)을 참조 하세요. |
| 개념 증명 (POC) 종단 간 사용자 환경 파일럿 | [Microsoft 코드 샘플](code-samples.md) 및 [커뮤니티 샘플](https://github.com/azure-ad-b2c/samples)부터 시작 합니다. |
| 마이그레이션 계획 만들기 |미리 계획을 만들면 마이그레이션이 더욱 원활 하 게 진행 될 수 있습니다. [사용자 마이그레이션](user-migration.md)에 대해 자세히 알아보세요.|
| 유용성 및 보안 | 솔루션은 응용 프로그램 사용 편의성과 조직의 허용 가능한 위험 수준 사이에서 적절 한 균형을 맞추어야 합니다. |
| 온-프레미스 종속성을 클라우드로 이동 | 복원 력 있는 솔루션을 위해 기존 응용 프로그램 종속성을 클라우드로 이동 하는 것이 좋습니다. |
| 기존 앱을 b2clogin.com로 마이그레이션 | Login.microsoftonline.com의 사용 중단은 04 년 12 월 2020에 모든 Azure AD B2C 테 넌 트에 적용 됩니다. [자세한 정보를 알아보세요](b2clogin.md). |
| Id 보호 및 조건부 액세스 사용 | 이러한 기능을 사용 하 여 위험한 인증 및 액세스 정책에 대해 훨씬 더 많은 제어를 제공 합니다. Azure AD B2C Premium P2가 필요 합니다. [자세한 정보를 알아보세요](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>구현

구현 단계에서 다음 권장 사항을 고려 합니다.

| 모범 사례 | 설명 |
|--|--|
| Visual Studio Code에 대 한 Azure AD B2C 확장으로 사용자 지정 정책 편집 | [Visual Studio Code Marketplace에서](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)Visual Studio Code 및이 커뮤니티에서 빌드된 확장을 다운로드 합니다. 공식 Microsoft 제품이 아니라 Visual Studio Code Azure AD B2C 확장에는 사용자 지정 정책 작업을 용이 하 게 하는 데 도움이 되는 몇 가지 기능이 포함 되어 있습니다. |
| Azure AD B2C 문제를 해결 하는 방법 알아보기 | 개발 하는 동안 [사용자 지정 정책의 문제를 해결](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) 하는 방법을 알아봅니다. 일반 인증 흐름이 어떻게 표시 되는지 알아보고 비정상 및 오류 검색을 위한 도구를 사용 합니다. 예를 들어 [Application Insights](troubleshoot-with-application-insights.md) 를 사용 하 여 사용자 경험의 출력 로그를 검토할 수 있습니다. |
| 입증 된 사용자 지정 정책 패턴의 라이브러리 활용 | 몇 가지 향상 된 Azure AD B2C CIAM (고객 id 및 액세스 관리) 사용자 경험에 대 한 [샘플](https://github.com/azure-ad-b2c/samples) 을 찾습니다. |


## <a name="testing"></a>테스트

Azure AD B2C 구현을 테스트 하 고 자동화 합니다.

| 모범 사례 | 설명 |
|--|--|
| 글로벌 트래픽 계정 | 다른 글로벌 주소의 트래픽 소스를 사용 하 여 성능 및 지역화 요구 사항을 테스트 합니다. 모든 HTMLs, CSS 및 종속성이 성능 요구를 충족할 수 있는지 확인 합니다. |
| 기능 및 UI 테스트 | 종단 간 사용자 흐름을 테스트 합니다. Selenium, VS 웹 테스트 등을 사용 하 여 몇 분 마다 가상 테스트를 추가 합니다. |
| 펜-테스트 | 솔루션과 함께 사용 하기 전에 침투 테스트 연습을 수행 하 여 모든 구성 요소가 안전 하 게 모든 타사 종속성을 포함 하 고 있는지 확인 합니다. 액세스 토큰을 사용 하 여 Api를 보호 하 고 응용 프로그램 시나리오에 올바른 인증 프로토콜을 사용 했는지 확인 합니다. [침투 테스트](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) 에 대해 자세히 알아보고 [Engagement의 통합 침투 테스트 규칙을 Microsoft 클라우드](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)합니다. |
| A/B 테스트 | 전체 모집단으로 롤아웃 하기 전에 소수의 사용자 집합을 사용 하 여 새로운 기능을 비행 합니다. Azure AD B2C에서 JavaScript를 사용 하도록 설정 하면 Optimizely, 명확성 및 기타와 같은 A/B 테스트 도구와 통합할 수 있습니다. |
| 부하 테스트 | Azure AD B2C 크기를 조정할 수 있지만 모든 종속성이 확장 될 수 있는 경우에만 응용 프로그램을 확장할 수 있습니다. Api 및 CDN을 부하 테스트 합니다. |
| 제한 |  짧은 기간 동안 동일한 원본에서 너무 많은 요청을 전송 하는 경우 제한 트래픽을 Azure AD B2C 합니다. 부하 테스트 중에 여러 트래픽 소스를 사용 하 고 `AADB2C90229` 응용 프로그램에서 오류 코드를 정상적으로 처리 합니다. |
| Automation | CI/CD (지속적인 통합 및 배달) 파이프라인을 사용 하 여 [Azure DevOps](deploy-custom-policies-devops.md)와 같은 테스트 및 배포를 자동화 합니다. |

## <a name="operations"></a>작업

Azure AD B2C 환경을 관리 합니다.

| 모범 사례 | 설명 |
|--|--|
| 여러 환경 만들기 | 작업 및 배포 롤아웃이 용이 하도록 개발, 테스트, 사전 프로덕션 및 프로덕션을 위한 별도의 환경을 만듭니다. 각각에 대해 Azure AD B2C 테 넌 트를 만듭니다. |
| 사용자 지정 정책에 대 한 버전 제어 사용 | Azure AD B2C 사용자 지정 정책에 GitHub, Azure Repos 또는 다른 클라우드 기반 버전 제어 시스템을 사용 하는 것이 좋습니다. |
| Microsoft Graph API를 사용 하 여 B2C 테 넌 트의 관리 자동화 | Microsoft Graph Api:<br/>[Id 경험 프레임 워크](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) 관리 (사용자 지정 정책)<br/>[키](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[사용자 흐름](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Azure DevOps와 통합 | [CI/CD 파이프라인](deploy-custom-policies-devops.md) 을 사용 하면 서로 다른 환경 간에 코드를 쉽게 이동할 수 있으며 항상 프로덕션 준비를 보장할 수 있습니다.   |
| Azure Monitor와 통합 | [감사 로그 이벤트](view-audit-logs.md) 는 7 일 동안 보존 됩니다. [Azure Monitor와 통합](azure-monitor.md) 하 여 장기 사용을 위한 로그를 유지 하거나 타사 siem (보안 정보 및 이벤트 관리) 도구와 통합 하 여 사용자 환경에 대 한 정보를 얻습니다. |
| 활성 경고 및 모니터링 설정 | Application Insights를 사용 하 여 Azure AD B2C에서 [사용자 동작을 추적](active-directory-b2c-custom-guide-eventlogger-appins.md) 합니다. |


## <a name="support-and-status-updates"></a>지원 및 상태 업데이트

서비스의 상태를 확인 하 고 지원 옵션을 확인 하세요.

| 모범 사례 | 설명 |
|--|--|
| [서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Azure AD B2C 제품 업데이트 및 공지를 최신으로 유지 합니다. |
| [Microsoft 지원](support-options.md) | Azure AD B2C 기술 문제에 대 한 지원 요청을 제공 합니다. 청구 및 구독 관리 지원은 무료로 제공됩니다. |
| [Azure 상태](https://status.azure.com/status) | 모든 Azure 서비스의 현재 상태를 확인 합니다. |
