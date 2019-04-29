---
title: Azure Application Insights 원격 분석 데이터 모델 - 원격 분석 컨텍스트 | Microsoft Docs
description: Application Insights 원격 분석 컨텍스트 데이터 모델
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 7c1f47c9b88bd68b326b3c8923ba5b81d425c3e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900713"
---
# <a name="telemetry-context-application-insights-data-model"></a>원격 분석 컨텍스트: Application Insights 데이터 모델

모든 원격 분석 항목에는 강력한 형식의 컨텍스트 필드가 있을 수 있습니다. 모든 필드는 특정 모니터링 시나리오를 지원합니다. 사용자 지정 속성 컬렉션을 사용하여 사용자 지정 또는 애플리케이션별 컨텍스트 정보를 저장합니다.


## <a name="application-version"></a>애플리케이션 버전

애플리케이션 컨텍스트 필드의 정보는 항상 원격 분석을 전송하는 애플리케이션과 관련이 있습니다. 애플리케이션 버전은 애플리케이션 동작 및 배포 상관 관계의 추세 변경을 분석하는 데 사용됩니다.

최대 길이: 1024


## <a name="client-ip-address"></a>클라이언트 IP 주소

클라이언트 디바이스의 IP 주소입니다. IPv4 및 IPv6이 지원됩니다. 원격 분석이 서비스에서 전송되는 경우 위치 컨텍스트는 서비스에서 작업을 시작한 사용자와 관련이 있습니다. Application Insights는 클라이언트 IP에서 지리적 위치 정보를 추출한 다음 자릅니다. 따라서 클라이언트 IP 자체는 최종 사용자가 식별할 수 있는 정보로 사용될 수 없습니다. 

최대 길이: 46


## <a name="device-type"></a>디바이스 유형

원래 이 필드는 애플리케이션의 최종 사용자가 사용하는 디바이스의 유형을 나타내는 데 사용되었습니다. 현재는 디바이스 유형이 'Browser'인 JavaScript 원격 분석을 디바이스 유형이 'PC'인 서버 쪽 원격 분석과 구별하는 데 주로 사용됩니다.

최대 길이: 64


## <a name="operation-id"></a>작업 ID

루트 작업의 고유 식별자입니다. 이 식별자를 사용하여 여러 구성 요소에서 원격 분석을 그룹화할 수 있습니다. 자세한 내용은 [원격 분석 상관 관계](../../azure-monitor/app/correlation.md)를 참조하세요. 작업 ID는 요청 또는 페이지 보기에서 생성됩니다. 다른 모든 원격 분석은 이 필드를 포함 요청 또는 페이지 보기의 값으로 설정합니다. 

최대 길이: 128


## <a name="parent-operation-id"></a>부모 작업 ID

원격 분석 항목의 직계 부모의 고유 식별자입니다. 자세한 내용은 [원격 분석 상관 관계](../../azure-monitor/app/correlation.md)를 참조하세요.

최대 길이: 128


## <a name="operation-name"></a>작업 이름

작업의 이름(그룹)입니다. 작업 이름은 요청 또는 페이지 보기에서 생성됩니다. 다른 모든 원격 분석 항목은 이 필드를 포함 요청 또는 페이지 보기의 값으로 설정합니다. 작업 이름은 작업 그룹(예를 들어 'GET Home/Index')에 대한 모든 원격 분석 항목을 찾는 데 사용됩니다. 이 컨텍스트 속성은 "이 페이지에서 발생하는 일반적인 예외"와 같은 질문에 답변하는 데 사용됩니다.

최대 길이: 1024


## <a name="synthetic-source-of-the-operation"></a>작업의 가상 원본

가상 원본의 이름입니다. 애플리케이션의 일부 원격 분석은 가상 트래픽을 나타낼 수 있습니다. 이는 Application Insights SDK 자체와 같은 진단 라이브러리에서 웹 사이트, 사이트 가용성 테스트 또는 추적을 인덱싱하는 웹 크롤러일 수 있습니다.

최대 길이: 1024


## <a name="session-id"></a>세션 ID

세션 ID - 응용 프로그램과의 사용자 상호 작용 인스턴스입니다. 세션 컨텍스트 필드의 정보는 항상 최종 사용자와 관련이 있습니다. 원격 분석이 서비스에서 전송되는 경우 서비스 컨텍스트는 서비스에서 작업을 시작한 사용자와 관련이 있습니다.

최대 길이: 64


## <a name="anonymous-user-id"></a>익명 사용자 ID

익명 사용자 ID입니다. 애플리케이션의 최종 사용자를 나타냅니다. 원격 분석이 서비스에서 전송되는 경우 사용자 컨텍스트는 서비스에서 작업을 시작한 사용자와 관련이 있습니다.

[샘플링](../../azure-monitor/app/sampling.md)은 수집된 원격 분석의 양을 최소화하는 기술 중 하나입니다. 샘플링 알고리즘은 상관 관계가 지정된 모든 원격 분석을 샘플링합니다. 익명 사용자 ID는 샘플링 점수 생성에 사용됩니다. 따라서 익명 사용자 ID는 충분히 임의의 값이어야 합니다. 

익명 사용자 ID를 사용하여 사용자 이름을 저장하는 것은 이 필드의 오용입니다. 인증된 사용자 ID를 사용하세요.

최대 길이: 128


## <a name="authenticated-user-id"></a>인증된 사용자 ID

인증된 사용자 ID입니다. 익명 사용자 ID와 반대되는 이 필드는 표시 이름으로 사용자를 나타냅니다. PII 정보이므로 기본적으로 대부분의 SDK에서 수집되지 않습니다.

최대 길이: 1024


## <a name="account-id"></a>계정 ID

다중 테넌트 애플리케이션에서 이는 사용자가 작업하는 데 사용하는 계정 ID 또는 이름입니다. 예를 들어 Azure Portal의 구독 ID 또는 플랫폼을 블로깅하는 블로그 이름일 수 있습니다.

최대 길이: 1024


## <a name="cloud-role"></a>클라우드 역할

애플리케이션이 속해 있는 역할의 이름입니다. Azure에서 역할 이름에 직접 매핑됩니다. 단일 애플리케이션의 일부인 마이크로 서비스를 구별하는 데 사용될 수도 있습니다.

최대 길이: 256


## <a name="cloud-role-instance"></a>클라우드 역할 인스턴스

애플리케이션이 실행되고 있는 인스턴스의 이름입니다. 온-프레미스의 경우 컴퓨터 이름, Azure의 경우 인스턴스 이름입니다.

최대 길이: 256


## <a name="internal-sdk-version"></a>내부: SDK 버전

SDK 버전입니다. 자세한 내용은 https://github.com/Microsoft/ApplicationInsights-Home/blob/master/SDK-AUTHORING.md#sdk-version-specification을 참조하세요.

최대 길이: 64


## <a name="internal-node-name"></a>내부: 노드 이름

이 필드는 요금 청구에 사용되는 노드 이름을 나타냅니다. 이를 사용하여 노드의 표준 검색을 재정의할 수 있습니다.

최대 길이: 256


## <a name="next-steps"></a>다음 단계

- [원격 분석을 확장 및 필터링](../../azure-monitor/app/api-filtering-sampling.md)하는 방법을 알아봅니다.
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](data-model.md)을 참조하세요.
- 표준 컨텍스트 속성 컬렉션 [구성](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)을 확인합니다.
