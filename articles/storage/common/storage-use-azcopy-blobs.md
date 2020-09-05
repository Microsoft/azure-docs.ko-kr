---
title: AzCopy v10를 사용 하 여 Azure Blob storage 간 데이터 전송 Microsoft Docs
description: 이 문서에는 컨테이너를 만들고, 파일을 복사 하 고, 로컬 파일 시스템과 컨테이너 간에 디렉터리를 동기화 하는 데 도움이 되는 AzCopy 예제 명령의 컬렉션이 포함 되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7ff8f3d18564140b4654b1591eec5c0e1f40b7cf
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077911"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송

AzCopy은 저장소 계정 간에 데이터를 복사 하거나 저장소 계정 간에 데이터를 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에는 Blob 저장소에서 작동 하는 예제 명령이 포함 되어 있습니다.

> [!TIP]
> 이 문서의 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

## <a name="get-started"></a>시작

[AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조 하 여 AzCopy를 다운로드 하 고, 저장소 서비스에 권한 부여 자격 증명을 제공할 수 있는 방법에 대해 알아보세요.

> [!NOTE]
> 이 문서의 예제에서는 명령을 사용 하 여 id를 인증 했다고 가정 합니다 `AzCopy login` . AzCopy는 Azure AD 계정을 사용 하 여 Blob storage의 데이터에 대 한 액세스 권한을 부여 합니다.
>
> 대신 SAS 토큰을 사용 하 여 blob 데이터에 대 한 액세스 권한을 부여 하는 경우 각 AzCopy 명령의 리소스 URL에 해당 토큰을 추가할 수 있습니다.
>
> 예: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`

## <a name="create-a-container"></a>컨테이너 만들기

[Azcopy 만들기](storage-ref-azcopy-make.md) 명령을 사용 하 여 컨테이너를 만들 수 있습니다. 이 단원의 예제에서는 라는 컨테이너를 만듭니다 `mycontainer` .

|    |     |
|--------|-----------|
| **구문** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **예제** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **예** (계층적 네임 스페이스) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

자세한 참조 문서는 [azcopy 만들기](storage-ref-azcopy-make.md)를 참조 하세요.

## <a name="upload-files"></a>파일 업로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 로컬 컴퓨터에서 파일 및 디렉터리를 업로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 업로드
> * 디렉터리 업로드
> * 디렉터리의 콘텐츠 업로드 
> * 특정 파일 업로드

> [!TIP]
> 선택적 플래그를 사용 하 여 업로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |추가 Blob 또는 페이지 Blob으로 파일을 업로드합니다.|**--blob-형식** = \[ BlockBlob \| pageblob \| appendblob\]|
> |특정 액세스 계층(예: 보관 계층)에 업로드합니다.|**--블록-blob 계층** = \[ 없음 \| 핫 \| 쿨 \| 아카이브\]|
> 
> 전체 목록은 [options](storage-ref-azcopy-copy.md#options)를 참조 하세요.

### <a name="upload-a-file"></a>파일 업로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **예제** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

파일 경로 또는 파일 이름 어디에 나 와일드 카드 기호 (*)를 사용 하 여 파일을 업로드할 수도 있습니다. 예를 들면 `'C:\myDirectory\*.txt'` , 또는 `C:\my*\*.txt` 입니다.

### <a name="upload-a-directory"></a>디렉터리 업로드

이 예에서는 디렉터리와 해당 디렉터리의 모든 파일을 blob 컨테이너에 복사 합니다. 결과는 컨테이너에서 이름이 같은 디렉터리입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

컨테이너 내의 디렉터리로 복사 하려면 명령 문자열에서 해당 디렉터리의 이름을 지정 하면 됩니다.

|    |     |
|--------|-----------|
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

컨테이너에 없는 디렉터리의 이름을 지정 하는 경우 AzCopy은 해당 이름으로 새 디렉터리를 만듭니다.

### <a name="upload-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 업로드

와일드 카드 기호 (*)를 사용 하 여 포함 하는 디렉터리 자체를 복사 하지 않고 디렉터리의 내용을 업로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **예제** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> 플래그를 추가 `--recursive` 하 여 모든 하위 디렉터리의 파일을 업로드 합니다.

### <a name="upload-specific-files"></a>특정 파일 업로드

전체 파일 이름, 와일드 카드 문자 (*)가 포함 된 부분 이름, 날짜 및 시간을 사용 하 여 특정 파일을 업로드할 수 있습니다.

#### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-path` . 세미콜론 ()을 사용 하 여 개별 파일 이름을 구분 `;` 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

이 예제에서 AzCopy는 `C:\myDirectory\photos` 디렉터리와 파일을 전송 합니다 `C:\myDirectory\documents\myFile.txt` . `--recursive`디렉터리의 모든 파일을 전송 하는 옵션을 포함 해야 `C:\myDirectory\photos` 합니다.

옵션을 사용 하 여 파일을 제외할 수도 있습니다 `--exclude-path` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

#### <a name="use-wildcard-characters"></a>와일드 카드 문자 사용

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-pattern` . 와일드 카드 문자를 포함 하는 부분 이름을 지정 합니다. Semicolin ()를 사용 하 여 이름을 구분 `;` 합니다. 

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

옵션을 사용 하 여 파일을 제외할 수도 있습니다 `--exclude-pattern` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 경로에는 적용 되지 않고 파일 이름에만 적용 됩니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사 하려는 경우 옵션을 사용 `–recursive` 하 여 전체 디렉터리 트리를 가져온 다음를 사용 하 여 `–include-pattern` `*.txt` 모든 텍스트 파일을 가져오도록 지정 합니다.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>날짜 및 시간 이후 수정 된 파일 업로드 

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-after` . ISO-8601 형식으로 날짜 및 시간을 지정 합니다 (예: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **예제** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

## <a name="download-files"></a>파일 다운로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 로컬 컴퓨터에 blob, 디렉터리 및 컨테이너를 다운로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 다운로드
> * 디렉터리 다운로드
> * 디렉터리의 콘텐츠 다운로드
> * 특정 파일 다운로드

> [!TIP]
> 선택적 플래그를 사용 하 여 다운로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |자동으로 파일의 압축을 해제 합니다.|**--압축 풀기**|
> |복사 관련 로그 항목에 대 한 세부 정보를 지정 합니다.|**--로그 수준** = \[ 경고 \| 오류 \| 정보 \| 없음\]|
> |대상과 충돌 하는 파일 및 blob를 덮어쓸지 여부를 지정 합니다.|**--덮어쓰기** = \[ true \| false \| ifSourceNewer \| prompt\]|
> 
> 전체 목록은 [options](storage-ref-azcopy-copy.md#options)를 참조 하세요.

> [!NOTE]
> `Content-md5`Blob의 속성 값에 해시가 포함 된 경우 AzCopy는 다운로드 된 데이터에 대 한 md5 해시를 계산 하 고 blob의 속성에 저장 된 md5 해시가 계산 된 해시와 일치 하는지 확인 합니다 `Content-md5` . 이러한 값이 일치 하지 않으면 `--check-md5=NoCheck` 복사 명령에 또는을 추가 하 여이 동작을 재정의 하지 않는 한 다운로드가 실패 합니다 `--check-md5=LogOnly` .

### <a name="download-a-file"></a>파일 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>디렉터리 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

이 예에서는 `C:\myDirectory\myBlobDirectory` 다운로드 한 모든 파일을 포함 하는 라는 디렉터리를 생성 합니다.

### <a name="download-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 다운로드

와일드 카드 기호 (*)를 사용 하 여 포함 하는 디렉터리 자체를 복사 하지 않고 디렉터리의 콘텐츠를 다운로드할 수 있습니다.

> [!NOTE]
> 현재이 시나리오는 계층 네임 스페이스가 없는 계정에 대해서만 지원 됩니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> 플래그를 추가 `--recursive` 하 여 모든 하위 디렉터리의 파일을 다운로드 합니다.

### <a name="download-specific-files"></a>특정 파일 다운로드

전체 파일 이름, 와일드 카드 문자 (*)가 포함 된 부분 이름, 날짜 및 시간을 사용 하 여 특정 파일을 다운로드할 수 있습니다. 

#### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-path` . Semicolin ()를 사용 하 여 개별 파일 이름을 구분 `;` 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

이 예제에서 AzCopy는 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 디렉터리와 파일을 전송 합니다 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` . `--recursive`디렉터리의 모든 파일을 전송 하는 옵션을 포함 해야 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 합니다.

옵션을 사용 하 여 파일을 제외할 수도 있습니다 `--exclude-path` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

#### <a name="use-wildcard-characters"></a>와일드 카드 문자 사용

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-pattern` . 와일드 카드 문자를 포함 하는 부분 이름을 지정 합니다. Semicolin ()를 사용 하 여 이름을 구분 `;` 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

옵션을 사용 하 여 파일을 제외할 수도 있습니다 `--exclude-pattern` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 경로에는 적용 되지 않고 파일 이름에만 적용 됩니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사 하려는 경우 옵션을 사용 `–recursive` 하 여 전체 디렉터리 트리를 가져온 다음를 사용 하 여 `–include-pattern` `*.txt` 모든 텍스트 파일을 가져오도록 지정 합니다.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>날짜 및 시간 이후 수정 된 파일 다운로드 

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-after` . ISO-8601 형식으로 날짜 및 시간을 지정 합니다 (예: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

#### <a name="download-previous-versions-of-a-blob"></a>이전 버전의 blob 다운로드

[Blob 버전 관리](../blobs/versioning-enable.md)를 사용 하도록 설정한 경우 blob의 이전 버전을 하나 이상 다운로드할 수 있습니다. 

먼저 [버전 id](../blobs/versioning-overview.md)목록을 포함 하는 텍스트 파일을 만듭니다. 각 버전 ID는 별도의 줄에 표시 되어야 합니다. 예를 들면 다음과 같습니다. 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

그런 다음 옵션을 사용 하 여 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 `--list-of-versions` 합니다. 버전 목록이 포함 된 텍스트 파일의 위치를 지정 합니다 (예: `D:\\list-of-versions.txt` ).  

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

다운로드 한 각 파일의 이름은 버전 ID와 blob 이름으로 시작 합니다. 

## <a name="copy-blobs-between-storage-accounts"></a>스토리지 계정 간에 Blob 복사

AzCopy를 사용하여 Blob을 다른 스토리지 계정에 복사할 수 있습니다. 복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다. 

AzCopy는 [서버](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) 간 [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)를 사용 하므로 저장소 서버 간에 데이터를 직접 복사 합니다. 이러한 복사 작업은 컴퓨터의 네트워크 대역폭을 사용 하지 않습니다. 환경 변수의 값을 설정 하 여 이러한 작업의 처리량을 늘릴 수 있습니다 `AZCOPY_CONCURRENCY_VALUE` . 자세히 알아보려면 [처리량 최적화](storage-use-azcopy-configure.md#optimize-throughput)를 참조 하세요.

> [!NOTE]
> 이 시나리오의 현재 릴리스에서는 다음과 같은 제한 사항이 있습니다.
>
> - 각 원본 URL에 SAS 토큰을 추가 해야 합니다. AD (Azure Active Directory)를 사용 하 여 권한 부여 자격 증명을 제공 하는 경우 대상 URL 에서만 SAS 토큰을 생략할 수 있습니다. 대상 계정에 적절 한 역할을 설정 했는지 확인 합니다. [옵션 1: Azure Active Directory 사용](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)을 참조 하세요.
>-  프리미엄 블록 blob 저장소 계정은 액세스 계층을 지원 하지 않습니다. 을로 설정 하 여 복사 작업에서 blob의 액세스 계층을 생략 `s2s-preserve-access-tier` `false` 합니다 (예: `--s2s-preserve-access-tier=false` ).

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 다른 저장소 계정에 blob 복사
> * 다른 저장소 계정에 디렉터리 복사
> * 컨테이너를 다른 저장소 계정에 복사
> * 모든 컨테이너, 디렉터리 및 파일을 다른 저장소 계정에 복사 합니다.

이러한 예제는 계층 네임 스페이스가 있는 계정 에서도 작동 합니다. [Data Lake Storage에 대 한 다중 프로토콜 액세스](../blobs/data-lake-storage-multi-protocol-access.md) 를 사용 하면 `blob.core.windows.net` 해당 계정에 대해 동일한 URL 구문 ()을 사용할 수 있습니다.

> [!TIP]
> 선택적 플래그를 사용 하 여 복사 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |블록, 페이지 또는 추가 Blob으로 blob을 복사 합니다.|**--blob-형식** = \[ BlockBlob \| pageblob \| appendblob\]|
> |특정 액세스 계층 (예: 보관 계층)에 복사 합니다.|**--블록-blob 계층** = \[ 없음 \| 핫 \| 쿨 \| 아카이브\]|
> |자동으로 파일의 압축을 해제 합니다.|**--압축 풀기** = \[ gzip \| deflate\]|
> 
> 전체 목록은 [options](storage-ref-azcopy-copy.md#options)를 참조 하세요.

### <a name="copy-a-blob-to-another-storage-account"></a>다른 저장소 계정에 blob 복사

계층 네임 스페이스가 있는 계정에 동일한 URL 구문 ()을 사용 합니다 `blob.core.windows.net` .

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>다른 저장소 계정에 디렉터리 복사

계층 네임 스페이스가 있는 계정에 동일한 URL 구문 ()을 사용 합니다 `blob.core.windows.net` .

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **예** (계층적 네임 스페이스)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>컨테이너를 다른 저장소 계정에 복사

계층 네임 스페이스가 있는 계정에 동일한 URL 구문 ()을 사용 합니다 `blob.core.windows.net` .

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **예** (계층적 네임 스페이스)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>모든 컨테이너, 디렉터리 및 blob을 다른 저장소 계정에 복사 합니다.

계층 네임 스페이스가 있는 계정에 동일한 URL 구문 ()을 사용 합니다 `blob.core.windows.net` .

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **예** (계층적 네임 스페이스)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>파일 동기화

로컬 파일 시스템의 내용을 blob 컨테이너와 동기화 할 수 있습니다. 컨테이너와 가상 디렉터리를 서로 동기화 할 수도 있습니다. 동기화는 단방향입니다. 즉, 이러한 두 끝점 중 원본 및 대상 끝점 중 하나를 선택 합니다. 또한 동기화는 서버 간 Api를 사용 합니다. 이 섹션에서 설명 하는 예제는 계층 네임 스페이스가 있는 계정 에서도 작동 합니다. 

> [!NOTE]
> AzCopy의 현재 릴리스는 다른 원본 및 대상 간에 동기화 되지 않습니다 (예: File storage 또는 Amazon Web Services (AWS) S3 버킷).

[Sync](storage-ref-azcopy-sync.md) 명령은 파일 이름과 마지막 수정 된 타임 스탬프를 비교 합니다. `--delete-destination`대상 디렉터리에서 파일을 삭제 하려면 선택적 플래그를 또는 값으로 설정 `true` 하 고, `prompt` 해당 파일이 원본 디렉터리에 더 이상 없으면 파일을 삭제 합니다.

`--delete-destination`플래그를 AzCopy로 설정 하면 `true` 프롬프트를 제공 하지 않고 파일이 삭제 됩니다. AzCopy에서 파일을 삭제 하기 전에 프롬프트가 표시 되도록 하려면 플래그를로 설정 `--delete-destination` `prompt` 합니다.

> [!NOTE]
> 실수로 인 한 삭제를 방지 하려면 플래그를 사용 하기 전에 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) 기능을 사용 하도록 설정 해야 합니다 `--delete-destination=prompt|true` .

> [!TIP]
> 선택적 플래그를 사용 하 여 동기화 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |다운로드 시의 MD5 해시 유효성 검사 방법을 지정 합니다.|**--확인-md5** = \[ NoCheck \| logonly \| \| FailIfDifferentOrMissing\]|
> |패턴을 기반으로 파일을 제외 합니다.|**--제외-경로**|
> |동기화 관련 로그 항목에 대 한 세부 정보를 지정 합니다.|**--로그 수준** = \[ 경고 \| 오류 \| 정보 \| 없음\]|
> 
> 전체 목록은 [options](storage-ref-azcopy-sync.md#options)를 참조 하세요.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>로컬 파일 시스템에 대 한 변경 내용으로 컨테이너 업데이트

이 경우 컨테이너는 대상이며 로컬 파일 시스템은 원본입니다. 

|    |     |
|--------|-----------|
| **구문** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>변경이 포함된 로컬 파일 시스템을 컨테이너로 업데이트

이 경우 로컬 파일 시스템은 대상이 고 컨테이너는 원본입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **예제** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>다른 컨테이너의 변경 내용으로 컨테이너 업데이트

이 명령에 표시 되는 첫 번째 컨테이너는 원본입니다. 두 번째는 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>다른 파일 공유의 디렉터리를 변경 하 여 디렉터리를 업데이트 합니다.

이 명령에 표시 되는 첫 번째 디렉터리는 원본입니다. 두 번째는 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)
