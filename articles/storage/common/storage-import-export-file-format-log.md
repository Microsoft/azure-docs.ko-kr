---
title: Azure Import/Export 로그 파일 형식 | Microsoft Docs
description: Import/Export 서비스 작업에 대한 단계를 실행할 때 만든 로그 파일의 형식에 대해 알아보기
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 00e226134039d29efd744290c4bc63abd50adc89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478609"
---
# <a name="azure-importexport-service-log-file-format"></a>Azure Import/Export 서비스 로그 파일 형식
Microsoft Azure Import/Export 서비스가 드라이브에서 가져오기 작업 또는 내보내기 작업의 일부로 작업을 수행하는 경우 로그는 해당 작업과 연결된 스토리지 계정의 블록 Blob에 작성됩니다.  
  
Import/Export 서비스에서 기록할 수 있는 로그에는 두 가지가 있습니다.  
  
-   오류 로그는 항상 오류가 발생한 경우에 생성됩니다.  
  
-   세부 정보 표시 로그는 기본적으로 해제되어 있지만 [작업 배치](/rest/api/storageimportexport/jobs) 또는 [작업 속성 업데이트](/rest/api/storageimportexport/jobs) 작업에서 `EnableVerboseLog` 속성을 설정하여 사용할 수 있습니다.  
  
## <a name="log-file-location"></a>로그 파일 위치  
로그는 `Put Job` 작업에서 설정할 수 있는 `ImportExportStatesPath` 설정에서 지정한 컨테이너 또는 가상 디렉터리의 블록 Blob에 작성됩니다. 로그가 기록되는 위치는 `ImportExportStatesPath`에 지정된 값과 함께 작업에 인증을 지정하는 방법에 따라 달라집니다. 저장소 계정 키 또는 컨테이너 SAS(공유 액세스 서명)를 통해 작업에 대한 인증을 지정할 수 있습니다.  
  
컨테이너나 가상 디렉터리의 이름은 기본 이름 `waimportexport` 또는 지정한 다른 컨테이너나 가상 디렉터리 이름 중 하나일 수 있습니다.  
  
아래 테이블에서는 가능한 옵션을 보여 줍니다.  
  
|인증 방법|`ImportExportStatesPath`요소의 값|로그 Blob의 위치|  
|---------------------------|----------------------------------------------|---------------------------|  
|Storage 계정 키|기본값|`waimportexport`라는 컨테이너가 기본 컨테이너입니다. 예를 들면 다음과 같습니다.<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Storage 계정 키|사용자 지정 값|사용자가 명명한 컨테이너 예를 들면 다음과 같습니다.<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|컨테이너 SAS|기본값|기본 이름인 `waimportexport`라는 가상 디렉터리는 SAS에 지정된 컨테이너 아래에 있습니다.<br /><br /> 예를 들어 작업에 대해 지정된 SAS가 `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`인 경우 로그 위치는 `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`입니다.|  
|컨테이너 SAS|사용자 지정 값|사용자가 명명한 가상 디렉터리는 SAS에 지정된 컨테이너 아래에 있습니다.<br /><br /> 예를 들어 작업에 대해 지정된 SAS가 `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`이고 지정된 가상 디렉터리 이름이 `mylogblobs`인 경우 로그 위치는 `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`입니다.|  
  
[작업 가져오기](/rest/api/storageimportexport/jobs) 작업을 호출하여 오류 및 세부 정보 표시 로그의 URL를 검색할 수 있습니다. 로그는 드라이브의 처리가 완료된 후에 사용할 수 있습니다.  
  
## <a name="log-file-format"></a>로그 파일 형식  
두 로그의 형식은 하드 드라이브와 고객의 계정 간에 Blob을 복사하는 동안 발생하는 이벤트의 XML 설명을 포함하는 Blob으로 동일합니다.  
  
자세한 정보 표시 로그는 모든 Blob(가져오기 작업의 경우) 또는 파일(내보내기 작업의 경우)에 대한 복사 작업 상태에 대한 전체 정보를 포함합니다. 반면 오류 로그는 가져오기 또는 내보내기 작업 중에 오류가 발생한 Blob 또는 파일에 대한 정보만을 포함합니다.  
  
자세한 정보 표시 로그 형식은 아래와 같습니다. 오류 로그는 동일한 구조를 갖지만 성공적인 작업을 필터링합니다.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

다음 테이블에서는 로그 파일의 요소에 대해 설명합니다.  
  
|XML 요소|Type|설명|  
|-----------------|----------|-----------------|  
|`DriveLog`|XML 요소|드라이브 로그를 나타냅니다.|  
|`Version`|특성, 문자열|로그 형식의 버전입니다.|  
|`DriveId`|String|드라이브의 하드웨어 일련 번호입니다.|  
|`Status`|String|처리 중인 드라이브의 상태입니다. 자세한 내용은 아래 `Drive Status Codes` 테이블을 참조하세요.|  
|`Blob`|중첩 XML 요소|Blob을 나타냅니다.|  
|`Blob/BlobPath`|String|Blob의 URI입니다.|  
|`Blob/FilePath`|String|드라이브의 파일에 대한 상대 경로입니다.|  
|`Blob/Snapshot`|DateTime|내보내기 작업에 대한 Blob의 스냅숏 버전입니다.|  
|`Blob/Length`|정수 |바이트 단위인 Blob의 총 길이입니다.|  
|`Blob/LastModified`|DateTime|내보내기 작업에 대해 Blob를 마지막으로 수정한 날짜/시간입니다.|  
|`Blob/ImportDisposition`|String|가져오기 작업에 대한 Blob의 가져오기 처리입니다.|  
|`Blob/ImportDisposition/@Status`|특성, 문자열|가져오기 처리의 상태입니다.|  
|`PageRangeList`|중첩 XML 요소|페이지 Blob에 대한 페이지 범위 목록을 나타냅니다.|  
|`PageRange`|XML 요소|페이지 범위를 나타냅니다.|  
|`PageRange/@Offset`|특성, 정수|Blob에서 페이지 범위의 시작 오프셋입니다.|  
|`PageRange/@Length`|특성, 정수|바이트 단위인 페이지 범위의 길이입니다.|  
|`PageRange/@Hash`|특성, 문자열|페이지 범위의 Base16 인코딩 MD5 해시입니다.|  
|`PageRange/@Status`|특성, 문자열|페이지 범위를 처리하는 상태입니다.|  
|`BlockList`|중첩 XML 요소|블록 Blob에 대한 블록 목록을 나타냅니다.|  
|`Block`|XML 요소|블록을 나타냅니다.|  
|`Block/@Offset`|특성, 정수|Blob에서 블록의 시작 오프셋입니다.|  
|`Block/@Length`|특성, 정수|바이트 단위인 블록의 길이입니다.|  
|`Block/@Id`|특성, 문자열|블록 ID입니다.|  
|`Block/@Hash`|특성, 문자열|블록의 Base16 인코딩 MD5 해시입니다.|  
|`Block/@Status`|특성, 문자열|블록을 처리하는 상태입니다.|  
|`Metadata`|중첩 XML 요소|Blob의 메타데이터를 나타냅니다.|  
|`Metadata/@Status`|특성, 문자열|Blob 메타데이터를 처리하는 상태입니다.|  
|`Metadata/GlobalPath`|String|전역 메타데이터 파일에 대한 상대 경로입니다.|  
|`Metadata/GlobalPath/@Hash`|특성, 문자열|전역 메타데이터 파일의 Base16 인코딩 MD5 해시입니다.|  
|`Metadata/Path`|String|메타데이터 파일에 대한 상대 경로입니다.|  
|`Metadata/Path/@Hash`|특성, 문자열|메타데이터 파일의 Base16 인코딩 MD5 해시입니다.|  
|`Properties`|중첩 XML 요소|Blob 속성을 나타냅니다.|  
|`Properties/@Status`|특성, 문자열|Blob 속성을 처리하는 상태입니다(예: 파일을 찾을 수 없음, 완료됨).|  
|`Properties/GlobalPath`|String|전역 속성 파일에 대한 상대 경로입니다.|  
|`Properties/GlobalPath/@Hash`|특성, 문자열|전역 속성 파일의 Base16 인코딩 MD5 해시입니다.|  
|`Properties/Path`|String|속성 파일에 대한 상대 경로입니다.|  
|`Properties/Path/@Hash`|특성, 문자열|속성 파일의 Base16 인코딩 MD5 해시입니다.|  
|`Blob/Status`|String|Blob을 처리하는 상태입니다.|  
  
## <a name="drive-status-codes"></a>드라이브 상태 코드  
다음 테이블에서는 드라이브를 처리하는 상태 코드를 나열합니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|`Completed`|드라이브는 오류 없이 처리를 완료했습니다.|  
|`CompletedWithWarnings`|드라이브는 Blob에 대해 지정된 가져오기 처리당 하나 이상의 Blob에서 경고 처리를 완료했습니다.|  
|`CompletedWithErrors`|드라이브는 하나 이상의 Blob 또는 청크에서 오류를 해결했습니다.|  
|`DiskNotFound`|드라이브에서 디스크를 찾을 수 없습니다.|  
|`VolumeNotNtfs`|디스크의 첫 번째 데이터 볼륨은 NTFS 형식이 아닙니다.|  
|`DiskOperationFailed`|드라이브에서 작업을 수행할 때 알 수 없는 오류가 발생했습니다.|  
|`BitLockerVolumeNotFound`|BitLocker 암호화 가능 볼륨을 찾을 수 없습니다.|  
|`BitLockerNotActivated`|BitLocker가 볼륨에서 활성화되지 않았습니다.|  
|`BitLockerProtectorNotFound`|숫자 암호 키 보호기가 볼륨에 존재하지 않습니다.|  
|`BitLockerKeyInvalid`|제공된 숫자 암호는 볼륨의 잠금을 해제할 수 없습니다.|  
|`BitLockerUnlockVolumeFailed`|볼륨의 잠금을 해제하려고 할 때 알 수 없는 오류가 발생했습니다.|  
|`BitLockerFailed`|BitLocker 작업을 수행하는 동안 알 수 없는 오류가 발생했습니다.|  
|`ManifestNameInvalid`|매니페스트 파일 이름이 잘못되었습니다.|  
|`ManifestNameTooLong`|매니페스트 파일 이름이 너무 깁니다.|  
|`ManifestNotFound`|매니페스트 파일을 찾을 수 없습니다.|  
|`ManifestAccessDenied`|매니페스트 파일에 대한 액세스가 거부되었습니다.|  
|`ManifestCorrupted`|매니페스트 파일이 손상되었습니다(콘텐츠가 해시와 일치하지 않습니다).|  
|`ManifestFormatInvalid`|매니페스트 콘텐츠가 필수 형식에 맞지 않습니다.|  
|`ManifestDriveIdMismatch`|매니페스트 파일의 드라이브 ID가 드라이브에서 읽은 ID와 일치하지 않습니다.|  
|`ReadManifestFailed`|매니페스트를 읽는 동안 디스크 I/O 오류가 발생했습니다.|  
|`BlobListFormatInvalid`|내보내기 Blob 목록 Blob이 필수 형식에 맞지 않습니다.|  
|`BlobRequestForbidden`|저장소 계정에 있는 Blob에 액세스할 수 없습니다. 잘못된 저장소 계정 키 또는 컨테이너 SAS 때문일 수 있습니다.|  
|`InternalError`|드라이브를 처리하는 동안 내부 오류가 발생했습니다.|  
  
## <a name="blob-status-codes"></a>Blob 상태 코드  
다음 테이블에서는 Blob을 처리하는 상태 코드를 나열합니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|`Completed`|Blob이 오류 없이 처리를 완료했습니다.|  
|`CompletedWithErrors`|Blob이 하나 이상의 페이지 범위나 블록, 메타데이터 또는 속성에 발생한 오류 처리를 완료했습니다.|  
|`FileNameInvalid`|파일 이름이 잘못되었습니다.|  
|`FileNameTooLong`|파일 이름이 너무 깁니다.|  
|`FileNotFound`|파일을 찾을 수 없습니다.|  
|`FileAccessDenied`|파일에 대한 액세스가 거부되었습니다.|  
|`BlobRequestFailed`|Blob에 액세스하는 Blob service 요청이 실패했습니다.|  
|`BlobRequestForbidden`|Blob에 액세스하는 Blob service 요청을 사용할 수 없습니다. 잘못된 저장소 계정 키 또는 컨테이너 SAS 때문일 수 있습니다.|  
|`RenameFailed`|Blob(가져오기 작업의 경우) 또는 파일(내보내기 작업의 경우)의 이름을 바꾸지 못했습니다.|  
|`BlobUnexpectedChange`|Blob(내보내기 작업의 경우)에서 예기치 않은 변경이 발생했습니다.|  
|`LeasePresent`|Blob에 임대가 있습니다.|  
|`IOFailed`|Blob을 처리하는 동안 디스크 또는 네트워크 I/O 오류가 발생했습니다.|  
|`Failed`|Blob을 처리하는 동안 알 수 없는 오류가 발생했습니다.|  
  
## <a name="import-disposition-status-codes"></a>처리 상태 코드 가져오기  
다음 테이블에서는 가져오기 처리를 해결하기 위한 상태 코드를 나열합니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|`Created`|Blob를 만들었습니다.|  
|`Renamed`|이름 바꾸기 가져오기 처리별로 Blob 이름이 변경되었습니다. `Blob/BlobPath` 요소는 이름이 변경된 Blob의 URI를 포함합니다.|  
|`Skipped`|`no-overwrite` 가져오기 처리별로 Blob을 건너뛰었습니다.|  
|`Overwritten`|Blob은 `overwrite` 가져오기 처리별로 기존 Blob을 덮어씁니다.|  
|`Cancelled`|이전 오류로 인해 가져오기 처리의 추가 처리가 중지되었습니다.|  
  
## <a name="page-rangeblock-status-codes"></a>페이지 범위/블록 상태 코드  
다음 테이블에서는 페이지 범위 또는 블록을 처리하는 상태 코드를 나열합니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|`Completed`|페이지 범위 또는 블록은 오류 없이 처리를 완료했습니다.|  
|`Committed`|다른 블록이 실패했거나 전체 블록 목록 배치 자체가 실패했기 때문에 블록이 커밋되었지만 전체 블록 목록은 아닙니다.|  
|`Uncommitted`|블록이 업로드되었지만 커밋되지 않습니다.|  
|`Corrupted`|페이지 범위 또는 블록이 손상되었습니다(콘텐츠가 해시와 일치하지 않습니다).|  
|`FileUnexpectedEnd`|예상하지 않게 파일이 끝났습니다.|  
|`BlobUnexpectedEnd`|예상하지 않게 Blob이 끝났습니다.|  
|`BlobRequestFailed`|페이지 범위 또는 블록에 액세스하는 Blob service 요청이 실패했습니다.|  
|`IOFailed`|페이지 범위 또는 블록을 처리하는 동안 디스크 또는 네트워크 I/O 오류가 발생했습니다.|  
|`Failed`|페이지 범위 또는 블록을 처리하는 동안 알 수 없는 오류가 발생했습니다.|  
|`Cancelled`|이전 오류로 인해 페이지 범위 또는 블록의 추가 처리가 중지되었습니다.|  
  
## <a name="metadata-status-codes"></a>메타데이터 상태 코드  
다음 테이블에서는 Blob 메타데이터를 처리하는 상태 코드를 나열합니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|`Completed`|메타데이터가 오류 없이 처리를 완료했습니다.|  
|`FileNameInvalid`|메타데이터 파일 이름이 잘못되었습니다.|  
|`FileNameTooLong`|메타데이터 파일 이름이 너무 깁니다.|  
|`FileNotFound`|메타데이터 파일을 찾을 수 없습니다.|  
|`FileAccessDenied`|메타데이터 파일에 대한 액세스가 거부되었습니다.|  
|`Corrupted`|메타데이터 파일이 손상되었습니다(콘텐츠가 해시와 일치하지 않습니다).|  
|`XmlReadFailed`|메타데이터 콘텐츠가 필수 형식에 맞지 않습니다.|  
|`XmlWriteFailed`|메타데이터 XML을 작성하지 못했습니다.|  
|`BlobRequestFailed`|메타데이터에 액세스하는 Blob service 요청이 실패했습니다.|  
|`IOFailed`|메타데이터를 처리하는 동안 디스크 또는 네트워크 I/O 오류가 발생했습니다.|  
|`Failed`|메타데이터를 처리하는 동안 알 수 없는 오류가 발생했습니다.|  
|`Cancelled`|이전 오류로 인해 메타데이터의 추가 처리가 중지되었습니다.|  
  
## <a name="properties-status-codes"></a>속성 상태 코드  
다음 테이블에서는 Blob 속성을 처리하는 상태 코드를 나열합니다.  
  
|상태 코드|설명|  
|-----------------|-----------------|  
|`Completed`|속성은 오류 없이 처리를 완료했습니다.|  
|`FileNameInvalid`|속성 파일 이름이 잘못되었습니다.|  
|`FileNameTooLong`|속성 파일 이름이 너무 깁니다.|  
|`FileNotFound`|속성 파일을 찾을 수 없습니다.|  
|`FileAccessDenied`|속성 파일에 대한 액세스가 거부되었습니다.|  
|`Corrupted`|속성 파일이 손상되었습니다(콘텐츠가 해시와 일치하지 않습니다).|  
|`XmlReadFailed`|속성 콘텐츠가 필수 형식에 맞지 않습니다.|  
|`XmlWriteFailed`|속성 XML을 작성하지 못했습니다.|  
|`BlobRequestFailed`|속성에 액세스하는 Blob service 요청이 실패했습니다.|  
|`IOFailed`|속성을 처리하는 동안 디스크 또는 네트워크 I/O 오류가 발생했습니다.|  
|`Failed`|속성을 처리하는 동안 알 수 없는 오류가 발생했습니다.|  
|`Cancelled`|이전 오류로 인해 속성의 추가 처리가 중지되었습니다.|  
  
## <a name="sample-logs"></a>샘플 로그  
다음은 자세한 정보 표시 로그의 예입니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
해당 오류 로그는 아래와 같습니다.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 가져오기 작업에 대한 다음 오류 로그에는 가져오기 드라이브에 없는 파일에 대한 오류가 포함됩니다. 이후 구성 요소의 상태는 `Cancelled`입니다.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

내보내기 작업에 대한 다음 오류 로그는 Blob 콘텐츠를 드라이브에 성공적으로 작성했지만 Blob의 속성을 내보내는 동안 오류가 발생했음을 나타냅니다.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>다음 단계
 
* [저장소 Import/Export REST API](/rest/api/storageimportexport/)
