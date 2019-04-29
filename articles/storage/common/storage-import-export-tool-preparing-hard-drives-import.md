---
title: Azure Import/Export 가져오기 작업을 위한 하드 드라이브 준비 | Microsoft Docs
description: WAImportExport 도구로 하드 드라이브를 준비하여 Azure Import/Export 서비스의 가져오기 작업을 만드는 방법에 대해 알아봅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 777e0aac46dbffb1e491874b5889667a888aadf5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478526"
---
# <a name="preparing-hard-drives-for-an-import-job"></a>가져오기 작업을 위한 하드 드라이브 준비

WAImportExport 도구는 [Microsoft Azure Import/Export 서비스](../storage-import-export-service.md)에서 사용할 수 있는 드라이브 준비 및 복구 도구입니다. 이 도구를 사용하여 Azure 데이터 센터에 운송할 하드 드라이브에 데이터를 복사할 수 있습니다. 가져오기 작업이 완료된 후에는 이 도구를 사용하여 손상되거나 누락되거나 다른 Blob과 충돌한 Blob을 복구할 수 있습니다. 완료된 내보내기 작업에서 드라이브를 받은 후에는 이 도구를 사용하여 드라이브에서 손상되거나 누락된 파일을 복구할 수 있습니다. 이 문서에서는 이 도구를 사용하는 방법을 살펴보겠습니다.

## <a name="prerequisites"></a>필수 조건

### <a name="requirements-for-waimportexportexe"></a>WAImportExport.exe에 대한 요구 사항

- **컴퓨터 구성**
  - Windows 7, Windows Server 2008 R2 또는 최신 Windows 운영 체제
  - .NET Framework 4가 설치되어 있어야 합니다. 참조 [FAQ](#faq) .NET Framework를 컴퓨터에 설치 되어 있는지 확인 하는 방법에 있습니다.
- **Storage 계정 키** - Storage 계정의 계정 키가 하나 이상 필요합니다.

### <a name="preparing-disk-for-import-job"></a>가져오기 작업을 위한 디스크 준비

- **BitLocker -** WAImportExport 도구를 실행하는 컴퓨터에서 BitLocker를 사용하도록 설정해야 합니다. BitLocker를 사용하도록 설정하는 방법은 [FAQ](#faq)를 참조하세요.
- **디스크** - WAImportExport 도구를 실행하는 컴퓨터에서 액세스할 수 있습니다. 디스크 사양은 [FAQ](#faq)를 참조하세요.
- **원본 파일** - 네트워크 공유 드라이브 또는 로컬 하드 드라이브 중 어느 것에 있든지 간에 가져올 파일은 복사할 컴퓨터에서 액세스할 수 있어야 합니다.

### <a name="repairing-a-partially-failed-import-job"></a>부분적으로 실패한 가져오기 작업 복구

- **복사 로그 파일** - Azure Import/Export 서비스에서 Storage 계정과 디스크 간에 데이터를 복사할 때 생성됩니다. 대상 저장소 계정에 있습니다.

### <a name="repairing-a-partially-failed-export-job"></a>부분적으로 실패한 내보내기 작업 복구

- **복사 로그 파일** - Azure Import/Export 서비스에서 Storage 계정과 디스크 간에 데이터를 복사할 때 생성됩니다. 원본 저장소 계정에 있습니다.
- **매니페스트 파일** - [선택 사항] Microsoft에서 반환하여 내보낸 드라이브에 있습니다.

## <a name="download-and-install-waimportexport"></a>WAImportExport 다운로드 및 설치

[최신 WAImportExport.exe 버전](https://www.microsoft.com/download/details.aspx?id=55280)을 다운로드합니다. 컴퓨터의 디렉터리에 압축된 내용을 추출합니다.

다음 작업은 CSV 파일을 만드는 것입니다.

## <a name="prepare-the-dataset-csv-file"></a>데이터 세트 CSV 파일 준비

### <a name="what-is-dataset-csv"></a>데이터 세트 CSV 정의

데이터 세트 CSV 파일은 /dataset 플래그의 값으로 대상 드라이브에 복사할 디렉터리 목록 및/또는 파일 목록을 포함하고 있는 CSV 파일입니다. 가져오기 작업을 만드는 첫 번째 단계는 가져올 디렉터리와 파일을 결정하는 것입니다. 디렉터리 목록, 고유한 파일 목록 또는 그 둘의 조합일 수 있습니다. 디렉터리가 포함되는 경우 디렉터리 및 하위 디렉터리의 모든 파일이 가져오기 작업의 일부가 됩니다.

가져올 각 디렉터리 또는 파일의 경우 Azure Blob service에서 대상 가상 디렉터리 또는 Blob을 식별해야 합니다. 이러한 대상을 WAImportExport 도구의 입력으로 사용합니다. 디렉터리는 "/"(슬래시) 문자로 구분해야 합니다.

다음 표에서는 Blob 대상의 몇 가지 예제를 보여 줍니다.

| 원본 파일 또는 디렉터리 | 대상 Blob 또는 가상 디렉터리 |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>샘플 dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>데이터 세트 CSV 파일 필드

| 필드 | 설명 |
| --- | --- |
| BasePath | **[필수]**<br/>이 매개 변수 값은 데이터를 가져올 수 있는 원본의 위치를 나타냅니다. 도구는 이 경로 아래에 있는 모든 데이터를 재귀적으로 복사합니다.<br><br/>**허용되는 값**: 로컬 컴퓨터 또는 유효한 공유 경로의 유효한 경로여야 하며 사용자가 액세스할 수 있어야 합니다. 디렉터리 경로는 절대 경로(상대 경로 아님)이어야 합니다. 경로가 "\\"로 끝나는 경우 디렉터리를 나타내고 "\\"로 끝나지 않는 경로는 파일을 나타냅니다.<br/>이 필드에는 정규식을 사용할 수 없습니다. 경로에 공백이 있으면 "" 안에 경로를 넣습니다.<br><br/>**예제**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[필수]**<br/> Microsoft Azure 저장소 계정의 대상 가상 디렉터리에 대한 경로입니다. 가상 디렉터리가 이미 있거나 없을 수도 있습니다. 없는 경우 Import/Export 서비스에서 하나의 가상 디렉터리를 만듭니다.<br/><br/>대상 가상 디렉터리 또는 BLOB를 지정할 때는 유효한 컨테이너 이름을 사용해야 합니다. 컨테이너 이름은 소문자여야 합니다. 컨테이너 명명 규칙에 대해서는 [컨테이너, Blob, 메타데이터의 명명 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)(영문)를 참조하세요. 루트를 지정하는 경우에만 소스의 디렉터리 구조가 대상 Blob 컨테이너에 복제됩니다. 소스의 디렉터리 구조가 아닌 다른 디렉터리 구조를 사용하는 경우 CSV에서 여러 행을 매핑합니다.<br/><br/>music/70s/와 같이 컨테이너 또는 Blob 접두사를 지정할 수 있습니다. 대상 디렉터리는 컨테이너 이름과 "/"(슬래시)로 시작해야 하며 선택적으로 "/"로 끝나는 가상 Blob 디렉터리를 포함할 수도 있습니다.<br/><br/>대상 컨테이너가 루트 컨테이너인 경우 슬래시를 포함하여 $root/로 루트 컨테이너를 명시적으로 지정해야 합니다. 루트 컨테이너 아래의 Blob에는 이름에 "/"를 포함할 수 없으므로 대상 디렉터리가 루트 컨테이너인 경우 원본 디렉터리의 모든 하위 디렉터리는 복사되지 않습니다.<br/><br/>**예제**<br/>대상 Blob 경로가 https://mystorageaccount.blob.core.windows.net/video이면 이 필드의 값은 video/일 수 있습니다.  |
| BlobType | **[선택]** block &#124; page<br/>현재 Import/Export 서비스는 두 가지 종류의 Blob을 지원합니다. 페이지 Blob과 블록 Blob은 기본적으로 모든 파일을 블록 Blob으로 가져옵니다. 그리고 \*.vhd\* 및 .vhdx는 페이지 Blob으로 가져오게 됩니다. 블록 Blob 및 페이지 Blob에 허용되는 크기는 제한됩니다. 자세한 내용은 [저장소 확장성 목표](storage-scalability-targets.md)를 참조하세요.  |
| Disposition | **[선택]** rename &#124; no-overwrite &#124; overwrite <br/> 이 필드는 가져오기 중, 즉 디스크에서 저장소 계정으로 데이터를 업로드할 때의 복사 동작을 지정합니다. 디스크에서 저장소 계정으로 데이터를 업로드할 때 사용 가능한 옵션은 rename&#124;overwrite&#124;no-overwrite입니다. 아무 것도 지정하지 않는 경우 기본값으로 "rename"을 사용합니다. <br/><br/>**Rename**: 이름이 같은 개체가 있으면 대상에 복사본을 만듭니다.<br/>overwrite: 파일을 새 파일로 덮어씁니다. 마지막으로 수정된 파일이 우선합니다.<br/>**No-overwrite**: 이미 파일이 있는 경우 파일 쓰기를 건너뜁니다.|
| MetadataFile | **[선택]** <br/>이 필드의 값은 개체의 메타데이터를 보존해야 하거나 사용자 지정 메타데이터를 제공해야 하는 경우 제공할 수 있는 메타데이터 파일입니다. 대상 Blob에 대한 메타데이터 파일의 경로입니다. 자세한 내용은 [Import/Export 서비스의 메타데이터 및 속성 파일 형식](../storage-import-export-file-format-metadata-and-properties.md)을 참조하세요. |
| PropertiesFile | **[선택]** <br/>대상 Blob에 대한 속성 파일의 경로입니다. 자세한 내용은 [Import/Export 서비스의 메타데이터 및 속성 파일 형식](../storage-import-export-file-format-metadata-and-properties.md)을 참조하세요. |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>InitialDriveSet 또는 AdditionalDriveSet CSV 파일 준비

### <a name="what-is-driveset-csv"></a>드라이브 집합 CSV 정의

/InitialDriveSet 또는 /AdditionalDriveSet 플래그의 값은 도구에서 준비할 디스크 목록을 정확하게 선택할 수 있도록 드라이브 문자가 매핑된 디스크 목록을 포함하고 있는 CSV 파일입니다. 데이터 크기가 단일 디스크 크기보다 큰 경우 WAImportExport 도구는 이 CSV 파일에 참여된 여러 디스크에 데이터를 최적화된 방식으로 분산합니다.

단일 세션에서 데이터를 쓸 수 있는 디스크의 수는 제한되지 않습니다. 이 도구는 디스크 크기와 폴더 크기에 따라 데이터를 분산합니다. 개체 크기에 가장 최적화된 디스크를 선택합니다. 저장소 계정에 업로드된 데이터는 데이터 세트 파일에 지정한 디렉터리 구조로 다시 정리됩니다. 드라이브 집합 CSV를 만들려면 다음 단계를 수행합니다.

### <a name="create-basic-volume-and-assign-drive-letter"></a>기본 볼륨 만들기 및 드라이브 문자 할당

기본 볼륨을 만들고 드라이브 문자를 할당하려면 [디스크 관리 개요](https://technet.microsoft.com/library/cc754936.aspx)에서 제공하는 "간단한 파티션 만들기" 지침을 따릅니다.

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>샘플 InitialDriveSet 및 AdditionalDriveSet CSV 파일

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>드라이브 집합 CSV 파일 필드

| 필드 | 값 |
| --- | --- |
| DriveLetter | **[필수]**<br/> 대상으로 도구에 제공되는 각 드라이브에는 NTFS 단순 볼륨과 여기에 할당된 드라이브 문자가 있어야 합니다.<br/> <br/>**예제**: R 또는 r |
| FormatOption | **[필수]** Format &#124; AlreadyFormatted<br/><br/> **Format**: 이 값을 지정하면 디스크의 모든 데이터가 포맷됩니다. <br/>**AlreadyFormatted**: 이 값을 지정하면 도구에서 포맷을 건너뜁니다. |
| SilentOrPromptOnFormat | **[필수]** SilentMode &#124; PromptOnFormat<br/><br/>**SilentMode**: 이 값을 제공하면 사용자가 자동 모드에서 도구를 실행할 수 있습니다. <br/>**PromptOnFormat**: 도구에서 해당 작업이 실제로 모든 형식을 대상으로 하는지 여부를 확인하라는 메시지를 사용자에게 표시합니다.<br/><br/>설정되지 않은 경우 명령이 중단되고 오류 메시지가 표시됩니다. “SilentOrPromptOnFormat에 대한 잘못된 값: none” |
| 암호화 | **[필수]** Encrypt &#124; AlreadyEncrypted<br/> 이 필드의 값은 암호화할 디스크와 암호화하지 않을 디스크를 결정합니다. <br/><br/>**Encrypt**: 도구에서 드라이브를 포맷합니다. "FormatOption" 필드의 값이 "Format"인 경우 이 값은 "Encrypt"여야 합니다. 이 경우 "AlreadyEncrypted"를 지정하면 "Format이 지정되면 Encrypt도 지정해야 합니다,"라는 오류가 발생합니다.<br/>**AlreadyEncrypted**: 도구에서 “ExistingBitLockerKey” 필드에 제공된 BitLockerKey를 사용하여 드라이브 암호를 해독합니다. "FormatOption" 필드의 값이 "AlreadyFormatted"인 경우 이 값은 "Encrypt" 또는 "AlreadyEncrypted"입니다. |
| ExistingBitLockerKey | **[필수]** "Encryption" 필드의 값이 "AlreadyEncrypted"인 경우<br/> 이 필드의 값은 특정 디스크와 연결되는 BitLocker 키입니다. <br/><br/>"Encryption" 필드의 값이 "Encrypt"인 경우 이 필드는 비워 두어야 합니다.  이 경우 BitLocker 키가 지정되면 “BitLocker 키는 지정할 수 없습니다.”라는 오류가 발생합니다.<br/>  **예제**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>가져오기 작업을 위한 디스크 준비

가져오기 작업을 위해 드라이브를 준비하려면 **PrepImport** 명령을 사용하여 WAImportExport 도구를 호출합니다. 포함할 매개 변수는 첫 번째 복사 세션인지 또는 후속 복사 세션인지의 여부에 따라 다릅니다.

### <a name="first-session"></a>첫 번째 세션

CSV 파일에 지정된 내용에 따라 단일/다중 디렉터리를 단일/다중 디스크에 복사하기 위한 첫 번째 복사 세션입니다. 첫 번째 복사 세션에서 새 복사 세션으로 디렉터리 및/또는 파일을 복사하기 위한 WAImportExport 도구의 PrepImport 명령은 다음과 같습니다.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] /DataSet:<dataset.csv>
```

**예제:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>후속 세션에서 데이터 추가

후속 복사 세션에서는 초기 매개 변수를 지정할 필요가 없습니다. 이전 세션에서 남겨진 위치를 기억하기 위해 도구에서 동일한 저널 파일을 사용해야 합니다. 복사 세션의 상태는 저널 파일에 기록됩니다. 추가 디렉터리 및/또는 파일을 복사하는 후속 복사 세션의 구문은 다음과 같습니다.

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**예제:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>마지막 세션에 드라이브 추가

데이터가 InitialDriveset에 지정한 드라이브에 맞지 않으면 도구를 사용하여 동일한 복사 세션에 추가 드라이브를 추가할 수 있습니다. 

> [!NOTE]
> 세션 ID는 이전 세션 ID와 일치해야 합니다. 저널 파일은 이전 세션에서 지정한 파일과 일치해야 합니다.
> 
> ```
> WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
> ```

**예제:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>마지막 세션 중단

복사 세션이 중단되어 다시 시작할 수 없는 경우(예: 원본 디렉터리에 액세스할 수 없다고 판명된 경우) 롤백하여 새 복사 세션을 시작할 수 있도록 현재 세션을 중단해야 합니다.

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**예제:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

마지막 복사 세션만 비정상적으로 종료되면 이 세션을 중단할 수 있습니다. 드라이브의 첫 번째 복사 세션은 중단할 수 없습니다. 대신 새 저널 파일로 복사 세션을 다시 시작해야 합니다.

### <a name="resume-a-latest-interrupted-session"></a>중단된 마지막 세션 다시 시작

어떤 이유로 복사 세션이 중단된 경우 지정된 저널 파일 만으로 도구를 실행하여 복사 세션을 다시 시작할 수 있습니다.

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**예제:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> 복사 세션을 다시 시작할 때는 파일을 추가하거나 제거하여 원본 데이터 파일과 디렉터리를 수정하지 마세요.

## <a name="waimportexport-parameters"></a>WAImportExport 매개 변수

| 매개 변수 | 설명 |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **필수**<br/> 저널 파일의 경로입니다. 저널 파일은 일단의 드라이브를 추적하고 이러한 드라이브를 준비하는 진행 상태를 기록합니다. 저널 파일은 항상 지정해야 합니다.  |
|     /logdir:&lt;LogDirectory&gt;  | **옵션**. 로그 디렉터리입니다.<br/> 일부 임시 파일뿐만 아니라 자세한 로그 파일도 이 디렉터리에 기록됩니다. 지정하지 않으면 현재 디렉터리가 로그 디렉터리로 사용됩니다. 로그 디렉터리는 동일한 저널 파일에 대해 한 번만 지정할 수 있습니다.  |
|     /id:&lt;SessionId&gt;  | **필수**<br/> 세션 ID는 복사 세션을 식별하는 데 사용됩니다. 중단된 복사 세션의 정확한 복구를 위해 사용됩니다.  |
|     /ResumeSession  | 선택 사항입니다. 마지막 복사 세션이 비정상적으로 종료된 경우 이 매개 변수를 지정하여 해당 세션을 다시 시작할 수 있습니다.   |
|     /AbortSession  | 선택 사항입니다. 마지막 복사 세션이 비정상적으로 종료된 경우 이 매개 변수를 지정하여 해당 세션을 중단할 수 있습니다.  |
|     /sn:&lt;StorageAccountName&gt;  | **필수**<br/> RepairImport 및 RepairExport에만 적용됩니다. 저장소 계정 이름입니다.  |
|     /sk:&lt;StorageAccountKey&gt;  | **필수**<br/> 저장소 계정 키입니다. |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **필수** 첫 번째 복사 세션을 실행할 때<br/> 준비할 드라이브 목록을 포함하고 있는 CSV 파일입니다.  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **필수입니다**. 현재 복사 세션에 드라이브를 추가할 때. <br/> 추가할 드라이브 목록을 포함하고 있는 CSV 파일입니다.  |
|      /r:&lt;RepairFile&gt; | **필수** RepairImport 및 RepairExport에만 적용됩니다.<br/> 복구 진행 상태를 추적하기 위한 파일의 경로입니다. 각 드라이브에는 하나의 복구 파일만 있어야 합니다.  |
|     /d:&lt;TargetDirectories&gt; | **필수입니다**. RepairImport 및 RepairExport에만 적용됩니다. RepairImport의 경우 세미콜론으로 하나 이상 구분된 디렉터리를 복구합니다. RepairExport의 경우 디렉터리 하나만 복구합니다(예: 드라이브의 루트 디렉터리).  |
|     /CopyLogFile:&lt;DriveCopyLogFile&gt; | **필수** RepairImport 및 RepairExport에만 적용됩니다. 드라이브 복사 로그 파일(자세한 정보 또는 오류)의 경로입니다.  |
|     /ManifestFile:&lt;DriveManifestFile&gt; | **필수** RepairExport에만 적용됩니다.<br/> 드라이브 매니페스트 파일의 경로입니다.  |
|     /PathMapFile:&lt;DrivePathMapFile&gt; | **옵션**. RepairImport에만 적용됩니다.<br/> 실제 파일의 위치(탭 구분 형식)와 드라이브 루트에 상대적인 파일 경로의 매핑을 포함하고 있는 파일의 경로입니다. 처음에 지정되면 빈 대상이 포함된 파일의 경로로 채워집니다. 즉 대상이 TargetDirectories에 없거나 액세스가 거부되었거나 잘못된 이름으로 되어 있거나 여러 디렉터리에 있음을 의미합니다. 정확한 대상 경로를 포함하도록 경로 매핑 파일을 수동으로 편집하고 도구에서 파일 경로를 정확하게 해석하도록 다시 지정할 수 있습니다.  |
|     /ExportBlobListFile:&lt;ExportBlobListFile&gt; | **필수입니다**. PreviewExport에만 적용됩니다.<br/> 내보낼 Blob에 대한 Blob 경로 또는 Blob 경로 접두사 목록을 포함하고 있는 XML 파일의 경로입니다. 파일 형식은 Import/Export 서비스 REST API의 작업 배치(Put Job) 작업에서 사용되는 Blob 목록 Blob 형식과 동일합니다.  |
|     /DriveSize:&lt;DriveSize&gt; | **필수입니다**. PreviewExport에만 적용됩니다.<br/>  내보내기에 사용할 드라이브 크기입니다. 예: 500GB, 1.5TB 참고: 1GB = 1,000,000,000바이트 1TB = 1,000,000,000,000바이트  |
|     /DataSet:&lt;dataset.csv&gt; | **필수**<br/> 대상 드라이브에 복사할 디렉터리 목록 및/또는 파일 목록을 포함하고 있는 CSV 파일입니다.  |
|     /silentmode  | **옵션**.<br/> 지정하지 않으면 드라이브 요구 사항을 알려주며 계속하려면 사용자의 확인이 필요합니다.  |

## <a name="tool-output"></a>도구 출력

### <a name="sample-drive-manifest-file"></a>샘플 드라이브 매니페스트 파일

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>각 드라이브의 샘플 저널 파일(XML)

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>세션 내역을 기록하는 세션의 샘플 저널 파일(JRN)

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>FAQ

### <a name="general"></a>일반

#### <a name="what-is-waimportexport-tool"></a>WAImportExport 도구란?

WAImportExport 도구는 Microsoft Azure Import/Export 서비스에서 사용할 수 있는 드라이브 준비 및 복구 도구입니다. 이 도구를 사용하여 Azure 데이터 센터에 운송할 하드 드라이브에 데이터를 복사할 수 있습니다. 가져오기 작업이 완료된 후에는 이 도구를 사용하여 손상되거나 누락되거나 다른 Blob과 충돌한 Blob을 복구할 수 있습니다. 완료된 내보내기 작업에서 드라이브를 받은 후에는 이 도구를 사용하여 드라이브에서 손상되거나 누락된 파일을 복구할 수 있습니다.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>WAImportExport 도구는 여러 소스 디렉터리와 디스크에서 어떻게 작동합니까?

데이터 크기가 단일 디스크 크기보다 큰 경우 WAImportExport 도구는 디스크에 데이터를 최적화된 방식으로 분산합니다. 여러 디스크에 대한 데이터 복사는 동시에 또는 순차적으로 수행할 수 있습니다. 동시에 데이터를 쓸 수 있는 디스크의 수는 제한되지 않습니다. 이 도구는 디스크 크기와 폴더 크기에 따라 데이터를 분산합니다. 개체 크기에 가장 최적화된 디스크를 선택합니다. 저장소 계정에 업로드된 데이터는 지정한 디렉터리 구조로 다시 정리됩니다.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>이전 버전의 WAImportExport 도구는 어디서 찾을 수 있습니까?

WAImportExport 도구에는 WAImportExport V1 도구의 모든 기능이 있습니다. WAImportExport 도구를 사용하면 여러 소스를 지정하고 여러 드라이브에 쓸 수 있습니다. 또한 하나의 CSV 파일에서 데이터를 복사해야 하는 여러 원본 위치를 쉽게 관리할 수 있습니다. 그러나 SAS 지원이 필요하거나 단일 원본을 단일 디스크에 복사하려면 [WAImportExport V1 도구를 다운로드](https://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409)하고 [WAImportExport V1 참조](storage-import-export-tool-how-to-v1.md)에서 WAImportExport V1 사용과 관련된 도움말을 참조하세요.

#### <a name="what-is-a-session-id"></a>세션 ID란?

데이터를 여러 디스크에 분산하려는 경우 도구에서는 동일한 복사 세션(/id) 매개 변수를 사용합니다. 복사 세션의 동일한 이름을 유지하면 하나 이상의 원본 위치에서 하나 이상의 대상 디스크/디렉터리로 데이터를 복사할 수 있습니다. 동일한 세션 ID를 유지하면 도구에서 마지막으로 남아 있던 파일의 복사본을 선택할 수 있습니다.

그러나 동일한 복사 세션을 사용하여 다른 저장소 계정으로 데이터를 가져올 수는 없습니다.

도구의 여러 실행에서 복사 세션 이름이 동일한 경우 로그 파일(/logdir) 및 저장소 계정 키(/sk)도 동일해야 합니다.

SessionId는 문자, 0-9, 밑줄(\_), 대시(-) 또는 해시(#)로 구성할 수 있으며 길이는 3-30자여야 합니다.

예: session-1, session#1 또는 session\_1

#### <a name="what-is-a-journal-file"></a>저널 파일이란?

WAImportExport 도구를 실행하여 파일을 하드 드라이브에 복사할 때마다 이 도구에서 복사 세션을 만듭니다. 복사 세션의 상태는 저널 파일에 기록됩니다. 복사 세션이 중단된 경우(예: 시스템 전원 손실로 인해) 도구를 다시 실행하고 명령줄에서 저널 파일을 지정하여 해당 세션을 다시 시작할 수 있습니다.

Azure Import/Export 도구를 사용하여 준비하는 각 하드 드라이브의 경우 이 도구에서 "&lt;DriveID&gt;.xml" 이름의 단일 저널 파일을 만듭니다. 여기서 드라이브 ID는 도구에서 디스크로부터 읽어 들이는 드라이브와 관련된 일련 번호입니다. 가져오기 작업을 만들려면 모든 드라이브의 저널 파일이 필요합니다. 도구가 중단되는 경우 저널 파일을 사용하여 드라이브 준비를 다시 시작할 수도 있습니다.

#### <a name="what-is-a-log-directory"></a>로그 디렉터리란?

로그 디렉터리는 자세한 로그와 임시 매니페스트 파일을 저장하는 데 사용할 디렉터리를 지정합니다. 지정하지 않으면 현재 디렉터리가 로그 디렉터리로 사용됩니다. 로그는 자세한 정보 표시 로그입니다.

### <a name="prerequisites"></a>필수 조건

#### <a name="what-are-the-specifications-of-my-disk"></a>내 디스크의 사양은?

복사 컴퓨터에 연결된 하나 이상의 빈 2.5"/3.5" SATA II/III 또는 SSD 하드 드라이브

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>내 컴퓨터에서 BitLocker를 사용하도록 설정하려면 어떻게 합니까?

시스템 드라이브를 마우스 오른쪽 단추로 클릭만 하면 간단히 확인할 수 있습니다. 이 기능이 설정되어 있으면 BitLocker에 대한 옵션이 표시되지만, 해제되어 있으면 해당 옵션이 표시되지 않습니다.

![BitLocker 확인](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

[BitLocker를 사용하도록 설정하는 방법](https://technet.microsoft.com/library/cc766295.aspx) 문서를 참조하세요.

컴퓨터에 TPM 칩이 없을 수도 있습니다. tpm.msc를 사용하여 출력을 얻지 못하면 다음 FAQ를 살펴보세요.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>BitLocker에서 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하지 않도록 설정하려면 어떻게 합니까?
> [!NOTE]
> 해당 서버에 TPM이 없는 경우에만 TPM 정책을 사용하지 않도록 해야 합니다. 사용자의 서버에서 신뢰할 수 있는 TPM이 없는 경우 TPM을 사용하지 않도록 설정할 필요는 없습니다. 
> 

BitLocker에서 TPM을 사용하지 않도록 설정하려면 다음 단계를 수행합니다.<br/>
1. 명령 프롬프트에서 gpedit.msc를 입력하여 **그룹 정책 편집기**를 시작합니다. **그룹 정책 편집기**에서 사용할 수 없는 것으로 표시되면 먼저 BitLocker를 사용하도록 설정합니다. 앞서의 FAQ를 참조하세요.
2. **로컬 컴퓨터 정책 &gt; 컴퓨터 구성 &gt; 관리 템플릿 &gt; Windows 구성 요소 &gt; BitLocker 드라이브 암호화 &gt; 운영 체제 드라이브**를 차례로 이동합니다.
3. **시작 시 추가 인증 요구** 정책을 편집합니다.
4. 정책을 **사용**으로 설정하고 **호환되는 TPM이 없이 BitLocker 허용**이 선택되어 있는지 확인합니다.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>내 컴퓨터에 .NET 4 이상의 버전이 설치되어 있는지 확인하려면 어떻게 합니까?

모든 Microsoft .NET Framework 버전은 %windir%\Microsoft.NET\Framework\ 디렉터리에 설치됩니다.

도구를 실행해야 하는 대상 컴퓨터에서 위에서 언급한 부분으로 이동합니다. "v4"로 시작하는 폴더 이름을 찾습니다. 이러한 디렉터리가 없으면 컴퓨터에 .NET 4가 설치되지 않은 것입니다. 사용 하 여 컴퓨터에.NET 4를 다운로드할 수 있습니다 [Microsoft.NET Framework 4 (웹 설치 관리자)](https://www.microsoft.com/download/details.aspx?id=17851)합니다.

### <a name="limits"></a>제한

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>동시에 준비/전송할 수 있는 드라이브는 몇 개입니까?

도구에서 준비할 수 있는 디스크의 수는 제한되지 않습니다. 그러나 이 도구는 드라이브 문자를 입력으로 사용합니다. 따라서 최대 25개의 디스크를 동시에 준비할 수 있습니다. 단일 작업에서 한 번에 최대 10개의 디스크를 처리할 수 있습니다. 동일한 저장소 계정을 대상으로 하는 10개 이상의 디스크를 준비하는 경우 디스크를 여러 작업에 분산할 수 있습니다.

#### <a name="can-i-target-more-than-one-storage-account"></a>둘 이상의 저장소 계정을 대상으로 지정할 수 있습니까?

단일 복사 세션에서 작업당 저장소 계정 하나만 제출할 수 있습니다.

### <a name="capabilities"></a>기능

#### <a name="does-waimportexportexe-encrypt-my-data"></a>WAImportExport.exe에서 내 데이터를 암호화합니까?

예. 이 프로세스에서 BitLocker 암호화는 사용할 수 있으며 필요합니다.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>내 데이터의 계층 구조가 저장소 계정에 표시되면 어떻게 됩니까?

데이터가 여러 디스크에 분산되어 있지만 저장소 계정에 업로드된 데이터는 데이터 세트 CSV 파일에 지정한 디렉터리 구조로 다시 정리됩니다.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>복사가 진행 중일 때 활성 IO를 병렬로 갖추게 되는 입력 디스크는 몇 개입니까?

도구에서는 입력 파일의 크기에 따라 입력 디스크에 데이터를 분산합니다. 즉, 병렬로 연결되는 활성 디스크의 수는 입력 데이터의 특성에 따라 다릅니다. 입력 데이터 세트의 개별 파일 크기에 따라 하나 이상의 디스크에 활성 IO가 병렬로 표시될 수 있습니다. 자세한 내용은 다음 질문을 참조하세요.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>도구에서 어떻게 파일을 디스크에 분산합니까?

WAImportExport 도구는 배치 기준으로 파일을 읽고 쓰며, 배치 하나당 최대 100,000개 파일을 포함할 수 있습니다. 즉 최대 100,000개 파일을 동시에 쓸 수 있습니다. 이러한 100,000개 파일을 여러 드라이브에 분산하는 경우 여러 디스크에 동시에 쓸 수 있습니다. 그러나 도구에서 여러 디스크에 동시에 쓰는지 또는 단일 디스크에 쓰는지의 여부는 배치의 누적 크기에 따라 다릅니다. 예를 들어 파일 크기가 작을 경우 10,0000개 파일을 모두 하나의 드라이브에 맞출 수 있는 경우 도구에서는 이 배치를 처리하는 동안 디스크 하나에만 기록합니다.

### <a name="waimportexport-output"></a>WAImportExport 출력

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>저널 파일이 2개 있습니다. Azure Portal에는 어느 파일을 업로드해야 합니까?

**.xml** - WAImportExport 도구를 사용하여 준비하는 각 하드 드라이브의 경우 이 도구에서 `<DriveID>.xml` 이름의 단일 저널 파일을 만듭니다. 여기서 드라이브 ID는 도구에서 디스크로부터 읽어 들이는 드라이브와 관련된 일련 번호입니다. Azure Portal에서 가져오기 작업을 만들려면 모든 드라이브의 저널 파일이 필요합니다. 도구가 중단되는 경우 이 저널 파일을 사용하여 드라이브 준비를 다시 시작할 수도 있습니다.

**.jrn** - `.jrn` 접미사가 있는 저널 파일에는 하드 드라이브의 모든 복사 세션에 대한 상태가 포함됩니다. 가져오기 작업을 만드는 데 필요한 정보도 포함됩니다. WAImportExport 도구를 실행할 때 항상 저널 파일과 복사 세션 ID를 지정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Import/Export 도구 설정](storage-import-export-tool-setup.md)
* [가져오기 프로세스 중에 속성 및 메타데이터 설정](storage-import-export-tool-setting-properties-metadata-import.md)
* [가져오기 작업을 위한 하드 드라이브를 준비하는 샘플 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [자주 사용 되는 명령에 대한 빠른 참조](storage-import-export-tool-quick-reference.md) 
* [복사 로그 파일을 사용하여 작업 상태 검토](storage-import-export-tool-reviewing-job-status-v1.md)
* [가져오기 작업 복구](storage-import-export-tool-repairing-an-import-job-v1.md)
* [내보내기 작업 복구](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Azure Import/Export 도구 문제 해결](storage-import-export-tool-troubleshooting-v1.md)
