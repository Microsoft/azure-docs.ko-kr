---
title: Azure 앱 구성 키-값 저장소 이해
description: 구성 데이터를 키 값으로 저장 하는 Azure 앱 구성의 키-값 저장소를 이해 합니다. 키-값은 응용 프로그램 설정에 대 한 표현입니다.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: b1998532c3d9e4272d91280d57d9ea2f6e7a262c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586395"
---
# <a name="keys-and-values"></a>키 및 값

Azure 앱 구성은 구성 데이터를 키 값으로 저장 합니다. 키-값은 개발자가 사용 하는 응용 프로그램 설정을 간단 하 고 유연 하 게 표현한 것입니다.

## <a name="keys"></a>구성

키는 키 값에 대 한 식별자 역할을 하며 해당 값을 저장 하 고 검색 하는 데 사용 됩니다. `/` 또는 `:`과 같은 문자 구분 기호를 사용하여 키를 계층 구조 네임스페이스에 구성하는 일반적인 방식입니다. 응용 프로그램에 가장 적합 한 규칙을 사용 합니다. App Configuration은 전체적으로 키를 처리합니다. 키를 구문 분석하여 키 이름을 구성하는 방법을 파악하거나 규칙을 적용하지 않습니다.

다음은 구성 요소 서비스에 따라 계층 구조로 구조화 된 키 이름의 예입니다.

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

응용 프로그램 프레임 워크 내에서 구성 데이터를 사용 하는 경우 키 값에 대 한 특정 명명 스키마를 지시할 수 있습니다. 예를 들어 Java의 스프링 클라우드 프레임 워크는 `Environment` 스프링 응용 프로그램에 설정을 제공 하는 리소스를 정의 합니다.  이러한 매개 변수는 *응용 프로그램 이름* 및 *프로필*을 포함 하는 변수에 의해 매개 변수화 됩니다. Spring Cloud 관련 구성 데이터에 대한 키는 일반적으로 구분 기호로 구분된 이러한 두 가지 요소로 시작합니다.

App Configuration에 저장된 키는 대/소문자를 구분하는 유니코드 기반의 문자열입니다. *app1* 및 *App1* 키는 App Configuration 저장소에서 고유합니다. 일부 프레임워크에서 구성 키의 대/소문자를 구분하지 않고 처리하므로 애플리케이션 내에서 구성 설정을 사용하는 경우 이 점에 유념하세요. 키를 구분 하는 데 대/소문자를 사용 하지 않는 것이 좋습니다.

을 제외 하 고 키 이름에 임의의 유니코드 문자를 사용할 수 있습니다 `%` . 키 이름은 이거나 일 수 `.` 없습니다 `..` . 키-값에는 10mbps의 결합 된 크기 제한이 있습니다. 이 제한에는 키의 모든 문자, 해당 값 및 모든 연결된 선택적 특성이 포함됩니다. 이 제한 내에 키의 여러 계층 구조 수준이 있을 수 있습니다.

### <a name="design-key-namespaces"></a>키 네임스페이스 디자인

구성 데이터에 사용되는 일반적인 키 이름 지정 방법에는 단일 구조 및 계층 구조 두 가지가 있습니다. 이러한 방법은 애플리케이션 사용 관점과 비슷하지만 계층 구조 명명에는 다음과 같은 여러 장점이 있습니다.

* 읽기가 쉬워집니다. 계층 키 이름에 있는 구분 기호는 문장에서 공백으로 작동 합니다. 또한 단어 간을 자연스럽게 구분합니다.
* 관리가 쉬워집니다. 키 이름 계층 구조는 구성 데이터의 논리 그룹을 나타냅니다.
* 사용이 쉬워집니다. 계층 구조의 키와 패턴이 일치하고 구성 데이터의 일부만 검색하는 쿼리를 작성하는 것이 쉽습니다. 또한 최신의 여러 프로그래밍 프레임워크는 애플리케이션에서 특정 구성 세트를 사용할 수 있도록 계층 구조 구성 데이터를 기본적으로 지원합니다.

다양한 방식의 계층 구조적으로 App Configuration에서 키를 구성할 수 있습니다. 이러한 키를 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)라고 합니다. 각 계층 구조 키는 구분 기호로 결합된 하나 이상의 구성 요소로 구성된 리소스 *경로*입니다. 애플리케이션, 프로그래밍 언어 또는 프레임워크의 필요에 따라 구분 기호로 사용할 문자를 선택합니다. App Configuration에서는 서로 다른 키에 대해 여러 구분 기호를 사용합니다.

### <a name="label-keys"></a>레이블 키

App Configuration의 키 값은 레이블 특성을 선택할 수 있습니다. 레이블은 동일한 키를 사용하여 키 값을 구분하는 데 사용됩니다. *A* 및 *B* 레이블이 지정된 *app1* 키는 App Configuration 저장소에서 별도의 키 2개를 구성합니다. 기본적으로 키-값에는 레이블이 없습니다. 레이블 없이 키-값을 명시적으로 참조 하려면 `\0` (URL 인코딩)를 사용 `%00` 합니다.

레이블은 키의 변형을 만드는 편리한 방법을 제공합니다. 레이블의 일반적인 용도는 같은 키에 여러 환경을 지정하는 것입니다.

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>버전 키-값

여러 버전의 키 값을 만드는 방법으로 레이블을 사용 합니다. 예를 들어 특정 소프트웨어 빌드와 연결된 키 값을 확인하려면 레이블에 애플리케이션 버전 번호 또는 Git 커밋 ID를 입력할 수 있습니다.

> [!NOTE]
> 변경 버전을 찾고 있는 경우에는 앱 구성이 지난 특정 기간 동안 발생 한 키 값의 모든 변경 내용을 자동으로 유지 합니다. 자세한 내용은 특정 [시점 스냅숏](./concept-point-time-snapshot.md) 을 참조 하세요.

### <a name="query-key-values"></a>쿼리 키-값

각 키 값은 `\0`일 수 있는 레이블 및 해당 키로만 식별됩니다. 패턴을 지정 하 여 응용 프로그램 구성 저장소에서 키 값을 쿼리 합니다. 앱 구성 저장소는 해당 값과 특성을 포함 하 여 패턴과 일치 하는 모든 키 값을 반환 합니다. App Configuration에 대한 REST API 호출에서 사용하는 키 패턴은 다음과 같습니다.

| 키 | Description |
|---|---|
| `key`은 생략 또는 `key=*` | 모든 키와 일치 |
| `key=abc` | **abc** 키 이름과 정확하게 일치 |
| `key=abc*` | **abc**로 시작하는 키 이름과 일치 |
| `key=abc,xyz` | 키 이름 **abc** 또는 **xyz**와 일치 합니다. 5 Csv으로 제한 됨 |

또한 포함할 수 있는 레이블 패턴은 다음과 같습니다.

| 레이블 | Description |
|---|---|
| `label`은 생략 또는 `label=*` | `\0`을 포함하는 모든 레이블과 일치 |
| `label=%00` | `\0` 레이블과 일치 |
| `label=1.0.0` | 레이블 **1.0.0**과 정확하게 일치 |
| `label=1.0.*` | **1.0**으로 시작하는 레이블과 일치합니다. |
| `label=%00,1.0.0` | `\0` 또는 **1.0.0** 레이블과 일치, 5개의 CSV로 제한 |

> [!NOTE]
> `*`, `,` 및 `\` 는 쿼리에서 예약 된 문자입니다. 키 이름이 나 레이블에서 예약 문자를 사용 하는 경우에는 쿼리에서를 사용 하 여 해당 문자를 이스케이프 해야 합니다 `\{Reserved Character}` .

## <a name="values"></a>값

키에 할당 된 값도 유니코드 문자열입니다. 값에 모든 유니코드 문자를 사용할 수 있습니다.

### <a name="use-content-type"></a>Content-type 사용
앱 구성의 각 키 값에는 content-type 특성이 있습니다. 필요에 따라이 특성을 사용 하 여 응용 프로그램에서 적절 하 게 처리 하는 데 도움이 되는 키-값의 값 형식에 대 한 정보를 저장할 수 있습니다. Content-type에 원하는 형식을 사용할 수 있습니다. 앱 구성에서는 기능 플래그, Key Vault 참조 및 JSON 키 값과 같은 기본 제공 데이터 형식에 대해 [미디어 형식]( https://www.iana.org/assignments/media-types/media-types.xhtml) (MIME 형식이 라고도 함)을 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [지정 시간 스냅샷](./concept-point-time-snapshot.md)
* [기능 관리](./concept-feature-management.md)
* [이벤트 처리](./concept-app-configuration-event.md)
