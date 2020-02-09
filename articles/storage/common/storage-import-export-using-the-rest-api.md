---
title: Azure Import/Export 서비스를 사용 하 여 REST API | Microsoft Docs
description: 방법 및 참조 자료를 포함 하 여 Azure Import/Export 서비스 REST API 사용에 대 한 리소스를 찾을 수 있는 위치를 알아봅니다.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978869"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Az Azure Import/Export szolgáltatás REST API-jának használata

Microsoft Azure Import/Export 서비스는 가져오기/내보내기 작업을 프로그래밍 방식으로 제어할 수 있도록 REST API를 노출 합니다. REST API를 사용 하 여 [Azure Portal](https://portal.azure.com/)에서 수행할 수 있는 모든 가져오기/내보내기 작업을 수행할 수 있습니다. 또한 REST API를 사용 하 여 현재 Azure Portal에서 사용할 수 없는 작업 완료 비율을 쿼리 하는 것과 같은 특정 세부적인 작업을 수행할 수 있습니다.

Import/Export 서비스의 개요 및 포털을 사용 하 여 가져오기 및 내보내기 작업을 만들고 관리 하는 방법을 보여 주는 자습서는 [Microsoft Azure Import/Export 서비스를 사용 하 여 데이터를 Blob Storage에 전송](../storage-import-export-service.md) 을 참조 하세요.

## <a name="service-endpoints"></a>Szolgáltatásvégpontok

Azure Import/Export 서비스는 Azure Resource Manager에 대 한 리소스 공급자 이며 가져오기/내보내기 작업을 관리 하기 위한 다음 HTTPS 엔드포인트에서 REST Api 집합을 제공 합니다.

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Verziókezelés

Import/Export 서비스에 대 한 요청은 `api-version` 매개 변수를 지정 하 고 해당 값을 `2016-11-01`으로 설정 해야 합니다.

## <a name="importexport-service-operations"></a>가져오기/내보내기 서비스 작업

[Importálási feladat létrehozása](../storage-import-export-creating-an-import-job.md)

[Exportálási feladat létrehozása](../storage-import-export-creating-an-export-job.md)

[Feladat állapotinformációinak lekérése](storage-import-export-retrieving-state-info-for-a-job.md)

[Feladatok számbavétele](../storage-import-export-enumerating-jobs.md)

[Feladatok megszakítása és törlése](storage-import-export-cancelling-and-deleting-jobs.md)

[드라이브 매니페스트 백업](../storage-import-export-backing-up-drive-manifests.md)

[Import/Export-feladatok diagnosztizálása és hibajavítása](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Következő lépések

* [저장소 가져오기/내보내기 REST](/rest/api/storageimportexport)
