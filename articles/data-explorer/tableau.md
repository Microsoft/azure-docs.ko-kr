---
title: Azure 데이터 탐색기에 대한 ODBC (Open Database Connectivity) 연결을 사용 하 여 Tableau로 데이터 시각화
description: 이 문서에서는 Azure 데이터 탐색기 연결에 대한 ODBC (Open Database Connectivity) 연결을 사용 하 여 Tableau로 데이터를 시각화 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4dd8fbd761a3442536919e17bae5465adf6b945f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023856"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Tableau의 Azure 데이터 탐색기에서 데이터 시각화

 [Tableau](https://www.tableau.com/) 는 비즈니스 인텔리전스를 위한 시각적 분석 플랫폼입니다. Tableau에서 Azure 데이터 탐색기에 연결 하 고 샘플 클러스터에서 데이터를 가져오려면 SQL Server ODBC (Open Database Connectivity) 드라이버를 사용 합니다. 

## <a name="prerequisites"></a>선행 조건

이 문서를 완료 하려면 다음이 필요 합니다.

* SQL Server ODBC 드라이버를 사용 하 여 [azure 데이터 탐색기에 연결](connect-odbc.md) 하 여 tableau에서 azure 데이터 탐색기에 연결 합니다. 

* Tableau Desktop, full 또는 [평가판](https://www.tableau.com/products/desktop/download) 버전

* StormEvents 샘플 데이터가 포함된 클러스터입니다. 자세한 내용은 [azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md) 및 [샘플 데이터를 Azure 데이터 탐색기에 수집](ingest-sample-data.md)을 참조 하세요.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Tableau에서 데이터 시각화 

ODBC 구성을 완료 한 후에는 Tableau로 샘플 데이터를 가져올 수 있습니다.

1. Tableau Desktop의 왼쪽 메뉴에서 **기타 데이터베이스 (ODBC)** 를 선택 합니다.

    ![ODBC로 연결](media/tableau/connect-odbc.png)

1. **DSN**의 경우 ODBC 용으로 만든 데이터 원본을 선택 하 고 **로그인**을 선택 합니다.

    ![ODBC 로그인](media/tableau/odbc-sign-in.png)

1. **데이터베이스**의 경우 예제 클러스터에서 *testdatabase*와 같은 데이터베이스를 선택 합니다. **스키마**에 대해 *dbo*를 선택 하 고 **테이블**에서 *stormevents* 샘플 테이블을 선택 합니다.

    ![데이터베이스 및 테이블 선택](media/tableau/select-database-table.png)

1. 이제 tableau는 샘플 데이터에 대한 스키마를 표시 합니다. **지금 업데이트** 를 선택 하 여 데이터를 tableau로 가져옵니다.

    ![데이터 업데이트](media/tableau/update-data.png)

    데이터를 가져올 때 Tableau는 다음 이미지와 유사한 데이터 행을 표시 합니다.

    ![결과 집합](media/tableau/result-set.png)

1. 이제 Azure 데이터 탐색기에서 가져온 데이터를 기반으로 Tableau에서 시각화를 만들 수 있습니다. 자세한 내용은 [Tableau Learning](https://www.tableau.com/learn)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)