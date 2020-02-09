---
title: Azure 앱 구성 FAQ | Microsoft Docs
description: Azure 앱 구성에 대한 질문과 대답
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904101"
---
# <a name="azure-app-configuration-faq"></a>Azure 앱 구성 FAQ

이 문서에서는 Azure 앱 구성에 대해 자주 묻는 질문을 해결 합니다.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>App Configuration은 Azure Key Vault와 어떻게 다른가요?

앱 구성은 고유한 사용 사례 집합을 위해 설계 되었습니다 .이를 통해 개발자는 응용 프로그램 설정을 관리 하 고 기능 가용성을 제어할 수 있습니다. 복잡 한 구성 데이터로 작업 하는 많은 작업을 간소화 하는 것을 목표로 합니다.

앱 구성에서 다음을 지원 합니다.

- 계층적 네임 스페이스
- 원인은
- 광범위 한 쿼리
- 일괄 처리 검색
- 특수 관리 작업
- 기능 관리 사용자 인터페이스

앱 구성은 Key Vault의 보완 이며, 대부분의 응용 프로그램 배포에서 둘을 함께 사용 해야 합니다.

## <a name="should-i-store-secrets-in-app-configuration"></a>앱 구성에 비밀을 저장 해야 하나요?

앱 구성에서 강화 된 보안을 제공 하지만 Key Vault은 여전히 응용 프로그램 비밀을 저장 하는 가장 좋은 장소입니다. Key Vault 하드웨어 수준 암호화, 세부적인 액세스 정책 및 인증서 회전과 같은 관리 작업을 제공 합니다.

Key Vault에 저장 된 암호를 참조 하는 앱 구성 값을 만들 수 있습니다. 자세한 내용은 [ASP.NET Core 앱에서 Key Vault 참조 사용](./use-key-vault-references-dotnet-core.md)을 참조 하세요.

## <a name="does-app-configuration-encrypt-my-data"></a>앱 구성이 내 데이터를 암호화 하나요?

예. 앱 구성은 보유 하 고 있는 모든 키 값을 암호화 하 고 네트워크 통신을 암호화 합니다. 키 이름은 구성 데이터 검색을 위한 인덱스로 사용 되며 암호화 되지 않습니다.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>앱 구성이 Azure App Service 설정과 어떻게 다른가?

Azure App Service를 사용 하 여 각 App Service 인스턴스에 대한 앱 설정을 정의할 수 있습니다. 이러한 설정은 응용 프로그램 코드에 환경 변수로 전달 됩니다. 원하는 경우 특정 배포 슬롯과 설정을 연결할 수 있습니다. 자세한 내용은 [앱 설정 구성](/azure/app-service/configure-common#configure-app-settings)을 참조 하세요.

반면 Azure 앱 구성을 사용 하면 App Service에서 실행 되는 앱을 포함 하 여 여러 앱 간에 공유할 수 있는 설정을 정의할 수 있습니다. 이러한 설정은 응용 프로그램 코드에서 .NET 및 Java의 구성 공급자를 통해, Azure SDK를 통해 또는 REST Api를 통해 직접 액세스할 수 있습니다.

App Service와 앱 구성 간에 설정을 가져오거나 내보낼 수도 있습니다. 이렇게 하면 기존 App Service 설정에 따라 새 앱 구성 저장소를 신속 하 게 설정 하거나 App Service 설정을 사용 하는 기존 앱과 구성을 쉽게 공유할 수 있습니다.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>앱 구성에 저장 된 키와 값에 대한 크기 제한이 있나요?

단일 키-값 항목에 대해 10KB 제한이 있습니다.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>여러 환경 (테스트, 스테이징, 프로덕션 등)에 대한 구성을 저장 하려면 어떻게 해야 하나요?

현재는 매장 별 앱 구성에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다. 다른 사용 권한이 필요한 각 환경에 대해 별도의 저장소를 사용 합니다. 이 방법은 최상의 보안 격리를 제공 합니다.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>앱 구성을 사용 하기 위해 권장 되는 방법은 무엇 인가요?

[모범 사례](./howto-best-practices.md)를 참조 하세요.

## <a name="how-much-does-app-configuration-cost"></a>앱 구성의 비용은 얼마 인가요?

공개 미리 보기 중에는 서비스를 무료로 사용할 수 있습니다.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>새 릴리스와 앱 구성과 관련 된 기타 정보에 대한 공지는 어떻게 받을 수 있나요?

[GitHub 공지 리포지토리](https://github.com/Azure/AppConfiguration-Announcements)를 구독 합니다.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>문제를 보고 하거나 제안을 제공 하려면 어떻게 해야 하나요?

[GitHub](https://github.com/Azure/AppConfiguration/issues)에서 직접 연락할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 앱 구성 정보](./overview.md)
