---
title: Azure 앱 구성 FAQ
description: Azure Key Vault와 다른 Azure 앱 구성에 대 한 FAQ (질문과 대답)를 참조 하세요.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: b1483230313b9e1b8e59cafea478b14ba0dfcc70
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587347"
---
# <a name="azure-app-configuration-faq"></a>Azure 앱 구성 FAQ

이 문서에서는 Azure 앱 구성에 대해 자주 묻는 질문과 대답을 설명 합니다.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>앱 구성이 Azure Key Vault와 어떻게 다른가?

앱 구성은 개발자가 응용 프로그램 설정을 관리 하 고 기능 가용성을 제어 하는 데 도움이 됩니다. 복잡 한 구성 데이터로 작업 하는 많은 작업을 간소화 하는 것을 목표로 합니다.

앱 구성에서 다음을 지원 합니다.

- 계층 구조 네임스페이스
- 레이블 지정
- 광범위 한 쿼리
- 일괄 처리 검색
- 특수 관리 작업
- 기능 관리 사용자 인터페이스

앱 구성은 Key Vault를 보완 하며, 대부분의 응용 프로그램 배포에서 둘을 함께 사용 해야 합니다.

## <a name="should-i-store-secrets-in-app-configuration"></a>앱 구성에 비밀을 저장 해야 하나요?

앱 구성에서 강화 된 보안을 제공 하지만 Key Vault은 여전히 응용 프로그램 비밀을 저장 하는 가장 좋은 장소입니다. Key Vault 하드웨어 수준 암호화, 세부적인 액세스 정책 및 인증서 회전과 같은 관리 작업을 제공 합니다.

Key Vault에 저장 된 암호를 참조 하는 앱 구성 값을 만들 수 있습니다. 자세한 내용은 [ASP.NET Core 앱에서 Key Vault 참조 사용](./use-key-vault-references-dotnet-core.md)을 참조 하세요.

## <a name="does-app-configuration-encrypt-my-data"></a>앱 구성이 내 데이터를 암호화 하나요?

예. 앱 구성은 보유 하 고 있는 모든 키 값을 암호화 하 고 네트워크 통신을 암호화 합니다. 키 이름과 레이블은 구성 데이터 검색을 위한 인덱스로 사용 되며 암호화 되지 않습니다.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>앱 구성이 Azure App Service 설정과 어떻게 다른가?

Azure App Service를 사용 하 여 각 App Service 인스턴스에 대 한 앱 설정을 정의할 수 있습니다. 이러한 설정은 응용 프로그램 코드에 환경 변수로 전달 됩니다. 원하는 경우 특정 배포 슬롯에 설정을 연결할 수 있습니다. 자세한 내용은 [앱 설정 구성](/azure/app-service/configure-common#configure-app-settings)을 참조 하세요.

반면 Azure 앱 구성을 사용 하면 여러 앱 간에 공유할 수 있는 설정을 정의할 수 있습니다. 여기에는 App Service에서 실행 되는 앱 및 기타 플랫폼이 포함 됩니다. 응용 프로그램 코드는 Azure SDK를 통해 또는 REST Api를 통해 직접 .NET 및 Java 용 구성 공급자를 통해 이러한 설정에 액세스 합니다.

App Service와 앱 구성 간에 설정을 가져오거나 내보낼 수도 있습니다. 이 기능을 사용 하면 기존 App Service 설정에 따라 새 앱 구성 저장소를 신속 하 게 설정할 수 있습니다. App Service 설정을 사용 하는 기존 앱과 구성을 공유할 수도 있습니다.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>앱 구성에 저장 된 키와 값에 대 한 크기 제한이 있나요?

단일 키-값 항목에는 10kb의 제한이 있습니다.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>여러 환경 (테스트, 스테이징, 프로덕션 등)에 대 한 구성을 저장 하려면 어떻게 해야 하나요?

매장 별 앱 구성에 액세스할 수 있는 사용자를 제어 합니다. 다른 사용 권한이 필요한 각 환경에 대해 별도의 저장소를 사용 합니다. 이 방법은 최상의 보안 격리를 제공 합니다.

환경 간에 보안 격리가 필요 하지 않은 경우에는 레이블을 사용 하 여 구성 값을 구분할 수 있습니다. [레이블을 사용 하 여 환경 마다 다른 구성을 사용 하도록 설정 하면](./howto-labels-aspnet-core.md) 전체 예제를 제공 합니다.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>앱 구성을 사용 하기 위해 권장 되는 방법은 무엇 인가요?

[모범 사례](./howto-best-practices.md)를 참조 하세요.

## <a name="how-much-does-app-configuration-cost"></a>앱 구성의 비용은 얼마 인가요?

가격 책정 계층에는 두 가지가 있습니다.

- 무료 계층
- 표준 계층.

표준 계층을 도입 하기 전에 저장소를 만든 경우 일반 공급 시 무료 계층으로 자동으로 이동 됩니다. 표준 계층으로 업그레이드 하거나 무료 계층에 유지 하도록 선택할 수 있습니다.

표준 계층에서 무료 계층으로 저장소를 다운 그레이드할 수 없습니다. 무료 계층에 새 저장소를 만든 다음 해당 저장소로 구성 데이터를 가져올 수 있습니다.

## <a name="which-app-configuration-tier-should-i-use"></a>어떤 앱 구성 계층을 사용 해야 하나요?

두 앱 구성 계층 모두 구성 설정, 기능 플래그, Key Vault 참조, 기본 관리 작업, 메트릭 및 로그를 포함 한 핵심 기능을 제공 합니다.

계층을 선택 하기 위한 고려 사항은 다음과 같습니다.

- **구독 당 리소스**: 리소스는 단일 구성 저장소로 구성 됩니다. 각 구독은 무료 계층의 구성 저장소 하나로 제한 됩니다. 구독은 표준 계층에 무제한의 구성 저장소를 포함할 수 있습니다.
- **리소스 당 저장소**: 무료 계층에서 각 구성 저장소는 10mb의 저장소로 제한 됩니다. 표준 계층에서 각 구성 저장소는 최대 1gb의 저장소를 사용할 수 있습니다.
- **키 기록**: 앱 구성은 키에 대 한 모든 변경 내용에 대 한 기록을 저장 합니다. 무료 계층에서는 7 일 동안이 기록이 저장 됩니다. 표준 계층에서는이 기록이 30 일 동안 저장 됩니다.
- **일별 요청**: 무료 계층 저장소는 하루에 1000 요청으로 제한 됩니다. 저장소가 1000 요청에 도달 하면 UTC 자정 까지의 모든 요청에 대해 HTTP 상태 코드 429이 반환 됩니다.

    표준 계층 저장소의 경우 매일 최초로 20만 요청이 매일 요금으로 포함 됩니다. 추가 요청은 초과분로 청구 됩니다.

- **서비스 수준 계약**: 표준 계층의 SLA는 99.9%입니다. 무료 계층에는 SLA가 없습니다.
- **보안 기능**: 두 계층에는 Microsoft에서 관리 하는 키로 암호화, HMAC 또는 Azure Active Directory를 통한 인증, RBAC 지원 및 관리 되는 id를 비롯 한 기본 보안 기능이 포함 되어 있습니다. 표준 계층은 개인 링크 지원 및 고객 관리 키를 통한 암호화를 비롯 한 고급 보안 기능을 제공 합니다.
- **비용**: 표준 계층 저장소에는 일일 사용 요금이 청구 됩니다. 또한 일일 할당을 지난 요청에 대해서는 초과분 요금이 부과 됩니다. 무료 계층 저장소를 사용 하는 것은 비용이 없습니다.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>무료 계층에서 표준 계층으로 저장소를 업그레이드할 수 있나요? 저장소를 표준 계층에서 무료 계층으로 다운 그레이드할 수 있나요?

언제 든 지 무료 계층에서 표준 계층으로 업그레이드할 수 있습니다.

표준 계층에서 무료 계층으로 저장소를 다운 그레이드할 수 없습니다. 무료 계층에 새 저장소를 만든 다음 [해당 저장소로 구성 데이터를 가져올](howto-import-export-data.md)수 있습니다.

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>앱 구성에 대 한 요청 수에 제한이 있나요?

무료 계층의 구성 저장소는 하루에 1000 요청으로 제한 됩니다. 표준 계층의 구성 저장소는 요청 비율이 시간당 2만 요청을 초과 하는 경우 임시 조정이 발생할 수 있습니다.

저장소가 해당 한도에 도달 하면 기간이 만료 될 때까지 생성 된 모든 요청에 대해 HTTP 상태 코드 429이 반환 됩니다. `retry-after-ms`응답의 헤더는 요청을 다시 시도 하기 전에 제안 된 대기 시간 (밀리초)을 제공 합니다.

응용 프로그램에서 HTTP 상태 코드 429 응답을 정기적으로 실행 하는 경우이를 다시 디자인 하 여 요청 수를 줄이는 것이 좋습니다. 자세한 내용은 [앱 구성에 대 한 요청 축소](./howto-best-practices.md#reduce-requests-made-to-app-configuration) 를 참조 하세요.

## <a name="my-application-receives-http-status-code-429-responses-why"></a>응용 프로그램에서 HTTP 상태 코드 429 응답을 수신 합니다. 그 이유는 무엇일까요?

다음과 같은 상황에서 HTTP 상태 코드 429 응답을 받게 됩니다.

* 무료 계층의 저장소에 대 한 일일 요청 제한을 초과 합니다.
* 표준 계층의 저장소에 대 한 요청 빈도가 높으면 일시적으로 조정 됩니다.
* 과도 한 대역폭 사용.
* 저장소 견적이 초과 될 때 키를 만들거나 수정 하려고 합니다.

429 응답의 본문에서 요청이 실패 한 특정 이유를 확인 합니다.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>새 릴리스와 앱 구성과 관련 된 기타 정보에 대 한 공지는 어떻게 받을 수 있나요?

[GitHub 공지 리포지토리](https://github.com/Azure/AppConfiguration-Announcements)를 구독 합니다.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>문제를 보고 하거나 제안을 제공 하려면 어떻게 해야 하나요?

[GitHub](https://github.com/Azure/AppConfiguration/issues)에서 직접 연락할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure App Configuration 정보](./overview.md)
