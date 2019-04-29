---
title: Azure Import/Export 내보내기 작업에 대한 드라이브 사용량 미리 보기 - v1 | Microsoft Docs
description: Azure Import/Export 서비스에서 내보내기 작업을 위해 선택한 Blob의 목록을 미리 보는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 53ab1e28c5864b403d52bf5e73f0c5c41b8f18a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478456"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>내보내기 작업에 대한 드라이브 사용량 미리 보기
내보내기 작업을 만들기 전에 내보낼 Blob 집합을 선택해야 합니다. Microsoft Azure Import/Export 서비스를 사용하면 Blob 경로 또는 Blob 접두사 목록을 사용하여 선택한 Blob을 나타낼 수 있습니다.  
  
그런 다음 보내야 하는 드라이브 수를 결정해야 합니다. Import/Export 도구는 사용하고자 하는 드라이브의 크기에 따라 선택한 Blob에 대한 드라이브 사용량을 미리 볼 수 있는 `PreviewExport` 명령을 제공합니다.

## <a name="command-line-parameters"></a>명령줄 매개 변수

Import/Export 도구의 `PreviewExport` 명령을 사용할 때 다음 매개 변수를 사용할 수 있습니다.

|명령줄 매개 변수|설명|  
|--------------------------|-----------------|  
|**/logdir:**<LogDirectory\>|선택 사항입니다. 로그 디렉터리입니다. 이 디렉터리에 자세한 로그 파일이 기록됩니다. 로그 디렉터리를 지정하지 않는 경우 현재 디렉터리가 로그 디렉터리로 사용됩니다.|  
|**/sn:**<StorageAccountName\>|필수 사항입니다. 내보내기 작업에 대한 저장소 계정의 이름입니다.|  
|**/sk:**<StorageAccountKey\>|컨테이너 SAS가 지정되지 않은 경우에만 필요합니다. 내보내기 작업에 대한 저장소 계정의 계정 키입니다.|  
|**/csas:**<ContainerSas\>|저장소 계정 키가 지정되지 않은 경우에만 필요합니다. 내보내기 작업에서 내보낼 Blob을 나열하기 위한 컨테이너 SAS입니다.|  
|**/ExportBlobListFile:**<ExportBlobListFile\>|필수 사항입니다. 내보낼 Blob에 대한 Blob 경로 또는 Blob 경로 접두사 목록을 포함하고 있는 XML 파일의 경로입니다. Import/Export 서비스 REST API의 [작업 배치](/rest/api/storageimportexport/jobs) 작업에서 `BlobListBlobPath` 요소에 사용되는 파일 형식입니다.|  
|**/DriveSize:**<DriveSize\>|필수 사항입니다. 내보내기 작업에 사용할 드라이브의 크기는 *예를 들어* 500GB, 1.5TB입니다.|  

## <a name="command-line-example"></a>명령줄 예제

다음 예제에서는 `PreviewExport` 명령을 보여 줍니다.  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
내보내기 Blob 목록 파일에는 다음과 같이 Blob 이름과 Blob 접두사가 포함될 수 있습니다.  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure Import/Export 도구는 내보낼 모든 Blob을 나열하고 필요한 오버헤드를 고려하여 지정된 크기의 드라이브에 패키지하는 방법을 계산한 다음 Blob 및 드라이브 사용 정보를 보유하는 데 필요한 드라이브 수를 추정합니다.  
  
정보 로그가 생략된 출력의 예제는 다음과 같습니다.  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>다음 단계

* [Azure Import/Export 도구 참조](../storage-import-export-tool-how-to-v1.md)
