---
title: Azure Import/Export 메타데이터 및 속성 파일 형식 | Microsoft Docs
description: 가져오기 또는 내보내기 작업의 일부인 하나 이상의 Blob에 대해 메타데이터 및 속성을 지정하는 방법을 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 2066d4a2ed6db97285d92d15e14dbd21629dbdfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478558"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure Import/Export 서비스 메타데이터 및 속성 파일 형식
가져오기 작업 또는 내보내기 작업의 일부로 하나 이상의 Blob에 대한 메타데이터 및 속성을 지정할 수 있습니다. 가져오기 작업의 일부로 만들어지는 Blob에 대한 메타데이터 또는 속성을 설정하려면 가져올 데이터가 있는 하드 드라이브에 메타데이터 또는 속성 파일을 제공합니다. 내보내기 작업의 경우 메타데이터 및 속성은 반환된 하드 드라이브에 포함된 메타데이터 또는 속성 파일에 기록됩니다.  
  
## <a name="metadata-file-format"></a>메타데이터 파일 형식  
메타데이터 파일의 형식은 다음과 같습니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML 요소|Type|설명|  
|-----------------|----------|-----------------|  
|`Metadata`|루트 요소|메타데이터 파일의 루트 요소입니다.|  
|`metadata-name`|String|선택 사항입니다. XML 요소는 Blob에 대한 메타데이터의 이름을 지정하고 값은 메타데이터 설정 값을 지정합니다.|  
  
## <a name="properties-file-format"></a>속성 파일 형식  
속성 파일의 형식은 다음과 같습니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML 요소|Type|설명|  
|-----------------|----------|-----------------|  
|`Properties`|루트 요소|속성 파일의 루트 요소입니다.|  
|`Last-Modified`|String|선택 사항입니다. Blob에 대한 마지막 수정 시간입니다. 내보내기 작업에만 해당합니다.|  
|`Etag`|String|선택 사항입니다. Blob의 ETag 값입니다. 내보내기 작업에만 해당합니다.|  
|`Content-Length`|String|선택 사항입니다. Blob의 크기(바이트)입니다. 내보내기 작업에만 해당합니다.|  
|`Content-Type`|String|선택 사항입니다. Blob의 콘텐츠 형식입니다.|  
|`Content-MD5`|String|선택 사항입니다. Blob의 MD5 해시입니다.|  
|`Content-Encoding`|String|선택 사항입니다. Blob의 콘텐츠 인코딩입니다.|  
|`Content-Language`|String|선택 사항입니다. Blob의 콘텐츠 언어입니다.|  
|`Cache-Control`|String|선택 사항입니다. Blob의 캐시 제어 문자열입니다.|  

## <a name="next-steps"></a>다음 단계

Blob 메타데이터 및 속성을 설정하는 방법에 대한 자세한 규칙은 [Blob 속성 설정](/rest/api/storageservices/set-blob-properties), [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata) 및 [Blob 리소스에 대한 속성 및 메타데이터 설정 및 검색](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources)을 참조하세요.
