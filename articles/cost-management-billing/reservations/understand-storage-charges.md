---
title: Azure Storage에 예약 할인이 적용되는 방식 이해 | Microsoft Docs
description: 블록 Blob 및 Azure Data Lake Storage Gen2 리소스에 Azure Storage 예약된 용량 할인이 적용되는 방법에 대해 알아봅니다.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: tamram
ms.openlocfilehash: fc1110c124f5e4e6d30c27de0911f86c186b67c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681637"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Azure Storage에 예약 할인이 적용되는 방식 이해

Azure Storage 예약된 용량을 구매한 후, 예약의 조건과 일치하는 블록 Blob 및 Azure Data Lake Storage Gen2 리소스에 예약 할인이 자동으로 적용됩니다. 예약 할인은 스토리지 용량에만 적용됩니다. 대역폭과 요청 빈도는 종량제 요금으로 청구됩니다.

Azure Storage 예약된 용량에 대한 자세한 내용은 [예약된 용량을 사용한 Blob 스토리지에 대한 비용 최적화](../../storage/blobs/storage-blob-reserved-capacity.md)를 참조하세요.

Azure Storage 예약 가격 책정에 대한 자세한 내용은 [블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 및 [Azure Data Lake Storage Gen 2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/)을 참조하세요.

## <a name="how-the-reservation-discount-is-applied"></a>예약 할인이 적용되는 방식

Azure Storage 예약된 용량 할인은 블록 Blob 및 Azure Data Lake Storage Gen2 리소스에 시간 기준으로 적용됩니다.

Azure Storage 예약된 용량 할인은 “use-it-or-lose-it(사용 또는 소멸)” 할인입니다. 지정된 시간 동안 예약 조건을 충족하는 블록 Blob 또는 Azure Data Lake Storage Gen2 리소스가 없으면 해당 시간에 대한 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

리소스를 삭제하면 지정된 범위에 있는 일치하는 다른 리소스에 예약 할인이 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간은 소멸됩니다.

## <a name="discount-examples"></a>할인 예제

다음 예제에서는 배포에 따라 Azure Storage 예약된 용량 할인이 적용되는 방법을 보여줍니다.

1년 동안 미국 서부 2 지역에서 100TB의 예약된 용량을 구입했다고 가정합니다. 예약은 핫 액세스 계층의 LRS(로컬 중복 스토리지)에 대한 것입니다.

이 샘플 예약의 비용을 18,540달러라고 가정합니다. 전체 금액을 미리 지불하거나 앞으로 12개월 동안 매월 1,545달러의 고정 월별 할부금을 지불하도록 선택할 수 있습니다.

이러한 예제에서는 월별 예약 지불 계획에 등록했다고 가정합니다. 다음 시나리오에서는 예약된 용량을 사용하거나 초과 사용하는 경우 발생하는 상황을 설명합니다.

### <a name="underusing-your-capacity"></a>용량 미달 사용

예약 기간 내의 지정된 시간에 100TB의 예약된 용량 중 80TB를 사용했다고 가정합니다. 나머지 20TB는 해당 시간 동안 적용되지 않으며 이월되지 않습니다.

### <a name="overusing-your-capacity"></a>용량 초과 사용

예약 기간 내의 지정된 시간에 101TB의 스토리지 용량을 사용했다고 가정합니다. 데이터 100TB에 예약 할인이 적용되며 나머지 1TB는 해당 시간에 대한 종량제 요금으로 청구됩니다. 다음 시간에 사용량이 100TB로 변경되면 모든 사용량은 예약에 포함됩니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [예약된 용량으로 Blob 스토리지에 대한 비용 최적화](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Azure 예약이란?](save-compute-costs-reservations.md)
