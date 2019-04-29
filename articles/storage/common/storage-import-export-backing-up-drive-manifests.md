---
title: Azure Import/Export 드라이브 매니페스트 백업 | Microsoft Docs
description: Microsoft Azure Import/Export 서비스에 대한 드라이브 매니페스트를가 자동으로 백업하도록 하는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483131"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Azure Import/Export 작업에 대한 드라이브 배니페스트 백업

드라이브 매니페스트는 [작업 배치](/rest/api/storageimportexport/jobs) 또는 [작업 속성 업데이트](/rest/api/storageimportexport/jobs) REST API 작업에서 `BackupDriveManifest` 속성을 `true`로 설정하여 자동으로 Blob에 백업될 수 있습니다. 기본적으로 드라이브 매니페스트는 백업되지 않습니다. 드라이브 매니페스트 백업은 작업과 연결된 저장소 계정 내의 컨테이너에 블록 blob으로 저장됩니다. 기본적으로 컨테이너 이름은 `waimportexport`이지만 `Put Job` 또는 `Update Job Properties` 작업을 호출할 때 `DiagnosticsPath` 속성에서 다른 이름을 지정할 수 있습니다. 백업 매니페스트 blob은 다음과 같은 형식으로 이름이 지정됩니다: `waies/jobname_driveid_timestamp_manifest.xml`.

 [작업 가져오기](/rest/api/storageimportexport/jobs) 작업을 호출하여 작업의 백업 드라이브 매니페스트 URI를 검색할 수 있습니다. Blob URI는 각 드라이브에 대한 `ManifestUri` 속성으로 반환됩니다.

## <a name="next-steps"></a>다음 단계

* [Import/Export 서비스 REST API 사용](storage-import-export-using-the-rest-api.md)
