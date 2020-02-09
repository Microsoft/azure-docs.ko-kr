---
title: Microsoft Excel 용 Azure 데이터 탐색기 커넥터를 사용 하 여 데이터 시각화
description: 이 문서에서는 Azure 데이터 탐색기 용 Excel connector를 사용 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849057"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Excel 용 Azure 데이터 탐색기 커넥터를 사용 하 여 데이터 시각화

Azure 데이터 탐색기는 Excel에서 데이터에 연결 하는 두 가지 옵션을 제공 합니다. 기본 커넥터를 사용 하거나 Azure 데이터 탐색기에서 쿼리를 가져옵니다. 이 문서에서는 Excel에서 네이티브 커넥터를 사용 하 고 Azure 데이터 탐색기 클러스터에 연결 하 여 데이터를 가져오고 시각화 하는 방법을 보여 줍니다.

Azure 데이터 탐색기 Excel native connector는 쿼리 결과를 Excel로 내보낼 수 있는 기능을 제공 합니다. 추가 계산 또는 시각화를 위해 KQL 쿼리를 Excel 데이터 원본으로 추가할 수도 있습니다.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Kusto를 Excel 데이터 원본으로 쿼리하고 Excel에 데이터 로드

1. **Microsoft Excel**을 엽니다.
1. **데이터** 탭에서 Azure **데이터 탐색기의** **azure > 에서** **데이터 가져오기** > 를 선택 합니다.

    ![Azure Data Explorer에서 데이터 가져오기](media/excel-connector/get-data-from-adx.png)

1. **Azure 데이터 탐색기 (Kusto)** 창에서 다음 필드를 완료 하 고 **확인**을 선택 합니다.

    ![Azure 데이터 탐색기 (Kusto) 창](media/excel-connector/adx-connection-window.png)
    
    |필드   |설명 |
    |---------|---------|
    |**Cluster**   |   클러스터 이름 (필수)      |    
    |**데이터베이스**     |    데이터베이스의 이름      |    
    |**테이블 이름 또는 Azure 데이터 탐색기 쿼리**    |     테이블 또는 Azure 데이터 탐색기 쿼리의 이름    | 
    
    **고급 옵션:**

     |필드   |설명 |
    |---------|---------|
    |**쿼리 결과 레코드 수 제한**     |     Excel로 로드 되는 레코드 수 제한  |    
    |**쿼리 결과 데이터 크기 제한 (바이트)**    |    데이터 크기 제한      |   
    |**결과 집합 잘림을 사용 하지 않습니다.**    |         |      
    |**추가 Set 문 (세미콜론으로 구분)**    |    데이터 원본에 적용할 `set` 문 추가     |   

1.  **탐색기** 창에서 올바른 테이블로 이동 합니다. 테이블 미리 보기 창에서 데이터 **변환** 을 선택 하 여 데이터를 변경 하거나 **로드** 를 선택 하 여 Excel로 로드 합니다.

![테이블 미리 보기 창](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > **데이터베이스** 및/또는 **테이블 이름 또는 Azure 데이터 탐색기 쿼리가** 이미 지정 된 경우 올바른 테이블 미리 보기 창이 자동으로 열립니다. 

## <a name="analyze-and-visualize-data-in-excel"></a>Excel에서 데이터 분석 및 시각화

데이터를 excel에 로드 하 고 Excel 시트에서 사용할 수 있게 되 면 관계와 시각적 개체를 만들어 데이터를 분석, 요약 및 시각화할 수 있습니다. 

1.  **테이블 디자인** 탭에서 **피벗 테이블을 사용 하 여 요약**을 선택 합니다. **피벗 테이블 만들기** 창에서 관련 테이블을 선택 하 고 **확인**을 선택 합니다.

    ![피벗 테이블 만들기](media/excel-connector/create-pivot-table.png)

1. **피벗 테이블 필드** 창에서 관련 테이블 열을 선택 하 여 요약 테이블을 만듭니다. 아래 예제에서는 **EventId** 및 **State** 를 선택 합니다.
    
    ![피벗 테이블 필드 선택](media/excel-connector/pivot-table-pick-fields.png)

1. **피벗 테이블 분석** 탭에서 **피벗 차트** 를 선택 하 여 테이블을 기반으로 시각적 개체를 만듭니다. 

    ![피벗 차트](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. 아래 예제에서는 **이벤트 Id**, **StartTime**및 **EventType** 을 사용 하 여 날씨 이벤트에 대한 추가 정보를 확인 합니다.

    ![데이터 가상화](media/excel-connector/visualize-excel-data.png)

1. 전체 대시보드를 만들어 데이터를 모니터링 합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Excel로 가져온 Azure 데이터 탐색기 Kusto 쿼리를 사용 하 여 데이터 시각화](excel-blank-query.md)