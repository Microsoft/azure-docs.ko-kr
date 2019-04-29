---
title: Microsoft Power BI에서 Azure Blockchain Workbench 데이터 사용
description: Microsoft Power BI에서 Azure Blockchain Workbench SQL DB 데이터를 로드하고 보는 방법을 알아봅니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e12af686a450d39332c37700b9a14b9eb620307
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557875"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Microsoft Power BI에 Azure Blockchain Workbench 데이터 사용

Microsoft Power BI는 Power BI Desktop을 사용하여 SQL DB 데이터베이스로 간편하게 강력한 보고서를 생성한 후 [https://www.powerbi.com](https://www.powerbi.com)에 게시하는 기능을 제공합니다.

이 문서에는 PowerBI Desktop 내에서 Azure Blockchain Workbench의 SQL Database에 연결하고, 보고서를 만들고, 그 보고서를 powerbi.com에 배포하는 단계별 연습이 포함되어 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Power BI Desktop](https://aka.ms/pbidesktopstore)을 다운로드합니다.

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Power BI를 Azure Blockchain Workbench의 데이터에 연결

1.  Power BI Desktop을 엽니다.
2.  **데이터 가져오기**를 선택합니다.

    ![데이터 가져오기](./media/data-powerbi/get-data.png)
3.  데이터 원본 형식 목록에서 **SQL Server**를 선택합니다.

4.  대화 상자에서 서버 및 데이터베이스 이름을 입력합니다. 데이터를 가져올 것인지 아니면 **DirectQuery**를 수행할 것인지 지정합니다. **확인**을 선택합니다.

    ![SQL Server 선택](./media/data-powerbi/select-sql.png)

5.  Azure Blockchain Workbench에 액세스하려면 데이터베이스 자격 증명을 입력합니다. **데이터베이스**를 선택하고 자격 증명을 입력합니다.

    Azure Blockchain Workbench 배포 프로세스에서 생성된 자격 증명을 사용하는 경우 사용자 이름은 **dbadmin**이고 암호는 배포 중에 사용자가 입력한 암호입니다.

    ![SQL DB 설정](./media/data-powerbi/db-settings.png)

6.  데이터베이스에 연결되면 **탐색기** 대화 상자에 데이터베이스 내에서 사용할 수 있는 테이블 및 보기가 표시됩니다. 보기는 보고용으로 설계되었으며 모든 보기에는 **vw**라는 접두사가 붙습니다.

    ![탐색기](./media/data-powerbi/navigator.png)

7.  포함할 보기를 선택합니다. 데모를 위해, 계약에 대해 발생한 작업에 대한 세부 정보를 제공하는 **vwContractAction**을 포함하겠습니다.

    ![보기 선택](./media/data-powerbi/select-views.png)

이제 평소에 Power BI로 하는 것처럼 보고서를 만들고 게시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench의 데이터베이스 뷰](database-views.md)