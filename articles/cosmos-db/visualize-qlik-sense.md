---
title: Qlik Sense를 Azure Cosmos DB에 연결하고 데이터 시각화
description: 이 문서에서는 Azure Cosmos DB를 Qlik Sense에 연결하고 데이터를 시각화하는 데 필요한 단계를 설명합니다.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 10/22/2018
ms.reviewer: sngun
ms.openlocfilehash: 4532962b6fd9f40fad625ab000116e5a617682e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765949"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Qlik Sense를 Azure Cosmos DB에 연결하고 데이터 시각화

Qlik Sense는 여러 소스의 데이터를 단일 보기로 결합하는 데이터 시각화 도구입니다. 데이터에 대한 인사이트를 즉시 얻을 수 있도록 Qlik Sense는 데이터에서 가능한 모든 관계를 인덱싱합니다. Qlik Sense를 사용하여 Azure Cosmos DB 데이터를 시각화할 수 있습니다. 이 문서에서는 Azure Cosmos DB를 Qlik Sense에 연결하고 데이터를 시각화하는 데 필요한 단계를 설명합니다. 

> [!NOTE]
> Qlik Sense를 Azure Cosmos DB에 연결하는 기능은 현재 SQL API 및 Azure Cosmos DB의 API for MongoDB 계정에만 지원됩니다.

다음 방법으로 Qlik Sense를 Azure Cosmos DB에 연결할 수 있습니다.

* ODBC 커넥터를 사용하여 Cosmos DB SQL API로.

* Qlik Sense MongoDB 커넥터(현재 미리 보기)를 사용하여 Azure Cosmos DB의 API for MongoDB로.

* Qlik Sense에서 REST API 커넥터를 사용하여 Azure Cosmos DB의 API for MongoDB 및 SQL API로.

* Qlik Core용 gRPC 커넥터를 사용하여 Cosmos DB Mongo DB API로.
이 문서에서는 ODBC 커넥터를 사용하여 Cosmos DB SQL API에 연결하는 자세한 방법을 설명합니다.

이 문서에서는 ODBC 커넥터를 사용하여 Cosmos DB SQL API에 연결하는 자세한 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 지침을 따르기 전에, 다음 리소스가 준비되었는지 확인합니다.

* [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense)을 다운로드하거나 [Qlik Sense 마켓플레이스 항목을 설치](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense)하여 Azure에서 Qlik Sense를 설치합니다.

* [비디오 게임 데이터](https://www.kaggle.com/gregorut/videogamesales)를 다운로드합니다. 이 샘플 데이터는 CSV 형식입니다. 이 데이터를 Cosmos DB 계정에 저장하고 Qlik Sense에서 시각화할 것입니다.

* 빠른 시작 문서의 [계정 만들기](create-sql-api-dotnet.md#create-account) 섹션에 설명된 단계를 사용하여 Azure Cosmos DB SQL API 계정을 만듭니다.

* [데이터베이스 및 컬렉션 만들기](create-sql-api-dotnet.md#create-collection-database) – 컬렉션 처리량 값을 1000 RU/s로 설정할 수 있습니다. 

* 샘플 비디오 게임 판매 데이터를 Cosmos DB 계정에 로드합니다. Azure Cosmos DB 데이터 마이그레이션 도구를 사용하여 데이터를 가져올 수 있습니다. 데이터를 [순차적](import-data.md#SQLSeqTarget)으로 또는 [대량으로 가져올 수 있습니다](import-data.md#SQLBulkTarget). Cosmos DB 계정으로 데이터를 가져올 때까지 약 3-5분이 걸립니다.

* [ODBC 드라이버를 사용하여 Cosmos DB에 연결](odbc-driver.md) 문서의 단계에 따라 ODBC 드라이버를 다운로드, 설치 및 구성합니다. 비디오 게임 데이터는 간단한 데이터 집합이며, 스키마를 편집할 필요 없이 기본 컬렉션 매핑 스키마를 사용하면 됩니다.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Qlik Sense를 Cosmos DB에 연결

1. Qlik Sense를 열고 **새 앱 만들기**를 선택합니다. 앱 이름을 입력하고 **만들기**를 선택합니다.

   ![새 Qlik Sense 앱 만들기](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. 새 앱을 만든 후에는 **앱 열기**를 선택하고 **파일 및 기타 소스의 데이터 추가**를 선택합니다. 

3. 데이터 원본에서 **ODBC**를 선택하여 새 연결 설정 창을 엽니다. 

4. **사용자 DSN**으로 전환하고, 앞에서 만든 ODBC 연결을 선택합니다. 연결 이름을 입력하고 **만들기**를 선택합니다. 

   ![새 연결 만들기](./media/visualize-qlik-sense/create-new-connection.png)

5. 연결을 만든 후에는 비디오 게임 데이터가 들어 있는 데이터베이스 및 컬렉션을 선택하여 미리 볼 수 있습니다.

   ![데이터베이스 및 컬렉션 선택](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. 다음으로, **데이터 추가**를 선택하여 Qlik Sense에 데이터를 로드합니다. Qlik Sense에 데이터를 로드한 후에는 데이터에 대한 인사이트를 생성하고 데이터를 분석할 수 있습니다. 인사이트를 사용할 수도 있고 비디오 게임 판매를 탐색하는 고유의 앱을 빌드할 수도 있습니다. 다음 이미지에 예가 나와 있습니다. 

   ![데이터 가상화](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>ODBC를 사용하여 연결할 때의 제한 사항 

Cosmos DB는 개발자 요구 사항을 중심으로 모델링된 드라이버를 사용하는 스키마 없는 분산 데이터베이스입니다. ODBC 드라이버를 사용하려면 열, 데이터 형식 및 기타 속성을 유추하는 스키마가 있는 데이터베이스가 필요합니다. SQL API는 ANSI SQL이 아니기 때문에 관계형 기능을 사용하는 일반 SQL 쿼리 또는 DML 구문을 Cosmos DB SQL API에 적용할 수 없습니다. 이러한 이유로 ODBC 드라이버를 통해 실행된 SQL 문은 일부 구문에 해당하는 항목이 없는 Cosmos DB별 SQL 구문으로 변환됩니다. 이러한 변환 문제를 방지하려면 ODBC 연결을 설정할 때 스키마를 적용해야 합니다. [ODBC 드라이버를 사용하여 연결](odbc-driver.md) 문서에서는 스키마 구성을 위한 권장 사항 및 방법을 제공합니다. Cosmos DB 계정 내의 모든 데이터베이스/컬렉션에 대해 이 매핑을 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

Power BI 같은 다른 시각화 도구를 사용하는 경우 다음 문서의 지침을 사용하여 도구에 연결할 수 있습니다.

* [Power BI 커넥터를 사용하여 Cosmos DB 데이터 시각화](powerbi-visualize.md)
