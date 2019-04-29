---
title: Azure Blockchain Workbench 데이터베이스 세부 정보 가져오기
description: Azure Blockchain Workbench 데이터베이스 및 데이터베이스 서버 정보를 가져오는 방법을 알아봅니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 0d806491c58c4b1881adc2fd830de7c7b9f0859d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816855"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Azure Blockchain Workbench 데이터베이스에 대한 정보 가져오기

이 문서에서는 Azure Blockchain Workbench 데이터베이스에 대한 상세 정보를 가져오는 방법을 설명합니다.

## <a name="overview"></a>개요

애플리케이션, 워크플로 및 스마트 계약 실행에 대한 정보는 Blockchain Workbench SQL DB의 데이터베이스 보기를 사용하여 제공됩니다. 개발자는 Microsoft Excel, Power BI, Visual Studio 및 SQL Server Management Studio 같은 도구를 사용할 때 이 정보를 사용할 수 있습니다.

개발자는 데이터베이스에 연결하려면 다음이 필요합니다.

* 데이터베이스 방화벽에서 허용되는 외부 클라이언트 액세스. 데이터베이스 방화벽 구성에 대한 이 문서에서는 액세스를 허용하는 방법에 대해 설명합니다.
* 데이터베이스 서버 이름 및 데이터베이스 이름.

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench 데이터베이스에 연결

데이터베이스에 연결하려면:

1. Azure Blockchain Workbench 리소스의 **소유자** 권한이 있는 계정으로 Azure Portal에 로그인합니다.
2. 왼쪽의 탐색 창에서 **리소스 그룹**을 선택합니다.
3. Blockchain Workbench 배포에 대한 리소스 그룹의 이름을 선택합니다.
4. **형식**을 선택하여 리소스 목록을 정렬한 다음, **SQL 서버**를 선택합니다. 그 다음 화면 캡처의 정렬된 목록은 두 개의 SQL 데이터베이스를 보여주는데, 하나는 "master"이고 다른 하나는 "lhgn"을 **리소스 접두사**로 사용합니다.

   ![정렬된 Blockchain Workbench 리소스 목록](./media/getdb-details/sorted-workbench-resource-list.png)

5. Blockchain Workbench 데이터베이스에 대한 상세 정보를 보려면 Blockchain Workbench를 배포하기 위해 입력한 **리소스 접두사**를 사용하는 데이터베이스 링크를 선택합니다.

   ![데이터베이스 세부 정보](./media/getdb-details/workbench-db-details.png)

개발 또는 보고 도구를 사용하여 데이터베이스 서버 이름 및 데이터베이스 이름으로 Blockchain Workbench 데이터베이스에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench의 데이터베이스 뷰](database-views.md)