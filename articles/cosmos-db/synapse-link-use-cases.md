---
title: Microsoft Azure Cosmos DB용 Synapse Link를 사용한 실시간 분석 사용 사례
description: Microsoft Azure Cosmos DB용 Azure Synapse Link가 공급망 분석, 예측, 보고, 실시간 개인 설정 및 IOT 예측 유지 관리에 어떻게 사용되는지 알아봅니다.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: 3a522a5a5945fe7a4b63dc759077d744f951a76f
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814419"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Microsoft Azure Cosmos DB용 Synapse Link: 근 실시간 분석 사용 사례

Microsoft Azure Cosmos DB용 [Azure Synapse Link](synapse-link.md)는 클라우드 네이티브 HTAP(하이브리드 트랜잭션 및 분석 처리) 기능으로, 이를 통해 작동 데이터에 대해 근 실시간 분석을 실행할 수 있습니다. Synapse Link를 통해 Microsoft Azure Cosmos DB와 Azure Synapse Analytics가 긴밀하게 통합됩니다.

작동 데이터에 대한 근 실시간 분석을 위해 이 클라우드 네이티브 HTAP 기능을 활용할 수 있는 업계 사용 사례에 대해 알아봅니다. Microsoft Azure Cosmos DB용 Synapse Link의 일반적인 세 가지 사용 사례를 소개하겠습니다.

* 공급망 분석, 예측 및 보고
* 실시간 개인 설정
* IOT 시나리오에서 예측 유지 관리, 변칙 검색

> [!NOTE]
> Microsoft Azure Cosmos DB용 Azure Synapse Link는 엔터프라이즈 팀이 근 실시간 분석을 실행하려는 시나리오를 대상으로 합니다. 이러한 분석은 Microsoft Azure Cosmos DB에 구축된 트랜잭션 애플리케이션에서 생성된 작동 데이터에 대한 ETL 없이 실행됩니다. 이는 여러 데이터 원본에 대한 워크로드 관리, 높은 동시성, 지 속성 집계와 같은 기존 데이터 웨어하우스 요구 사항이 있는 경우 별도의 데이터 웨어하우스의 필요를 대체하지 않습니다.

## <a name="supply-chain-analytics-forecasting--reporting"></a>공급망 분석, 예측 및 보고

연구 조사에 따르면, 공급망 운영에 빅 데이터 분석을 포함할 경우 주문 접수 후 발송에 걸리는 시간과 공급망 효율성이 향상됩니다.

제조업체는 기존 ERP(Enterprise Resource Planning) 및 SCM(Supply Chain Management) 시스템의 제약을 극복하기 위해 클라우드 네이티브 기술을 도입하고 있습니다. 공급망에서 매분 점점 더 많은 양의 작동 데이터(주문, 배송, 트랜잭션 데이터)를 생성함에 따라 제조업체에는 운영 데이터베이스가 필요합니다. 이 운영 데이터베이스는 데이터 볼륨과 분석 플랫폼을 처리 할 수 있도록 스케일링되어 계속 앞서 나가는 데 활용할 수 있는 일정 수준의 실시간 컨텍스트 인텔리전스를 제공합니다.

다음 아키텍처에서는 공급망 분석에서 클라우드 네이티브 운영 데이터베이스 및 Synapse Link로 Microsoft Azure Cosmos DB를 활용하는 모습을 보여줍니다.

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="공급망 분석의 Azure Cosmos DB에 대 한 Azure Synapse 링크 " border="false":::

이전 아키텍처를 기반으로 Microsoft Azure Cosmos DB용 Synapse Link를 사용하여 다음과 사용 사례가 가능합니다.

* **예측 파이프라인 준비 및 학습:** 기계 학습 번역을 사용하여 공급망 전체의 작동 데이터에 대한 인사이트를 생성합니다. 이러한 방식으로 인벤토리와 운영 비용을 절감하고 주문부터 배송까지 걸리는 시간을 줄일 수 있습니다.

  Synapse Link를 사용하면 수동 ETL 프로세스 없이 Microsoft Azure Cosmos DB에서 변경되는 작동 데이터를 분석할 수 있습니다. 이를 통해 추가 비용, 대기 시간 및 운영상의 복잡성을 줄일 수 있습니다. 데이터 엔지니어와 데이터 과학자는 Synapse Link를 통해 강력한 예측 파이프 라인을 구축할 수 있습니다.

  * Azure Synapse Analytics에서 Apache Spark 풀과의 네이티브 통합을 활용하여 Microsoft Azure Cosmos DB 분석 저장소에서 작동 데이터를 쿼리합니다. 복잡한 데이터 엔지니어링 없이 대화형 노트북 또는 예약된 원격 작업에서 데이터를 쿼리할 수 ​​있습니다.

  * Azure Synapse Analytics에서 Spark ML 알고리즘 및 Azure ML 통합을 사용하여 ML(기계 학습) 모델을 구축합니다.

  * 운영상 근 실시간 채점을 위해 모델 유추 후 결과를 Microsoft Azure Cosmos DB에 다시 씁니다.

* **운영 보고:** 공급망 팀에는 정확한 실시간 작동 데이터에 대한 유연하고 맞춤형 보고서가 필요합니다. 이러한 보고서는 공급망 효율성, 수익성 및 생산성에 대한 스냅샷 보기를 얻는 데 필요합니다. 이를 통해 데이터 분석가와 기타 주요 관련자는 지속적으로 비즈니스를 재평가하고 조정할 영역을 식별하여 운영 비용을 절감할 수 있습니다. 

  Microsoft Azure Cosmos DB용 Synapse Link는 다양한 BI(비즈니스 인텔리전스)/보고 시나리오를 지원합니다.

  * Synapse SQL server를 사용 하지 않는 기본 및 T-sql 언어의 표현을를 사용 하 여 네이티브 통합을 사용 하 여 Azure Cosmos DB 분석 저장소에서 작동 데이터를 쿼리 합니다.

  * 친숙 한 BI 도구에 대 한 Synapse SQL 서버를 사용 하지 않는 지원을 통해 Azure Cosmos DB를 통해 BI 대시보드를 모델 및 게시 합니다. Azure Analysis Services, Power BI Premium 등을 예로 들 수 있습니다.

다음은 Microsoft Azure Cosmos DB에 일괄 처리 및 스트리밍 데이터 통합에 대한 몇 가지 지침입니다.

* **일괄 처리 데이터 통합 및 오케스트레이션:** 공급망이 더욱 복잡해짐에 따라 공급망 데이터 플랫폼은 다양한 데이터 원본과 형식과 통합해야 합니다. Azure Synapse Analytics는 Azure Data Factory와 동일한 데이터 통합 엔진과 환경을 기본 제공합니다. 이러한 통합을 통해 데이터 엔지니어는 별도의 오케스트레이션 엔진 없이도 풍부한 데이터 파이프라인을 만들 수 있습니다.

  * [Azure Data Factory를 사용하여 Microsoft Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md)로 85개 이상의 지원되는 데이터 원본의 데이터를 이동합니다.

  * [매핑 데이터 흐름을 사용한 관계-계층 및 계층-계층 매핑 ](../data-factory/how-to-sqldb-to-cosmosdb.md)을 포함하여 코드가 없는 ETL 파이프라인을 Microsoft Azure Cosmos DB에 작성합니다.

* **스트리밍 데이터 통합 및 처리:** 산업용 IoT('매장 간', 연결된 물류 차량 등의 자산 추적 센서)가 성장함에 따라 인사이트 생성을 위해 기존의 느리게 이동하는 데이터와의 통합이 필요한 스트리밍 방식으로 생성되는 실시간 데이터가 급증하고 있습니다. Azure Stream Analytics는 [광범위한 시나리오](../stream-analytics/streaming-technologies.md)로 Azure에서 ETL과 처리 스트리밍에 권장되는 서비스입니다. Azure Stream Analytics는 [Microsoft Azure Cosmos DB를 원시 데이터 싱크](../stream-analytics/stream-analytics-documentdb-output.md)로 지원합니다.

## <a name="real-time-personalization"></a>실시간 개인 설정

오늘날 소매업체는 고객 및 비즈니스의 요구를 모두 충족하는 안전하고 확장 가능한 전자 상거래 솔루션을 구축해야 합니다. 이러한 전자 상거래 솔루션은 사용자 지정 제품 및 제안을 통해 고객을 참여시키고, 빠르고 안전하게 트랜잭션을 처리하며, 이행 및 고객 서비스에 집중해야 합니다. Microsoft Azure Cosmos DB와 최신 Microsoft Azure Cosmos DB용 Synapse Link를 통해 소매업체는 고객을 위한 맞춤형 추천을 실시간으로 생성할 수 있습니다. 이들은 즉각적인 인사이트를 얻기 위해 다음 아키텍처와 같이 지연 시간이 짧고 조정 가능한 일관성 설정을 사용합니다.

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="실시간 개인 설정의 Microsoft Azure Cosmos DB용 Synapse Link" border="false":::

Microsoft Azure Cosmos DB용 Azure Synapse Link 사용 사례:

* **예측 파이프라인 준비 및 학습:** Synapse Spark 및 기계 학습 모델을 사용하여 사업부나 고객 부문 전체의 작동 데이터에 대한 인사이트를 생성할 수 있습니다. 이는 최종 사용자 요구 사항에 맞게 고객 부문, 예측 최종 사용자 환경 및 대상 마케팅을 대상으로 하는 맞춤형 제공으로 이어집니다.

## <a name="iot-predictive-maintenance"></a>IOT 예측 유지 관리

산업 IOT 혁신은 기계의 가동 중지 시간을 대폭 줄이고 모든 산업 분야에서 전반적인 효율성을 높였습니다. 이러한 혁신 중 하나는 클라우드 에지의 기계에 대한 예측 유지 관리 분석입니다.

다음은 IoT 예측 유지 관리에 Microsoft Azure Cosmos DB용 Azure Synapse Link의 클라우드 네이티브 HTAP 기능을 활용하는 아키텍처입니다.

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="IOT 예측 유지 관리의 Microsoft Azure Cosmos DB용 Synapse Link" border="false" :::

Microsoft Azure Cosmos DB용 Azure Synapse Link 사용 사례:

* **예측 파이프라인 준비 및 학습:** IoT 디바이스 센서의 기록 작동 데이터를 사용하여 변칙 감지기와 같은 예측 모델을 학습할 수 있습니다. 이러한 변칙 감지기는 실시간 모니터링을 위해 에지에 다시 배포됩니다. 이러한 선순환 고리는 예측 모델의 지속적인 재학습을 가능하게 합니다.

* **운영 보고:** 디지털 트윈 이니셔티브의 성장으로 기업들은 수많은 센서에서 방대한 양의 작동 데이터를 수집하여 각 머신의 디지털 사본을 구축합니다. 이 데이터를 통해 BI는 실시간 애플리케이션뿐만 아니라 최근의 핫 데이터에 대한 기록 데이터의 추세를 파악할 수 있습니다.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>샘플 시나리오: Microsoft Azure Cosmos DB용 HTAP

10년 가까이 수천 명의 고객이 트랜잭션 워크로드에서 짧은 읽기 및 쓰기 대기 시간과 고가용성을 위해 탄력적 스케일링, 턴키 글로벌 배포, 다중 마스터 복제가 필요한 중요 업무용 애플리케이션에 Microsoft Azure Cosmos DB를 사용해 왔습니다.
 
다음 목록에서는 Microsoft Azure Cosmos DB를 사용하여 작동 데이터에서 지원되는 다양한 워크로드 패턴의 개요를 보여줍니다.

* 실시간 앱 및 서비스
* 이벤트 스트림 처리
* BI 대시보드
* 빅 데이터 분석
* 기계 학습

Azure Synapse Link를 통해 Microsoft Azure Cosmos DB는 트랜잭션 워크로드를 지원할 뿐만 아니라 기록 작동 데이터에 대해 근 실시간 분석 워크로드를 수행할 수도 있습니다. 이는 ETL 요구 사항이 없고 트랜잭션 워크로드에서 성능 격리가 보장되는 경우에 한합니다.

다음 이미지에서는 Microsoft Azure Cosmos DB를 사용하는 워크로드 패턴을 보여줍니다. :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Microsoft Azure Cosmos DB용 Synapse Link 워크로드 패턴" border="false":::

인벤토리 관리 플랫폼의 트랜잭션 및 분석 요구 사항을 모두 지원하는 단일 실시간 데이터베이스로 Microsoft Azure Cosmos DB를 선택하는 이점을 설명하기 위해 전 세계 20개 국가/지역에서 사업을 운영 중인 전자 상거래 회사 CompanyXYZ를 예로 들어 보겠습니다.

* CompanyXYZ의 핵심 사업은 인벤토리 시스템에 의존하므로 가용성 및 신뢰성이 핵심 기둥 요구 사항입니다. Microsoft Azure Cosmos DB 사용의 이점:

  * Microsoft Azure Cosmos DB는 Azure 인프라 및 투명 다중 마스터 글로벌 복제와의 긴밀한 통합을 통해 지역 중단이 발생하지 않도록 [99.999%에 달하는 업계 최고 수준의 고가용성](high-availability.md)을 제공합니다.

* CompanyXYZ의 공급망 파트너는 서로 다른 지리적 위치에 있지만 현지 운영 지원을 위해 전 세계 제품 인벤토리의 단일 보기가 필요할 수 있습니다. 또한 다른 공급망 파트너가 작성한 업데이트를 실시간으로 읽을 수 있어야 합니다. 처리량이 높아 다른 파트너와의 충돌에 대해 걱정하지 않고 업데이트할 수도 있습니다. Microsoft Azure Cosmos DB 사용의 이점:

  * Microsoft Azure Cosmos DB는 고유한 다중 마스터 복제 프로토콜과 쓰기에 최적화된 무제한 트랜잭션 저장소를 통해 전역적으로 인덱싱된 읽기 및 쓰기 작업 모두에 대해 99번째 백분위수에서 10밀리초 미만의 대기 시간을 보장합니다.

  * 트랜잭션 저장소에서 [실시간 인덱싱](index-policy.md)을 사용하여 일괄 처리와 스트리밍 데이터 피드의 수집 처리량이 높습니다.

  * Microsoft Azure Cosmos DB 트랜잭션 저장소는 비즈니스 요구에 따라 [가용성과 성능을 절충](consistency-levels-tradeoffs.md)할 수 있도록 강력하고 최종적인 2가지 일관성 수준보다 더 많은 3가지 옵션을 제공합니다.

* CompanyXYZ의 공급망 파트너의 트래픽 패턴은 초당 트래픽 수가 수백 개에서 수백만 개로 증가하는 등 변동이 많으므로 인벤토리 플랫폼에서 예기치 않은 트래픽 급증을 처리해야 합니다.  Microsoft Azure Cosmos DB 사용의 이점:

  * Microsoft Azure Cosmos DB의 트랜잭션 저장소는 행 분할을 사용하여 스토리지와 처리량의 탄력적 확장성을 지원합니다. Autopilot 모드로 구성된 컨테이너와 데이터베이스는 워크로드의 가용성, 대기 시간, 처리량 또는 성능에 영향을 주지 않고 애플리케이션 요구에 따라 프로비저닝된 처리량을 자동으로 즉시 스케일링할 수 있습니다.

* CompanyXYZ는 공급망 파트너, 사업부 및 역할에 대한 분석과 인사이트를 제공하기 위해 시스템 차원의 기록 인벤토리 데이터를 보관하는 안전한 분석 플랫폼을 구축해야 합니다. 분석 플랫폼은 운영 인벤토리 데이터에 대한 시스템, 기존 BI/보고 사용 사례, 고급 분석 사용 사례 및 예측 인텔리전트 솔루션에서 협업을 지원해야 합니다. Microsoft Azure Cosmos DB용 Synapse Link 사용의 이점:

  * Synapse Link는 완전 격리된 열 저장소인 [Microsoft Azure Cosmos DB 분석 저장소](analytical-store-introduction.md)를 사용하여 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)에서 전역적으로 분산된 작동 데이터에 대해 대규모로 ETL(추출, 변환 및 로드) 분석을 수행할 수 없습니다.  이제 비즈니스 분석가, 데이터 엔지니어 및 데이터 과학자는 상호 운용 가능한 방식으로 Synapse Spark 또는 Synapse SQL을 사용하여 Microsoft Azure Cosmos DB의 트랜잭션 워크로드 성능에 영향을 주지 않으면서 근 실시간 비즈니스 인텔리전스, 분석 및 기계 학습 파이프라인을 실행할 수 있습니다. 자세한 내용은 [Microsoft Azure Cosmos DB에서 Synapse Link의 이점](synapse-link.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요

* [Microsoft Azure Cosmos DB용 Azure Synapse Link](synapse-link.md) 

* [Microsoft Azure Cosmos DB 분석 저장소](analytical-store-introduction.md)

* [Microsoft Azure Cosmos DB용 Synapse Link 작업](configure-synapse-link.md)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics의 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)

* [Azure Synapse Analytics의 SQL 서버리스/주문형](../synapse-analytics/sql/on-demand-workspace-overview.md)
