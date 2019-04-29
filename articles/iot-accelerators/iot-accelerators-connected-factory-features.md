---
title: 연결된 팩터리 솔루션 특징 - Azure | Microsoft Docs
description: 미리 구성된 솔루션인 연결된 팩터리의 특징을 간략하게 살펴봅니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 25812e10b4c2b90aa4f40c38610c30a26ab11d9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450011"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>연결된 팩터리 IoT 솔루션 가속기란?

연결된 팩터리는 Microsoft의 Azure Industrial IoT 참조 아키텍처를 구현한 것으로, 오픈 소스 솔루션으로 패키징됩니다. 연결된 팩터리는 상용 제품 개발을 위한 시작점으로 사용할 수 있습니다. 미리 빌드된 버전의 연결된 팩터리 솔루션을 [Azure IoT Solution Accelerator](https://www.azureiotsolutions.com/#solutions/types/CF)에서 Azure 구독에 배포할 수 있습니다.

![연결된 팩터리 솔루션 대시보드](./media/iot-accelerators-connected-factory-features/dashboard.png)

연결된 팩터리 솔루션 가속기 [코드는 GitHub에서 사용할 수 있습니다](https://github.com/Azure/azure-iot-connected-factory).

연결된 팩터리의 특징은 다음과 같습니다.

## <a name="industrial-device-interoperability"></a>산업용 디바이스 상호 운용성

- 산업 자산을 OPC UA 인터페이스에 연결합니다.
- 시뮬레이션된 생산 라인(Docker 컨테이너에서 OPC UA 서버를 실행하는)을 사용하여 원격 분석을 봅니다.
- 클라우드 대시보드에서 OPC UA 서버의 OPC UA 정보 모델을 찾습니다.

## <a name="remote-management"></a>원격 관리

- 클라우드 대시보드에서 OPC UA 자산을 구성합니다(메서드 호출, 데이터 읽기 및 쓰기).
- 클라우드 대시보드의 OPC UA 자산에서 원격 분석 데이터를 게시 및 게시 취소합니다.

## <a name="cloud-dashboard"></a>클라우드 대시보드

- 클라우드 대시보드에서 바로 원격 분석 미리 보기를 봅니다.
- Time Series Insights 탐색기 대시보드를 사용하여 원격 분석 데이터의 추세를 살펴보고 상관 관계를 만듭니다.
- 클라우드 대시보드에서 계산된 OEE(설비종합효율) 및 KPI(핵심 성과 지표)를 봅니다.
- 트리 토폴로지 및 대화형 맵에서 산업 자산 계층을 봅니다.
- 클라우드 대시보드에서 경고를 보고, 확인하고, 닫습니다.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md)는 대량의 시계열 데이터를 저장하고, 시각화하고, 쿼리하기 위해 구축되었습니다. 연결된 팩터리는 이 서비스를 활용합니다.
- 연결된 팩터리는 이 서비스와 통합되어 디바이스 데이터의 실시간 심층 분석을 가능하게 합니다.

## <a name="rules-and-alerts"></a>규칙 및 경고

[경고의 임계값 기반 규칙을 구성합니다](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>통합형 보안

- RBAC(역할 기반 Access Control)를 사용하여 사용자에 대한 보안 권한을 구성합니다.
- OPC UA 인증(X.509 인증서 사용) 및 보안 토큰을 사용하여 통합형 암호화를 구현합니다.

## <a name="customizability"></a>사용자 지정 가능

- 특정 비즈니스 요구 사항을 충족하도록 솔루션을 사용자 지정할 수 있습니다.
- GitHub에서 전체 솔루션 소스 코드를 제공합니다. [연결된 팩터리 미리 구성된 솔루션](https://github.com/Azure/azure-iot-connected-factory) 리포지토리를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 연결된 팩터리 미리 구성된 솔루션에 대해 자세히 알아봅니다.

* [연결된 팩터리 미리 구성된 솔루션 연습](iot-accelerators-connected-factory-sample-walkthrough.md)
* [연결된 팩터리에 대한 게이트웨이 배포]( iot-accelerators-connected-factory-gateway-deployment.md)
