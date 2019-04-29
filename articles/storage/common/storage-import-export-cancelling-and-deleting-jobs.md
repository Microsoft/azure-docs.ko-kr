---
title: Azure Import/Export 작업의 취소/삭제 | Microsoft Docs
description: Microsoft Azure Import/Export 서비스에 대해 작업을 취소하고 삭제하는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 6eb56413319208feef1b4ab51296fe12a1e0bcf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483148"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Azure Import/Export 서비스 작업 취소 및 삭제

 작업이 `Packaging` 상태가 되기 전에 취소되도록 요청하려면 [작업 속성 업데이트](/rest/api/storageimportexport/jobs) 작업을 호출하고 `CancelRequested` 요소를 `true`로 설정합니다. 작업은 최선의 결과가 얻어지는 방향으로 취소됩니다. 드라이브가 데이터 전송 중인 상태이면 취소가 요청된 후에도 데이터가 계속 전송될 수 있습니다.

 취소된 작업은 `Completed` 상태로 이동되고 90일 동안 보관되며 이 시점에서 삭제됩니다.

 작업을 삭제하려면 작업이 배송되기 전에(즉, 작업이 `Creating` 상태에 있는 동안) [작업 삭제](/rest/api/storageimportexport/jobs) 작업을 호출합니다. 또한 작업이 `Completed` 상태인 경우 작업을 삭제할 수 있습니다. 작업이 삭제된 후에는 REST API 또는 Azure Portal을 통해 정보 및 상태에 더 이상 액세스할 수 없습니다.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
