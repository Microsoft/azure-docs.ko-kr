---
title: 자습서 - Azure Cost Management에서 내보낸 데이터 만들기 및 관리
description: 이 문서에서는 내보낸 Azure Cost Management 데이터를 외부 시스템에서 사용할 수 있도록 만들고 관리하는 방법을 보여줍니다.
author: bandersmsft
ms.author: banders
ms.date: 08/05/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: c0fcc9d5753ce90365829fba658031e633819513
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683269"
---
# <a name="tutorial-create-and-manage-exported-data"></a>자습서: 내보낸 데이터 만들기 및 관리

비용 분석 자습서를 읽으셨다면 Cost Management 데이터를 수동으로 다운로드하는 방법에 익숙하실 것입니다. 그러나 매일, 매주 또는 매월 자동으로 Azure Storage에 Cost Management 데이터를 내보내는 되풀이 작업을 만들 수 있습니다. 내보낸 데이터는 CSV 형식이며 Cost Management에서 수집한 모든 정보를 포함하고 있습니다. Azure Storage로 내보낸 데이터를 외부 시스템에 사용하고 사용자 고유의 사용자 지정 데이터와 결합할 수 있습니다. 그리고 내보낸 데이터를 대시보드나 기타 재무 시스템 같은 외부 시스템에서 사용할 수 있습니다.

Azure Storage로의 Azure 비용 데이터 예약된 내보내기를 만드는 방법에 대해서는 [Azure Cost Management를 사용하여 스토리지로의 내보내기를 예약하는 방법](https://www.youtube.com/watch?v=rWa_xI1aRzo) 비디오를 시청하세요. 다른 비디오를 시청하려면 [Cost Management YouTube 채널](https://www.youtube.com/c/AzureCostManagement)을 방문하세요.

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

이 자습서의 예제는 비용 관리 데이터를 내보낸 후 데이터 내보내기가 성공했는지 확인하는 단계를 안내합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

## <a name="prerequisites"></a>필수 구성 요소
데이터 내보내기는 [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) 및 [Microsoft 고객 계약](get-started-partners.md) 고객을 포함한 다양한 종류의 Azure 계정에 사용할 수 있습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요. 다음 Azure 사용 권한 또는 범위는 사용자 및 그룹별 데이터 내보내기에 대해 구독별로 지원됩니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 – 구독에 대한 예약된 내보내기를 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 – 자신의 예약된 내보내기를 만들고, 수정하거나 삭제할 수 있습니다. 다른 사용자가 만든 예약된 내보내기의 이름을 수정할 수 있습니다.
- 읽기 권한자 - 사용 권한이 있는 내보내기를 예약할 수 있습니다.

Azure Storage 계정의 경우:
- 내보내기에 대한 사용 권한과 상관 없이 구성된 스토리지 계정을 변경하려면 쓰기 사용 권한이 필요합니다.
- BLOB 또는 파일 스토리지에 대해 Azure 스토리지 계정이 구성되어 있어야 합니다.

새 구독이 있는 경우 Cost Management 기능을 즉시 사용할 수 없습니다. 모든 Cost Management 기능을 사용하려면 최대 48시간이 걸릴 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.

## <a name="create-a-daily-export"></a>일일 내보내기 만들기

데이터 내보내기를 만들거나 보려면 또는 내보내기를 예약하려면 Azure Portal에서 원하는 범위를 열고 메뉴에서 **비용 분석**을 선택합니다. 예를 들어 **구독**으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석**을 선택합니다. 비용 분석 페이지의 맨 위에서 **설정**을 선택한 다음, **내보내기**를 선택합니다.

> [!NOTE]
> - 구독 외에도 리소스 그룹, 관리 그룹, 부서 및 등록에서 내보내기를 만들 수 있습니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.
>- 청구 계정 범위 또는 고객 테넌트에서 파트너로 로그인한 경우 파트너 스토리지 계정에 연결된 Azure Storage 계정으로 데이터를 내보낼 수 있습니다. 그러나 CSP 테넌트에 활성 구독이 있어야 합니다.

1. **추가**를 선택하고 내보내기의 이름을 입력합니다. 
1. **메트릭**에서 선택합니다.
    - **실제 비용(사용량 및 구매)** - 표준 사용량 및 구매를 내보내려면 선택합니다.
    - **분할상환 비용(사용량 및 구매)** - Azure 예약과 같은 구매 분할상환 비용을 내보내려면 선택합니다.
1. **내보내기 유형**에서 선택합니다.
    - **월간 누계 비용 매일 내보내기** - 월간 누계 비용에 대한 새로운 내보내기 파일을 매일 제공합니다. 최신 데이터는 이전의 매일 내보내기에서 집계됩니다.
    - **지난 7일간 비용의 주간 내보내기** – 내보내기의 선택한 시작 날짜로부터 지난 7일 동안 발생한 비용의 주별 내보내기를 만듭니다.  
    - **지난달 비용의 월간 내보내기** – 내보내기를 만든 현재 달에 비해 지난 달의 비용에 대한 내보내기를 제공합니다. 앞으로 진행하면 일정은 매월 5일에 이전 달의 비용으로 내보내기를 실행합니다.  
    - **일회성 내보내기** – Azure Blob 스토리지로 내보낼 기록 데이터의 날짜 범위를 선택할 수 있습니다. 선택한 날부터 최대 90일의 기록 비용을 내보낼 수 있습니다. 이 내보내기는 즉시 실행되며 2시간 이내에 스토리지 계정에서 사용할 수 있습니다.  
        내보내기 유형에 따라 시작 날짜를 선택하거나 **From** 및 **To** 날짜를 선택합니다.
1. Azure 스토리지 계정에 대한 구독을 지정한 다음, 리소스 그룹을 선택하거나 새로 만듭니다. 
1. 스토리지 계정 이름을 선택하거나 새로 만듭니다. 
1. 위치(Azure 지역)를 선택합니다.
1. 내보내기 파일을 보낼 스토리지 컨테이너 및 디렉터리 경로를 지정합니다. 
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="새 내보내기 예제" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. 내보내기 세부 정보를 검토하고 **만들기**를 선택합니다.

새 내보내기는 내보내기 목록에 표시됩니다. 기본적으로 새 내보내기가 사용되도록 설정됩니다. 예정된 내보내기를 해제 또는 삭제하려면 목록에서 항목을 선택한 다음, **사용 안 함** 또는 **삭제**를 선택합니다.

처음에는 내보내기가 실행될 때까지 12~24시간이 걸릴 수 있습니다. 그러나 내보낸 파일에 데이터가 표시될 때까지는 더 오래 걸릴 수 있습니다.

### <a name="export-schedule"></a>내보내기 일정

예약된 내보내기는 처음에 내보내기를 만든 요일과 시간의 영향을 받습니다. 예약된 내보내기를 만들면 이후의 각 내보내기 발생에 대해 동일한 빈도로 내보내기가 실행됩니다. 예를 들어 일별 빈도로 월간 누계 비용 매일 내보내기를 설정하는 경우 내보내기가 매일 실행됩니다. 마찬가지로 매주 내보내기의 경우에는 예약된 것과 동일한 요일에 내보내기가 매주 실행됩니다. 내보내기의 정확한 배달 시간은 보장되지 않으며, 내보낸 데이터는 런타임의 4시간 이내에 사용할 수 있습니다.

내보내기마다 새 파일이 만들어지므로 이전 내보내기를 덮어쓰지 않습니다.

#### <a name="create-an-export-for-multiple-subscriptions"></a>여러 구독에 대한 내보내기 만들기

기업계약을 보유한 경우 관리 그룹을 사용하여 단일 컨테이너의 구독 비용 정보를 집계할 수 있습니다. 그런 다음, 관리 그룹에 대한 비용 관리 데이터를 내보낼 수 있습니다.

다른 구독 유형의 관리 그룹에 대한 내보내기는 지원되지 않습니다.

1. 관리 그룹을 아직 만들지 않은 경우 그룹을 하나 만들고 구독을 할당합니다.
1. 비용 분석에서 범위를 관리 그룹으로 설정하고 **이 관리 그룹 선택**을 선택합니다.  
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="이 관리 그룹 선택 옵션을 보여 주는 예제" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. 범위에서 내보내기를 만들어 관리 그룹의 구독에 대한 비용 관리 데이터를 가져옵니다.  
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="관리 그룹 범위를 사용하여 새 내보내기 옵션을 보여 주는 예제":::

## <a name="verify-that-data-is-collected"></a>데이터가 수집되는지 확인

Azure Storage Explorer를 사용하여 간단하게 Cost Management 데이터가 수집되는지 확인하고 내보낸 CSV 파일을 볼 수 있습니다.

내보내기 목록에서 스토리지 계정 이름을 선택합니다. 스토리지 계정 페이지의 탐색기에서 열기를 선택합니다. 확인 상자가 나타나면 **예**를 선택하여 Azure Storage Explorer에서 파일을 엽니다.

![예제 정보 및 탐색기에서 열기 링크를 표시하는 스토리지 계정 페이지](./media/tutorial-export-acm-data/storage-account-page.png)

Storage Explorer에서 열고 싶은 컨테이너로 이동한 후 현재 월에 해당하는 폴더를 선택합니다. CSV 파일 목록이 표시됩니다. 파일을 하나 선택하고 **열기**를 선택합니다.

![Storage Explorer에 표시되는 예제 정보](./media/tutorial-export-acm-data/storage-explorer.png)

CSV 파일 확장명을 열도록 설정된 프로그램 또는 애플리케이션에서 파일이 열립니다. 다음은 Excel 예제입니다.

![Excel에 표시되는 내보낸 CSV 데이터 예제](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>내보낸 CSV 데이터 파일 다운로드

내보낸 CSV 파일은 Azure Portal에서 다운로드할 수도 있습니다. 다음 단계에서는 비용 분석에서 이를 찾는 방법을 설명합니다.

1. 비용 분석에서 **설정**을 선택하고 **내보내기**를 선택합니다.
1. 내보내기 목록에서 내보내기에 대한 스토리지 계정을 선택합니다.
1. 스토리지 계정에서 **컨테이너**를 클릭합니다.
1. 컨테이너 목록에서 컨테이너를 선택합니다.
1. 디렉터리 및 스토리지 BLOB를 통해 원하는 날짜로 이동합니다.
1. CSV 파일을 선택한 다음, **다운로드**를 선택합니다.

[![데이터 내보내기 예제](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>내보내기 실행 기록 보기  

내보내기 목록 페이지에서 개별 내보내기를 선택하여 예약된 내보내기의 실행 기록을 볼 수 있습니다. 또한 내보내기 목록 페이지에서는 이전 내보내기의 실행 시간을 볼 수 있는 빠른 액세스와 다음 번 및 내보내기가 실행될 때 사용할 수 있습니다. 실행 기록을 보여 주는 예제는 다음과 같습니다.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="내보내기 실행 기록을 보여 주는 예제":::

실행 기록을 보려면 내보내기를 선택합니다.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="내보내기 실행 기록을 보여 주는 예제":::

## <a name="access-exported-data-from-other-systems"></a>다른 시스템에서 내보낸 데이터에 액세스

Cost Management 데이터를 내보내는 목적 중 하나는 외부 시스템에서 데이터에 액세스하는 것입니다. 대시보드 시스템 또는 기타 재무 시스템을 사용할 수 있습니다. 이러한 시스템의 종류는 매우 다양하기 때문에 예제를 보여드리는 것이 별 의미가 없습니다.  하지만 [Azure Storage 소개](../../storage/common/storage-introduction.md) 문서를 참조하여 애플리케이션에서 데이터에 액세스하는 방법을 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 일일 내보내기 만들기
> * 데이터가 수집되는지 확인

그 다음 자습서로 넘어가서 유휴 리소스 및 미달 사용 리소스를 식별하여 최적화하고 효율성을 개선하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [최적화 권장 사항 검토 및 필요한 조치 수행](tutorial-acm-opt-recommendations.md)
