---
title: azcopy remove | Microsoft Docs
description: 이 문서에서는 azcopy remove 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d79b647d216fe28241e5891def574ab598304828
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078223"
---
# <a name="azcopy-remove"></a>azcopy remove

Azure 저장소 계정에서 blob 또는 파일을 삭제 합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예

SAS 토큰을 사용 하 여 단일 blob을 제거 합니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS 토큰을 사용 하 여 전체 가상 디렉터리를 제거 합니다.
 
```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

가상 디렉터리 내의 blob만 제거 하 고 해당 하위 디렉터리 내의 하위 디렉터리 또는 blob은 제거 하지 않습니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

가상 디렉터리에서 blob의 하위 집합을 제거 합니다 (예: jpg 및 pdf 파일만 제거 또는 blob 이름이 인 경우 `exactName` ).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

전체 가상 디렉터리를 제거 하지만 범위에서 특정 blob을 제외 합니다 (예: foo로 시작 하는 모든 blob 또는 막대로 끝남).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

상대 경로 (URL 인코딩되지 않음)를 파일에 넣어 특정 blob 및 가상 디렉터리를 제거 합니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
- file content:
    dir1/dir2
    blob1
    blob2
```
계층 네임 스페이스가 있는 Blob Storage 계정에서 단일 파일을 제거 합니다. 포함/제외는 지원 되지 않습니다.

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

계층 네임 스페이스가 있는 Blob Storage 계정에서 단일 디렉터리 제거 (포함/제외는 지원 되지 않음):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>옵션

**--스냅숏 삭제** 문자열은 기본적으로 blob에 스냅숏이 있는 경우 삭제 작업이 실패 합니다. `include`루트 blob 및 모든 해당 스냅숏을 제거 하려면를 지정 하 고, `only` 스냅숏을 제거 하 고 루트 blob은 유지 하도록 지정 합니다.

**--제외-경로 문자열은** 제거할 때 이러한 경로를 제외 합니다. 이 옵션은 와일드 카드 문자 (*)를 지원 하지 않습니다. 상대 경로 접두사를 확인 합니다. `myFolder;myFolder/subDirName/file.pdf`

**--exclude-패턴** 문자열은 이름이 패턴 목록과 일치 하는 파일을 제외 합니다. 예: `*.jpg` ; `*.pdf` ;`exactName`

**--force-읽기**   전용   Azure Files 파일 또는 폴더를 삭제 하는 경우 기존 개체에 읽기 전용 특성이 설정 된 경우에도 삭제 작업을 강제로 수행 합니다.

**--**   제거에 대 한 도움말 도움말입니다.

**--include-경로** 문자열은 제거할 때 이러한 경로만 포함 합니다. 이 옵션은 와일드 카드 문자 (*)를 지원 하지 않습니다. 상대 경로 접두사를 확인 합니다. `myFolder;myFolder/subDirName/file.pdf`

**--include-패턴** 문자열은 이름이 패턴 목록과 일치 하는 파일만 포함 합니다. 예: * `.jpg` ;* `.pdf` ;`exactName`

**--파일 목록** 문자열은 파일의 위치를 정의 합니다. 여기에는 삭제할 파일 및 디렉터리 목록이 포함 됩니다. 상대 경로는 줄 바꿈으로 구분 되어야 하며 경로는 URL 인코딩되지 않아야 합니다. 

**--버전 목록** 문자열은 각 버전 id가 별도의 줄에 나열 되는 파일을 지정 합니다. 원본이 단일 blob을 가리켜야 하며이 플래그를 사용 하 여 파일에 지정 된 모든 버전 id가 원본 blob에만 속해야 합니다. 지정 된 blob의 지정 된 버전 id가 Azure Storage에서 삭제 됩니다. 

**--로그 수준** 문자열 로그 파일의 자세한 정도를 정의 합니다. 사용 가능한 수준에는 `INFO` (모든 요청/응답), `WARNING` (저속 응답), ( `ERROR` 실패 한 요청만) 및 `NONE` (출력 로그 없음)이 포함 됩니다. (기본값 `INFO` ) (기본값 `INFO` )

**--recursive**    디렉터리 간에 동기화 할 때 하위 디렉터리를 재귀적으로 확인 합니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps float|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|
|--신뢰할 수 있는 microsoft 접미사 문자열   |Azure Active Directory 로그인 토큰이 전송 될 수 있는 추가 도메인 접미사를 지정 합니다.  기본값은 '*. core.windows.net;* 입니다. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. 여기에 나열 된 Any는 기본값에 추가 됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치 해야 합니다. 여러 항목을 세미콜론으로 구분 합니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
