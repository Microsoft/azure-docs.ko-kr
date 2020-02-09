---
title: Azure 앱 구성 키-값 저장소
description: 구성 데이터가 Azure 앱 구성에 저장 되는 방식에 대한 개요입니다.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: fbb30b0a290011a5edfb05c1de9b5d4717a5f733
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898698"
---
# <a name="keys-and-values"></a>키 및 값

Azure App Configuration은 구성 데이터를 키-값 쌍으로 저장합니다. Azure App Configuration은 개발자에게 익숙한 다양한 종류의 애플리케이션 설정을 나타내는 간단하면서도 유연한 방법입니다.

## <a name="keys"></a>구성

키는 키-값 쌍의 이름 역할을 하며 해당 값을 저장 및 검색하는 데 사용됩니다. `/` 또는 `:`과 같은 문자 구분 기호를 사용하여 키를 계층 구조 네임스페이스에 구성하는 일반적인 방식입니다. 애플리케이션에 가장 적합한 규칙을 사용합니다. App Configuration은 전체적으로 키를 처리합니다. 키를 구문 분석하여 키 이름을 구성하는 방법을 파악하거나 규칙을 적용하지 않습니다.

애플리케이션 프레임워크 내에서 구성 데이터를 사용하면 키 값에 대한 특정 명명 체계를 지정할 수 있습니다. 예를 들어 Java의 Spring Cloud 프레임워크는 *애플리케이션 이름*과 *프로필*을 포함한 변수로 매개 변수화되도록 Spring 애플리케이션에 설정을 제공하는 `Environment` 리소스를 정의합니다. Spring Cloud 관련 구성 데이터에 대한 키는 일반적으로 구분 기호로 구분된 이러한 두 가지 요소로 시작합니다.

App Configuration에 저장된 키는 대/소문자를 구분하는 유니코드 기반의 문자열입니다. *app1* 및 *App1* 키는 App Configuration 저장소에서 고유합니다. 일부 프레임워크에서 구성 키의 대/소문자를 구분하지 않고 처리하므로 애플리케이션 내에서 구성 설정을 사용하는 경우 이 점에 유념하세요. 예를 들어 ASP.NET Core 구성 시스템에서는 대/소문자를 구분하지 않는 문자열로 키를 처리합니다. ASP.NET Core 애플리케이션 내에서 App Configuration을 쿼리할 때 예기치 않은 동작을 방지하려면 대/소문자만 다른 키를 사용하지 마세요.

App Configuration에 입력되는 키 이름에는 `*`, `,` 및 `\`를 제외한 모든 유니코드 문자를 사용할 수 있습니다. 이러한 문자는 예약되어 있습니다. 예약된 문자를 포함해야 하는 경우 `\{Reserved Character}`를 사용하여 해당 문자를 이스케이프해야 합니다. 키-값 쌍에는 10,000개 문자의 결합된 크기 제한이 있습니다. 이 제한에는 키의 모든 문자, 해당 값 및 모든 연결된 선택적 특성이 포함됩니다. 이 제한 내에 키의 여러 계층 구조 수준이 있을 수 있습니다.

### <a name="design-key-namespaces"></a>키 네임스페이스 디자인

구성 데이터에 사용되는 일반적인 키 이름 지정 방법에는 단일 구조 및 계층 구조 두 가지가 있습니다. 이러한 방법은 애플리케이션 사용 관점과 비슷하지만 계층 구조 명명에는 다음과 같은 여러 장점이 있습니다.

* 읽기가 쉬워집니다. 하나의 긴 시퀀스 문자 대신 계층 구조 키 이름에서 구분 기호는 문장의 공백으로 기능합니다. 또한 단어 간을 자연스럽게 구분합니다.
* 관리가 쉬워집니다. 키 이름 계층 구조는 구성 데이터의 논리 그룹을 나타냅니다.
* 사용이 쉬워집니다. 계층 구조의 키와 패턴이 일치하고 구성 데이터의 일부만 검색하는 쿼리를 작성하는 것이 쉽습니다. 또한 최신의 여러 프로그래밍 프레임워크는 애플리케이션에서 특정 구성 세트를 사용할 수 있도록 계층 구조 구성 데이터를 기본적으로 지원합니다.

다양한 방식의 계층 구조적으로 App Configuration에서 키를 구성할 수 있습니다. 이러한 키를 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)라고 합니다. 각 계층 구조 키는 구분 기호로 결합된 하나 이상의 구성 요소로 구성된 리소스 *경로*입니다. 애플리케이션, 프로그래밍 언어 또는 프레임워크의 필요에 따라 구분 기호로 사용할 문자를 선택합니다. App Configuration에서는 서로 다른 키에 대해 여러 구분 기호를 사용합니다.

키 이름을 계층 구조로 구성할 수 있는 몇 가지 방법의 예는 다음과 같습니다.

* 구성 요소 서비스에 따라

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* 배포 지역에 따라

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>레이블 키

App Configuration의 키 값에는 필요에 따라 레이블 특성이 있을 수 있습니다. 레이블은 동일한 키를 사용하여 키 값을 구분하는 데 사용됩니다. *A* 및 *B* 레이블이 지정된 *app1* 키는 App Configuration 저장소에서 별도의 키 2개를 구성합니다. 기본적으로 키 값에 대한 레이블은 비어있거나 `null`입니다.

레이블은 키의 변형을 만드는 편리한 방법을 제공합니다. 레이블의 일반적인 용도는 같은 키에 여러 환경을 지정하는 것입니다.

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>키 값 버전 관리

키 값이 수정되면 App Configuration에서 해당 키 값의 버전을 자동으로 관리하지 않습니다. 키 값의 여러 버전을 만드는 방법으로 레이블을 사용합니다. 예를 들어 레이블에 애플리케이션 버전 번호 또는 Git 커밋 ID를 입력하여 특정 소프트웨어 빌드와 연결된 키 값을 식별할 수 있습니다.

레이블에는 `*`, `,` 및 `\`를 제외한 모든 유니코드 문자를 사용할 수 있습니다. 이러한 문자는 예약되어 있습니다. 예약된 문자를 포함하려면 `\{Reserved Character}`를 사용하여 해당 문자를 이스케이프해야 합니다.

### <a name="query-key-values"></a>키 값 쿼리

각 키 값은 해당 키와 `null`일 수 있는 레이블로 고유하게 식별됩니다. 패턴을 지정하여 App Configuration 저장소에 키 값을 쿼리합니다. App Configuration 저장소는 패턴, 해당 값 및 특성과 일치하는 모든 키 값을 반환합니다. App Configuration에 대한 REST API 호출에서 사용하는 키 패턴은 다음과 같습니다.

| 키 | |
|---|---|
| `key`은 생략 또는 `key=*` | 모든 키와 일치 |
| `key=abc` | **abc** 키 이름과 정확하게 일치 |
| `key=abc*` | **abc**로 시작하는 키 이름과 일치 |
| `key=*abc` | **abc**로 끝나는 키 이름과 일치 |
| `key=*abc*` | **abc**를 포함하는 키 이름과 일치 |
| `key=abc,xyz` | **abc** 또는 **xyz** 키 이름과 일치, 5개의 CSV로 제한 |

또한 포함할 수 있는 레이블 패턴은 다음과 같습니다.

| 레이블 | |
|---|---|
| `label`은 생략 또는 `label=*` | `null`을 포함하는 모든 레이블과 일치 |
| `label=%00` | `null` 레이블과 일치 |
| `label=1.0.0` | 레이블 **1.0.0**과 정확하게 일치 |
| `label=1.0.*` | **1.0**으로 시작하는 레이블과 일치합니다. |
| `label=*.0.0` | **.0.0**으로 끝나하는 레이블과 일치 |
| `label=*.0.*` | **.0**을 포함하는 레이블과 일치합니다. |
| `label=%00,1.0.0` | `null` 또는 **1.0.0** 레이블과 일치, 5개의 CSV로 제한 |

## <a name="values"></a>값

키에 할당 된 값도 유니코드 문자열입니다. 값에 모든 유니코드 문자를 사용할 수 있습니다. 각 값과 연결되고 필요에 따라 사용자 정의되는 콘텐츠 형식이 있습니다. 이 특성을 사용하여 애플리케이션에서 제대로 처리하는 데 도움이 되는 값에 대한 정보(예: 인코딩 체계)를 저장합니다.

모든 키와 값을 포함하는 App Configuration 저장소에 저장되는 구성 데이터는 저장 시 또는 전송 중에 암호화됩니다. App Configuration은 Azure Key Vault에 대한 대체 솔루션이 아닙니다. 애플리케이션 비밀은 여기에 저장하지 마세요.

## <a name="next-steps"></a>다음 단계

* [지정 시간 스냅샷](./concept-point-time-snapshot.md)  
* [기능 관리](./concept-feature-management.md)  
