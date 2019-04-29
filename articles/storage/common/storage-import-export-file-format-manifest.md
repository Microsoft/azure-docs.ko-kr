---
title: Azure Import/Export 매니페스트 파일 형식 | Microsoft Docs
description: Import/Export 서비스에서 Azure Blob Storage의 Blob와 가져오기 또는 내보내기 작업에 있는 드라이브의 파일 간에 매핑을 설명하는 드라이브 매니페스트 파일의 형식에 대해 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ee53cc3a639a79e1b29ac6cd537bfb04e05b1bca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478626"
---
# <a name="azure-importexport-service-manifest-file-format"></a>Azure Import/Export 서비스 매니페스트 파일 형식
드라이브 매니페스트 파일에서는 Azure Blob Storage의 Blob와 가져오기 또는 내보내기 작업으로 구성된 드라이브의 파일 간에 매핑을 설명합니다. 가져오기 작업의 경우 매니페스트 파일은 드라이브 준비 프로세스의 일부로 만들어지고 Azure 데이터 센터에 드라이브를 전송하기 전에 드라이브에 저장됩니다. 내보내기 작업 도중 Azure Import/Export 서비스에 의해 매니페스트가 생성되어 드라이브에 저장됩니다.  
  
가져오기 또는 내보내기 작업의 경우 드라이브 매니페스트 파일은 가져오기 및 내보내기 드라이브에 저장되고 API 작업을 통해 서비스에 전송되지 않습니다.  
  
다음은 드라이브 매니페스트 파일의 일반 형식에 대해 설명합니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveManifest Version="2014-11-01">  
  <Drive>  
    <DriveId>drive-id</DriveId>  
    import-export-credential  
  
    <!-- First Blob List -->  
    <BlobList>  
      <!-- Global properties and metadata that applies to all blobs -->  
      [<MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>]  
      [<PropertiesPath   
        Hash="md5-hash">global-properties-file-path</PropertiesPath>]  
  
      <!-- First Blob -->  
      <Blob>  
        <BlobPath>blob-path-relative-to-account</BlobPath>  
        <FilePath>file-path-relative-to-transfer-disk</FilePath>  
        [<ClientData>client-data</ClientData>]  
        [<Snapshot>snapshot</Snapshot>]  
        <Length>content-length</Length>  
        [<ImportDisposition>import-disposition</ImportDisposition>]  
        page-range-list-or-block-list          
        [<MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>]  
        [<PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>]  
      </Blob>  
  
      <!-- Second Blob -->  
      <Blob>  
      . . .  
      </Blob>  
    </BlobList>  
  
    <!-- Second Blob List -->  
    <BlobList>  
    . . .  
    </BlobList>  
  </Drive>  
</DriveManifest>  
  
import-export-credential ::=   
  <StorageAccountKey>storage-account-key</StorageAccountKey> | <ContainerSas>container-sas</ContainerSas>  
  
page-range-list-or-block-list ::=   
  page-range-list | block-list  
  
page-range-list ::=   
    <PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       Hash="md5-hash"/>]  
    </PageRangeList>  
  
block-list ::=  
    <BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       Hash="md5-hash"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       Hash="md5-hash"/>]  
    </BlockList>  

```

## <a name="manifest-xml-elements-and-attributes"></a>매니페스트 XML 요소 및 특성

드라이브 매니페스트 XML 형식의 데이터 요소 및 특성은 다음 테이블에 지정됩니다.  
  
|XML 요소|Type|설명|  
|-----------------|----------|-----------------|  
|`DriveManifest`|루트 요소|매니페스트 파일의 루트 요소입니다. 파일의 다른 모든 요소는 이 요소에 있습니다.|  
|`Version`|특성, 문자열|매니페스트 파일의 버전입니다.|  
|`Drive`|중첩 XML 요소|각 드라이브에 대한 매니페스트를 포함합니다.|  
|`DriveId`|String|드라이브의 고유한 드라이브 식별자입니다. 해당 일련 번호에 대한 드라이브를 쿼리하여 드라이브 식별자를 찾을 수 있습니다. 드라이브 일련 번호는 일반적으로 드라이브의 외부에 인쇄됩니다. `DriveID` 요소는 매니페스트 파일에서 `BlobList` 요소보다 먼저 나타나야 합니다.|  
|`StorageAccountKey`|String|`ContainerSas`이 지정되지 않은 경우 가져오기 작업에 필요합니다. Azure Storage 계정의 계정 키는 작업과 연결되었습니다.<br /><br /> 이 요소는 내보내기 작업에 대한 매니페스트에서 생략됩니다.|  
|`ContainerSas`|String|`StorageAccountKey`이 지정되지 않은 경우 가져오기 작업에 필요합니다. 작업과 연결된 Blob에 액세스하기 위한 컨테이너 SAS입니다. 해당 형식은 [작업 배치](/rest/api/storageimportexport/jobs)를 참조하세요. 이 요소는 내보내기 작업에 대한 매니페스트에서 생략됩니다.|  
|`ClientCreator`|String|XML 파일을 만든 클라이언트를 지정합니다. 이 값은 Import/Export 서비스에서 해석되지 않습니다.|  
|`BlobList`|중첩 XML 요소|가져오기 또는 내보내기 작업의 일부인 Blob의 목록을 포함합니다. Blob 목록에 있는 각 Blob는 동일한 메타데이터 및 속성을 공유합니다.|  
|`BlobList/MetadataPath`|String|선택 사항입니다. 기본 메타데이터를 포함하는 디스크에 대한 파일의 상대 경로를 지정합니다. 메타데이터는 가져오기 작업의 Blob 목록에 있는 Blob에 대해 설정됩니다. 이 메타데이터는 Blob 별로 선택적으로 재정의될 수 있습니다.<br /><br /> 이 요소는 내보내기 작업에 대한 매니페스트에서 생략됩니다.|  
|`BlobList/MetadataPath/@Hash`|특성, 문자열|메타데이터 파일의 Base16 인코딩 MD5 해시 값을 지정합니다.|  
|`BlobList/PropertiesPath`|String|선택 사항입니다. 기본 속성을 포함하는 디스크에 대한 파일의 상대 경로를 지정합니다. 속성은 가져오기 작업의 Blob 목록에 있는 Blob에 대해 설정됩니다. 이러한 속성은 Blob 별로 선택적으로 재정의될 수 있습니다.<br /><br /> 이 요소는 내보내기 작업에 대한 매니페스트에서 생략됩니다.|  
|`BlobList/PropertiesPath/@Hash`|특성, 문자열|속성 파일의 Base16 인코딩 MD5 해시 값을 지정합니다.|  
|`Blob`|중첩 XML 요소|각 Blob 목록에 있는 각 Blob에 대한 정보를 포함합니다.|  
|`Blob/BlobPath`|String|컨테이너 이름으로 시작하는 Blob에 대한 상대 URI입니다. Blob가 루트 컨테이너에 있으면 `$root`로 시작해야 합니다.|  
|`Blob/FilePath`|String|드라이브의 파일에 대한 상대 경로를 지정합니다. 내보내기 작업의 경우 가능하면 Blob 경로를 파일 경로에 사용합니다. *예를 들어* `pictures/bob/wild/desert.jpg`을 `\pictures\bob\wild\desert.jpg`로 내보냅니다. 그러나 NTFS 이름의 제한으로 인해 Blob 경로와 유사하지 않은 경로를 사용하여 Blob를 파일로 내보낼 수 있습니다.|  
|`Blob/ClientData`|String|선택 사항입니다. 고객의 의견을 포함하고 있습니다. 이 값은 Import/Export 서비스에서 해석되지 않습니다.|  
|`Blob/Snapshot`|DateTime|내보내기 작업의 경우 선택 사항입니다. 내보낸 Blob 스냅숏의 스냅숏 식별자를 지정합니다.|  
|`Blob/Length`|정수 |Blob의 총 길이를 바이트 단위로 지정합니다. 값은 블록 Blob의 경우 최대 200GB이고 페이지 Blob의 경우 최대 1TB가 될 수 있습니다. 페이지 Blob의 경우 이 값은 512의 배수입니다.|  
|`Blob/ImportDisposition`|String|가져오기 작업의 경우 선택 사항이고 내보내기 작업의 경우 생략됩니다. 그러면 동일한 이름의 Blob가 이미 있는 가져오기 작업인 경우 Import/Export 서비스에서 처리해야 하는 방법을 지정합니다. 이 값을 가져오기 매니페스트에서 생략하면 기본값은 `rename`입니다.<br /><br /> 이 요소의 값은 다음과 같습니다.<br /><br /> -   `no-overwrite`: 같은 이름의 대상 Blob가 이미 있는 경우 가져오기 작업에서는 이 파일을 가져오는 단계를 건너뜁니다.<br />-   `overwrite`: 새로 가져온 파일이 모든 기존 대상 Blob를 완전히 덮어씁니다.<br />-   `rename`: 새 Blob가 수정된 이름으로 업로드됩니다.<br /><br /> 이름 바꾸기 규칙은 다음과 같습니다.<br /><br /> - Blob 이름에 점이 포함되지 않는 경우 `(2)`을 원래 Blob 이름에 추가하여 새 이름을 생성합니다. 새 이름이 기존 Blob 이름과 충돌할 경우 `(2)` 대신 `(3)`가 추가됩니다.<br />- Blob 이름에 점이 포함되는 경우 마지막 점의 뒷부분이 확장명으로 간주됩니다. 위의 절차와 비슷하게 새 이름을 생성하려면 마지막 점 앞에 `(2)`을 삽입합니다. 새 이름이 여전히 기존 Blob 이름과 충돌하는 경우 서비스에서는 충돌하지 않는 이름을 찾을 때까지 `(3)`, `(4)` 등을 시도합니다.<br /><br /> 일부 사례:<br /><br /> Blob `BlobNameWithoutDot`의 이름은 다음으로 바뀝니다.<br /><br /> `BlobNameWithoutDot (2)  // if BlobNameWithoutDot exists`<br /><br /> `BlobNameWithoutDot (3)  // if both BlobNameWithoutDot and BlobNameWithoutDot (2) exist`<br /><br /> Blob `Seattle.jpg`의 이름은 다음으로 바뀝니다.<br /><br /> `Seattle (2).jpg  // if Seattle.jpg exists`<br /><br /> `Seattle (3).jpg  // if both Seattle.jpg and Seattle (2).jpg exist`|  
|`PageRangeList`|중첩 XML 요소|페이지 Blob에 필요합니다.<br /><br /> 가져오기 작업의 경우 가져올 파일의 바이트 범위 목록을 지정합니다. 각 페이지 범위는 지역의 MD5 해시와 함께 페이지 범위를 설명하는 원본 파일의 오프셋과 길이를 기준으로 설명됩니다. 페이지 범위의 `Hash` 특성이 필요합니다. 서비스에서는 Blob에 있는 데이터의 해시가 페이지 범위에서 계산된 MD5 해시와 일치하는지 확인합니다. 총 크기가 최대 1TB인 가져오기의 파일을 설명하는 데 페이지 범위의 모든 수를 사용할 수 있습니다. 오프셋을 기준으로 모든 페이지 범위를 정렬해야 하고 겹치지 않아야 합니다.<br /><br /> 내보내기 작업의 경우 드라이브로 내보내진 Blob의 바이트 범위 집합을 지정합니다.<br /><br /> 페이지 범위는 Blob 또는 파일의 하위 범위만을 허용할 수 있습니다.  페이지 범위에서 허용되지 않는 파일의 나머지 부분이 발생하면 해당 콘텐츠는 정의되지 않을 수 있습니다.|  
|`PageRange`|XML 요소|페이지 범위를 나타냅니다.|  
|`PageRange/@Offset`|특성, 정수|지정된 페이지 범위가 시작되는 전송 파일 및 Blob의 오프셋을 지정합니다. 이 값은 512의 배수여야 합니다.|  
|`PageRange/@Length`|특성, 정수|페이지 범위의 길이를 지정합니다. 이 값은 512의 배수이고 4MB 이하여야 합니다.|  
|`PageRange/@Hash`|특성, 문자열|페이지 범위의 Base16 인코딩 MD5 해시 값을 지정합니다.|  
|`BlockList`|중첩 XML 요소|블록의 이름을 지정한 블록 Blob에 필요합니다.<br /><br /> 가져오기 작업의 경우 블록 목록은 Azure Storage로 가져올 블록 집합을 지정합니다. 내보내기 작업의 경우 블록 목록은 각 블록이 내보내기 디스크의 파일에 저장된 위치를 지정합니다. 각 블록은 파일 및 블록 길이에 있는 오프셋을 기준으로 설명되고 추가로 블록 ID 특성 별로 이름이 지정되며 블록에 대한 MD5 해시를 포함합니다. Blob을 설명하는 데 최대 50,000개의 블록을 사용할 수 있습니다.  모든 블록은 오프셋을 기준으로 정렬되고 파일의 전체 범위를 포함해야 합니다. *즉*, 블록 사이에 간격이 없어야 합니다. Blob이 64MB 이하인 경우 각 블록의 블록 ID는 모두 지워지거나 모두 표시되어야 합니다. 블록 ID는 Base64 인코딩 문자열이어야 합니다. 블록 ID에 대한 추가 요구 사항은 [블록 배치](/rest/api/storageservices/put-block)를 참조하세요.|  
|`Block`|XML 요소|블록을 나타냅니다.|  
|`Block/@Offset`|특성, 정수|지정된 블록이 시작하는 오프셋을 지정합니다.|  
|`Block/@Length`|특성, 정수|블록에서 바이트 수를 지정합니다. 이 값은 4MB 이하여야 합니다.|  
|`Block/@Id`|특성, 문자열|블록의 블록 ID를 나타내는 문자열을 지정합니다.|  
|`Block/@Hash`|특성, 문자열|블록의 Base16 인코딩 MD5 해시를 지정합니다.|  
|`Blob/MetadataPath`|String|선택 사항입니다. 메타데이터 파일의 상대 경로를 지정합니다. 가져오기 작업 중 대상 Blob에 메타데이터가 설정됩니다. 내보내기 작업 중 Blob의 메타데이터는 드라이브의 메타데이터 파일에 저장됩니다.|  
|`Blob/MetadataPath/@Hash`|특성, 문자열|Blob 메타데이터 파일의 Base16 인코딩 MD5 해시를 지정합니다.|  
|`Blob/PropertiesPath`|String|선택 사항입니다. 속성 파일의 상대 경로를 지정합니다. 가져오기 작업 중 대상 Blob에 속성이 설정됩니다. 내보내기 작업 중 Blob 속성은 드라이브의 속성에 저장됩니다.|  
|`Blob/PropertiesPath/@Hash`|특성, 문자열|Blob 속성 파일의 Base16 인코딩 MD5 해시를 지정합니다.|  
  
## <a name="next-steps"></a>다음 단계
 
* [저장소 Import/Export REST API](/rest/api/storageimportexport/)
