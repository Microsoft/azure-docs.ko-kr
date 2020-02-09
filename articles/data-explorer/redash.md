---
title: Redash를 사용 하 여 Azure 데이터 탐색기 시각화
description: 이 문서에서는 Redash native 커넥터를 사용 하 여 Azure 데이터 탐색기에서 데이터를 시각화 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773952"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Redash의 Azure 데이터 탐색기에서 데이터 시각화

[Redash](https://redash.io/) 는 데이터 원본을 연결 하 고 쿼리하고 데이터를 시각화 하 고 동료와 공유할 수 있는 대시보드를 작성 합니다. 이 문서에서는 Redash에 대한 데이터 원본으로 Azure 데이터 탐색기를 설정 하 고 데이터를 시각화 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

1. [클러스터 및 데이터베이스를 만듭니다](create-cluster-database-portal.md).
1. [Azure 데이터 탐색기에 샘플 데이터 수집](ingest-sample-data.md)에 설명 된 대로 데이터를 수집 합니다. 수집 옵션에 대한 자세한 내용은 수집 [개요](ingest-data-overview.md)를 참조 하세요.

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Redash에서 Azure 데이터 탐색기 커넥터 만들기 

1. [Redash](https://www.redash.io/)에 로그인 합니다. **시작** 을 선택 하 여 계정을 만듭니다.
1. **시작 하기**에서 **데이터 원본 연결**을 선택 합니다.

    ![데이터 원본 연결](media/redash/connect-data-source.png)

1. **새 데이터 원본 만들기** 창에서 **Azure 데이터 탐색기 (kusto)** 를 선택 하 고 **만들기**를 선택 합니다. 

    ![Azure 데이터 탐색기 데이터 원본 선택](media/redash/select-adx-data-source.png)

1. **Azure 데이터 탐색기 (Kusto)** 창에서 다음 양식을 작성 하 고 **만들기**를 선택 합니다.

    ![Azure 데이터 탐색기 (Kusto) 설정 창](media/redash/adx-settings-window.png)

1. **설정** 창에서 연결 **저장** 및 **테스트** 를 선택 하 여 **Azure 데이터 탐색기 (kusto)** 데이터 원본 연결을 테스트 합니다.

## <a name="create-queries-in-redash"></a>Redash에서 쿼리 만들기

1. Redash의 왼쪽 위에서 > **쿼리** **만들기** 를 선택 합니다. **새 쿼리** 를 클릭 하 고 쿼리 이름을 바꿉니다.

    ![쿼리 만들기](media/redash/create-query.png)

1. 위쪽 편집 창에 쿼리를 입력 하 고 **저장** 및 **실행**을 선택 합니다. **게시** 를 선택 하 여 나중에 사용 하기 위해 쿼리를 게시 합니다.

    ![쿼리 저장 및 실행](media/redash/save-and-execute-query.png)

    왼쪽 창에서 드롭다운 메뉴의 데이터 원본 연결 이름 (**Github 커넥터** )과 선택한 데이터베이스의 테이블을 볼 수 있습니다. 

1. 아래쪽 가운데 창에서 쿼리 결과를 확인 합니다. **새 시각화** 단추를 선택 하 여 쿼리와 함께 이동 하는 시각화를 만듭니다.

    ![새 시각화](media/redash/new-visualization.png)

1. 시각화 화면에서 **시각화 유형** 및 **X 열** 및 **Y 열**과 같은 관련 필드를 선택 합니다. 시각화를 **저장** 합니다.

    ![시각화 구성 및 저장](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>매개 변수를 사용 하 여 쿼리 만들기

1. 새 쿼리를 만들기 위한 > **쿼리** 를 **만듭니다** . {{}} 중괄호를 사용 하 여 매개 변수를 추가 합니다. **{{}}** 을 선택 하 여 **매개 변수 추가** 창을 엽니다. *설정 아이콘* 을 선택 하 여 기존 매개 변수의 특성을 수정 하 고 **< parameter_name >** 창으로 열 수도 있습니다. 

    ![매개 변수 삽입](media/redash/insert-parameter.png)

1. 매개 변수의 이름을로 합니다. 드롭다운 메뉴에서 **유형**: **쿼리 기반 드롭다운 목록** 을 선택 합니다. **확인**을 선택합니다.

    ![쿼리 기반 드롭다운 목록](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > 쿼리에서는 여러 값을 사용 하므로 `| where Type in ((split('{{Type}}', ',')))`다음 구문을 포함 해야 합니다. 자세한 내용은 [in 연산자](/azure/kusto/query/inoperator)를 참조 하십시오. 그러면 [redash app의 여러 쿼리 매개 변수 옵션이](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io) 생성 됩니다.

## <a name="create-a-dashboard-in-redash"></a>Redash에서 대시보드 만들기

1. 대시보드를 만들려면 > **대시보드**를 **만듭니다** . 또는 기존 대시보드를 선택 하 **> 대시보드** 를 선택 하 고 목록에서 대시보드를 선택 합니다.

    ![대시보드 만들기](media/redash/create-dashboard.png)

1. **새 대시보드** 창에서 대시보드 이름을로 선택 하 고 **저장**을 선택 합니다. **< Dashboard_name >** 창에서 **위젯 추가** 를 선택 하 여 새 위젯을 만듭니다. 

1. **위젯 추가** 창에서 쿼리 이름을 선택 하 고 **시각화**를 선택한 다음 **매개 변수**를 선택 합니다. **대시보드에 추가를 선택 합니다** .

   ![시각화를 선택 하 고 대시보드에 추가](media/redash/add-widget-window.png)

1. **편집 완료** 를 선택 하 여 대시보드 만들기를 완료 합니다.

1.  대시보드 편집 모드에서 **대시보드 수준 필터 사용** 을 선택 하 여 이전에 정의 된 **형식** 매개 변수를 사용 합니다.

    ![대시보드 만들기 완료](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)


