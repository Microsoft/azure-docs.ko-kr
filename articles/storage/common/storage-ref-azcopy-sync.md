---
title: azcopy sync | Microsoft Docs
description: 이 문서에서는 azcopy sync 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 16ee2f01e1b7771e71afe49c4b69b1fb39e43f37
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869442"
---
# <a name="azcopy-sync"></a>azcopy sync

원본 위치를 대상 위치에 복제 합니다.

## <a name="synopsis"></a>개요

마지막으로 수정 된 시간이 비교에 사용 됩니다. 대상에서 마지막으로 수정 된 시간이 더 최신인 경우 파일을 건너뜁니다.

지원 되는 쌍은 다음과 같습니다.

- 로컬 <-Azure Blob > (SAS 또는 OAuth 인증 사용 가능)
- Azure Blob <-Azure Blob > (원본에는 SAS가 포함 되거나 공개적으로 액세스할 수 있어야 합니다. 대상에 SAS 또는 OAuth 인증을 사용할 수 있습니다.)
- Azure 파일 <-Azure 파일 > (소스는 SAS를 포함 하거나 공개적으로 액세스할 수 있어야 합니다. 대상에 SAS 인증을 사용 해야 합니다.

Sync 명령은 다음과 같은 여러 가지 방법으로 복사 명령과 다릅니다.

1. 기본적으로 재귀 플래그는 true 이며 sync는 모든 하위 디렉터리를 복사 합니다. 동기화는 재귀 플래그가 false 인 경우 디렉터리 내의 최상위 파일만 복사 합니다.
2. 가상 디렉터리 간에 동기화 할 때 가상 디렉터리와 동일한 이름의 blob이 있는 경우 경로에 후행 슬래시를 추가 합니다 (예제 참조).
3. `deleteDestination`플래그가 true 또는 prompt로 설정 된 경우 sync는 대상에서 원본에 없는 파일 및 blob을 삭제 합니다.

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

### <a name="advanced"></a>고급

파일 확장명을 지정 하지 않으면 AzCopy는 파일 확장명 또는 콘텐츠 (확장명이 지정 되지 않은 경우)에 따라 로컬 디스크에서 업로드할 때 파일의 콘텐츠 형식을 자동으로 검색 합니다.

기본 제공 조회 테이블은 작지만 Unix에서는 다음 이름 중 하나 이상에서 사용할 수 있는 경우 로컬 시스템의 mime. types 파일에 의해 확장 됩니다.

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows에서는 레지스트리에서 MIME 형식이 추출 됩니다.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>예

단일 파일 동기화:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

위와 동일 하지만 파일 콘텐츠의 MD5 해시를 계산 하 고 해당 MD5 해시를 blob의 콘텐츠-MD5 속성으로 저장 합니다. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

하위 디렉터리를 포함 하 여 전체 디렉터리를 동기화 합니다 (기본적으로 재귀적으로 설정 됨).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

또는

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

디렉터리 내의 파일만 동기화 하 고 하위 디렉터리 또는 하위 디렉터리 내의 파일은 동기화 하지 않습니다.

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

디렉터리에 있는 파일의 하위 집합을 동기화 합니다 (예: jpg 및 pdf 파일만, 파일 이름이 인 경우 `exactName` ).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

전체 디렉터리를 동기화 하지만 범위에서 특정 파일을 제외 합니다. 예를 들어 foo로 시작 하는 모든 파일 또는 막대로 끝나는 모든 파일을 제외 합니다.

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

단일 blob 동기화:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

가상 디렉터리 동기화:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Blob과 이름이 같은 가상 디렉터리를 동기화 합니다 (모호성을 위해 후행 슬래시를 경로에 추가).

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure 파일 디렉터리 동기화 (Blob과 동일한 구문):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Include/exclude 플래그를 함께 사용 하는 경우 include 패턴과 일치 하는 파일만 조회 되지만 제외 패턴과 일치 하는 파일만 항상 무시 됩니다.

## <a name="options"></a>옵션

**--블록 크기** 는 Azure Storage에서 Azure Storage 업로드 하거나 다운로드 하는 경우 MiB에 지정 된이 블록 크기 (MiB에 지정)를 사용 합니다. 기본값은 파일 크기에 따라 자동으로 계산 됩니다. 소수 부분을 사용할 수 있습니다 (예: `0.25` ).

**--확인란을 선택** 하면 md5 문자열을 다운로드할 때 유효성을 검사 하는 방법을 지정 합니다. 이 옵션은를 다운로드 하는 경우에만 사용할 수 있습니다. 사용 가능한 값은 `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` 입니다. (기본값 `FailIfDifferent` ). (기본값 `FailIfDifferent` )

**--delete 대상** 문자열은 원본에 존재 하지 않는 대상에서 추가 파일을 삭제할지 여부를 정의 합니다. , 또는로 설정할 수 있습니다 `true` `false` `prompt` . 로 설정 하면 `prompt` 파일 및 blob을 삭제 하기 전에 사용자에 게 문의 하 라는 메시지가 표시 됩니다. (기본값 `false` ). (기본값 `false` )

**--exclude 특성** 문자열 (Windows에만 해당)은 특성이 특성 목록과 일치 하는 파일을 제외 합니다. `A;S;R`

**--제외 경로** 문자열은 대상과 대상과 비교할 때 이러한 경로를 제외 합니다. 이 옵션은 와일드 카드 문자 (*)를 지원 하지 않습니다. 상대 경로 접두사 (예:)를 확인 `myFolder;myFolder/subDirName/file.pdf` 합니다.

**--exclude-패턴** 문자열은 이름이 패턴 목록과 일치 하는 파일을 제외 합니다. `*.jpg;*.pdf;exactName`

**--**    동기화에 대 한 도움말 도움말입니다.

**--include** 특성 문자열 (Windows에만 해당)은 특성 목록과 일치 하는 특성을 가진 파일만 포함 합니다. `A;S;R`

**--include-패턴** 문자열은 이름이 패턴 목록과 일치 하는 파일만 포함 합니다. `*.jpg;*.pdf;exactName`

**--로그 수준** 문자열은 로그 파일에 대 한 로그의 자세한 정도, 사용 가능한 수준 `INFO` (모든 요청 및 응답), `WARNING` (저속 응답), ( `ERROR` 실패 한 요청만) 및 `NONE` (출력 로그 없음)을 정의 합니다. (기본값 `INFO` ). 

**--smb-정보**     를 유지 합니다. False 이면 기본적으로 False입니다.SMB 인식 리소스 (Windows 및 Azure Files) 간에 SMB 속성 정보 (마지막으로 쓴 시간, 만든 시간, 특성 비트)를 유지 합니다.이 플래그는 파일 전용 필터가 지정 된 경우를 제외 하 고 파일 및 폴더에 모두 적용 됩니다 (예: include-패턴).폴더에 대해 전송 되는 정보는 폴더에 대해 유지 되지 않는 마지막 쓰기 시간을 제외 하 고 파일의 경우와 동일 합니다.

**--smb-권한 유지**     False 이면 기본적으로 False입니다.인식 리소스 (Windows 및 Azure Files) 사이에서 SMB Acl을 유지 합니다.이 플래그는 파일 전용 필터가 지정 된 경우를 제외 하 고 파일 및 폴더에 모두 적용 됩니다 (예:  `include-pattern` ).

**--입력-md5**     각 파일의 MD5 해시를 만들고 대상 blob 또는 파일의 콘텐츠-MD5 속성으로 해시를 저장 합니다. 기본적으로 해시가 생성 되지 않습니다. 업로드할 때만 사용할 수 있습니다.

**--recursive** `True` 디렉터리 간에 동기화 할 때 기본적으로 하위 디렉터리를 재귀적으로 확인 합니다.     (기본값 `True` ). 

**--s2s-보존-액세스 계층**  서비스를 복사 하는 동안 액세스 계층을 유지 합니다. 대상 저장소 계정에서 액세스 계층 설정을 지원 하도록 하려면 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) 을 참조 하세요. 액세스 계층 설정이 지원 되지 않는 경우에는 s2sPreserveAccessTier = false를 사용 하 여 액세스 계층 복사를 무시 하세요. (기본값 `true` ). 

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|
|--신뢰할 수 있는 microsoft 접미사 문자열   |Azure Active Directory 로그인 토큰이 전송 될 수 있는 추가 도메인 접미사를 지정 합니다.  기본값은 '*. core.windows.net;* 입니다. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. 여기에 나열 된 Any는 기본값에 추가 됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치 해야 합니다. 여러 항목을 세미콜론으로 구분 합니다.|

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
