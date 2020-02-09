---
title: Azure Cosmos DB에 대한 일반적인 사용 사례 및 시나리오
description: Azure Cosmos DB의 5가지 상위 사용 사례인 사용자 생성 콘텐츠, 이벤트 로깅, 카탈로그 데이터, 사용자 기본 설정 데이터 및 IoT(사물 인터넷)에 대해 알아봅니다.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888927"
---
# <a name="common-azure-cosmos-db-use-cases"></a>일반적인 Azure Cosmos DB 사용 사례
이 문서에서는 Azure Cosmos DB의 몇 가지 일반적인 사용 사례를 간략하게 설명합니다.  이 문서의 권장 사항은 Cosmos DB로 애플리케이션을 개발할 때 시작점 역할을 합니다.   

이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다. 

* Azure Cosmos DB의 일반적인 사용 사례는 무엇인가요?
* Azure Cosmos DB를 소매 애플리케이션에 사용할 경우 어떤 이점이 있나요?
* Azure Cosmos DB를 IoT(사물 인터넷) 시스템의 데이터 저장소로 사용할 경우 어떤 이점이 있나요?
* Azure Cosmos DB를 웹 및 모바일 애플리케이션에 사용할 경우 어떤 이점이 있나요?

## <a name="introduction"></a>소개
[Azure Cosmos DB](../cosmos-db/introduction.md)는 전 세계에 배포된 Microsoft 데이터베이스 서비스입니다. 서비스는 고객이 원하는 수의 지역에서 처리량 및 스토리지 크기를 탄력적 및 독립적으로 조정할 수 있도록 설계되었습니다. Azure Cosmos DB는 처리량, 대기 시간, 가용성 및 일관성을 포괄하는 총체적인 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/cosmos-db/)을 제공하는, 현재 출시된 최초의 전역 분산 데이터베이스 서비스입니다. 

Azure Cosmos DB는 광범위한 애플리케이션 및 사용 사례에서 사용되는, 전역으로 분산된 다중 모델 데이터베이스입니다. 낮은 응답 시간(밀리초)이 필요하며 신속하게 전역으로 크기를 조정해야 하는 [서버가 없는](https://azure.com/serverless) 애플리케이션에 적합합니다. 여러 데이터 모델(키-값, 문서, 그래프 및 칼럼 형식) 및 [Azure Cosmos DB의 MongoDB API](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md) 및 [Tables API](table-introduction.md) 등의 많은 데이터 액세스용 API를 기본적으로, 그리고 확장 가능한 방식으로 지원합니다. 

전역으로 분산하려는 고성능 애플리케이션에 적합한 Azure Cosmos DB의 몇 가지 특성은 다음과 같습니다.

* Azure Cosmos DB는 기본적으로 고가용성 및 확장성을 위해 데이터를 분할합니다. Azure Cosmos DB는 가용성, 처리량, 짧은 대기 시간 및 일관성에 대한 99.99% 보증을 모든 단일 지역 계정 및 평범한 일관성 수준의 모든 다중 지역 계정에 제공하고, 99.999% 읽기 가용성을 모든 다중 지역 데이터베이스 계정에 제공합니다.
* Azure Cosmos DB에는 낮은 대기 시간의 응답 시간(밀리초)을 지원하는 SSD 지원 스토리지가 있습니다.
* Azure Cosmos DB는 최종, 일관된 접두사, 세션, 제한된 부실 등의 일관성 수준을 지원하므로 완전한 유연성과 성능 대비 낮은 비용이 제공됩니다. 수준 일관성에서 Azure Cosmos DB만큼 큰 유연성을 제공하는 데이터베이스 서비스는 없습니다. 
* Azure Cosmos DB에는 스토리지 및 처리량을 독립적으로 측정하는, 유연하고 데이터 친화적인 가격 책정 모델이 있습니다.
* Azure Cosmos DB의 예약된 처리량 모델을 사용하면 기본 하드웨어의 CPU/메모리/IOP 대신 읽기/쓰기 수로 예측할 수 있습니다.
* Azure Cosmos DB는 일별 요청 수가 조 단위에 이르는 대량 요청 볼륨으로 확장되도록 설계되었습니다.

이러한 특성은 낮은 응답 시간이 필요하고 방대한 양의 읽기 및 쓰기를 처리해야 하는 웹, 모바일, 게임 및 IoT 애플리케이션에 유용합니다.

## <a name="iot-and-telematics"></a>IoT 및 텔레매틱스
IoT 사용 사례는 일반적으로 데이터를 수집, 처리 및 저장하는 방법에서 일부 패턴을 공유합니다.  먼저 이러한 시스템이 다양한 로컬의 디바이스 센서에서 데이터 버스트를 수집해야 합니다. 다음으로 이러한 시스템이 스트리밍 데이터를 처리 및 분석하여 실시간 통찰력을 끌어냅니다. 그런 다음 배치 분석에 사용할 수 있도록 데이터를 콜드 스토리지에 보관합니다. Microsoft Azure Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight 및 Power BI를 비롯 한 IoT 사용 사례에 적용할 수 있는 다양 한 서비스를 제공 합니다. 

![Azure Cosmos DB IoT 참조 아키텍처](./media/use-cases/iot.png)

Azure Event Hubs는 짧은 대기 시간으로 높은 처리량 데이터 수집을 제공하기 때문에 데이터 버스트를 수집할 수 있습니다. 실시간으로 처리해야 하는 수집된 데이터는 분석을 위해 Azure Stream Analytics로 전달할 수 있습니다. 임시 쿼리를 위해 데이터를 Azure Cosmos DB에 로드할 수 있습니다. 데이터가 Azure Cosmos DB에 로드되고 나면 데이터를 쿼리할 수 있습니다. 또한 새 데이터와 기존 데이터의 변경 내용을 변경 피드에서 읽을 수 있습니다. 변경 피드는 지속적으로 Cosmos 컨테이너에 대한 변경 내용을 순차적으로 저장 하는 로그만 추가 합니다. 실시간 분석의 일부로 Azure Cosmos DB의 모든 데이터 또는 변경된 데이터만 참조 데이터로 사용할 수 있습니다. 또한 Pig, Hive 또는 Map/Reduce 작업을 위해 Azure Cosmos DB 데이터를 HDInsight에 연결하여 데이터를 추가로 구체화하고 처리할 수 있습니다.  그런 다음 보고를 위해 구체화된 데이터가 Azure Cosmos DB에 다시 로드됩니다.   

Azure Cosmos DB, EventHub 및 Storm을 사용한 샘플 IoT 솔루션은 [GitHub의 hdinsight-storm-examples 리포지토리](https://github.com/hdinsight/hdinsight-storm-examples/)를 참조하세요.

IoT용 Azure 제품에 대한 자세한 내용은 [사물 인터넷 만들기](https://www.microsoft.com/en-us/internet-of-things)를 참조하세요. 

## <a name="retail-and-marketing"></a>소매 및 마케팅
Azure Cosmos DB는 Windows 스토어 및 XBox Live를 실행하는 Microsoft 고유의 전자 상거래 플랫폼에서 광범위하게 사용됩니다. 또한 카탈로그 데이터 저장 및 주문 처리 파이프라인의 이벤트 소싱을 위해 소매 업계에서 사용됩니다.

카탈로그 데이터 사용 시나리오에는 사용자, 장소 및 제품과 같은 엔터티의 특성 집합 저장 및 쿼리가 포함됩니다. 카탈로그 데이터의 몇 가지 예로 사용자 계정, 제품 카탈로그, IoT 디바이스 레지스트리, BOM 시스템 등이 있습니다. 이 데이터의 특성은 다양하며, 시간에 따라 애플리케이션 요구 사항에 맞게 변경될 수 있습니다.

제품 카탈로그의 예로 자동차 부품 공급업체를 고려해 보세요. 각 부품에는 모든 부품이 공유하는 공통 특성 외에 고유한 특성이 있을 수 있습니다. 또한 특정 부품의 특성이 다음 연도에 새 모델이 출시될 때는 변경될 수 있습니다. Azure Cosmos DB는 유연한 스키마와 계층형 데이터를 지원하므로 제품 카탈로그 데이터를 저장하는 데 적합합니다.

![Azure Cosmos DB 소매 카탈로그 참조 아키텍처](./media/use-cases/product-catalog.png)

Azure Cosmos DB는 해당 [변경 피드](change-feed.md) 기능을 사용하여 이벤트 소싱에서 이벤트 기반 아키텍처를 지원하는 데 종종 사용됩니다. 변경 피드는 다운스트림 마이크로 서비스에 Azure Cosmos DB의 삽입 및 업데이트(예: 주문 이벤트)를 안정적으로 증분 방식으로 읽는 기능을 제공합니다. 이 기능은 영구 이벤트 저장소를 상태 변경 이벤트에 대한 메시지 브로커로 제공하고 많은 마이크로 서비스 간에 주문 처리 워크플로를 구동하는 데 사용될 수 있습니다([서버 없는 Azure Functions](https://azure.com/serverless)로 구현될 수 있음).

![Azure Cosmos DB 주문 파이프라인 참조 아키텍처](./media/use-cases/event-sourcing.png)

뿐만 아니라 Azure Cosmos DB에 저장된 데이터는 Apache Spark 작업을 통해 빅 데이터 분석에 사용되는 HDInsight와 통합할 수 있습니다. Azure Cosmos DB용 Spark 커넥터에 대한 자세한 내용은 [Cosmos DB 및 HDInsight를 사용하여 Spark 작업 실행](spark-connector.md)을 참조하세요.

## <a name="gaming"></a>게임
데이터베이스 계층은 게임 애플리케이션의 중요한 구성 요소입니다. 오늘날의 게임은 모바일/콘솔 클라이언트에서 그래픽 처리를 수행하지만 게임 내 통계, 소셜 미디어 통합 및 고득점 순위표와 같은 사용자 지정되고 개인 설정된 콘텐츠를 제공하기 위해 클라우드에 의존합니다. 게임은 몰입도 높은 게임 환경을 제공하기 위해 1밀리초의 읽기 및 쓰기 대기 시간을 요구하는 경우가 종종 있습니다. 게임 데이터베이스는 속도가 빨라야 하며 신규 게임 출시 및 기능 업데이트 동안 요청 속도의 대량 스파이크를 처리할 수 있어야 합니다.

Azure Cosmos DB는 [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)([Next Games](https://www.nextgames.com/)), [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) 등의 게임에서 사용됩니다. Azure Cosmos DB는 게임 개발자에게 다음과 같은 이점을 제공합니다.

* Azure Cosmos DB는 성능을 탄력적으로 확장하거나 축소할 수 있습니다. 따라서 게임에서 단일 API 호출을 통해 수십 명부터 수백만 명에 이르는 동시 게이머의 프로필 및 통계를 처리할 수 있습니다.
* Azure Cosmos DB는 게임 플레이 중 지연을 방지하기 위해 밀리초 단위의 읽기 및 쓰기를 지원합니다.
* Azure Cosmos DB의 자동 인덱싱은 여러 속성에 대한 실시간 필터링을 지원합니다. 예를 들어 내부 플레이어 ID로 플레이어를 찾거나 GameCenter, Facebook, Google ID 또는 플레이드의 길드 멤버 자격에 따른 쿼리로 플레이어를 찾을 수 있습니다. 이는 복잡한 인덱싱을 작성하거나 인프라를 분할하지 않고도 가능합니다.
* 게임 내 채팅 메시지, 플레이어 길드 멤버 자격, 완료된 챌린지, 고득점 순위표, 소셜 그래프 등의 소셜 기능을 유연한 스키마로 보다 쉽게 구현할 수 있습니다.
* Azure Cosmos DB는 최소한의 설치 및 관리 작업만 필요한 관리되는 PaaS(Platform-as-a-Service)이므로 신속한 반복이 가능하며 시장 출시 시간을 단축해 줍니다.

![Azure Cosmos DB 게임 참조 아키텍처](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>웹 및 모바일 애플리케이션
Azure Cosmos DB는 일반적으로 웹 및 모바일 애플리케이션 내에서 사용되며 소셜 상호 작용을 모델링하여 타사 서비스와 통합하고 풍부한 개인 설정 환경을 빌드하는 데 적합합니다. Cosmos DB SDK를 사용하면 인기 있는 [Xamarin 프레임워크](mobile-apps-with-xamarin.md)를 사용하여 풍부한 iOS 및 Android 애플리케이션을 빌드할 수 있습니다.  

### <a name="social-applications"></a>소셜 애플리케이션
Azure Cosmos DB의 일반적인 사용 사례는 웹, 모바일 및 소셜 미디어 애플리케이션에 대한 UGC(사용자 생성 콘텐츠)를 저장 및 쿼리하는 것입니다. UGC의 몇 가지 예로 채팅 세션, 트윗, 블로그 게시물, 평가, 의견 등이 있습니다. 대개 소셜 미디어 애플리케이션의 UGC는 고정된 구조로 제한되지 않는 자유 형식 텍스트, 속성, 태그 및 관계의 혼합입니다. 변환이나 복합 개체-관계형 매핑 계층을 요구하지 않고 채팅, 의견, 게시물 등의 콘텐츠를 Cosmos DB에 저장할 수 있습니다.  개발자가 애플리케이션 코드를 반복할 때 요구 사항에 맞게 데이터 속성을 쉽게 추가하거나 수정할 수 있으므로 신속한 개발이 가능합니다.  

타사 소셜 네트워크와 통합되는 애플리케이션은 이러한 네트워크의 스키마 변화에 응답해야 합니다. Cosmos DB에서는 기본적으로 데이터가 자동으로 인덱싱되므로 언제든지 데이터를 쿼리할 수 있습니다. 따라서 이러한 애플리케이션이 해당 요구에 따라 유연하게 프로젝션을 검색할 수 있습니다.

많은 소셜 애플리케이션은 글로벌 규모로 실행되며 예측할 수 없는 사용 패턴을 나타낼 수 있습니다. 사용량 수요에 따라 애플리케이션 계층의 크기가 조정되므로 데이터 저장소의 크기를 조정하는 유연성이 필요합니다.  Cosmos DB 계정으로 데이터 파티션을 더 추가하여 규모를 확장할 수 있습니다.  또한 여러 지역에 추가 Cosmos DB 계정을 만들 수도 있습니다. Cosmos DB 서비스 지역 가용성은 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

![Azure Cosmos DB 웹앱 참조 아키텍처](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>개인 설정
오늘날 최신 애플리케이션은 복잡한 뷰와 환경으로 제공됩니다. 이는 일반적으로 동적이며, 사용자 기본 설정이나 기분 및 브랜딩 요구에 맞게 조정됩니다. 따라서 애플리케이션이 UI 요소와 환경을 신속하게 렌더링하려면 개인 설정을 효과적으로 검색할 수 있어야 합니다. 

Cosmos DB에서 지원되는 형식인 JSON은 간단할 뿐 아니라 JavaScript에서 쉽게 해석될 수 있으므로 UI 레이아웃 데이터를 나타내는 효과적인 형식입니다. Cosmos DB는 빠른 읽기 및 대기 시간이 짧은 쓰기를 허용하는 조정 가능한 일관성 수준을 제공합니다. 따라서 개인 설정을 비롯한 UI 레이아웃 데이터를 Cosmos DB에 JSON 문서로 저장하면 네트워크를 통해 이 데이터를 효과적으로 전달할 수 있습니다.

![Azure Cosmos DB 웹앱 참조 아키텍처](./media/use-cases/personalization.png)

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB를 시작하려면 계정을 만들고 Cosmos DB를 시작하는 과정을 안내하는 [빠른 시작](create-sql-api-dotnet.md)을 따릅니다.

* Azure Cosmos DB를 사용 하는 고객에 대한 자세한 내용을 알아보려면 [고객 사례 연구](https://azure.microsoft.com/case-studies/?service=cosmos-db) 페이지를 참조 하세요.
