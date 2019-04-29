---
title: Azure Application Insights의 분산 추적 | Microsoft Docs
description: OpenCensus 프로젝트에서 현지 전달자 및 파트너 관계를 통해 분산 추적에 대한 Microsoft의 지원 정보를 제공합니다.
services: application-insights
keywords: ''
author: nikmd23
ms.author: nimolnar
ms.reviewer: mbullwin
ms.date: 09/17/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7bc04748f2a5b8caa8f589140dd46f0650b7b390
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898850"
---
# <a name="what-is-distributed-tracing"></a>분산 추적이란?

최신 클라우드 및 [마이크로 서비스](https://azure.com/microservices) 아키텍처가 등장하면서 비용을 절감하면서 가용성과 처리량을 높일 수 있는 독립적으로 배포할 수 있는 간단한 서비스가 생겨났습니다. 이러한 움직임으로 인해 개별 서비스를 전반적으로 이해하기는 쉬워졌지만 전체 시스템을 추론하고 디버그하기가 더 어려워졌습니다.

모놀리식 아키텍처에서는 호출 스택을 사용하여 디버깅하는 데 익숙해져 왔습니다. 호출 스택은 각 호출에 대한 세부 정보 및 매개 변수와 함께 실행 흐름(메서드 A가 메서드 B를 호출하고 이로 인해 메서드 C가 호출됨)을 표시하기에 훌륭한 도구입니다. 이런 방식은 단일 프로세스에서 실행되는 단일 조직이나 서비스에는 적합하지만 호출이 로컬 스택의 참조가 아닌 프로세스 경계를 넘어서는 경우에는 어떻게 디버그해야 할까요? 

바로 이런 경우 분산 추적이 사용됩니다.  

분산 추적은 최신 클라우드 및 마이크로 서비스 아키텍처의 호출 스택과 동일하며 단순한 성능 프로파일러가 추가되었습니다. Azure Monitor에는 분산 추적 데이터를 사용하는 두 가지 환경이 제공됩니다. 첫째는 [트랜잭션 진단](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) 보기입니다. 이것은 호출 스택에 시간 차원이 추가된 것과 동일합니다. 트랜잭션 진단 보기는 단일 트랜잭션/요청에 대한 가시성을 제공하며 요청 단위를 기준으로 안정성 문제와 성능 병목 현상의 근본 원인을 찾는 데 유용합니다.

Azure Monitor에는 [애플리케이션 맵](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) 보기도 제공됩니다. 여기에는 다수의 트랜잭션을 집계하여 시스템이 어떻게 상호 작용하는지에 대한 토폴로지 보기가 표시되고 평균 성능 및 오류율이 얼마인지를 보여줍니다. 

## <a name="how-to-enable-distributed-tracing"></a>분산 추적을 사용하는 방법

애플리케이션에서 서비스 전반에 걸쳐 분산 추적을 사용하는 것은 서비스가 구현된 언어를 기반으로 각 서비스에 적절한 SDK 또는 라이브러리를 추가하는 것만큼 간단합니다.

## <a name="enabling-via-application-insights-sdks"></a>Application Insights SDK를 통해 사용하도록 설정

.NET, .NET Core, Java, Node.js 및 JavaScript용 Application Insights SDK는 모두 기본적으로 분산 추적을 지원합니다. 각 Application Insights SDK를 설치 및 구성하는 지침이 다음과 같이 제공됩니다.

* [.NET](https://docs.microsoft.com/azure/application-insights/quick-monitor-portal)
* [.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-dotnetcore-quick-start)
* [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-get-started)
* [Node.JS](https://docs.microsoft.com/azure/application-insights/app-insights-nodejs-quick-start)
* [JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript)

적절한 Application Insight SDK가 설치되고 구성되면, 많이 사용되는 프레임워크, 라이브러리 및 기술에 대한 추적 정보가 SDK 종속성 자동 수집기를 통해 자동으로 수집됩니다. 지원되는 기술의 전체 목록은 [종속성 자동 수집 설명서](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)에 제공됩니다.

 또한 [TelemetryClient](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)의 [TrackDependency](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)를 호출하여 원하는 기술을 수동으로 추적할 수 있습니다.

## <a name="enable-via-opencensus"></a>OpenCensus를 통해 사용하도록 설정

Application Insights SDK 외에, Application Insights는 [OpenCensus](https://opencensus.io/)를 통해서도 분산 추적을 지원합니다. OpenCensus는 오픈 소스, 벤더에 구애받지 않는, 라이브러리의 단일 배포판이며 서비스에 대한 메트릭 컬렉션 및 분산 추적 기능을 제공합니다. 또한 오픈 소스 커뮤니티에서 Redis, Memcached 또는 MongoDB와 같이 널리 사용되는 기술을 통해 분산 추적이 가능합니다. [Microsoft는 여러 다른 모니터링 및 클라우드 파트너와 OpenCensus에 대해 협력하고 있습니다](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

OpenCensus를 사용하여 애플리케이션에 분산 추적 기능을 추가하려면 먼저 [Application Insights 로컬 전달자를 설치 및 구성](./../../azure-monitor/app/opencensus-local-forwarder.md)합니다. 여기에서 로컬 전달자를 통해 분산 추적 데이터를 라우팅하도록 OpenCensus를 구성합니다. [Python](./../../azure-monitor/app/opencensus-python.md)과 [Go](./../../azure-monitor/app/opencensus-go.md)가 둘 다 지원됩니다.

OpenCensus 웹 사이트에는 [Python](https://opencensus.io/api/python/trace/usage.html)과 [Go](https://godoc.org/go.opencensus.io)에 대한 API 참조 설명서는 물론 OpenCensus 사용을 위한 다양한 가이드가 있습니다. 

## <a name="next-steps"></a>다음 단계

* [OpenCensus Python 사용 가이드](https://opencensus.io/api/python/trace/usage.html)
* [애플리케이션 맵](./../../azure-monitor/app/app-map.md)
* [종단 간 성능 모니터링](./../../azure-monitor/learn/tutorial-performance.md)
