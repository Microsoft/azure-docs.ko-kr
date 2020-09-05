---
title: azcopy 복사 | Microsoft Docs
description: 이 문서에서는 azcopy copy 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b9d5a9e071cc1b2ac81e8cacea8c974181fbb3b6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070397"
---
# <a name="azcopy-copy"></a>azcopy copy

원본 데이터를 대상 위치에 복사 합니다.

## <a name="synopsis"></a>개요

원본 데이터를 대상 위치에 복사 합니다. 지원 되는 지침은 다음과 같습니다.

  - 로컬 <-> Azure Blob (SAS 또는 OAuth 인증)
  - 로컬 <-> Azure Files (공유/디렉터리 SAS 인증)
  - 로컬 <-> Azure Data Lake Storage Gen 2 (SAS, OAuth 또는 공유 키 인증)
  - Azure Blob (SAS 또는 public)-> Azure Blob (SAS 또는 OAuth 인증)
  - Azure Blob (SAS 또는 공용)-SAS (> Azure Files)
  - Sas (Azure Files)-SAS (> Azure Files)
  - SAS (Azure Files)-> Azure Blob (SAS 또는 OAuth 인증)
  - Amazon Web Services (AWS) S3 (액세스 키)-> Azure 블록 Blob (SAS 또는 OAuth 인증)

자세한 내용은이 문서의 예 섹션을 참조 하세요.

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="advanced"></a>고급

AzCopy는 로컬 디스크에서 파일을 업로드할 때 파일의 콘텐츠 형식을 자동으로 검색 합니다. AzCopy는 파일 확장명 또는 콘텐츠를 기준으로 콘텐츠 형식을 검색 합니다 (확장명이 지정 되지 않은 경우).

기본 제공 조회 테이블은 작지만 Unix에서는 `mime.types` 다음 이름 중 하나 이상에서 사용할 수 있는 경우 로컬 시스템의 파일에 의해 확대 됩니다.

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows에서는 레지스트리에서 MIME 형식이 추출 됩니다. 플래그를 사용 하 여이 기능을 끌 수 있습니다. 자세한 내용은이 문서의 플래그 섹션을 참조 하세요.

명령줄을 사용 하 여 환경 변수를 설정 하는 경우 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록에서 자격 증명을 포함 하는 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않게 하려면 스크립트를 사용 하 여 사용자에 게 자격 증명을 묻는 메시지를 표시 하 고 환경 변수를 설정할 수 있습니다.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>예

OAuth 인증을 사용 하 여 단일 파일을 업로드 합니다. AzCopy에 아직 로그인 하지 않은 경우 `azcopy login` 다음 명령을 실행 하기 전에 명령을 실행 합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
위와 동일 하지만 이번에는 파일 콘텐츠의 MD5 해시를 계산 하 고 blob의 콘텐츠-MD5 속성으로 저장 합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

SAS 토큰을 사용 하 여 단일 파일을 업로드 합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS 토큰과 파이프 (블록 blob만)를 사용 하 여 단일 파일을 업로드 합니다.
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

SAS 토큰을 사용 하 여 전체 디렉터리를 업로드 합니다.
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

또는

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

SAS 토큰 및 와일드 카드 (*) 문자를 사용 하 여 파일 집합을 업로드 합니다.
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

SAS 토큰 및 와일드 카드 (*) 문자를 사용 하 여 파일 및 디렉터리를 업로드 합니다.

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

OAuth 인증을 사용 하 여 단일 파일을 다운로드 합니다. AzCopy에 아직 로그인 하지 않은 경우 `azcopy login` 다음 명령을 실행 하기 전에 명령을 실행 합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

SAS 토큰을 사용 하 여 단일 파일을 다운로드 합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

SAS 토큰을 사용 하 여 단일 파일을 다운로드 한 다음 출력을 파일로 파이프 합니다 (블록 blob에만 해당).
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

SAS 토큰을 사용 하 여 전체 디렉터리를 다운로드 합니다.
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Url에 와일드 카드 문자 (*)를 사용 하는 방법에 대 한 참고 사항:

URL에 와일드 카드 문자를 사용 하는 데는 두 가지 방법이 지원 됩니다. 

- URL의 마지막 슬래시 (/) 바로 다음에 하나를 사용할 수 있습니다. 와일드 카드 문자를 사용 하면 디렉터리에 있는 모든 파일을 하위 디렉터리에 배치 하지 않고 대상에 직접 복사 합니다. 

- URL이 blob이 아닌 컨테이너를 참조 하는 한 컨테이너 이름에 와일드 카드 문자를 사용할 수도 있습니다. 이 방법을 사용 하 여 컨테이너의 하위 집합에서 파일을 가져올 수 있습니다. 

포함 하는 디렉터리 자체를 복사 하지 않고 디렉터리의 콘텐츠를 다운로드 합니다.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

전체 저장소 계정을 다운로드 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

컨테이너 이름에 와일드 카드 기호 (*)를 사용 하 여 저장소 계정 내에서 컨테이너의 하위 집합을 다운로드 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

SAS 토큰을 사용 하 여 단일 blob을 다른 blob에 복사 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS 토큰과 인증 토큰을 사용 하 여 단일 blob을 다른 blob에 복사 합니다. 원본 계정 URL의 끝에 SAS 토큰을 사용 해야 하지만 명령을 사용 하 여 AzCopy에 로그인 하는 경우 대상 계정이 필요 하지 않습니다 `azcopy login` . 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

SAS 토큰을 사용 하 여 blob 가상 디렉터리 하나를 다른 blob 가상 디렉터리에 복사 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

SAS 토큰을 사용 하 여 모든 blob 컨테이너, 디렉터리 및 blob을 저장소 계정에서 다른 계정으로 복사 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

액세스 키 및 SAS 토큰을 사용 하 여 AWS (Amazon Web Services) s 3에서 Blob Storage 단일 개체를 복사 합니다. 먼저 환경 변수를 설정 `AWS_ACCESS_KEY_ID` 하 고 `AWS_SECRET_ACCESS_KEY` AWS S3 원본을 설정 합니다.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

액세스 키 및 SAS 토큰을 사용 하 여 AWS s 3에서 전체 디렉터리를 Blob Storage에 복사 합니다. 먼저 환경 변수를 설정 `AWS_ACCESS_KEY_ID` 하 고 `AWS_SECRET_ACCESS_KEY` AWS S3 원본을 설정 합니다.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  [폴더] 자리 표시자에 대 한 자세한 내용은를 참조 하십시오 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html .

액세스 키 및 SAS 토큰을 사용 하 여 Blob Storage Amazon Web Services (AWS)에서 모든 버킷을 복사 합니다. 먼저 환경 변수를 설정 `AWS_ACCESS_KEY_ID` 하 고 `AWS_SECRET_ACCESS_KEY` AWS S3 원본을 설정 합니다.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

액세스 키 및 SAS 토큰을 사용 하 여 Amazon Web Services (AWS) 지역에서 Blob Storage 모든 버킷을 복사 합니다. 먼저 환경 변수를 설정 `AWS_ACCESS_KEY_ID` 하 고 `AWS_SECRET_ACCESS_KEY` AWS S3 원본을 설정 합니다.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

버킷 이름에 와일드 카드 기호 (*)를 사용 하 여 버킷의 하위 집합을 복사 합니다. 이전 예제와 마찬가지로 액세스 키와 SAS 토큰이 필요 합니다. 환경 변수를 설정 하 `AWS_ACCESS_KEY_ID` 고 `AWS_SECRET_ACCESS_KEY` AWS S3 source에 대해를 설정 해야 합니다.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

## <a name="options"></a>옵션

**--백업** 파일 시스템 사용 권한에 관계 없이 AzCopy가 모든 파일을 보고 읽고 모든 사용 권한을 복원 하도록 Windows ' SeBackupPrivilege to 업로드가 나 SeRestorePrivilege for 다운로드를 활성화 합니다. AzCopy를 실행 하는 계정에는이 권한이 이미 있어야 합니다. 예를 들어 관리자 권한이 있거나 그룹의 멤버 여야 `Backup Operators` 합니다. 이 플래그는 계정에 이미 있는 권한을 활성화 합니다.

**--blob 형식** 문자열은 대상에서 blob의 유형을 정의 합니다. 이는 blob을 업로드 하 고 계정 간에 복사할 때 사용 됩니다 (기본값 `Detect` ). 유효한 값은 `Detect`, `BlockBlob`, `PageBlob`및 `AppendBlob`입니다. 계정 간에 복사 하는 경우 값이 이면 `Detect` AzCopy에서 원본 blob의 유형을 사용 하 여 대상 blob의 유형을 결정 합니다. 파일을 업로드할 때는 파일이 `Detect` 파일 확장명을 기반으로 하는 VHD 또는 VHDX 파일 인지 여부를 확인 합니다. 파일이 VHD 또는 VHDX 파일 에테르 스코프 경우 AzCopy는 해당 파일을 페이지 blob으로 처리 합니다. (기본 "검색")

**--블록-blob** 계층 문자열 업로드 블록 blob는이 blob 계층을 사용 하 여 Azure Storage 합니다. (기본 "없음")

**--블록 크기-mb** 부동 소수점 Azure Storage에 업로드 하 고 Azure Storage에서 다운로드 하는 경우이 블록 크기 (MiB에 지정)를 사용 합니다. 기본값은 파일 크기에 따라 자동으로 계산 됩니다. 소수 부분을 사용할 수 있습니다 (예: 0.25).

**--cache 컨트롤** 문자열이 cache-control 헤더를 설정 합니다. 다운로드 시 반환 됩니다.

**--check length** 전송 후 대상에서 파일의 길이를 확인 합니다. 원본과 대상이 일치 하지 않는 경우 전송이 실패 한 것으로 표시 됩니다. (기본값은 `true` )

**--확인란을 선택** 하면 md5 문자열을 다운로드할 때 유효성을 검사 하는 방법을 지정 합니다. 다운로드 하는 경우에만 사용할 수 있습니다. 사용할 수 있는 옵션은 `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` 입니다. (기본값 `FailIfDifferent` ) (기본값 "FailIfDifferent")

**--콘텐츠** 처리 문자열이 콘텐츠 처리 헤더를 설정 합니다. 다운로드 시 반환 됩니다.

**--콘텐츠** 인코딩 문자열이 콘텐츠 인코딩 헤더를 설정 합니다. 다운로드 시 반환 됩니다.

**--content-type** 문자열이 content-type 헤더를 설정 합니다. 다운로드 시 반환 됩니다.

**--content 형식** 문자열은 파일의 콘텐츠 형식을 지정 합니다. -추측-mime 형식이 없음을 의미 합니다. 다운로드 시 반환 됩니다.

**--압축 풀기** 다운로드 시 파일의 압축을 자동으로 풀고 (콘텐츠 인코딩 시 압축 된 것으로 표시 된 경우). 지원 되는 콘텐츠 인코딩 값은 `gzip` 및 `deflate` 입니다. 또는의 파일 확장명은 `.gz` / `.gzip` `.zz` 필요 하지 않지만 존재 하는 경우 제거 됩니다.

**--exclude 특성** 문자열 (Windows에만 해당)은 특성이 특성 목록과 일치 하는 파일을 제외 합니다. 예: A; 삭제 &

**--exclude-blob 형식** 문자열은 필요에 따라 `BlockBlob` /  `PageBlob` /  `AppendBlob` 컨테이너 또는 계정에서 blob을 복사할 때 제외할 blob 유형 ()을 지정 합니다. 비 Azure 서비스에서 서비스로 데이터를 복사 하는 데는이 플래그를 사용할 수 없습니다. 둘 이상의 blob는로 구분 해야 합니다 `;` . 

**--제외-경로 문자열은** 복사할 때 이러한 경로를 제외 합니다. 이 옵션은 와일드 카드 문자 (*)를 지원 하지 않습니다. 상대 경로 접두사 (예:)를 확인 `myFolder;myFolder/subDirName/file.pdf` 합니다. 계정 순회와 함께 사용 하는 경우 경로는 컨테이너 이름을 포함 하지 않습니다.

**--exclude-패턴 문자열은** 복사할 때 이러한 파일을 제외 합니다. 이 옵션은 와일드 카드 문자 (*)를 지원 합니다.

**--팔 로우-symlink**  로컬 파일 시스템에서 업로드할 때 기호화 된 링크를 따르세요.

**--force-읽기** 전용 Windows 또는 Azure Files에서 기존 파일을 덮어쓸 때 기존 파일에 읽기 전용 특성이 설정 되어 있는 경우에도 강제로 덮어쓰도록 합니다.

**--from** 문자열은 필요에 따라 원본 대상 조합을 지정 합니다. 예: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--**  복사에 대 한 도움말 도움말입니다.

**--include-after** 문자열에는 지정 된 날짜/시간 이후에 수정 된 파일만 포함 됩니다. 값은 ISO8601 형식 이어야 합니다. 표준 시간대가 지정 되지 않은 경우이 값은 AzCopy를 실행 하는 컴퓨터의 로컬 표준 시간대에 있는 것으로 간주 됩니다. 예를 들어 `2020-08-19T15:04:00Z` UTC 시간 또는 `2020-08-19` 현지 표준 시간대의 자정 (00:00)에 대 한입니다. AzCopy 10.5에서와 같이이 플래그는 폴더가 아닌 파일에만 적용 되므로 또는와 함께이 플래그를 사용 하는 경우 폴더 속성이 복사 되지 않습니다 `--preserve-smb-info` `--preserve-smb-permissions` .

**--include** 특성 문자열 (Windows에만 해당)은 특성 목록과 일치 하는 특성을 가진 파일을 포함 합니다. 예: A; 삭제 &

**--include-path** 문자열은 복사할 때 이러한 경로만 포함 합니다. 이 옵션은 와일드 카드 문자 (*)를 지원 하지 않습니다. 상대 경로 접두사 (예:)를 확인 `myFolder;myFolder/subDirName/file.pdf` 합니다.

**--include-패턴** 문자열은 복사할 때 이러한 파일만 포함 합니다. 이 옵션은 와일드 카드 문자 (*)를 지원 합니다. 를 사용 하 여 파일을 구분 `;` 합니다.

**--버전 목록** 문자열은 각 버전 id가 별도의 줄에 나열 되는 파일을 지정 합니다. 원본이 단일 blob을 가리켜야 하며이 플래그를 사용 하 여 파일에 지정 된 모든 버전 id가 원본 blob에만 속해야 합니다. AzCopy는 제공 된 대상 폴더에 지정 된 버전을 다운로드 합니다. 자세한 내용은 [이전 버전의 Blob 다운로드](storage-use-azcopy-blobs.md#download-previous-versions-of-a-blob)를 참조 하세요.

**--로그 수준** 문자열은 로그 파일에 대 한 로그의 자세한 정도, 사용 가능한 수준: 정보 (모든 요청/응답), 경고 (저속 응답), 오류 (실패 한 요청만) 및 없음 (출력 로그 없음)을 정의 합니다. (기본값 `INFO` ). 

**--** 이러한 키-값 쌍을 메타 데이터로 Azure Storage에 대 한 메타 데이터 문자열 업로드입니다.

**--추측-mime 형식**  AzCopy에서 파일의 확장명 또는 내용을 기준으로 콘텐츠 형식을 검색 하지 못하도록 합니다.

**--** 이 플래그가 true로 설정 된 경우 문자열을 덮어써서 destination은 충돌 하는 파일 및 Blob을 덮어씁니다. (기본값 `true` ) 가능한 값은,, `true` `false` `prompt` 및 `ifSourceNewer` 입니다. 폴더를 지 원하는 대상의 경우 충돌 하는 폴더 수준 속성을 덮어씁니다 .이 플래그는 `true` 메시지에 긍정 응답이 제공 되는 경우 또는입니다. (기본값 "true")

**--** 이 blob 계층을 사용 하 여 Azure Storage에 페이지 blob 계층 문자열을 업로드 합니다. (기본값 `None` ). (기본 "없음")

**--유지-마지막-수정 시간**  Destination이 파일 시스템 인 경우에만 사용할 수 있습니다.

**--소유자 유지**    는 다운로드에만 적용 되 고가 사용 되는 경우에만 적용 `--preserve-smb-permissions` 됩니다. True (기본값) 이면 다운로드 시 파일 소유자 및 그룹이 유지 됩니다. False로 설정 된 경우 `--preserve-smb-permissions` 는 여전히 acl을 유지 하지만 소유자 및 그룹은 AzCopy를 실행 하는 사용자 (기본값 true)를 기반으로 합니다.

**--smb-정보를 유지**   합니다.   False 이면 기본적으로 False입니다. SMB 인식 리소스 (Windows 및 Azure Files) 간에 SMB 속성 정보 (마지막으로 쓴 시간, 만든 시간, 특성 비트)를 유지 합니다. Azure Files에서 지 원하는 특성 비트만 전송 됩니다. 다른 모든 항목은 무시 됩니다. 이 플래그는 파일 전용 필터가 지정 된 경우를 제외 하 고 파일 및 폴더에 모두 적용 됩니다 (예: include-패턴). 폴더에 대해 전송 되는 정보는 폴더에 대해 유지 되지 않는 마지막 쓰기 시간을 제외 하 고 파일의 경우와 동일 합니다.

**--smb-권한 유지**   False 이면 기본적으로 False입니다. 인식 리소스 (Windows 및 Azure Files) 사이에서 SMB Acl을 유지 합니다. 다운로드를 위해 `--backup` 새 소유자가 AzCopy를 실행 하는 사용자가 아닌 경우에도 복원 하는 플래그를 사용 해야 합니다. 이 플래그는 파일 전용 필터가 지정 된 경우를 제외 하 고 파일 및 폴더에 모두 적용 됩니다 (예: `include-pattern` ).

**--입력-md5**    각 파일의 MD5 해시를 만들고 대상 blob 또는 파일의 콘텐츠-MD5 속성으로 해시를 저장 합니다. 기본적으로 해시가 생성 되지 않습니다. 업로드할 때만 사용할 수 있습니다.

**--recursive**    로컬 파일 시스템에서 업로드할 때 하위 디렉터리를 재귀적으로 확인 합니다.

**--s2s-검색-소스-변경 됨**   읽을 때 원본 파일/b a s e 파일의 변경 여부를 검색 합니다. (이 매개 변수는 서비스 간 복사본에만 적용 됩니다. 해당 확인은 업로드 및 다운로드에 대해 영구적으로 설정 되어 있기 때문입니다.)

**--s2s-핸들-잘못** 된 메타 데이터 문자열은 잘못 된 메타 데이터 키를 처리 하는 방법을 지정 합니다. 사용 가능한 옵션: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (기본값 `ExcludeIfInvalid` ). (기본 "ExcludeIfInvalid")

**--s2s-보존-액세스 계층**   서비스를 복사 하는 동안 액세스 계층을 유지 합니다. 대상 저장소 계정에서 액세스 계층 설정을 지원 하도록 하려면 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) 을 참조 하세요. 액세스 계층 설정이 지원 되지 않는 경우에는 s2sPreserveAccessTier = false를 사용 하 여 액세스 계층 복사를 무시 합니다. 기본값은 true입니다.  (기본값 true)

**--s2s-preserve-속성**   서비스에서 서비스를 복사 하는 동안 전체 속성을 유지 합니다. AWS S3 및 Azure 파일 비 단일 파일 원본의 경우 목록 작업에서 개체 및 파일의 전체 속성을 반환 하지 않습니다. 전체 속성을 유지 하려면 AzCopy는 개체 또는 파일당 하나의 추가 요청을 전송 해야 합니다. (기본값 true)

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

**--0mbps float**   전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.

**--output-** 명령 출력의 문자열 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 `text`입니다. (기본 "텍스트")

**--trusted-microsoft-접미사** 문자열 Azure Active Directory 로그인 토큰이 전송 될 수 있는 추가 도메인 접미사를 지정 합니다.  기본값은 `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`입니다. 여기에 나열 된 Any는 기본값에 추가 됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치 해야 합니다. 여러 항목을 세미콜론으로 구분 합니다.

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
