---
title: 데이터 정렬 지원
description: Azure Synapse Analytics에서 Synapse SQL에 대한 데이터 정렬 형식 지원
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9a83646556838732a3b79f93771c762f00ed5afc
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144298"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Synapse SQL에 대한 데이터베이스 데이터 정렬 지원 

데이터 정렬은 문자 기반 데이터 형식에 대한 로캘, 코드 페이지, 정렬 순서 및 문자 민감도 규칙을 제공합니다. 데이터 정렬 정보를 요구하는 모든 열과 식은 데이터베이스 설정에서 선택한 데이터 정렬을 상속합니다. 기본 상속은 문자 기반 데이터 형식에 대해 다른 데이터 정렬을 명시적으로 지정하여 재정의될 수 있습니다.

새 전용 SQL 풀 데이터베이스를 만들 때 Azure Portal에서 기본 데이터베이스 데이터 정렬을 변경할 수 있습니다. 이 기능을 사용하면 3800개의 지원 데이터베이스 데이터 정렬 중 하나를 사용하여 새 데이터베이스를 훨씬 쉽게 만들 수 있습니다.

생성 시 CREATE DATABASE 문을 사용하여 기본 서버리스 SQL 풀 데이터베이스 데이터 정렬을 지정할 수 있습니다.

## <a name="change-collation"></a>데이터 정렬 변경
전용 SQL 풀 데이터베이스의 기본 데이터 정렬을 변경하려면 프로비전 환경의 데이터 정렬 필드를 업데이트합니다. 예를 들어, 기본 데이터 정렬이 대/소문자를 구분하도록 변경하려면 데이터 정렬의 이름을 SQL_Latin1_General_CP1_CI_AS에서 SQL_Latin1_General_CP1_CS_AS로 변경합니다. 

서버리스 SQL 풀 데이터베이스의 기본 데이터 정렬을 변경하려면 ALTER DATABASE 문을 사용하면 됩니다.

## <a name="list-of-unsupported-collation-types"></a>지원되지 않는 데이터 정렬 형식 목록
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

또한 전용 SQL 풀은 다음 데이터 정렬 형식을 지원하지 않습니다.

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>현재 데이터 정렬 확인
데이터베이스의 현재 데이터 정렬을 확인하려면 다음 T-SQL 조각을 실행합니다.
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
'Collation'을 속성 매개 변수로 전달하는 경우 DatabasePropertyEx 함수는 지정된 데이터베이스에 대한 현재 데이터 정렬을 반환합니다. 자세한 내용은 MSDN의 DatabasePropertyEx 함수를 확인하세요.

## <a name="next-steps"></a>다음 단계

전용 SQL 풀 및 서버리스 SQL 풀의 모범 사례에 대한 추가 정보는 다음 문서에서 찾을 수 있습니다.

- [전용 SQL 풀에 대한 모범 사례](./best-practices-dedicated-sql-pool.md)
- [서버리스 SQL 풀에 대한 모범 사례](./best-practices-serverless-sql-pool.md)