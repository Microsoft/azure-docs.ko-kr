---
title: Azure Data Box Edge 제한 | Microsoft Docs
description: Microsoft Azure Data Box Edge의 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: a687398032adffe5e787ef1469abe0b2627a38fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758027"
---
# <a name="azure-data-box-edge-limits"></a>Azure 데이터 가장자리가 상자의 제한

Microsoft Azure Data Box Edge 솔루션을 배포 및 운영하면서 이러한 제한을 고려합니다.

## <a name="data-box-edge-service-limits"></a>Data Box Edge 서비스 제한

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Data Box Edge 디바이스 제한

다음 표에서는 Data Box Edge 디바이스에 대한 제한을 설명합니다.

| 설명 | 값 |
|---|---|
|아니요. 디바이스당 파일 수 |1억 |
|아니요. 디바이스당 공유 수 |24 |
|아니요. 컨테이너당 공유 수 |1 |
|공유에 기록되는 최대 파일 크기| 5TB |

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure Storage 계정 크기 및 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Data Box Gateway 배포 준비](data-box-gateway-deploy-prep.md)
