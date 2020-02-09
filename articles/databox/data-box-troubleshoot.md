---
title: Azure Data Box에 대한 문제 해결 Azure Data Box Heavy | Microsoft Docs
description: 이러한 장치에 데이터를 복사할 때 Azure Data Box 및 Azure Data Box Heavy에 표시 되는 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 83f6f7c7f8cd5155669f12fd6e426f86ef1c7baa
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848504"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 및 Azure Data Box Heavy 관련 된 문제 해결

이 문서에서는 Azure Data Box 또는 Azure Data Box Heavy를 사용할 때 나타날 수 있는 문제를 해결 하는 방법에 대한 정보를 자세히 설명 합니다. 이 문서에는 데이터를 Data Box 복사 하거나 Data Box에서 데이터를 업로드할 때 표시 되는 가능한 오류 목록이 포함 되어 있습니다.

## <a name="error-classes"></a>오류 클래스

Data Box 및 Data Box Heavy의 오류는 다음과 같이 요약 됩니다.

| 오류 범주 *        | 설명        | 권장 조치    |
|----------------------------------------------|---------|--------------------------------------|
| 컨테이너 또는 공유 이름 | 컨테이너 또는 공유 이름은 Azure 명명 규칙을 따르지 않습니다.  |오류 목록을 다운로드 합니다. <br> 컨테이너 또는 공유의 이름을 바꿉니다. [자세히 알아보기](#container-or-share-name-errors).  |
| 컨테이너 또는 공유 크기 제한 | 컨테이너 또는 공유의 총 데이터가 Azure 제한을 초과 합니다.   |오류 목록을 다운로드 합니다. <br> 컨테이너 또는 공유의 전체 데이터를 줄입니다. [자세히 알아보기](#container-or-share-size-limit-errors).|
| 개체 또는 파일 크기 제한 | 컨테이너 또는 공유의 개체 또는 파일이 Azure 제한을 초과 합니다.|오류 목록을 다운로드 합니다. <br> 컨테이너 또는 공유에서 파일 크기를 줄입니다. [자세히 알아보기](#object-or-file-size-limit-errors). |    
| 데이터 또는 파일 형식 | 데이터 형식 또는 파일 형식은 지원 되지 않습니다. |오류 목록을 다운로드 합니다. <br> 페이지 blob 또는 managed disks의 경우 데이터의 512 바이트를 정렬 하 고 미리 만든 폴더에 복사 해야 합니다. [자세히 알아보기](#data-or-file-type-errors). |
| 중요 하지 않은 blob 또는 파일 오류  | Blob 또는 파일 이름이 Azure 명명 규칙을 따르지 않거나 파일 형식이 지원 되지 않습니다. | 이러한 blob 또는 파일은 복사 되지 않거나 이름이 변경 될 수 있습니다. [이러한 오류를 해결 하는 방법을 알아봅니다](#non-critical-blob-or-file-errors). |

\*처음 네 개의 오류 범주는 심각한 오류 이며 제공 준비를 진행 하기 전에 수정 해야 합니다.


## <a name="container-or-share-name-errors"></a>컨테이너 또는 공유 이름 오류

컨테이너 및 공유 이름과 관련 된 오류입니다.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**오류 설명:** 컨테이너 또는 공유 이름은 3자~63자여야 합니다. 

**권장 해결 방법:** 데이터를 복사한 Data Box 또는 Data Box Heavy 공유 (SMB/NFS) 아래의 폴더는 저장소 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 하 여 문제가 있는 폴더 이름을 확인 합니다.
- Data Box 또는 Data Box Heavy 공유에서 폴더 이름을 변경 하 여 다음을 확인 합니다.

    - 이름은 3 ~ 007e; 63 자 사이입니다.
    - 이름에는 문자, 숫자 및 하이픈만 사용할 수 있습니다.
    - 이름은 하이픈으로 시작 하거나 끝날 수 없습니다.
    - 이름에는 하이픈을 연속 해 서 사용할 수 없습니다.
    - 유효한 이름 예: `my-folder-1`,`my-really-extra-long-folder-111`
    - 잘못 된 이름의 `my-folder_1`예: `--myfolder`, `my`,, `myfolder--`,`myfolder!`

    자세한 내용은 [컨테이너 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)에 대한 Azure 명명 규칙을 참조 하세요.


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**오류 설명:** 컨테이너 또는 공유 이름은 문자, 숫자 또는 하이픈만으로 구성되어야 합니다.

**권장 해결 방법:** 데이터를 복사한 Data Box 또는 Data Box Heavy 공유 (SMB/NFS) 아래의 폴더는 저장소 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 하 여 문제가 있는 폴더 이름을 확인 합니다.
- Data Box 또는 Data Box Heavy 공유에서 폴더 이름을 변경 하 여 다음을 확인 합니다.

    - 이름은 3 ~ 007e; 63 자 사이입니다.
    - 이름에는 문자, 숫자 및 하이픈만 사용할 수 있습니다.
    - 이름은 하이픈으로 시작 하거나 끝날 수 없습니다.
    - 이름에는 하이픈을 연속 해 서 사용할 수 없습니다.
    - 유효한 이름 예: `my-folder-1`,`my-really-extra-long-folder-111`
    - 잘못 된 이름의 `my-folder_1`예: `--myfolder`, `my`,, `myfolder--`,`myfolder!`

    자세한 내용은 [컨테이너 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)에 대한 Azure 명명 규칙을 참조 하세요.

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**오류 설명:** 컨테이너 이름 및 공유 이름은 하이픈으로 시작 하거나 끝날 수 없으며 하이픈을 연속 해 서 사용할 수 없습니다.

**권장 해결 방법:** 데이터를 복사한 Data Box 또는 Data Box Heavy 공유 (SMB/NFS) 아래의 폴더는 저장소 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 하 여 문제가 있는 폴더 이름을 확인 합니다.
- Data Box 또는 Data Box Heavy 공유에서 폴더 이름을 변경 하 여 다음을 확인 합니다.

    - 이름은 3 ~ 007e; 63 자 사이입니다.
    - 이름에는 문자, 숫자 및 하이픈만 사용할 수 있습니다.
    - 이름은 하이픈으로 시작 하거나 끝날 수 없습니다.
    - 이름에는 하이픈을 연속 해 서 사용할 수 없습니다.
    - 유효한 이름 예: `my-folder-1`,`my-really-extra-long-folder-111`
    - 잘못 된 이름의 `my-folder_1`예: `--myfolder`, `my`,, `myfolder--`,`myfolder!`

    자세한 내용은 [컨테이너 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)에 대한 Azure 명명 규칙을 참조 하세요.

## <a name="container-or-share-size-limit-errors"></a>컨테이너 또는 공유 크기 제한 오류

이러한 오류는 컨테이너 또는 공유에서 허용 되는 데이터의 크기를 초과 하는 데이터와 관련 된 오류입니다.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**오류 설명:** Azure 파일 공유는 공유를 5TB의 데이터로 제한 합니다. 일부 공유에 대해이 제한을 초과 했습니다.

**권장 해결 방법:** 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.

오류 로그에서이 문제가 있는 폴더를 확인 하 고 해당 폴더의 파일이 5TB 미만 인지 확인 합니다.


## <a name="object-or-file-size-limit-errors"></a>개체 또는 파일 크기 제한 오류

이러한 오류는 개체의 최대 크기 또는 Azure에서 허용 되는 파일을 초과 하는 데이터와 관련 된 오류입니다. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**오류 설명:** 파일 크기가 업로드할 수 있는 최대 파일 크기를 초과합니다.

**권장 해결 방법:** Blob 또는 파일 크기가 업로드에 허용 된 최대한도를 초과 합니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.
- Blob 및 파일 크기가 Azure 개체 크기 제한을 초과 하지 않는지 확인 합니다.

## <a name="data-or-file-type-errors"></a>데이터 또는 파일 형식 오류

이러한 오류는 지원 되지 않는 파일 형식 또는 컨테이너 또는 공유에 있는 데이터 형식과 관련 된 오류입니다. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**오류 설명:** Blob 또는 파일이 잘못 정렬되었습니다.

**권장 해결 방법:** Data Box 또는 Data Box Heavy의 페이지 blob 공유는 512 바이트 정렬 된 파일만 지원 합니다 (예: VHD/VHDX). 페이지 blob 공유에 복사 된 데이터는 페이지 blob으로 Azure에 업로드 됩니다.

페이지 blob 공유에서 비 VHD/VHDX 데이터를 제거 합니다. 블록 blob 또는 Azure files for generic data에 대한 공유를 사용할 수 있습니다.

자세한 내용은 [페이지 Blob 개요](../storage/blobs/storage-blob-pageblob-overview.md)를 참조 하세요.

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**오류 설명:** 지원 되지 않는 파일 형식이 관리 디스크 공유에 있습니다. 고정 된 Vhd만 허용 됩니다.

**권장 해결 방법:**

- 고정 Vhd만 업로드 하 여 관리 디스크를 만들어야 합니다.
- VHDX 파일 또는 **동적** 및 **차이점 보관용** vhd는 지원 되지 않습니다.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**오류 설명:** 디렉터리는 관리 디스크에 대한 기존 폴더에는 허용 되지 않습니다. 이러한 폴더에는 고정 Vhd만 사용할 수 있습니다.

**권장 해결 방법:** 관리 디스크의 경우 각 공유 내에서 저장소 계정의 컨테이너에 해당 하는 다음 3 개의 폴더가 만들어집니다. 프리미엄 SSD, 표준 HDD 및 표준 SSD. 이러한 폴더는 관리 디스크의 성능 계층에 해당 합니다.

- 이러한 기존 폴더 중 하나에 페이지 blob 데이터 (Vhd)를 복사 하는지 확인 합니다.
- 이러한 기존 폴더에서는 폴더 또는 디렉터리를 사용할 수 없습니다. 기존 폴더 내에서 만든 폴더를 모두 제거 합니다.

자세한 내용은 [managed disks에 복사](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)를 참조 하세요.

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**오류 설명:** 기호화 된 링크는 Linux에서 허용 되지 않습니다. 

**권장 해결 방법:** 기호화 된 링크는 일반적으로 링크, 파이프 및 기타 파일입니다. 링크를 제거 하거나 링크를 확인 하 고 데이터를 복사 합니다.


## <a name="non-critical-blob-or-file-errors"></a>중요 하지 않은 blob 또는 파일 오류

다음 섹션에서는 데이터 복사 중에 표시 되는 blob, 파일 또는 컨테이너의 이름과 관련 된 모든 중요 하지 않은 오류를 요약 하 여 설명 합니다. 이러한 오류가 발생 하는 경우 Azure 명명 규칙을 준수 하도록 이름이 수정 됩니다. 데이터 업로드에 대한 해당 주문 상태가 **경고와 함께 완료**됩니다.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**오류 설명:** Blob 또는 파일 이름에 지원 되지 않는 제어 문자가 포함 되어 있습니다.

**권장 해결 방법:** 복사한 blob 또는 파일에 지원 되지 않는 문자가 포함 된 이름이 포함 되어 있습니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.
파일을 제거 하거나 이름을 변경 하 여 지원 되지 않는 문자를 제거 합니다.

자세한 내용은 [blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)에 대한 Azure 명명 규칙을 참조 하세요.

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**오류 설명:** Blob 또는 파일 이름에 잘못 된 문자가 포함 되어 있습니다.

**권장 해결 방법:** 복사한 blob 또는 파일에 지원 되지 않는 문자가 포함 된 이름이 포함 되어 있습니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.
파일을 제거 하거나 이름을 변경 하 여 지원 되지 않는 문자를 제거 합니다.

자세한 내용은 [blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)에 대한 Azure 명명 규칙을 참조 하세요.


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**오류 설명:** Blob 또는 파일 이름이 잘못 된 문자로 끝납니다.

**권장 해결 방법:** 복사한 blob 또는 파일에 지원 되지 않는 문자가 포함 된 이름이 포함 되어 있습니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.
파일을 제거 하거나 이름을 변경 하 여 지원 되지 않는 문자를 제거 합니다.

자세한 내용은 [blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)에 대한 Azure 명명 규칙을 참조 하세요.


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**오류 설명:** Blob 또는 파일 이름에 너무 많은 경로 세그먼트가 포함 되어 있습니다.

**권장 해결 방법:** 복사한 blob 또는 파일이 최대 경로 세그먼트 수를 초과 합니다. 경로 세그먼트는 연속 구분 기호 문자 사이의 문자열입니다 (예: 슬래시/).

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.
- [Blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름이](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 준수 하는지 확인 합니다.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**오류 설명:** Blob 또는 파일 이름이 너무 깁니다.

**권장 해결 방법:** Blob 또는 파일 이름이 최대 길이를 초과 합니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.
- Blob 이름은 1024 자를 초과 하면 안 됩니다.
- 이름이 1024 자를 초과 하지 않도록 blob 또는 파일을 제거 하거나 이름을 바꿉니다.

자세한 내용은 blob 이름 및 파일 이름에 대한 Azure 명명 규칙을 참조 하세요.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**오류 설명:** Blob 또는 파일의 세그먼트 중 하나가 너무 깁니다.

**권장 해결 방법:** Blob 또는 파일 이름에 있는 경로 세그먼트 중 하나가 최대 문자 수를 초과 합니다. 경로 세그먼트는 연속 구분 기호 문자 사이의 문자열입니다 (예: 슬래시/).

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드 하 고 검토 합니다.
- [Blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름이](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 준수 하는지 확인 합니다.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**오류 설명:** 관리 디스크 공유에 잘못 된 컨테이너 이름이 지정 되었습니다.

**권장 해결 방법:** 관리 디스크의 경우 각 공유 내에서 저장소 계정의 컨테이너에 해당 하는 다음과 같은 폴더가 만들어집니다. 프리미엄 SSD, 표준 HDD 및 표준 SSD. 이러한 폴더는 관리 디스크의 성능 계층에 해당 합니다.

- 이러한 기존 폴더 중 하나에 페이지 blob 데이터 (Vhd)를 복사 하는지 확인 합니다. 이러한 기존 컨테이너의 데이터만 Azure에 업로드 됩니다.
- 프리미엄 SSD, 표준 HDD 및 표준 SSD와 같은 수준에서 만든 다른 폴더는 유효한 성능 계층에 해당 하지 않으며 사용할 수 없습니다.
- 성능 계층 외부에서 만든 파일 또는 폴더를 제거 합니다.

자세한 내용은 [managed disks에 복사](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [Data Box Blob storage 시스템 요구 사항](data-box-system-requirements-rest.md)에 대해 알아봅니다.
