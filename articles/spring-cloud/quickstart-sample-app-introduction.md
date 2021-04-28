---
title: 빠른 시작 - 샘플 앱 소개 - Azure Spring Cloud
description: Azure Spring Cloud에 배포에 대한 이 빠른 시작 시리즈에 사용되는 샘플 앱을 설명합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 5c3bc83d19eddc448992e2d3fab7287e8d84e43a
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108132722"
---
# <a name="introduction-to-the-sample-app"></a>샘플 앱 소개

::: zone pivot="programming-language-csharp"
이 빠른 시작의 시리즈는 두 개의 마이크로서비스로 구성된 샘플 앱을 사용하여 Azure Spring Cloud 서비스에 .NET Core Steeltoe 앱을 배포하는 방법을 보여 줍니다. 서비스 검색, 구성 서버, 로그, 메트릭 및 분산 추적과 같은 Azure Spring Cloud 기능을 사용합니다.

## <a name="functional-services"></a>기능 서비스

샘플 앱은 다음과 같은 두 개의 마이크로서비스로 구성됩니다.

* `planet-weather-provider` 서비스는 행성 이름을 지정하는 HTTP 요청에 대한 응답으로 날씨 텍스트를 반환합니다. 예를 들어 수성 행성에 대해 "매우 따뜻함"을 반환할 수 있습니다. 구성 서버에서 날씨 데이터를 가져옵니다. 구성 서버는 Git 리포지토리의 YAML 파일에서 날씨 데이터를 가져옵니다. 예를 들면 다음과 같습니다.

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* `solar-system-weather` 서비스는 HTTP 요청에 대한 응답으로 4개의 행성에 대한 데이터를 반환합니다. `planet-weather-provider`에 대한 4 개의 HTTP 요청을 만들어 데이터를 가져옵니다. Eureka 서버 검색 서비스를 사용하여 `planet-weather-provider`를 호출합니다. JSON을 반환합니다. 예를 들면 다음과 같습니다.

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

다음 다이어그램은 샘플 앱 아키텍처를 보여 줍니다.

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="샘플 앱 다이어그램":::

## <a name="code-repository"></a>코드 리포지토리

샘플 앱은 GitHub의 Azure-Samples/Azure-Spring-Cloud-Samples 리포지토리의 [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) 폴더에 있습니다.

다음 빠른 시작의 지침은 필요한 경우 소스 코드를 참조합니다.

::: zone-end

::: zone pivot="programming-language-java"
이 빠른 시작에서는 PiggyMetrics라는 개인 재무 샘플을 사용하여 Azure Spring Cloud 서비스에 앱을 배포하는 방법을 보여 줍니다. PiggyMetrics는 마이크로서비스 아키텍처 패턴을 설명하고 서비스 내역을 강조 표시합니다. 서비스 검색, 구성 서버, 로그, 메트릭 및 분산 추적을 비롯하여 강력한 Azure Spring Cloud 기능을 사용하여 Azure에 배포하는 방법을 확인할 수 있습니다.

Azure Spring Cloud 배포 예제를 따르려면 필요에 따라 제공되는 소스 코드의 위치만 필요합니다.

## <a name="functional-services"></a>기능 서비스

PiggyMetrics는 세 가지 핵심 마이크로서비스로 분해됩니다. 이러한 모든 항목은 비즈니스 도메인별로 구성된 독립적으로 배포 가능한 애플리케이션입니다.

* **계정 서비스(배포 예정)** : 일반 사용자 입력 논리 및 유효성 검사(소득/지출 항목, 절감액 및 계정 설정)를 포함합니다.
* **통계 서비스(이 빠른 시작에서 사용되지 않음)** : 주요 통계 매개 변수에 대한 계산을 수행하고 각 계정에 대한 시계열을 캡처합니다. 데이터 요소에는 기본 통화와 기간으로 정규화된 값이 포함됩니다. 이 데이터는 계정 수명에서 현금 흐름 역학을 추적하는 데 사용됩니다.
* **알림 서비스(이 빠른 시작에서 사용되지 않음)** : 사용자 연락처 정보 및 알림 설정(예: 알림 및 백업 빈도)을 저장합니다. 예약된 작업자는 다른 서비스에서 필요한 정보를 수집하고 구독된 고객에게 이메일 메시지를 보냅니다.

## <a name="infrastructure-services"></a>인프라 서비스

분산 시스템에는 핵심 서비스를 작동하는 데 도움이 되는 몇 가지 일반적인 패턴이 있습니다. Azure Spring Cloud는 이러한 패턴을 구현하는 Spring Boot 애플리케이션 동작을 향상시키는 강력한 도구를 제공합니다. 

* **구성 서비스(Azure Spring Cloud에서 호스트됨)** : Azure Spring Cloud 구성은 분산 시스템에 대한 수평 확장 가능한 중앙 집중식 구성 서비스입니다. 현재 로컬 스토리지, Git 및 Subversion을 지원하는 플러그형 리포지토리를 사용합니다.
* **서비스 검색(Azure Spring Cloud에서 호스트됨)** : 자동 크기 조정, 오류 및 업그레이드로 인해 주소가 동적으로 할당될 수 있는 서비스 인스턴스에 대한 네트워크 위치의 자동 검색을 허용합니다.
* **인증 서비스(배포 예정)** 권한 부여 책임이 개별 서버에 완전히 추출되어 백 엔드 리소스 서비스에 대한 OAuth2 토큰을 부여합니다. 인증 서버는 사용자 권한 부여를 수행하고 경계 내에서 머신 간 통신을 보호합니다.
* **API 게이트웨이(배포 예정)** : 세 가지 핵심 서비스는 외부 API를 클라이언트에 노출합니다. 실제 시스템에서 함수 수는 시스템 복잡성으로 인해 매우 빠르게 증가할 수 있습니다. 수백 개의 서비스는 복잡한 웹 페이지 하나를 렌더링하는 데 사용할 수 있습니다. API 게이트웨이는 요청을 처리하고 적절한 백 엔드 서비스로 라우트하거나 여러 백 엔드 서비스를 호출하여 결과를 집계하는 데 사용되는 시스템의 단일 진입점입니다. 

## <a name="sample-usage-of-piggymetrics"></a>PiggyMetrics의 샘플 사용

전체 구현에 대한 자세한 내용은 [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics)를 참조하세요. 샘플은 필요한 경우 소스 코드를 참조합니다.
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Spring Cloud 인스턴스 프로비저닝](./quickstart-provision-service-instance.md)