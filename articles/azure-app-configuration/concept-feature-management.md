---
title: Azure 앱 구성 기능 관리
description: Azure App Configuration을 통해 요청에 따라 애플리케이션 기능을 설정하거나 해제하는 방법에 대한 개요입니다.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 85228854f6c106c68cedc3eeea81e15fd662774a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898743"
---
# <a name="feature-management-overview"></a>기능 관리 개요

새 애플리케이션 기능을 제공하려면 일반적으로 애플리케이션 자체를 완전하게 다시 배포해야 합니다. 기능을 테스트하려면 애플리케이션을 여러 번 배포하여 기능이 표시되는 경우와 사용자를 제어해야 합니다.

기능 관리는 기능 릴리스를 코드 배포와 분리하여 요청 시 기능 가용성을 빠르게 변경할 수 있게 하는 최신 소프트웨어 개발 사례입니다. 기능의 수명 주기를 동적으로 관리하기 위해 *기능 플래그*(*기능 토글*, *기능 스위치* 등이라고도 함)라는 기술을 사용합니다.

기능 관리를 통해 개발자는 다음 문제를 처리할 수 있습니다.

* **코드 분기 관리**: 기능 플래그는 개발 중인 새 응용 프로그램 기능을 래핑하는 데 자주 사용 됩니다. 이러한 기능은 기본적으로 "숨겨져" 있습니다. 아직 완성되지 않은 경우에도 기능을 안전하게 제공할 수 있으며, 기능이 프로덕션 환경에서 유휴 상태로 유지됩니다. *은밀 배포*(dark deployment)라고 하는 이 방법을 사용하면 각 개발 주기가 끝날 때 모든 코드를 릴리스할 수 있습니다. 완료할 주기가 둘 이상 남아 있는 기능으로 인해 더 이상 코드 분기를 여러 주기에 걸쳐 유지 관리할 필요가 없습니다.
* **프로덕션에서 테스트**: 기능 플래그를 사용 하 여 프로덕션의 새 기능에 대한 초기 액세스 권한을 부여할 수 있습니다. 예를 들어 이 액세스 권한은 팀 구성원 또는 내부 베타 테스터로만 제한할 수 있습니다. 이러한 사용자는 테스트 환경에서 시뮬레이션된 환경 또는 부분 환경이 아닌 완전한 프로덕션 환경을 사용하게 됩니다.
* **Flighting**: 기능 플래그를 사용 하 여 최종 사용자에 게 새 기능을 점진적으로 롤아웃 할 수도 있습니다. 처음에는 소수의 사용자를 대상으로 시작하고, 이후에 시간이 지나면서 구현에 대한 확신이 생기면 사용자 수를 점진적으로 늘릴 수 있습니다.
* **즉시 중지 스위치**: 기능 플래그는 새로운 기능을 해제 하기 위한 내재 된 보안 네트워크를 제공 합니다. 이를 통해 애플리케이션 기능을 쉽게 설정하거나 해제할 수 있습니다. 필요한 경우 애플리케이션을 다시 빌드하고 다시 배포하지 않고도 기능을 빠르게 사용하지 않도록 설정할 수 있습니다.
* **선택적 활성화**: 대부분의 기능 플래그는 연결 된 기능이 성공적으로 해제 될 때까지 존재 하지만, 일부는 오랜 시간 동안 지속 될 수 있습니다. 기능 플래그를 사용하여 사용자를 분할하고 특정 기능 세트를 각 그룹에 제공할 수 있습니다. 예를 들어 특정 웹 브라우저에서만 작동하는 기능이 있을 수 있습니다. 해당 브라우저의 사용자만 보고 사용할 수 있도록 기능 플래그를 정의할 수 있습니다. 이 방법을 사용하면 나중에 코드를 변경하지 않고도 지원되는 브라우저 목록을 쉽게 확장할 수 있습니다.

## <a name="basic-concepts"></a>기본 개념

기능 관리와 관련된 몇 가지 새로운 용어는 다음과 같습니다.

* **기능 플래그**: 기능 플래그는 *on* 또는 *off*의 이진 상태를 사용 하는 변수입니다. 기능 플래그에는 연결된 코드 블록도 있습니다. 기능 플래그의 상태는 코드 블록의 실행 여부를 트리거합니다.
* **기능 관리자**: 기능 관리자는 응용 프로그램에서 모든 기능 플래그의 수명 주기를 처리 하는 응용 프로그램 패키지입니다. 기능 관리자는 일반적으로 플래그 캐싱 및 상태 업데이트와 같은 추가 기능을 제공합니다.
* **필터**: 필터는 기능 플래그의 상태를 평가 하는 규칙입니다. 사용자 그룹, 디바이스 또는 브라우저 종류, 지리적 위치 및 시간 범위는 모두 필터에서 나타낼 수 있는 항목의 예입니다.

효과적인 기능 관리 구현을 구성하기 위해 협력하여 작동하는 최소한의 두 가지 구성 요소는

* 기능 플래그를 사용하는 애플리케이션 및
* 기능 플래그 및 해당 현재 상태를 저장하는 별도의 리포지토리입니다.

이러한 구성 요소가 상호 작용하는 방법은 다음 예제에서 설명하고 있습니다.

## <a name="feature-flag-usage-in-code"></a>코드에 기능 플래그 사용

애플리케이션에서 기능 플래그를 구현하는 기본 패턴은 간단합니다. 기능 플래그는 코드에서 `if` 조건문에 사용되는 부울 상태 변수로 간주할 수 있습니다.

```csharp
if (featureFlag) {
    // Run the following code
}
```

이 경우 `featureFlag`가 `True`로 설정되면 묶인 코드 블록이 실행되고, 그렇지 않으면 건너뜁니다. 다음 코드 예제와 같이 `featureFlag` 값은 정적으로 설정할 수 있습니다.

```csharp
bool featureFlag = true;
```

다음과 같이 특정 규칙에 따라 플래그 상태를 평가할 수도 있습니다.

```csharp
bool featureFlag = isBetaUser();
```

약간 더 복잡한 기능 플래그 패턴에는 `else` 문도 포함됩니다.

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

그러나 이 동작은 기본 패턴으로 다시 작성할 수 있습니다. [ASP.NET Core 앱에서 기능 플래그 사용](./use-feature-flags-dotnet-core.md)에서는 간단한 코드 패턴에서 표준화하는 경우의 장점을 보여 줍니다. 예:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>기능 플래그 리포지토리

기능 플래그를 효과적으로 사용하려면 애플리케이션에서 사용되는 모든 기능 플래그를 외부화해야 합니다. 이 방법을 사용하면 애플리케이션 자체를 수정 및 다시 배포하지 않고 기능 플래그 상태를 변경할 수 있습니다.

Azure App Configuration은 기능 플래그의 중앙 집중식 리포지토리로 설계되었습니다. 이를 사용하여 다양한 종류의 기능 플래그를 정의하고 해당 상태를 빠르고 자신 있게 조작할 수 있습니다. 그런 다음, 다양한 프로그래밍 언어 프레임워크용 App Configuration 라이브러리를 사용하여 애플리케이션에서 이러한 기능 플래그에 쉽게 액세스할 수 있습니다.

[ASP.NET Core 앱에서 기능 플래그 사용](./use-feature-flags-dotnet-core.md)에서는 .NET Core App Configuration 공급자와 기능 관리 라이브러리를 함께 사용하여 ASP.NET 웹 애플리케이션에 대한 기능 플래그를 구현하는 방법을 보여 줍니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱에 기능 플래그 추가](./quickstart-feature-flag-aspnet-core.md)  
