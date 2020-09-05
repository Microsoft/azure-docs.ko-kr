---
title: Azure Stack Edge 제한 | Microsoft Docs
description: 서비스 한도, 장치 제한 및 저장소 제한을 포함 하 여 Azure Stack Edge를 배포 하 고 작동할 때 제한 사항 및 권장 크기에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: e21f5a89d9f1f21eb99071a141794f99c07a8dfa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079815"
---
# <a name="azure-stack-edge-limits"></a>Azure Stack Edge 제한 사항

Microsoft Azure Stack Edge 솔루션을 배포 하 고 작동할 때 이러한 제한을 고려 합니다. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge 서비스 제한

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack 장치 제한

다음 표에서는 Azure Stack Edge 장치에 대 한 제한을 설명 합니다. 

| Description | 값 |
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

- [Azure Stack Edge 배포 준비](azure-stack-edge-deploy-prep.md)
