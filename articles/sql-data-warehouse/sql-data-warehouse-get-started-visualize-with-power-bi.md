---
title: Power BI Microsoft Azure로 SQL Data Warehouse 데이터 시각화
description: Power BI로 SQL Data Warehouse 데이터 시각화
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2377466ba830eed9fed0e9a4033b3eca3d351c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474757"
---
# <a name="visualize-data-with-power-bi"></a>Power BI를 사용하여 데이터 시각화
이 자습서에서는 Power BI를 사용하여 SQL Data Warehouse에 연결하고 몇 가지 기본적인 시각화를 만드는 방법을 보여줍니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

* AdventureWorksDW 데이터베이스로 미리 로드된 SQL Data Warehouse. 데이터 웨어하우스를 프로비전하려면 [SQL Data Warehouse 만들기](create-data-warehouse-portal.md)를 참조하고 샘플 데이터 로드를 선택합니다. 데이터 웨어하우스는 있지만 샘플 데이터가 없는 경우 [WideWorldImportersDW를 로드](load-data-wideworldimportersdw.md)할 수 있습니다.

## <a name="1-connect-to-your-database"></a>1. 데이터베이스 연결
Power BI를 열고 AdventureWorksDW 데이터베이스에 연결하려면

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **SQL 데이터베이스** 를 클릭하고 AdventureWorks SQL Data Warehouse 데이터베이스를 선택합니다.
   
    ![데이터베이스 찾기](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. 'Power BI에서 열기' 단추를 클릭합니다.
   
    ![Power BI 단추](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. 이제 데이터베이스 웹 주소를 표시하는 SQL Data Warehouse 연결 페이지가 표시됩니다. 다음을 클릭합니다.
   
    ![Power BI 연결](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. Azure SQL 서버 사용자 이름 및 암호를 입력합니다.
   
    ![Power BI 로그인](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. 데이터베이스를 열려면 왼쪽 블레이드에서 AdventureWorksDW 데이터 세트를 클릭합니다.
   
    ![Power BI AdventureWorksDW 열기](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2. 보고서 만들기
이제 Power BI를 사용하여 AdventureWorksDW 샘플 데이터를 분석할 준비가 되었습니다. 분석을 수행하기 위해 AdventureWorksDW에는 AggregateSales라는 뷰가 포함되어 있습니다. 이 뷰는 회사의 판매를 분석하기 위한 주요 메트릭 중 일부를 포함합니다.

1. 우편 번호에 따라 판매액 지도를 만들려면 오른쪽 필드 창에서 AggregateSales 뷰를 클릭하여 확장합니다. PostalCode 및 SalesAmount 열을 클릭하여 선택합니다.
   
    ![Power BI가 AggregateSales 선택](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI는 지리적 데이터로 자동으로 인식하고 지도에 배치합니다.
   
    ![Power BI 맵](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. 이 단계에서는 고객 수익당 매출액을 보여주는 막대 그래프를 만듭니다. 막대 그래프를 만들려면 확장된 AggregateSales 보기로 이동합니다. SalesAmount 필드를 클릭합니다. 고객 수익 필드를 왼쪽으로 끌어 축에 놓습니다.
   
    ![Power BI가 축 선택](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    왼쪽에 가로 막대형 차트가 있습니다.
   
    ![Power BI 막대형](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. 이 단계에서는 주문 날짜당 판매 금액을 보여주는 꺽은선형 차트를 만듭니다. 꺽은선형 차트를 만들려면 확장된 AggregateSales 보기로 이동합니다. SalesAmount 및 OrderDate를 클릭합니다. 시각화 열에서 꺽은선형 차트 아이콘을 클릭합니다. 이 아이콘은 시각화에서 두 번째 열의 첫 번째 아이콘입니다.
   
    ![Power BI가 꺾은선형 차트 선택](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    이제 데이터를 다양한 시각화로 보여주는 보고서가 작성되었습니다.
   
    ![Power BI 꺾은선형](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

언제든지 **파일**을 클릭하고 **저장**을 선택하여 진행 상황을 저장할 수 있습니다.

## <a name="using-direct-connect"></a>직접 연결 사용
Azure SQL Database와 함께, SQL Data Warehouse 직접 연결을 사용하면 Power BI의 분석 기능과 함께 논리 푸시 다운을 할 수 있습니다. 직접 연결을 사용하여 쿼리는 데이터를 탐색할 때 실시간으로 Azure SQL Data Warehouse로 다시 전송됩니다.  SQL Data Warehouse의 크기와 결합된 기능을 사용하면 테라바이트 규모의 데이터에 대해 동적 보고서를 몇 분 내에 만들 수 있습니다. 또한 Power BI 단추에서 열기 소개를 사용하면 사용자는 Azure의 다른 부분에서 정보를 수집하지 않고 Power BI를 직접 SQL Data Warehouse에 연결할 수 있습니다.

직접 연결을 사용하는 경우:

* 연결할 때 정규화된 서버 이름을 지정합니다.
* 데이터베이스에 대한 방화벽 규칙은 Azure 서비스에 액세스를 허용하도록 구성되어야 합니다.
* 열 선택 또는 필터 추가와 같은 모든 작업은 데이터 웨어하우스에 직접 쿼리됩니다.
* 타일은 약 15분마다 자동으로 새로 고쳐집니다.
* Q&amp;A는 직접 연결 데이터 세트에 대해 사용할 수 없습니다.
* 스키마 변경 내용은 자동으로 통합됩니다.
* 모든 직접 연결 쿼리는 2분 후에 시간 초과됩니다.

환경이 개선되면서 이러한 제한 및 참고 사항은 변경될 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 샘플 데이터로 [개발](sql-data-warehouse-overview-develop.md), [로드](design-elt-data-loading.md) 또는 [마이그레이션](sql-data-warehouse-overview-migrate.md)할 준비 시간을 드리겠습니다. 또는 [Power BI 웹 사이트](https://www.powerbi.com/)를 살펴보세요.
