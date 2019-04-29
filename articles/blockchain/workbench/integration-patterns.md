---
title: Azure Blockchain Workbench의 스마트 계약 통합 패턴
description: Azure Blockchain Workbench의 스마트 계약 통합 패턴 개요입니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 11e0e1436e3f640c30fec5e8d6fd9ca10adbd707
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60867693"
---
# <a name="smart-contract-integration-patterns"></a>스마트 계약 통합 패턴

스마트 계약은 외부 시스템 및 디바이스와 통합해야 하는 비즈니스 워크플로를 나타내는 경우가 많습니다.

이러한 워크플로의 요구 사항 중에는 외부 시스템, 서비스 또는 디바이스의 데이터가 포함된 분산 원장에서 트랜잭션을 시작해야 할 필요가 있습니다. 또한 외부 시스템이 분산 원장의 스마트 계약에서 나온 이벤트에 반응해야 합니다.

REST API와 메시징을 통합하면 외부 시스템에서 Azure Blockchain Workbench 애플리케이션에 포함된 스마트 계약으로 트랜잭션이 전송됩니다. 또한 애플리케이션 내에서 수행되는 변경을 기준으로 하여 외부 시스템으로 이벤트 알림도 전송됩니다.

데이터 통합 ​​시나리오의 경우 Azure Blockchain Workbench에는 애플리케이션 및 스마트 계약에 대한 블록체인의 트랜잭션 데이터와 메타데이터의 조합을 병합하는 데이터베이스 뷰 집합이 포함되어 있습니다.

또한 공급망이나 미디어와 관련된 시나리오와 같이 문서 통합이 필요할 수도 있습니다. Azure Blockchain Workbench는 문서를 직접 처리하기 위한 API 호출을 제공하지는 않지만 Blockchain 애플리케이션으로 문서를 통합할 수는 있습니다. 이 섹션에는 해당 패턴도 포함됩니다.

이 섹션에는 종단 간 솔루션에서 이러한 유형의 통합을 각각 구현하기 위해 식별된 패턴이 포함됩니다.

## <a name="rest-api-based-integration"></a>REST API 기반 통합

Azure Blockchain Workbench에서 생성된 웹 애플리케이션의 기능은 REST API를 통해 공개됩니다. 기능에는 Azure Blockchain Workbench 업로드, 응용 프로그램 구성 및 관리, 트랜잭션을 분산 원장으로 전송, 응용 프로그램 메타데이터 및 원장 데이터 쿼리가 포함됩니다.

REST API는 주로 웹, 모바일 및 봇 애플리케이션과 같은 대화형 클라이언트에 사용됩니다.

이 섹션에서는 분산 원장으로 트랜잭션을 전송하는 REST API의 측면을 중심으로 한 패턴과, Azure Blockchain Workbench *오프 체인* SQL 데이터베이스에서 트랜잭션 관련 데이터를 쿼리하는 패턴에 대해 설명합니다.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>외부 시스템에서 분산 원장으로 트랜잭션 전송

Azure Blockchain Workbench REST API는 분산 원장에서 트랜잭션을 실행하기 위해 인증된 요청을 전송합니다.

![분산 원장으로 트랜잭션 보내기](./media/integration-patterns/send-transactions-ledger.png)

트랜잭션은 위의 그림에 나와 있는 프로세스를 통해 다음과 같이 진행됩니다.

-   외부 애플리케이션은 Azure Blockchain Workbench 배포의 일부로 프로비전되는 Azure Active Directory에 인증합니다.
-   인증된 사용자는 요청과 함께 API로 전송할 수 있는 전달자 토큰을 받습니다.
-   외부 애플리케이션은 전달자 토큰을 사용하여 REST API를 호출합니다.
-   REST API는 요청을 메시지로 패키지화하여 Service Bus로 보냅니다. 여기서 요청이 검색되어 서명된 다음 적절한 분산 원장으로 전송됩니다.
-   REST API는 요청을 기록하고 현재 프로비저닝 상태를 설정하도록 Azure Blockchain Workbench SQL DB에 요청합니다.
-   SQL DB는 프로비저닝 상태를 반환하고 API 호출은 이를 호출한 외부 애플리케이션에 ID를 반환합니다.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Blockchain Workbench 메타데이터 및 분산 원장 트랜잭션 쿼리

Azure Blockchain Workbench REST API는 분산 원장의 스마트 계약 실행과 관련된 세부 정보를 쿼리하기 위해 인증된 요청을 전송합니다.

![메타데이터 쿼리](./media/integration-patterns/querying-metadata.png)

쿼리는 위의 그림에 나와 있는 프로세스를 통해 다음과 같이 진행됩니다.

1. 외부 애플리케이션은 Azure Blockchain Workbench 배포의 일부로 프로비전되는 Azure Active Directory에 인증합니다.
2. 인증된 사용자는 요청과 함께 API로 전송할 수 있는 전달자 토큰을 받습니다.
3. 외부 애플리케이션은 전달자 토큰을 사용하여 REST API를 호출합니다.
4. REST API는 SQL DB의 요청에 대한 데이터를 쿼리하여 클라이언트에 반환합니다.

## <a name="messaging-integration"></a>메시징 통합

메시징 기능을 통합하면 대화형 로그인을 수행할 수 없거나 수행하기를 원치 않는 경우 시스템/서비스/디바이스와 원활하게 상호 작용할 수 있습니다. 메시징 기능을 통합하면 두 가지 유형의 메시지(분산 원장에서 실행할 트랜젝션을 요청하는 메시지와 트랜잭션이 실행될 때 해당 원장에서 표시되는 이벤트)가 중점적으로 표시됩니다.

메시징 통합은 사용자 생성, 계약 생성 및 계약에서 트랜잭션 실행과 관련된 트랜잭션의 실행 및 모니터링에 중점을 두고 있으며 주로 *헤드리스* 백 엔드 시스템에서 사용됩니다.

이 섹션에서는 분산 원장으로 트랜잭션을 전송하는 메시지 기반 API의 측면을 중심으로 한 패턴과, 기본 분산 원장에서 표시되는 이벤트 메시지를 나타내는 패턴에 대해 설명합니다.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>스마트 계약에서 이벤트 소비자로의 단방향 이벤트 전달 

이 시나리오에서는 스마트 계약 내에서 상태 변경이나 특정 트랜잭션 유형 실행과 같은 이벤트가 발생합니다. 이 이벤트는 이벤트 그리드를 통해 다운스트림 소비자에게 브로드캐스팅되며 이러한 소비자는 적절한 작업을 수행합니다.

이 시나리오의 예는 트랜잭션이 발생하면 소비자가 경고를 받고 SQL DB 또는 Common Data Service에 정보를 기록하는 등 작업을 수행할 수 있다는 것입니다. 이 시나리오는 Workbench가 해당 *오프 체인* SQL DB를 채우기 위해 따르는 것과 동일한 패턴입니다.

또 다른 예로 계약이 *OutOfCompliance*로 이동하는 경우와 같이 스마트 계약이 특정 상태로 전환되는 경우도 있습니다. 이 상태가 변경되면 관리자의 휴대폰으로 보내도록 경고를 트리거할 수 있습니다.

![단방향 이벤트 전송](./media/integration-patterns/one-way-event-delivery.png)

이 시나리오는 위의 그림에 나와 있는 프로세스를 통해 다음과 같이 진행됩니다.

-   스마트 계약이 새로운 상태로 전환되고 원장에 이벤트를 보냅니다.
-   원장은 이벤트를 수신하여 Azure Blockchain Workbench에 전달합니다.
-   Azure Blockchain Workbench는 원장의 이벤트를 구독하고 이벤트를 수신합니다.
-   Azure Blockchain Workbench는 이벤트 그리드에서 구독자에게 이벤트를 게시합니다.
-   외부 시스템은 Event Grid를 구독하고 메시지를 사용하며 적절한 작업을 수행합니다.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>외부 시스템에서 스마트 계약으로의 메시지 단방향 이벤트 전달

반대 방향에서 이동하는 시나리오도 있습니다. 이 경우 센서 또는 외부 시스템에서 이벤트를 생성하고 해당 이벤트의 데이터를 스마트 계약으로 보내야 합니다.

상품, 주식 또는 채권의 가격과 같은 데이터를 금융 시장에서 스마트 계약으로 전달하는 경우가 일반적인 예입니다.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Azure Blockchain Workbench에서 예상되는 형식으로 직접 전달

일부 애플리케이션은 Azure Blockchain Workbench와 통합되도록 빌드되었으며 예상되는 형식으로 메시지를 직접 생성하고 전송합니다.

![직접 전송](./media/integration-patterns/direct-delivery.png)

이 전송은 위의 그림에 나와 있는 프로세스를 통해 다음과 같이 진행됩니다.

-   Azure Blockchain Workbench에 대한 메시지 생성을 트리거하는 외부 시스템에서 이벤트가 발생합니다.
-   이 메시지를 알려진 형식으로 만들기 위해 작성된 코드가 포함되어 있는 외부 시스템에서 해당 메시지를 Service Bus에 직접 전송합니다.
-   Azure Blockchain Workbench는 Service Bus의 이벤트를 구독하고 메시지를 검색합니다.
-   Azure Blockchain Workbench는 외부 시스템에서 특정 계약으로 데이터를 전송하여 원장에 대한 호출을 시작합니다.
-   메시지를 수신하면 계약이 새로운 상태로 전환됩니다.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Azure Blockchain Workbench에서 알 수 없는 형식으로 메시지 전달

일부 시스템은 Azure Blockchain Workbench에서 사용하는 표준 형식으로 메시지를 전달하도록 수정할 수 없습니다. 그런 경우 이러한 시스템의 기존 메커니즘과 메시지 형식이 종종 사용되는 경우가 있습니다. 특히 Logic Apps, Azure Functions 또는 예상되는 표준 메시징 형식 중 하나에 매핑하는 기타 사용자 지정 코드를 사용하여 이러한 시스템의 기본 메시지 유형을 변환할 수 있습니다.

![알 수 없는 메시지 형식](./media/integration-patterns/unknown-message-format.png)

이 전송은 위의 그림에 나와 있는 프로세스를 통해 다음과 같이 진행됩니다.

-   외부 시스템에서 메시지 생성을 트리거하는 이벤트가 발생합니다.
-   Logic App 또는 사용자 지정 코드는 해당 메시지를 수신하여 표준 Azure Blockchain Workbench 형식의 메시지로 변환하는 데 사용됩니다.
-   Logic App은 변환된 메시지를 Service Bus로 직접 전송합니다.
-   Azure Blockchain Workbench는 Service Bus의 이벤트를 구독하고 메시지를 검색합니다.
-   Azure Blockchain Workbench는 외부 시스템에서 계약의 특정 함수로 데이터를 전송하여 원장에 대한 호출을 시작합니다.
-   함수가 실행되고 일반적으로 상태가 수정됩니다. 상태 변경은 스마트 계약에 반영된 비즈니스 워크플로를 진행시켜 다른 함수가 적절하게 실행되도록 합니다.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>외부 프로세스로 제어를 전환하고 완료되기를 기다립니다.

스마트 계약에서 내부 실행을 중단하고 외부 프로세스로 전달해야 하는 시나리오가 있습니다. 해당 외부 프로세스가 완료되고 스마트 계약에 메시지를 보내면 스마트 계약 내에서 계속 실행됩니다.

#### <a name="transition-to-the-external-process"></a>외부 프로세스로 전환

이 패턴은 일반적으로 다음 방법을 사용하여 구현됩니다.

-   스마트 계약이 특정 상태로 전환됩니다. 이 상태에서는 외부 시스템이 원하는 작업을 수행할 때까지 함수가 실행되지 않거나 제한된 수만큼 실행될 수 있습니다.
-   상태 변화는 다운스트림 소비자에게 이벤트로 나타납니다.
-   다운스트림 소비자는 이벤트를 수신하고 외부 코드 실행을 트리거합니다.

![외부 프로세스로의 전환 제어](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>스마트 계약에서 제어 반환

외부 시스템을 사용자 지정하는 기능에 따라 Azure Blockchain Workbench에 필요한 표준 형식 중 하나로 메시지를 전송하는 것이 가능할 수도 있고 그렇지 않을 수도 있습니다. 이러한 메시지 중 하나를 생성하는 외부 시스템 기능에 따라 다음 두 가지 반환 경로 중 사용할 경로를 결정합니다.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Azure Blockchain Workbench에서 예상되는 형식으로 직접 전달

![](./media/integration-patterns/direct-delivery.png)

이 모델에서는 위의 프로세스가 진행된 다음 계약에 대한 통신과 후속 상태 변경이 다음과 같이 진행됩니다.

-   외부 코드 실행이 완료되거나 특정한 지점에 도달하면 Azure Blockchain Workbench에 연결된 Service Bus로 이벤트가 전송됩니다.

-   API의 조건을 충족하는 메시지를 작성하도록 직접 조정할 수 없는 시스템은 변환됩니다.

-   메시지의 콘텐츠는 패키지되어 스마트 계약의 특정 함수로 전송됩니다. 이 전송은 외부 시스템과 연결된 사용자를 대신하여 수행됩니다.

-   함수가 실행되고 일반적으로 상태가 수정됩니다. 상태 변경은 스마트 계약에 반영된 비즈니스 워크플로를 진행시켜 다른 함수가 적절하게 실행되도록 합니다.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Azure Blockchain Workbench에서 알 수 없는 형식으로 메시지 전달

![알 수 없는 메시지 형식](./media/integration-patterns/unknown-message-format.png)

표준 형식의 메시지를 직접 전송할 수 없는 이 모델에서는 위의 프로세스가 진행된 다음 계약에 대한 통신과 후속 상태 변경이 다음과 같이 진행합니다.

1.  외부 코드 실행이 완료되거나 특정한 지점에 도달하면 Azure Blockchain Workbench에 연결된 Service Bus로 이벤트가 전송됩니다.
2.  Logic App 또는 사용자 지정 코드는 해당 메시지를 수신하여 표준 Azure Blockchain Workbench 형식의 메시지로 변환하는 데 사용됩니다.
3.  Logic App은 변환된 메시지를 Service Bus로 직접 전송합니다.
4.  Azure Blockchain Workbench는 Service Bus의 이벤트를 구독하고 메시지를 검색합니다.
5.  Azure Blockchain Workbench는 외부 시스템에서 특정 계약으로 데이터를 전송하여 원장에 대한 호출을 시작합니다.
6. 메시지의 콘텐츠는 패키지되어 스마트 계약의 특정 함수로 전송됩니다. 이 전송은 외부 시스템과 연결된 사용자를 대신하여 수행됩니다.
7.  함수가 실행되고 일반적으로 상태가 수정됩니다. 상태 변경은 스마트 계약에 반영된 비즈니스 워크플로를 진행시켜 다른 함수가 적절하게 실행되도록 합니다.

## <a name="iot-integration"></a>IoT 통합

일반적인 통합 시나리오는 스마트 계약의 센서에서 검색된 원격 분석 데이터를 포함합니다. 센서에 의해 전달된 데이터를 기반으로 하여 스마트 계약은 정보에 입각한 작업을 수행하고 계약 상태를 변경할 수 있습니다.

예를 들어 약품을 운반하는 트럭의 온도가 110도까지 상승하면 약품의 효능에 영향을 미칠 수 있으며 공급망에서 감지되고 제거되지 않으면 공공 안전 문제가 발생할 수 있습니다. 운전자가 시간당 161km(100마일)로 자동차 속도를 높이면 센서 정보에 따라 보험 회사가 보험을 취소할 수 있습니다. 자동차가 렌트카인 경우 GPS 데이터는 운전자가 임대 계약서의 적용을 받는 지역을 벗어날 때를 나타내고 벌금을 부과할 수 있습니다.

문제는 이러한 센서가 정기적으로 데이터를 전달할 수 있기 때문에 모든 데이터를 스마트 계약으로 보내는 것이 적절하지 않다는 것입니다. 일반적인 접근 방법은 모든 메시지를 보조 저장소로 전달하는 반면 블록체인으로 전송되는 메시지의 수를 제한하는 것입니다. 예를 들어 정해진 간격으로만 수신되는 메시지를 전달하거나 포함된 값이 스마트 계약에 대해 합의된 범위를 벗어날 때 메시지를 전달합니다. 허용 오차를 벗어나는 값을 확인하면 계약 비즈니스 논리와 관련된 데이터가 수신되고 실행됩니다. 간격을 띄워서 값을 확인하면 센서가 계속 보고 중인지 확인할 수 있습니다. 보다 광범위한 보고, 분석 및 기계 학습을 위해 모든 데이터가 보조 보고 저장소로 전송됩니다. 예를 들어 스마트 계약의 경우 1분마다 GPS에 대한 센서 판독 값을 가져올 필요가 없지만 보고서 또는 매핑 경로에 사용할 흥미로운 데이터를 제공할 수 있습니다.

Azure 플랫폼에서 디바이스와의 통합은 일반적으로 IoT Hub를 통해 수행됩니다. IoT Hub는 콘텐츠를 기반으로 메시지를 라우팅하는 기능을 제공하며 위에 설명된 기능 유형을 사용할 수 있도록 설정합니다.

![IoT 메시지](./media/integration-patterns/iot.png)

이 프로세스는 다음과 같은 패턴을 나타냅니다.

-   디바이스는 직접 또는 필드 게이트웨이를 통해 IoT Hub와 통신합니다.
-   IoT Hub는 메시지를 수신하고 메시지 콘텐츠를 확인하는 설정된 경로 등에 대해 메시지를 평가합니다. *센서가 50도 이상의 온도를 보고하나요?*
-   IoT Hub는 기준에 맞는 메시지를 경로에 대해 정의된 Service Bus로 전송합니다.
-   Logic App 또는 기타 코드는 IoT Hub가 경로에 대해 설정한 Service Bus를 수신합니다.
-   Logic App 또는 기타 코드는 메시지를 검색하여 알려진 형식으로 변환합니다.
-   변환된 메시지는 이제 표준 형식으로 Azure Blockchain Workbench용 Service Bus로 전송됩니다.
-   Azure Blockchain Workbench는 Service Bus의 이벤트를 구독하고 메시지를 검색합니다.
-   Azure Blockchain Workbench는 외부 시스템에서 특정 계약으로 데이터를 전송하여 원장에 대한 호출을 시작합니다.
-   메시지를 수신하면 계약이 데이터를 평가하고 해당 평가의 결과에 따라 상태를 변경할 수 있습니다. 예를 들어 고온의 경우 상태를 *준수하지 않음*으로 변경합니다.

## <a name="data-integration"></a>데이터 통합

Azure Blockchain Workbench는 REST 및 메시지 기반 API 외에도 분산 원장의 트랜잭션 데이터는 물론 애플리케이션 및 계약 메타데이터로 채워진 SQL DB에 대한 액세스를 제공합니다.

![데이터 통합](./media/integration-patterns/data-integration.png)

데이터 통합은 잘 알려져 있습니다.

-   Azure Blockchain Workbench는 응용 프로그램, 워크플로, 계약 및 트랜잭션에 대한 메타데이터를 정상 작동 동작의 일부로 저장합니다.
-   외부 시스템 또는 도구는 하나 이상의 대화 상자를 제공하여 데이터베이스 서버 이름, 데이터베이스 이름, 인증 유형, 로그인 자격 증명 및 활용할 데이터베이스 뷰와 같은 데이터베이스 관련 정보 수집을 용이하게 합니다.
-   외부 시스템, 서비스, 보고, 개발자 도구 및 엔터프라이즈 생산성 도구에 의한 다운스트림 사용을 용이하게 하기 위해 SQL 데이터베이스 뷰에 대해 쿼리가 작성됩니다.

## <a name="storage-integration"></a>저장소 통합

많은 시나리오에서는 증명할 수 있는 파일을 통합해야 할 필요가 있습니다. 여러 이유로 인해 파일을 블록체인에 포함하는 것은 적절하지 않습니다. 대신 일반적인 접근 방식은 파일에 대해 암호화 해시(예: SHA-256)를 수행하고 분산 원장에서 해시를 공유하는 것입니다. 나중에 언제든지 해시를 다시 수행하면 동일한 결과가 반환됩니다. 파일이 수정되면 이미지에서 한 픽셀만 수정되더라도 해시는 다른 값을 반환합니다.

![저장소 통합](./media/integration-patterns/storage-integration.png)

패턴은 다음과 같이 구현될 수 있습니다.

-   외부 시스템은 Azure Storage와 같은 스토리지 메커니즘에 파일을 유지합니다.
-   해시는 파일 또는 파일 및 소유자의 식별자, 파일이 있는 URL 등과 같은 관련 메타데이터로 생성됩니다.
-   해시 및 모든 메타데이터는 *FileAdded*와 같은 스마트 계약의 함수로 전송됩니다.
-   나중에 파일과 메타데이터를 다시 해시할 수 있고 원장에 저장된 값과 비교할 수 있습니다.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>REST 및 메시지 API를 사용하여 통합 패턴을 구현하기 위한 전제 조건

외부 시스템 또는 디바이스가 REST 또는 메시지 API를 사용하여 스마트 계약과 쉽게 상호 작용할 수 있게 하려면 다음을 수행해야 합니다.

1. 컨소시엄용 Azure Active Directory에서 외부 시스템이나 디바이스를 나타내는 계정이 만들어집니다.
2. Azure Blockchain Workbench 애플리케이션용으로 적합한 스마트 계약 하나 이상에는 외부 시스템이나 디바이스의 이벤트를 수락하도록 정의된 함수가 포함되어 있습니다.
3. 스마트 계약의 애플리케이션 구성 파일에는 시스템이나 디바이스가 할당되는 역할이 포함되어 있습니다.
4. 스마트 계약의 애플리케이션 구성 파일은 정의된 역할을 기준으로 이 함수가 호출되는 상태를 식별합니다.
5. 애플리케이션 구성 파일과 스마트 계약이 Azure Blockchain Workbench에 업로드됩니다.

애플리케이션이 업로드되면 외부 시스템에 대한 Azure Active Directory 계정이 계약 및 관련 역할에 할당됩니다.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>통합 코드를 작성하기 전에 외부 시스템 통합 흐름 테스트 

대다수 시나리오에서는 외부 시스템과의 통합이 가능해야 합니다. 외부 시스템과 통합하기 위한 코드 개발에 앞서 또는 병렬로 스마트 계약 설계의 유효성을 검사할 수 있도록 하는 것이 바람직합니다.

Azure AD(Azure Active Directory)를 사용하면 개발자 생산성과 가치 실현 시간을 크게 단축할 수 있습니다. 특히 외부 시스템과의 코드 통합은 적지 않은 시간이 걸릴 수 있습니다. Azure AD와 Azure Blockchain Workbench의 UX 자동 생성 기능을 사용하면 개발자가 Blockchain Workbench에 외부 시스템으로 로그인한 다음 UX를 통해 외부 시스템에서 값을 채우도록 할 수 있습니다. 그러면 외부 시스템용 통합 코드를 작성하기 전에 개념 증명 환경에서 아이디어를 빠르게 개발하고 유효성을 검사할 수 있습니다.
