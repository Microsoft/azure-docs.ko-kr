---
title: Azure Cosmos DB를 사용 하는 기본 제공 분석에 대한 사용 사례입니다.
description: 다른 사용 사례에서 Azure Cosmos DB에 기본 제공 분석을 사용 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757068"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용 하는 기본 제공 분석에 대한 사용 사례

Azure Cosmos DB에서 Apache Spark를 사용 하 여 기본 제공 분석을 사용 하면 다음과 같은 사용 사례를 달성할 수 있습니다.

## <a name="htap-scenarios"></a>HTAP 시나리오

Azure Cosmos DB의 기본 제공 분석을 사용 하 여 다음 작업을 수행할 수 있습니다.

* 트랜잭션 처리 중 사기 행위를 감지 합니다.
* 구매자가 전자 상거래 저장소를 검색할 때 권장 사항을 제공 합니다.
* 중요 한 제조 장비에서 발생 하는 오류에 대해 운영자에 게 경고 합니다.
* 운영 데이터에 실시간 분석을 직접 포함 하 여 신속 하 고 조치 가능한 통찰력을 만드세요.

Azure Cosmos DB는 기본적으로 기본 제공 Apache Spark를 사용 하 여 HTAP (하이브리드 트랜잭션/분석 처리) 시나리오를 지원 합니다. Azure Cosmos DB는 기존 시스템과 함께 제공 되는 운영 및 분석 분리를 제거 합니다.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>ETL을 요구 하지 않고 전역으로 분산 된 data lake

기본적으로 기본 제공 Apache Spark Azure Cosmos DB는 단일 시스템 이미지를 제공 하는 전역적으로 분산 된 data lake를 구축 하는 빠르고 간단 하며 확장성 있는 방법을 제공 합니다. 전 세계적으로 분산 된 다중 모델 데이터는 데이터 팀이 데이터 집합을 분석 하는 방식을 revolutionizing 하 여 비용이 많이 드는 ETL 파이프라인에 투자 하 고 주문형으로 확장할 필요가 없습니다.

## <a name="time-series-analytics-over-historic-data"></a>기록 데이터에 대한 시계열 분석

일부 경우에는 과거에 완료 된 이벤트에 대한 특정 시점의 데이터를 기반으로 하 여 질문에 대답 해야 할 수도 있습니다. 예를 들어 특정 날짜의 CRM 활동 상태 수를 가져올 수 있습니다. 주 전에 보고서를 실행 한 경우 상태 수는 해당 시점에 각 활동의 상태별로 표시 됩니다. 오늘 동일한 보고서를 실행 하면 현재 상태와 동일 하 게 상태가 되는 활동의 수를 알 수 있습니다. 이러한 활동의 수명은 지난 주 이후에 변경 되었을 수 있습니다. 따라서 사례 수명 주기의 각 단계에서 스냅숏에 대해 보고 해야 합니다.

기존 데이터 웨어하우스 시나리오에서는 데이터 웨어하우스가이를 통합 하도록 설계 되지 않았으며 데이터는 발생 하는 상황에 대한 최신 보기만 제공 하므로 스냅숏의 개념을 사용할 수 없습니다. Azure Cosmos DB를 통해 사용자는 시간 이동의 개념을 구현 하 고, 데이터 소급 분석을 쿼리하고 실행할 수 있으며, 데이터를 기록에서 특정 시점으로 확인 하는 방법을 요청할 수 있습니다. 즉, 사용자가 데이터의 현재 및 기록 보기를 쉽게 확인 하 고 분석을 실행할 수 있습니다.

## <a name="globally-distributed-machine-learning-and-ai"></a>전 세계적으로 분산 기계 학습 및 AI

비즈니스에서 데이터 볼륨을 신속 하 게 증가 하 고 다양 한 데이터 형식 및 형식을 확장 하는 데 경쟁 함에 따라 전 세계 페타바이트 규모에서 보다 심층적이 고 정확한 정보를 얻을 수 있는 기능이 거의 불가능 합니다. 기본적으로 기본 제공 Apache Spark Azure Cosmos DB는 광범위 한 기계 학습 알고리즘 라이브러리를 제공 하는 전역적으로 분산 된 분석 플랫폼을 제공 합니다. 대화형 Jupyter 노트북을 사용 하 여 모델을 작성 및 학습 하 고 클러스터 관리 기능을 수행할 수 있습니다. 이러한 기능을 사용 하 여 요청 시 매우 튜닝 되 고 자동 탄력적 Spark 클러스터를 프로 비전 할 수 있습니다.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>다중 모델 전역 분산 데이터에 대한 심층 학습

심층 학습은 데이터 볼륨 및 복잡성이 계속 증가 함에 따라 빅 데이터 예측 분석 솔루션을 제공 하는 이상적인 방법입니다. 심층 학습을 통해 기업은 구조화 되지 않은 데이터와 반 구조화 된 데이터의 강력한 기능을 활용 하 여 AI, 자연어 처리 등과 같은 기술을 활용 하는 사용 사례를 제공할 수 있습니다.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>보고 (Power Apps와 통합, Power BI)

Power BI는 셀프 서비스 비즈니스 인텔리전스 기능을 통해 대화형 시각화를 제공 하 여 최종 사용자가 보고서와 대시보드를 직접 만들 수 있도록 합니다. 기본 제공 Spark 커넥터를 사용 하 여 Power BI Desktop를 Azure Cosmos DB의 Apache Spark 클러스터에 연결할 수 있습니다. 이 커넥터를 사용 하면 직접 쿼리를 사용 하 여 Azure Cosmos DB의 Apache Spark에 대한 처리를 오프 로드할 수 있습니다 .이는 Power BI 로드 하지 않으려는 데이터의 양이 크거나 실시간 분석을 수행 하려는 경우에 유용 합니다.

## <a name="iot-analytics-at-global-scale"></a>글로벌 규모의 IoT 분석

네트워크 센서를 늘려서 생성 된 데이터는 이전의 불투명 시스템 및 프로세스에 대한 전례 없는 가시성을 제공 합니다. 핵심은 IoT 장치가 전 세계에 배포 되는 위치에 관계 없이 정보에 대한이 torrent에서 실행 가능한 정보를 찾는 것입니다. Azure Cosmos DB를 통해 IOT 기업은 전 세계 어디에서 든 실시간 센서 및 시계열 데이터를 실시간으로 분석할 수 있습니다. 이를 통해 상호 연결 된 환경의 진정한 가치를 활용 하 여 향상 된 고객 환경, 운영 효율성 및 새로운 수익 기회를 제공할 수 있습니다.

## <a name="stream-processing-and-event-analytics"></a>스트림 처리 및 이벤트 분석 

로그 파일에서 센서 데이터에 이르기까지 기업은 점점 더 많은 데이터를 "스트림" 하 여 처리할 필요가 있습니다. 이 데이터는 종종 여러 원본에서 동시에 안정 된 스트림으로 도착 합니다. 이러한 데이터 스트림을 디스크에 저장 하 고 소급 분석할 수는 있지만, 데이터가 도착할 때 데이터를 처리 하 고 작동 하는 것이 중요 하거나 중요할 수 있습니다. 예를 들어 금융 트랜잭션과 관련 된 데이터 스트림을 실시간으로 처리 하 여 잠재적으로 사기성 트랜잭션을 식별 하 고 거부할 수 있습니다.

## <a name="interactive-analytics"></a>대화형 분석

미리 정의 된 쿼리를 실행 하 여 판매, 생산성 또는 주식 가격에 대한 정적 대시보드를 만드는 것 외에도 대화형으로 데이터를 탐색할 수 있습니다. 대화형 분석을 사용 하면 질문을 하 고, 결과를 확인 하 고, 응답을 기반으로 하는 초기 질문을 변경 하거나, 결과를 자세히 파악할 수 있습니다. Azure Cosmos DB Apache Spark은 신속 하 게 응답 하 고 적응 하 여 대화형 쿼리를 지원 합니다.

## <a name="data-exploration-using-jupyter-notebooks"></a>Jupyter 노트북을 사용 하 여 데이터 탐색

새 데이터 집합이 있는 경우 실행 중인 모델과 테스트를 살펴보기 전에 데이터를 검사 해야 합니다. 즉, 예비 데이터 분석을 수행 해야 합니다. 데이터 탐색은 몇 가지 결정을 내릴 수 있습니다. 예를 들어 데이터에 사용 하기에 적합 한 방법, 데이터가 특정 모델링 가정을 충족 하는지 여부, 데이터를 정리 해야 하는지 여부, 재구성 등의 세부 정보를 찾을 수 있습니다. Azure Cosmos DB의 기본적으로 기본 제공 되는 Jupyter 노트북 및 Apache Spark를 사용 하 여 트랜잭션 및 분석 데이터에 대해 빠르고 효과적인 예비 데이터 분석을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이러한 사용 사례를 시작 하려면 다음 문서를 참조 하세요.

* [Azure Cosmos DB의 기본 제공 Jupyter 노트북](cosmosdb-jupyter-notebooks.md)
* [Azure Cosmos 계정에 대해 노트북을 사용 하도록 설정 하는 방법](enable-notebooks.md)
* [데이터를 분석 하 고 시각화할 노트북 만들기](create-notebook-visualize-data.md)