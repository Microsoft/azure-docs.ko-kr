---
title: Azure Import/Export 가져오기 작업을 위한 하드 드라이브 준비 - v1 | Microsoft Docs
description: WAImportExport v1 도구로 하드 드라이브를 준비하여 Azure Import/Export 서비스의 가져오기 작업을 만드는 방법에 대해 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 03b504524b2f489f1ee042c6e825ccffe0a60bb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478473"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>가져오기 작업을 위한 하드 드라이브 준비
가져오기 작업을 위해 하드 드라이브를 하나 이상 준비하려면 다음 단계를 수행합니다.

- Blob service로 가져올 데이터 식별

- Blob service에서 대상 가상 디렉터리 및 Blob 식별

- 필요한 드라이브 수 결정

- 각 하드 드라이브에 데이터 복사

  샘플 워크플로는 [가져오기 작업을 위한 하드 드라이브를 준비하는 샘플 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)(영문)를 참조하세요.

## <a name="identify-the-data-to-be-imported"></a>가져올 데이터 식별
 가져오기 작업을 만드는 첫 번째 단계는 가져올 디렉터리와 파일을 결정하는 것입니다. 디렉터리 목록, 고유한 파일 목록 또는 그 둘의 조합일 수 있습니다. 디렉터리가 포함되는 경우 디렉터리 및 하위 디렉터리의 모든 파일이 가져오기 작업의 일부가 됩니다.

> [!NOTE]
>  부모 디렉터리가 포함될 때 하위 디렉터리가 반복적으로 포함되기 때문에 부모 디렉터리만 지정합니다. 어떤 하위 디렉터리도 지정하지 마세요.
>
>  현재 Microsoft Azure Import/Export 도구에는 하드 드라이브에서 포함할 수 있는 것보다 더 많은 데이터가 디렉터리에 있는 경우 해당 디렉터리를 더 작은 디렉터리로 분할해야 하는 제한 사항이 있습니다. 예를 들어 디렉터리에 2.5TB 데이터가 있고 하드 드라이브 용량이 2TB이면 2.5TB 디렉터리를 더 작은 디렉터리로 분할해야 합니다. 이 제한 사항은 향후 버전의 도구에서 해결할 예정입니다.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Blob Service에서 대상 위치 식별
 가져올 각 디렉터리 또는 파일의 경우 Azure Blob service에서 대상 가상 디렉터리 또는 Blob을 식별해야 합니다. 이러한 대상은 Azure Import/Export 도구에 대한 입력으로 사용합니다. 디렉터리는 "/"(슬래시) 문자로 구분해야 합니다.

 다음 표에서는 Blob 대상의 몇 가지 예제를 보여 줍니다.

|원본 파일 또는 디렉터리|대상 Blob 또는 가상 디렉터리|
|------------------------------|-------------------------------------------|
|H:\Video|https:\//mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https:\//mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https:\//mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https:\//mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>필요한 드라이브 수 결정
 여기서는 다음을 결정해야 합니다.

- 데이터를 저장하는 데 필요한 하드 드라이브의 수

- 각 하드 드라이브에 복사될 디렉터리 및/또는 독립 실행형 파일

  전송할 데이터를 저장하는 데 필요한 하드 드라이브 수를 확인합니다.

## <a name="copy-data-to-your-hard-drive"></a>하드 드라이브에 데이터 복사
 이 섹션에서는 Azure Import/Export 도구를 호출하여 데이터를 하나 이상의 하드 드라이브에 복사하는 방법에 대해 설명합니다. Azure Import/Export 도구를 호출할 때마다 새 *복사 세션*을 만듭니다. 데이터를 복사할 각 드라이브에 대해 복사 세션을 하나 이상 만듭니다. 어떤 경우에는 모든 데이터를 단일 드라이브에 복사하기 위해 둘 이상의 복사 세션이 필요할 수 있습니다. 여러 개의 복사 세션이 필요할 수 있는 몇 가지 이유는 다음과 같습니다.

-   복사할 각 드라이브마다 별도의 복사 세션을 만들어야 합니다.

-   복사 세션에서 단일 디렉터리 또는 단일 Blob을 드라이브에 복사할 수 있습니다. 여러 디렉터리, 여러 Blob 또는 둘 모두의 조합을 복사하는 경우 여러 복사 세션을 만들어야 합니다.

-   가져오기 작업의 일부로 가져온 Blob에 설정할 속성과 메타데이터를 지정할 수 있습니다. 복사 세션에 지정하는 속성이나 메타데이터는 해당 복사 세션에서 지정한 모든 Blob에 적용됩니다. 일부 Blob에 다른 속성이나 메타데이터를 지정하려면 별도의 복사 세션을 만들어야 합니다. 자세한 내용은 [가져오기 프로세스 중에 속성 및 메타데이터 설정](storage-import-export-tool-setting-properties-metadata-import-v1.md)을 참조하세요.

> [!NOTE]
>  [Azure Import/Export 도구 설치](storage-import-export-tool-setup-v1.md)에서 설명하는 요구 사항을 충족하는 컴퓨터가 여러 개 있는 경우 각 컴퓨터에서 이 도구의 인스턴스를 실행하여 여러 하드 드라이브에 데이터를 동시에 복사할 수 있습니다.

 Azure Import/Export 도구를 사용하여 준비하는 각 하드 드라이브에 대해 이 도구는 저널 파일을 하나씩 만듭니다. 가져오기 작업을 만들려면 모든 드라이브의 저널 파일이 필요합니다. 도구가 중단되는 경우 저널 파일을 사용하여 드라이브 준비를 다시 시작할 수도 있습니다.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>가져오기 작업을 위한 Azure Import/Export 도구 구문
 가져오기 작업을 위해 드라이브를 준비하려면 **PrepImport** 명령을 사용하여 Azure Import/Export 도구를 호출합니다. 포함할 매개 변수는 첫 번째 복사 세션인지 또는 후속 복사 세션인지의 여부에 따라 다릅니다.

 드라이브의 첫 번째 복사 세션에는 저장소 계정 키, 대상 드라이브 문자, 드라이브 포맷 여부, 드라이브 암호화 여부 및 암호화하는 경우 사용할 BitLocker 키 및 로그 디렉터리를 지정하는 몇 가지 추가 매개 변수가 필요합니다. 다음은 디렉터리 또는 단일 파일을 복사하기 위한 초기 복사 세션의 구문입니다.

 **단일 디렉터리를 복사하는 첫 번째 복사 세션**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **단일 파일을 복사하는 첫 번째 복사 세션**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 후속 복사 세션에서는 초기 매개 변수를 지정할 필요가 없습니다. 다음은 디렉터리 또는 단일 파일을 복사하기 위한 후속 복사 세션의 구문입니다.

 **단일 디렉터리를 복사하는 후속 복사 세션**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **단일 파일을 복사하는 후속 복사 세션**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>하드 드라이브의 첫 번째 복사 세션에 대한 매개 변수
 Azure Import/Export 도구를 실행하여 파일을 하드 드라이브에 복사할 때마다 이 도구에서 복사 세션을 만듭니다. 각 복사 세션마다 단일 디렉터리 또는 단일 파일을 하드 드라이브에 복사합니다. 복사 세션의 상태는 저널 파일에 기록됩니다. 복사 세션이 중단된 경우(예: 시스템 전원 손실로 인해) 도구를 다시 실행하고 명령줄에서 저널 파일을 지정하여 해당 세션을 다시 시작할 수 있습니다.

> [!WARNING]
>  첫 번째 복사 세션에 **/format** 매개 변수를 지정하면 드라이브가 포맷되고 드라이브의 모든 데이터가 지워집니다. 복사 세션에만 빈 드라이브를 사용하는 것이 좋습니다.

 각 드라이브의 첫 번째 복사 세션에 사용되는 명령에는 후속 복사 세션의 명령과 다른 매개 변수가 필요합니다. 다음 표에서는 첫 번째 복사 세션에 사용할 수 있는 추가 매개 변수를 나열하고 있습니다.

|명령줄 매개 변수|설명|
|-----------------------------|-----------------|
|**/sk:**<StorageAccountKey\>|`Optional.` 데이터를 가져올 저장소 계정의 저장소 계정 키입니다. 명령에 **/sk:**<StorageAccountKey\> 또는 **/csas:**<ContainerSas\> 중 하나를 포함해야 합니다.|
|**/csas:**<ContainerSas\>|`Optional`. 저장소 계정에 데이터를 가져오는 데 사용할 컨테이너 SAS입니다. 명령에 **/sk:**<StorageAccountKey\> 또는 **/csas:**<ContainerSas\> 중 하나를 포함해야 합니다.<br /><br /> 이 매개 변수의 값은 컨테이너 이름, 물음표(?) 및 SAS 토큰으로 시작해야 합니다. 예를 들면 다음과 같습니다.<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> URL 또는 저장된 액세스 정책에 지정되어 있는지 여부에 관계없이 권한은 가져오기 작업의 경우 읽기(Read), 쓰기(Write) 및 삭제(Delete), 내보내기 작업의 경우 읽기, 쓰기 및 목록 작성(List)을 포함해야 합니다.<br /><br /> 이 매개 변수를 지정하면 가져오거나 내보낼 모든 Blob은 공유 액세스 서명에 지정된 컨테이너 내에 있어야 합니다.|
|**/t:**<TargetDriveLetter\>|`Required.` 현재 복사 세션의 대상 하드 드라이브 문자입니다(후행 콜론 없음).|
|**/format**|`Optional.` 드라이브를 포맷해야 하는 경우 이 매개 변수를 지정합니다. 그렇지 않으면 생략합니다. 이 도구는 드라이브를 포맷하기 전에 콘솔에서 확인 메시지를 표시합니다. 확인을 하지 않으려면 /silentmode 매개 변수를 지정합니다.|
|**/silentmode**|`Optional.` 대상 드라이브에 대 한 확인 하지 않으려면이 매개 변수를 지정 합니다.|
|**/encrypt**|`Optional.` 아직 BitLocker로 드라이브를 암호화하지 않았고 도구를 통해 암호화해야 하는 경우 이 매개 변수를 지정합니다. 이미 BitLocker로 드라이브를 암호화했으면 이 매개 변수를 생략하고 `/bk` 매개 변수를 지정하여 기존 BitLocker 키를 제공합니다.<br /><br /> `/format` 매개 변수를 지정하는 경우 `/encrypt` 매개 변수도 지정해야 합니다.|
|**/bk:**<BitLockerKey\>|`Optional.` `/encrypt`를 지정하는 경우 이 매개 변수를 생략합니다. `/encrypt`를 생략하면 이미 BitLocker로 드라이브를 암호화했어야 합니다. 이 매개 변수를 사용하여 BitLocker 키를 지정합니다. 가져오기 작업을 위한 모든 하드 드라이브에는 BitLocker 암호화가 필요합니다.|
|**/logdir:**<LogDirectory\>|`Optional.` 로그 디렉터리는 자세한 로그와 임시 매니페스트 파일을 저장하는 데 사용할 디렉터리를 지정합니다. 지정하지 않으면 현재 디렉터리가 로그 디렉터리로 사용됩니다.|

### <a name="parameters-required-for-all-copy-sessions"></a>모든 복사 세션에 필요한 매개 변수
 저널 파일에는 하드 드라이브의 모든 복사 세션에 대한 상태가 포함됩니다. 가져오기 작업을 만드는 데 필요한 정보도 포함됩니다. Azure Import/Export 도구를 실행할 때 항상 저널 파일과 복사 세션 ID를 지정해야 합니다.

|||
|-|-|
|명령줄 매개 변수|설명|
|**/j:**<JournalFile\>|`Required.` 저널 파일의 경로입니다. 각 드라이브에는 정확히 하나의 저널 파일이 있어야 합니다. 저널 파일은 대상 드라이브에 있지 않아야 합니다. 저널 파일 확장명은 `.jrn`입니다.|
|**/id:**<SessionId\>|`Required.` 세션 ID는 복사 세션을 식별합니다. 중단된 복사 세션의 정확한 복구를 위해 사용됩니다. 복사 세션에서 복사된 파일은 대상 드라이브의 세션 ID의 이름을 딴 디렉터리에 저장됩니다.|

### <a name="parameters-for-copying-a-single-directory"></a>단일 디렉터리 복사 매개 변수
 단일 디렉터리를 복사할 때 적용되는 필수 및 선택적 매개 변수는 다음과 같습니다.

|명령줄 매개 변수|설명|
|----------------------------|-----------------|
|**/srcdir:**<SourceDirectory\>|`Required.` 대상 드라이브에 복사할 파일이 들어 있는 원본 디렉터리입니다. 디렉터리 경로는 절대 경로(상대 경로 아님)이어야 합니다.|
|**/dstdir:**<DestinationBlobVirtualDirectory\>|`Required.` Microsoft Azure 저장소 계정의 대상 가상 디렉터리에 대한 경로입니다. 가상 디렉터리가 이미 있거나 없을 수도 있습니다.<br /><br /> `music/70s/`와 같이 컨테이너 또는 Blob 접두사를 지정할 수 있습니다. 대상 디렉터리는 컨테이너 이름과 "/"(슬래시)로 시작해야 하며 선택적으로 "/"로 끝나는 가상 Blob 디렉터리를 포함할 수도 있습니다.<br /><br /> 대상 컨테이너가 루트 컨테이너인 경우 슬래시를 포함하여 `$root/`로 루트 컨테이너를 명시적으로 지정해야 합니다. 루트 컨테이너 아래의 Blob에는 이름에 "/"를 포함할 수 없으므로 대상 디렉터리가 루트 컨테이너인 경우 원본 디렉터리의 모든 하위 디렉터리는 복사되지 않습니다.<br /><br /> 대상 가상 디렉터리 또는 BLOB를 지정할 때는 유효한 컨테이너 이름을 사용해야 합니다. 컨테이너 이름은 소문자여야 합니다. 컨테이너 명명 규칙에 대해서는 [컨테이너, Blob, 메타데이터의 명명 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)(영문)를 참조하세요.|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` 지정된 주소의 Blob이 이미 있는 경우 동작을 지정합니다. 이 매개 변수의 유효한 값은 `rename`, `no-overwrite` 및 `overwrite`입니다. 이러한 값은 대/소문자를 구분합니다. 값을 지정하지 않을 경우 기본값은 `rename`입니다.<br /><br /> 이 매개 변수에 지정된 값은 `/srcdir` 매개 변수로 지정된 디렉터리의 모든 파일에 영향을 줍니다.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` 대상 Blob의 Blob 유형을 지정합니다. 유효한 값은 `BlockBlob` 및 `PageBlob`입니다. 이러한 값은 대/소문자를 구분합니다. 값을 지정하지 않을 경우 기본값은 `BlockBlob`입니다.<br /><br /> 대부분의 경우 `BlockBlob`이 권장됩니다. `PageBlob`을 지정하면 디렉터리에 있는 각 파일의 길이는 페이지 Blob의 페이지 크기인 512의 배수여야 합니다.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` 대상 Blob에 대한 속성 파일의 경로입니다. 자세한 내용은 [Import/Export 서비스의 메타데이터 및 속성 파일 형식](../storage-import-export-file-format-metadata-and-properties.md)을 참조하세요.|
|**/MetadataFile:**<MetadataFile\>|`Optional.` 대상 Blob에 대한 메타데이터 파일의 경로입니다. 자세한 내용은 [Import/Export 서비스의 메타데이터 및 속성 파일 형식](../storage-import-export-file-format-metadata-and-properties.md)을 참조하세요.|

### <a name="parameters-for-copying-a-single-file"></a>단일 파일 복사 매개 변수
 단일 파일을 복사할 때 적용되는 필수 및 선택적 매개 변수는 다음과 같습니다.

|명령줄 매개 변수|설명|
|----------------------------|-----------------|
|**/srcfile:**<SourceFile\>|`Required.` 복사할 파일의 전체 경로입니다. 디렉터리 경로는 절대 경로(상대 경로 아님)이어야 합니다.|
|**/dstblob:**<DestinationBlobPath\>|`Required.` Microsoft Azure 저장소 계정의 대상 Blob에 대한 경로입니다. Blob이 이미 있거나 없을 수도 있습니다.<br /><br /> 컨테이너 이름으로 시작하는 Blob 이름을 지정합니다. Blob 이름은 "/" 또는 저장소 계정 이름으로 시작할 수 없습니다. Blob 명명 규칙에 대해서는 [컨테이너, Blob, 메타데이터의 명명 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)(영문)를 참조하세요.<br /><br /> 대상 컨테이너가 루트 컨테이너인 경우 `$root/sample.txt`와 같이 `$root`를 컨테이너로 명시적으로 지정해야 합니다. 루트 컨테이너 아래의 Blob은 이름에 "/"를 포함할 수 없습니다.|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.` 지정된 주소의 Blob이 이미 있는 경우 동작을 지정합니다. 이 매개 변수의 유효한 값은 `rename`, `no-overwrite` 및 `overwrite`입니다. 이러한 값은 대/소문자를 구분합니다. 값을 지정하지 않을 경우 기본값은 `rename`입니다.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` 대상 Blob의 Blob 유형을 지정합니다. 유효한 값은 `BlockBlob` 및 `PageBlob`입니다. 이러한 값은 대/소문자를 구분합니다. 값을 지정하지 않을 경우 기본값은 `BlockBlob`입니다.<br /><br /> 대부분의 경우 `BlockBlob`이 권장됩니다. `PageBlob`을 지정하면 디렉터리에 있는 각 파일의 길이는 페이지 Blob의 페이지 크기인 512의 배수여야 합니다.|
|**/PropertyFile:**<PropertyFile\>|`Optional.` 대상 Blob에 대한 속성 파일의 경로입니다. 자세한 내용은 [Import/Export 서비스의 메타데이터 및 속성 파일 형식](../storage-import-export-file-format-metadata-and-properties.md)을 참조하세요.|
|**/MetadataFile:**<MetadataFile\>|`Optional.` 대상 Blob에 대한 메타데이터 파일의 경로입니다. 자세한 내용은 [Import/Export 서비스의 메타데이터 및 속성 파일 형식](../storage-import-export-file-format-metadata-and-properties.md)을 참조하세요.|

### <a name="resuming-an-interrupted-copy-session"></a>중단된 복사 세션 다시 시작
 어떤 이유로 복사 세션이 중단된 경우 지정된 저널 파일 만으로 도구를 실행하여 복사 세션을 다시 시작할 수 있습니다.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 마지막 복사 세션만 비정상적으로 종료되면 다시 시작할 수 있습니다.

> [!IMPORTANT]
>  복사 세션을 다시 시작할 때는 파일을 추가하거나 제거하여 원본 데이터 파일과 디렉터리를 수정하지 마세요.

### <a name="aborting-an-interrupted-copy-session"></a>중단된 복사 세션 중단
 복사 세션이 중단되어 다시 시작할 수 없는 경우(예: 원본 디렉터리에 액세스할 수 없다고 판명된 경우) 롤백하여 새 복사 세션을 시작할 수 있도록 현재 세션을 중단해야 합니다.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 마지막 복사 세션만 비정상적으로 종료되면 이 세션을 중단할 수 있습니다. 드라이브의 첫 번째 복사 세션은 중단할 수 없습니다. 대신 새 저널 파일로 복사 세션을 다시 시작해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Import/Export 도구 설정](storage-import-export-tool-setup-v1.md)
* [가져오기 프로세스 중에 속성 및 메타데이터 설정](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [가져오기 작업을 위한 하드 드라이브를 준비하는 샘플 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [자주 사용 되는 명령에 대한 빠른 참조](storage-import-export-tool-quick-reference-v1.md) 
* [복사 로그 파일을 사용하여 작업 상태 검토](storage-import-export-tool-reviewing-job-status-v1.md)
* [가져오기 작업 복구](storage-import-export-tool-repairing-an-import-job-v1.md)
* [내보내기 작업 복구](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Azure Import/Export 도구 문제 해결](storage-import-export-tool-troubleshooting-v1.md)
