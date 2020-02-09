---
title: AzCopy v10를 사용 하 여 Azure Files에서 데이터 전송 Microsoft Docs
description: AzCopy 및 file storage를 사용 하 여 데이터를 전송 합니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: fe2a6cb7943875732e6ce2fad630b2af69e6197b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559133"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy 및 file storage를 사용 하 여 데이터 전송 

AzCopy는 blob 또는 파일을 저장소 계정에 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에는 Azure Files를 사용 하는 예제 명령이 포함 되어 있습니다.

시작 하기 전에 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조 하 여 AzCopy를 다운로드 하 고 도구를 숙지 하세요.

## <a name="create-file-shares"></a>파일 공유 만들기

[Azcopy 만들기](storage-ref-azcopy-make.md) 명령을 사용 하 여 파일 공유를 만들 수 있습니다. 이 섹션의 예제에서는 `myfileshare`라는 파일 공유를 만듭니다.

> [!TIP]
> 이 단원의 예제에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용 합니다. Windows 명령 셸 (cmd.exe)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **예제** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

자세한 참조 문서는 [azcopy 만들기](storage-ref-azcopy-make.md)를 참조 하세요.

## <a name="upload-files"></a>파일 업로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 로컬 컴퓨터에서 파일 및 디렉터리를 업로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 업로드
> * 디렉터리 업로드
> * 디렉터리의 콘텐츠 업로드
> * 특정 파일 업로드

> [!NOTE]
> AzCopy는 파일의 md5 해시 코드를 자동으로 계산 하 고 저장 하지 않습니다. AzCopy는이 작업을 수행 하려면 각 copy 명령에 `--put-md5` 플래그를 추가 합니다. 이렇게 하면 파일이 다운로드 될 때 AzCopy는 다운로드 된 데이터에 대 한 MD5 해시를 계산 하 고 파일의 `Content-md5` 속성에 저장 된 MD5 해시가 계산 된 해시와 일치 하는지 확인 합니다.

자세한 참조 문서는 [azcopy copy](storage-ref-azcopy-copy.md)를 참조 하세요.

> [!TIP]
> 이 단원의 예제에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용 합니다. Windows 명령 셸 (cmd.exe)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

### <a name="upload-a-file"></a>파일 업로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **예제** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

파일 경로 또는 파일 이름 어디에 나 와일드 카드 기호 (*)를 사용 하 여 파일을 업로드할 수도 있습니다. 예: `'C:\myDirectory\*.txt'`또는 `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>디렉터리 업로드

이 예에서는 디렉터리와 해당 디렉터리의 모든 파일을 파일 공유에 복사 합니다. 결과는 파일 공유에서 이름이 같은 디렉터리입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

파일 공유 내의 디렉터리에 복사 하려면 명령 문자열에서 해당 디렉터리의 이름을 지정 하면 됩니다.

|    |     |
|--------|-----------|
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

파일 공유에 없는 디렉터리의 이름을 지정 하는 경우 AzCopy은 해당 이름으로 새 디렉터리를 만듭니다.

### <a name="upload-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 업로드

와일드 카드 기호 (*)를 사용 하 여 포함 하는 디렉터리 자체를 복사 하지 않고 디렉터리의 내용을 업로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **예제** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> `--recursive` 플래그를 추가 하 여 모든 하위 디렉터리의 파일을 업로드 합니다.

### <a name="upload-specific-files"></a>특정 파일 업로드

전체 파일 이름을 지정 하거나 부분 이름에 와일드 카드 문자 (*)를 사용할 수 있습니다.

#### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

`--include-path` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다. 세미콜론 (`;`)을 사용 하 여 개별 파일 이름을 구분 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

이 예제에서 AzCopy는 `C:\myDirectory\photos` 디렉터리와 `C:\myDirectory\documents\myFile.txt` 파일을 전송 합니다. `C:\myDirectory\photos` 디렉터리의 모든 파일을 전송 하려면 `--recursive` 옵션을 포함 해야 합니다.

`--exclude-path` 옵션을 사용 하 여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

#### <a name="use-wildcard-characters"></a>와일드 카드 문자 사용

`--include-pattern` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다. 와일드 카드 문자를 포함 하는 부분 이름을 지정 합니다. Semicolin (`;`)를 사용 하 여 이름을 구분 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` 옵션을 사용 하 여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

`--include-pattern` 및 `--exclude-pattern` 옵션은 파일 이름에만 적용 되 고 경로에는 적용 되지 않습니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사 하려면 `–recursive` 옵션을 사용 하 여 전체 디렉터리 트리를 가져온 다음 `–include-pattern`를 사용 하 고 `*.txt`를 지정 하 여 모든 텍스트 파일을 가져옵니다.

## <a name="download-files"></a>파일 다운로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 로컬 컴퓨터에 파일, 디렉터리 및 파일 공유를 다운로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 다운로드
> * 디렉터리 다운로드
> * 디렉터리의 콘텐츠 다운로드
> * 특정 파일 다운로드

> [!NOTE]
> 파일의 `Content-md5` 속성 값에 해시가 포함 되어 있으면 AzCopy는 다운로드 된 데이터에 대 한 MD5 해시를 계산 하 고 파일의 `Content-md5` 속성에 저장 된 MD5 해시가 계산 된 해시와 일치 하는지 확인 합니다. 이러한 값이 일치 하지 않으면 복사 명령에 `--check-md5=NoCheck` 또는 `--check-md5=LogOnly`을 추가 하 여이 동작을 재정의 하지 않는 한 다운로드가 실패 합니다.

자세한 참조 문서는 [azcopy copy](storage-ref-azcopy-copy.md)를 참조 하세요.

> [!TIP]
> 이 단원의 예제에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용 합니다. Windows 명령 셸 (cmd.exe)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

### <a name="download-a-file"></a>파일 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>디렉터리 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

이 예에서는 다운로드 한 모든 파일을 포함 하는 `C:\myDirectory\myFileShareDirectory` 라는 디렉터리를 생성 합니다.

### <a name="download-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 다운로드

와일드 카드 기호 (*)를 사용 하 여 포함 하는 디렉터리 자체를 복사 하지 않고 디렉터리의 콘텐츠를 다운로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> `--recursive` 플래그를 추가 하 여 모든 하위 디렉터리의 파일을 다운로드 합니다.

### <a name="download-specific-files"></a>특정 파일 다운로드

전체 파일 이름을 지정 하거나 부분 이름에 와일드 카드 문자 (*)를 사용할 수 있습니다.

#### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

`--include-path` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다. Semicolin (`;`)를 사용 하 여 개별 파일 이름을 구분 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

이 예제에서 AzCopy는 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 디렉터리와 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` 파일을 전송 합니다. `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 디렉터리의 모든 파일을 전송 하려면 `--recursive` 옵션을 포함 해야 합니다.

`--exclude-path` 옵션을 사용 하 여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

#### <a name="use-wildcard-characters"></a>와일드 카드 문자 사용

`--include-pattern` 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다. 와일드 카드 문자를 포함 하는 부분 이름을 지정 합니다. Semicolin (`;`)를 사용 하 여 이름을 구분 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>?<SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` 옵션을 사용 하 여 파일을 제외할 수도 있습니다. 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

`--include-pattern` 및 `--exclude-pattern` 옵션은 파일 이름에만 적용 되 고 경로에는 적용 되지 않습니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사 하려면 `–recursive` 옵션을 사용 하 여 전체 디렉터리 트리를 가져온 다음 `–include-pattern`를 사용 하 고 `*.txt`를 지정 하 여 모든 텍스트 파일을 가져옵니다.

## <a name="copy-files-between-storage-accounts"></a>저장소 계정 간에 파일 복사

AzCopy를 사용 하 여 다른 저장소 계정에 파일을 복사할 수 있습니다. 복사 작업은 동기식 이므로 명령이 반환 될 때 모든 파일이 복사 되었음을 나타냅니다.

AzCopy는 [서버](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) 간 [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)를 사용 하므로 저장소 서버 간에 데이터를 직접 복사 합니다. 이러한 복사 작업은 컴퓨터의 네트워크 대역폭을 사용 하지 않습니다. `AZCOPY_CONCURRENCY_VALUE` 환경 변수의 값을 설정 하 여 이러한 작업의 처리량을 늘릴 수 있습니다. 자세히 알아보려면 [처리량 최적화](storage-use-azcopy-configure.md#optimize-throughput)를 참조 하세요.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 다른 저장소 계정에 파일 복사
> * 다른 저장소 계정에 디렉터리 복사
> * 다른 저장소 계정에 파일 공유 복사
> * 모든 파일 공유, 디렉터리 및 파일을 다른 저장소 계정에 복사 합니다.

자세한 참조 문서는 [azcopy copy](storage-ref-azcopy-copy.md)를 참조 하세요.

> [!TIP]
> 이 단원의 예제에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용 합니다. Windows 명령 셸 (cmd.exe)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

### <a name="copy-a-file-to-another-storage-account"></a>다른 저장소 계정에 파일 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>'` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>다른 저장소 계정에 디렉터리 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>다른 저장소 계정에 파일 공유 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>모든 파일 공유, 디렉터리 및 파일을 다른 저장소 계정에 복사 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/?<SAS-token>' --recursive'` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>파일 동기화

파일 공유의 내용을 다른 파일 공유와 동기화 할 수 있습니다. 또한 파일 공유에 있는 디렉터리의 내용을 다른 파일 공유에 있는 디렉터리의 내용과 동기화 할 수 있습니다. 동기화는 단방향입니다. 즉, 이러한 두 엔드포인트 중 원본 및 대상 엔드포인트 중 하나를 선택 합니다. 또한 동기화는 서버 간 Api를 사용 합니다.

> [!NOTE]
> 현재이 시나리오는 계층 네임 스페이스가 없는 계정에 대해서만 지원 됩니다. 현재 AzCopy의 릴리스는 Azure Files와 Blob Storage 간에 동기화 되지 않습니다.

[Sync](storage-ref-azcopy-sync.md) 명령은 파일 이름과 마지막 수정 된 타임 스탬프를 비교 합니다. `--delete-destination` 옵션 플래그를 `true` 값으로 설정 하거나, 해당 파일이 원본 디렉터리에 더 이상 없는 경우 대상 디렉터리에서 파일을 삭제 하려면 `prompt` 합니다.

`--delete-destination` 플래그를 `true` AzCopy에서 프롬프트를 제공 하지 않고 파일을 삭제 하도록 설정 하는 경우 AzCopy에서 파일을 삭제 하기 전에 프롬프트가 표시 되도록 하려면 `--delete-destination` 플래그를 `prompt`로 설정 합니다.

자세한 참조 문서는 [azcopy sync](storage-ref-azcopy-sync.md)를 참조 하세요.

> [!TIP]
> 이 단원의 예제에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용 합니다. Windows 명령 셸 (cmd.exe)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>다른 파일 공유에 대 한 변경 내용으로 파일 공유 업데이트

이 명령에 표시 되는 첫 번째 파일 공유는 원본입니다. 두 번째는 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>다른 파일 공유의 디렉터리를 변경 하 여 디렉터리를 업데이트 합니다.

이 명령에 표시 되는 첫 번째 디렉터리는 원본입니다. 두 번째는 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)
