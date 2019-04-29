---
title: SQL Data Warehouse와 함께 Azure Stream Analytics 사용 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse와 함께 Azure Stream Analytics 사용을 위한 팁
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
origin.date: 03/22/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 3a260102e9964cfc248ed3dbabab487bd1c5dfc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474337"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>SQL Data Warehouse와 함께 Azure Stream Analytics 사용
Azure Stream Analytics는 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. [Azure Stream Analytics 소개][Introduction to Azure Stream Analytics]를 읽어 기본 사항을 배울 수 있습니다. [Azure Stream Analytics를 사용하여 시작][Get started using Azure Stream Analytics] 자습서에 따라 Stream Analytics로 종단간 솔루션을 만드는 방법에 대해 알 수 있습니다.

이 문서에서 Stream Analytics 작업에 대한 출력 싱크로 Azure SQL Data Warehouse 데이터베이스를 사용하는 방법에 대해 배웁니다.

## <a name="prerequisites"></a>필수 조건
먼저, [Azure Stream Analytics를 사용하여 시작][Get started using Azure Stream Analytics] 자습서에서 다음 단계를 실행합니다.  

1. 이벤트 허브 입력 만들기
2. 이벤트 생성기 애플리케이션 구성 및 시작
3. Stream Analytics 작업 프로비전
4. 작업 입력 및 쿼리 지정

그런 다음 SQL Data Warehouse 데이터베이스를 만듭니다.

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>작업 출력 지정: SQL Data Warehouse 데이터베이스
### <a name="step-1"></a>1단계
Stream Analytics 작업에서 클릭 **출력** 클릭 한 다음 확인 하 고 페이지 맨 위에서 **추가**합니다.

### <a name="step-2"></a>2단계
SQL Database를 선택 합니다.

### <a name="step-3"></a>3단계
다음 페이지에 다음 값을 입력합니다.

* *출력 별칭*: 이 작업 출력의 식별 이름을 입력합니다.
* *구독*:
  * SQL Data Warehouse가 Stream Analytics 작업과 동일한 구독 내에 있는 경우 현재 구독에서 SQL Database 사용을 선택할 수 있습니다.
  * 데이터베이스가 다른 구독에 있는 경우 다른 구독에서 SQL Database 사용을 선택합니다.
* *데이터베이스*: 대상 데이터베이스의 이름을 지정합니다.
* *서버 이름*: 방금 지정한 데이터베이스용 서버 이름을 지정합니다. Azure Portal을 사용하여 찾을 수 있습니다.

![][server-name]

* *사용자 이름*: 데이터베이스에 대한 쓰기 권한이 있는 계정의 사용자 이름을 지정합니다.
* *암호*: 지정된 사용자 계정의 암호를 입력합니다.
* *테이블*: 데이터베이스의 대상 테이블 이름을 지정합니다.

![][add-database]

### <a name="step-4"></a>4단계:
확인 단추를 클릭하여 이 작업 출력을 추가하고 Stream Analytics가 데이터베이스에 성공적으로 연결될 수 있는지 확인합니다.

데이터베이스에 연결에 성공 하면 포털에서 알림이 표시 됩니다. 데이터베이스에 연결을 테스트 하려면 테스트를 클릭할 수 있습니다.

## <a name="next-steps"></a>다음 단계
통합 개요는 [SQL Data Warehouse 통합 개요][SQL Data Warehouse integration overview]를 참조하세요.

더 많은 개발 팁은 [SQL Data Warehouse 개발 개요][SQL Data Warehouse development overview]를 참조하세요.

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: /stream-analytics/

<!-- Update_Description: update meta properties, wording update -->