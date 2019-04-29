---
title: SQL Server Management Studio에 Azure Blockchain Workbench 데이터 사용
description: SQL Server Management Studio 내에서 Azure Blockchain Workbench의 SQL Database에 연결하는 방법을 알아봅니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: a5554f1fe0a14799a100aefc975a110d3a9a6311
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118998"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>SQL Server Management Studio에 Azure Blockchain Workbench 데이터 사용

Microsoft SQL Server Management Studio는 Azure Blockchain Workbench의 SQL DB에 대한 쿼리를 신속하게 작성하고 테스트할 수 있는 기능을 제공합니다. 이 섹션에서는 SQL Server Management Studio 내에서 Azure Blockchain Workbench의 SQL Database에 연결하는 방법을 단계별로 안내합니다.

## <a name="prerequisites"></a>필수 조건

* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)를 다운로드합니다.

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio를 Azure Blockchain Workbench의 데이터에 연결

1. SQL Server Management Studio를 열고 **연결**을 선택합니다.
2. **데이터베이스 엔진**을 선택합니다.

    ![데이터베이스 엔진](./media/data-sql-management-studio/database-engine.png)

3. **서버에 연결** 대화 상자에서 서버 이름 및 데이터베이스 자격 증명을 입력합니다.

    Azure Blockchain Workbench 배포 프로세스에서 생성된 자격 증명을 사용하는 경우 사용자 이름은 **dbadmin**이고 암호는 배포 중에 사용자가 입력한 암호입니다.

    ![SQL 자격 증명 입력](./media/data-sql-management-studio/sql-creds.png)

   1. SQL Server Management Studio는 Azure Blockchain Workbench 데이터베이스의 데이터베이스, 데이터베이스 보기 및 저장된 프로시저 목록을 표시합니다.

      ![데이터베이스 목록](./media/data-sql-management-studio/db-list.png)

5. 데이터베이스 보기와 연결된 데이터를 보려면 다음 단계를 사용하여 자동으로 select 문을 생성하면 됩니다.
6. 개체 탐색기에서 아무 데이터베이스 보기를 마우스 오른쪽 단추로 클릭합니다.
7. **스크립트 보기**를 선택합니다.
8. **SELECT to**를 선택합니다.
9. **새 쿼리 편집기 창**을 선택합니다.
10. **새 쿼리**를 선택하여 새 쿼리를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench의 데이터베이스 뷰](database-views.md)
