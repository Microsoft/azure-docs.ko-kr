---
title: Microsoft Excel에서 Azure Blockchain Workbench 데이터 사용
description: Microsoft Excel에서 Azure Blockchain Workbench SQL DB 데이터를 로드하고 보는 방법을 알아봅니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 560958c2b420d3a1ec592e6bd7e0124ecad7c984
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715342"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Microsoft Excel로 Azure Blockchain Workbench 데이터 보기

Microsoft Excel을 사용하여 Azure Blockchain Workbench SQL DB의 데이터를 볼 수 있습니다. 이 문서는 다음 작업에 필요한 단계를 제공합니다.

* Microsoft Excel에서 Blockchain Workbench 데이터베이스에 연결
* Blockchain Workbench 데이터베이스 테이블 및 보기 확인
* Blockchain Workbench 보기 데이터를 Excel로 로드

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench 데이터베이스에 연결

Blockchain Workbench 데이터베이스에 연결하려면:

1. Microsoft Excel을 엽니다.
2. **데이터** 탭에서 **데이터 가져오기**를 선택합니다.
3. **Azure에서**를 선택하고 **Azure SQL Database에서**를 선택합니다.

   ![Azure SQL 데이터베이스에 연결](./media/data-excel/connect-sql-db.png)

4. **SQL Server 데이터베이스** 대화 상자에서:

    * **서버**에 Blockchain Workbench 서버 이름을 입력합니다.
    * **데이터베이스(선택 사항)** 에 데이터베이스 이름을 입력합니다.

   ![데이터베이스 서버 및 데이터베이스 입력](./media/data-excel/provide-server-db.png)

5. **SQL Server 데이터베이스** 대화 상자 탐색 모음에서 **데이터베이스**를 선택합니다. **사용자 이름** 및 **암호**를 입력한 다음, **연결**을 선택합니다.

    > [!NOTE]
    > Azure Blockchain Workbench 배포 과정에서 만든 자격 증명을 사용하는 경우 **사용자 이름**은 `dbadmin`입니다. **암호**는 Blockchain Workbench를 배포할 때 만든 암호입니다.
    
   ![자격 증명을 입력하여 데이터베이스에 액세스](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>데이터베이스 테이블 및 보기 확인

데이터베이스에 연결하면 Excel 탐색기 대화 상자가 열립니다. 탐색기를 사용하여 데이터베이스의 테이블 및 보기를 살펴볼 수 있습니다. 보기는 보고용으로 설계되었으며 보기의 이름에 **vw**라는 접두사가 붙습니다.

   ![보기의 Excel 탐색기 미리 보기](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>보기 데이터를 Excel 통합 문서로 로드

다음 예제에서는 보기의 데이터를 Excel 통합 문서에 로드하는 방법을 보여줍니다.

1. **탐색기** 스크롤 막대에서 **vwContractAction** 보기를 선택합니다. Blockchain Workbench 데이터베이스의 계약과 관련된 모든 작업이 **vwContractAction** 미리 보기에 표시됩니다.
2. **로드**를 선택하여 보기의 모든 데이터를 검색하고 Excel 통합 문서에 배치합니다.

   ![보기에서 로드된 데이터](./media/data-excel/view-data.png)

데이터가 로드되었으니, Azure Blockchain Workbench 데이터베이스의 메타데이터 및 트랜잭션 데이터를 사용하여 사용자 고유의 보고서를 만드는 Excel 기능을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench의 데이터베이스 뷰](database-views.md)