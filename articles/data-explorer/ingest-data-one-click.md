---
title: 단일 클릭 수집을 사용 하 여 데이터를 Azure 데이터 탐색기 수집 합니다.
description: 한 번의 클릭으로 수집을 사용 하 여 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 4a53f7e68501ce7f9b19dea0822d3896ec241fb8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444550"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>단일 클릭 수집을 사용 하 여 데이터를 Azure 데이터 탐색기 수집 합니다.

이 문서에서는 단일 클릭 수집을 사용 하 여 JSON 또는 CSV 형식으로 새 테이블을 신속 하 게 수집 하는 방법을 보여 줍니다. 데이터는 저장소 또는 로컬 파일에서 기존 테이블이 나 새 테이블로 수집 수 있습니다. 직관적인 한 번 클릭 마법사를 사용 하 고 몇 분 내에 데이터를 수집. 그런 다음 Azure 데이터 탐색기 웹 UI를 사용 하 여 테이블을 편집 하 고 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [애플리케이션](https://dataexplorer.azure.com/)에 로그인합니다.
* [Azure 데이터 탐색기 클러스터와 데이터베이스](create-cluster-database-portal.md)를 만듭니다.
* [웹 UI](https://dataexplorer.azure.com/) 에 로그인 하 고 [클러스터에 대한 연결을 추가](/azure/data-explorer/web-query-data#add-clusters)합니다.

## <a name="ingest-new-data"></a>새 데이터 수집

1. 웹 UI의 왼쪽 메뉴에서 *데이터베이스* 또는 *테이블* 행을 마우스 오른쪽 단추로 클릭 하 고 **새 데이터 수집 (미리 보기)** 을 선택 합니다.

    ![웹 UI에서 한 번 클릭으로 수집을 선택 합니다.](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. **새 데이터 수집 (미리 보기)** 창에서 **원본** 탭을 선택 하 고 **프로젝트 세부 정보**를 완료 합니다.

    * **테이블**의 드롭다운 메뉴에서 기존 테이블 이름을 선택 하거나 **새로 만들기** 를 선택 하 여 새 테이블을 만듭니다.
    * 수집 **유형**의 경우 **저장소** 또는 **파일**에서를 선택 합니다.
      * **저장소에서**를 선택한 경우에는 **저장소에 연결** 을 선택 하 여 URL을 추가 합니다. 개인 저장소 계정에는 [BLOB SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 을 사용 합니다. 
      * **파일에서**를 선택한 경우 **찾아보기** 를 선택 하 고 파일을 상자로 끌어 놓습니다.
    * 테이블 열 구성을 보고 편집 하려면 **스키마 편집** 을 선택 합니다.
 
    ![한 번 클릭으로 수집 원본 세부 정보](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > *테이블* 행에 **새 데이터 수집 (미리 보기)** 을 선택 하면 **프로젝트 세부 정보**에 선택한 테이블 이름이 표시 됩니다.

1. 기존 테이블을 선택한 경우 원본 데이터 열을 대상 테이블 열에 매핑하기 위해 **맵 열** 창이 열립니다. 
    * **생략 열** 을 사용 하 여 테이블에서 대상 열을 제거 합니다.
    * **새 열** 을 사용 하 여 테이블에 새 열을 추가 합니다.

    ![열 매핑 창](media/ingest-data-one-click/one-click-map-columns-window.png)

1. **스키마** 탭에서 다음을 수행 합니다.

    * 드롭다운 메뉴에서 **압축 유형** 을 선택 하 고 **압축** 되지 않은 또는 **GZip**중 하나를 선택 합니다.
    * 드롭다운 메뉴에서 **데이터 형식** 을 선택한 다음 **JSON**, **CSV**, **TSV**, **SCSV**, **SOHSV**, **tsve**또는 **psv**를 선택 합니다. 
        * **Json** 형식을 선택 하는 경우에는 1부터 10 까지의 **json 수준도**선택 해야 합니다. 수준은 테이블 열 데이터 표현에 영향을 줍니다. 
        * JSON 이외의 형식을 선택 하는 경우 파일의 머리글 행을 무시 하려면 **열 이름 포함** 확인란을 선택 해야 합니다.
    * **매핑 이름은** 자동으로 설정 되지만 편집할 수 있습니다.
    * 기존 테이블을 선택한 경우 **지도 열** 을 선택 하 여 **맵 열** 창을 열 수 있습니다.

    ![한 번 클릭으로 수집 하는 CSV 형식 스키마](media/ingest-data-one-click/one-click-csv-format.png)

1. **편집기** 창 위에서 **v** 단추를 선택 하 여 편집기를 엽니다. 편집기에서 사용자 입력 으로부터 생성 된 자동 쿼리를 보고 복사할 수 있습니다. 

1. 테이블에서 다음을 수행 합니다. 
    * 새 열 머리글을 마우스 오른쪽 단추로 클릭 하 여 **데이터 형식 변경**, **열 이름 바꾸기**, **열 삭제**, **오름차순 정렬**또는 **내림차순 정렬**을 클릭 합니다. 기존 열에서는 데이터 정렬만 사용할 수 있습니다. 
    * 편집할 새 열 이름을 두 번 클릭 합니다.

1. 수집 **시작** 을 선택 하 여 테이블 및 매핑을 만들고 데이터 수집을 시작 합니다.

    ![한 번 클릭으로 수집 JSON 형식 스키마](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>쿼리 데이터

1. 데이터 수집 **완료** 창에서 데이터 수집이 성공적으로 완료 되 면 세 단계가 모두 녹색 확인 표시로 표시 됩니다.
 
    ![한 번 클릭으로 데이터 수집 완료](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. **V** 단추를 선택 하 여 쿼리를 엽니다. 웹 UI에 복사 하 여 쿼리를 편집 합니다.

1. 오른쪽의 메뉴에는 **빠른 쿼리** 및 **도구** 옵션이 포함 되어 있습니다. 

    * **빠른 쿼리** 는 예제 쿼리를 사용 하 여 웹 UI에 대한 링크를 포함 합니다.
    * **도구** 에는 관련 된 `.drop` 명령을 실행 하 여 문제를 해결할 수 있는 웹 UI의 **명령을 놓는** 링크가 포함 됩니다.

    > [!TIP]
    > `.drop` 명령을 사용 하면 데이터가 손실 될 수 있습니다. 신중 하 게 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 웹 UI에서 데이터 쿼리](web-query-data.md)
* [Kusto 쿼리 언어를 사용 하 여 Azure 데이터 탐색기에 대한 쿼리 작성](write-queries.md)
