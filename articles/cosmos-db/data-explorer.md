---
title: Azure Cosmos DB 탐색기를 사용하여 데이터 관리
description: Azure Cosmos DB 탐색기는 Azure Cosmos DB에 저장된 데이터를 보고 관리할 수 있는 독립 실행형 웹 기반 인터페이스입니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: dech
ms.openlocfilehash: d50bf7d865cb286f2a1421156b477dc92dc978d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889455"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Azure Cosmos 탐색기를 사용하여 데이터 작업 

Azure Cosmos DB 탐색기는 Azure Cosmos DB에 저장된 데이터를 보고 관리할 수 있는 독립 실행형 웹 기반 인터페이스입니다. Azure Cosmos DB 탐색기는 Azure Cosmos DB 계정을 만들 때 Azure Portal에서 사용할 수 있는 기존 **데이터 탐색기** 탭과 동일합니다. 기존 데이터 탐색기에 비해 Azure Cosmos DB 탐색기의 주요 이점은 다음과 같습니다.

* 전체 화면에서 데이터를 보고, 쿼리, 저장 프로시저, 트리거를 실행하고, 해당 결과를 볼 수 있습니다.  

* Azure Portal 또는 구독에 대한 액세스 권한이 없는 다른 사용자에게 데이터베이스 계정 및 해당 컬렉션에 대한 임시 또는 영구 읽기 또는 읽기/쓰기 권한을 제공할 수 있습니다.  

* Azure Portal 또는 구독에 대한 액세스 권한이 없는 다른 사용자와 쿼리 결과를 공유할 수 있습니다.  

## <a name="access-azure-cosmos-db-explorer"></a>Azure Cosmos DB 탐색기 액세스

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 

2. **모든 리소스**에서 Azure Cosmos DB 계정을 찾아서 탐색한 다음, 키를 선택하고 **기본 연결 문자열**을 복사합니다.  

3. https://cosmos.azure.com/으로 이동하여 연결 문자열을 붙여넣고 **연결**을 선택합니다. 연결 문자열을 사용하면 시간 제한 없이 Azure Cosmos DB 탐색기에 액세스할 수 있습니다.  

   Azure Cosmos DB 계정에 대한 임시 액세스를 다른 사용자에게 제공하려는 경우, 읽기/쓰기 및 읽기 권한 URL을 사용하면 됩니다. 

4. **데이터 탐색기** 블레이드를 열고 **전체 화면 열기**를 선택합니다. 팝업 대화 상자에서 **읽기/쓰기** 및 **읽기**라는 두 개의 액세스 URL을 볼 수 있습니다. 이러한 URL을 사용하면 Azure Cosmos DB 계정을 다른 사용자와 임시로 공유할 수 있습니다. 계정에 대한 액세스는 24시간 후에 만료되며, 이때 새 액세스 URL 또는 연결 문자열을 사용하여 다시 연결할 수 있습니다. 

   **읽기/쓰기** - 읽기/쓰기 URL을 다른 사용자와 공유할 경우, 이 사용자는 해당 특정 계정과 연결된 데이터베이스, 컬렉션, 쿼리 및 기타 리소스를 보고 수정할 수 있습니다.

   **읽기** - 읽기 전용 URL을 다른 사용자와 공유할 경우, 이 사용자는 해당 특정 계정과 연결된 데이터베이스, 컬렉션, 쿼리 및 기타 리소스를 볼 수 있습니다. 예를 들어, Azure Portal 또는 Azure Cosmos DB 계정에 대한 액세스 권한이 없는 팀원과 쿼리 결과를 공유하려는 경우, 이 URL을 제공할 수 있습니다.

   계정을 열 때 사용할 액세스 유형을 선택하고 **열기**를 클릭합니다. 탐색기를 연 후의 환경은 Azure Portal의 데이터 탐색기 탭과 동일합니다.   

   ![Azure Cosmos DB 탐색기 열기](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>알려진 문제

현재, 임시 읽기/쓰기 또는 읽기 권한을 공유할 수 있는 **전체 화면 열기** 환경은 Azure Cosmos DB Gremlin 및 Table API 계정에 대해 아직 지원되지 않습니다. 하지만 Azure Cosmos DB 탐색기에 연결 문자열을 전달하면 Gremlin 및 Table API 계정을 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB 탐색기를 시작하여 데이터를 관리하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* SQL 구문을 사용하여 [쿼리](sql-api-query-reference.md) 정의를 시작하고, 저장 프로시저, UDF, 트리거를 사용하여 [서버 쪽 프로그래밍](stored-procedures-triggers-udfs.md)을 수행합니다. 
