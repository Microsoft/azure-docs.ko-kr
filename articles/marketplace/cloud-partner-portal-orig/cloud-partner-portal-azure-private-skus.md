---
title: 비공개 Sku 및 요금제 | Azure Marketplace
description: 프라이빗 SKU를 사용하여 제품 가용성을 관리하는 방법입니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: pabutler
ms.openlocfilehash: eb6eac5eafaeea239bfaf9cf2aface3db659dd57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818828"
---
<a name="private-skus-and-plans"></a>프라이빗 SKU 및 플랜
============

프라이빗 SKU를 사용하면 SKU의 가용성을 특정 고객으로 제한할 수 있습니다. SKU가 프라이빗으로 표시되는 경우 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com)을 포함한 공용 카탈로그에서는 사용할 수 없습니다. Azure Portal에서 해당 SKU에 대한 액세스 권한이 있는 고객만 SKU를 볼 수 있습니다. 또한 프라이빗 제품에 대한 액세스 권한이 있다는 메시지가 표시됩니다.

>[!NOTE]
>공개 SKU와의 충돌을 피하기 위해 프라이빗 SKU에는 새 고유 SKU/플랜 ID가 있어야 합니다.

프라이빗 SKU를 사용하여 다음 시나리오를 처리할 수 있습니다.

1.  일반 공급하지 않고 특정 고객에게만 제공하려는 소프트웨어를 게시합니다.
2.  특정 고객에게 사용자 지정 가격으로 제공하려는 공개 소프트웨어의 변형을 게시합니다.
3.  새 제품을 통해 사용자 지정 설명 및 조건으로 공개 소프트웨어의 변형을 게시합니다.

가격만 변경하려는 경우 동일한 제품에 있는 다른 SKU의 디스크를 재사용할 수 있습니다. 프라이빗 SKU를 사용하면 SKU 간에 디스크를 다시 제출할 필요가 없습니다.

<a name="mark-a-sku-private"></a>SKU를 프라이빗으로 표시
---------------------

SKU를 프라이빗으로 표시하려면 SKU가 프라이빗인지 여부를 묻는 옵션을 토글합니다.

![SKU를 프라이빗으로 표시](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

다른 SKU의 디스크를 재사용하고 가격 또는 설명을 수정할 수 있습니다. 디스크를 다시 사용 하려면 "이 SKU가 공용 SKU에서 이미지를 다시 사용 합니까" 프롬프트에 대 한 응답으로 **예** 를 선택 합니다.

SKU가 프라이빗으로 표시되었으며 제품이 재사용 가능한 디스크가 있는 다른 SKU를 포함하는 경우 SKU에서 다른 SKU의 디스크를 재사용함을 표시해야 합니다. 또한 프라이빗 SKU의 대상 그룹을 지정해야 합니다.

>[!NOTE]
>게시된 후에는 공개 SKU를 프라이빗으로 설정할 수 없습니다.

<a name="select-an-image"></a>이미지 선택
------------------

프라이빗 SKU에 대해 새 디스크를 제공하거나, 다른 SKU에 이미 제공된 동일한 디스크를 재사용하고 가격 또는 설명만 수정할 수 있습니다. 디스크를 다시 사용 하려면 "이 SKU에서 공용 SKU에서 이미지를 다시 사용 합니다." 라는 메시지에 대 한 응답으로 **예** 를 선택 합니다.

![이미지 다시 사용 표시](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

SKU에서 이미지를 다시 사용할 것을 확인 한 후 이미지의 원본 또는 *기본* SKU를 선택 합니다.

![이미지 선택](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

제품을 게시할 때 선택한 SKU의 이미지가 사용자 지정 요금/조건과 함께 프라이빗 SKU ID 아래에 제공됩니다. 프라이빗 SKU는 대상 그룹에게만 표시됩니다.

이미지 업데이트의 경우 기본 SKU의 이미지만 업데이트하면 됩니다. 그러면 프라이빗 SKU의 이미지도 자동으로 업데이트됩니다. 마찬가지로, 기본 SKU에서 이미지를 삭제하면 프라이빗 SKU의 이미지도 제거됩니다.

<a name="restricting-the-audience"></a>대상 그룹 제한
------------------------

프라이빗 제품은 대상 사용자만 찾아서 배포할 수 있습니다.
현재, 구독 ID를 사용하여 대상 사용자를 지정할 수 있습니다.

이러한 구독은 수동 입력 양식을 통해 **최대 10개 구독**까지 입력하거나, CSV 파일을 업로드하여 **최대 20,000개의 구독**까지 입력할 수 있습니다.

제한된 대상 그룹에 대한 수동 입력:

![수동으로 대상 그룹 제한](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

제한된 대상 그룹에 대한 CSV 업로드:

![CSV를 사용하여 대상 그룹 제한](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

샘플 CSV 파일 콘텐츠:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

수동 입력에서 CSV 업로드 뷰로 전환하거나 CSV에서 수동 입력으로 전환할 때 SKU에 대한 액세스 권한이 있는 이전 구독 ID 목록은 유지되지 않습니다. 경고가 표시되며, 제품을 저장하는 경우에만 목록을 덮어씁니다.

<a name="managing-private-audiences"></a>개인 대상 관리
-------------------------

**전체 제품을 다시 게시 하지 않고 대상을 업데이트 하기 위해 대상 그룹을 변경 하 고 (UI 또는 API 사용) "개인 대상 사용자 동기화" 작업을 시작 합니다.**

대상이 10 개 이하인 경우에는 CPP UI를 사용 하 여 해당 구독을 완전히 관리할 수 있습니다.

사용자가 10 개를 초과 하는 구독을 사용 하는 경우에는 CPP UI에 업로드 하거나 API를 사용 하 여 CSV 파일을 사용 하 여 관리할 수 있습니다.

API를 사용 하 고 CSV 파일을 유지 관리 하지 않으려는 경우 아래 지침에 따라 API를 사용 하 여 직접 대상 그룹을 관리할 수 있습니다.

> [!NOTE]
> Azure 구독 ID (요금제 및 Sku) 또는 테넌트 ID (요금제만 해당)를 사용 하 여 개인 제품에 대상 그룹을 추가 합니다.

###  <a name="managing-subscriptions-with-the-api"></a>API를 사용 하 여 구독 관리

API를 사용 하 여 csv를 업로드 하거나 CSV를 사용 하지 않고 직접 대상 그룹을 관리할 수 있습니다. 일반적으로 제품을 검색 하 고 `restrictedAudience` 개체를 업데이트 한 다음 해당 변경 내용을 제품에 다시 제출 하 여 대상 그룹 구성원을 추가 하거나 제거 해야 합니다.

다음은 사용자 그룹 목록을 프로그래밍 방식으로 업데이트 하는 방법입니다.

1. [제품 데이터를 검색 합니다](cloud-partner-portal-api-retrieve-specific-offer.md) .

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. 이 JPath 쿼리를 사용 하 여 제품의 각 SKU에서 제한 된 대상 개체를 찾습니다.

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. 제품에 대 한 제한 된 대상 개체를 업데이트 합니다.

    **CSV 파일에서 개인 제품에 대 한 구독 목록을 원래 업로드 한 경우:**

    *RestrictedAudience* 개체는 다음과 같습니다.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    제한 된 각 대상 개체에 대해 다음을 수행 합니다.

    a. `restrictedAudience.uploadedCsvUri`의 콘텐츠를 다운로드 합니다. 콘텐츠는 헤더를 포함 하는 CSV 파일 일 뿐입니다. 예:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. 다운로드 한 CSV 파일에서 필요에 따라 구독을 추가 하거나 삭제 합니다.

    c. [Azure Blob storage](../../storage/blobs/storage-blobs-overview.md) 또는 [OneDrive](https://onedrive.live.com)와 같은 위치에 업데이트 된 CSV 파일을 업로드 하 고 파일에 대 한 읽기 전용 링크를 만듭니다. 새 *SasUrl*됩니다.

    ㄹ. `restrictedAudience.uploadedCsvUri` 키를 새 *SasUrl*업데이트 합니다.

    **Cloud 파트너 포털에서 개인 제품의 원래 구독 목록을 수동으로 입력 한 경우:**

    *RestrictedAudience* 개체는 다음과 같습니다.

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. 제한 된 각 대상 개체의 경우 필요에 따라 `restrictedAudience.manualEntries` 목록에서 항목을 추가 하거나 삭제 합니다.

4. 개인 제품의 각 SKU에 대 한 모든 *restrictedAudience* 개체를 업데이트 하는 작업을 완료 하면 [제품을 업데이트 합니다](cloud-partner-portal-api-creating-offer.md).

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    이제 업데이트 된 대상 그룹 목록이 적용 됩니다.

<a name="previewing-private-offers"></a>비공개 제품 미리 보기
-------------------------

미리 보기/스테이징 단계 중에는 제품 수준 미리 보기 구독만 SKU에 액세스할 수 있습니다. 이 테스트 단계에서는 대상 고객에 게 표시 되는 제품을 미리 볼 수 있습니다.

스테이징된 제품에 액세스하기 위한 제품 수준 미리 보기 구독:

![미리 보기 구독 ID](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

제품이 라이브 상태가 되면 제한된 대상 구독(수동 입력 또는 CSV를 통해 입력됨)만 프라이빗 SKU를 보고 배포할 수 있습니다. 유효성 검사를 위해 프라이빗 SKU의 **제한된 대상 그룹에 자신의 구독을 항상 포함**하는 것이 좋습니다.

>[!NOTE]
>디버깅을 위해 Microsoft 지원 및 엔지니어링 팀도 이러한 프라이빗 제품에 액세스할 수 있습니다.
