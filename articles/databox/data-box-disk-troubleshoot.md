---
title: Azure Data Box Disk 문제 해결 | Microsoft Docs
description: Azure Data Box Disk에서 발견된 문제 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805717"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>로그를 사용 하 여 Azure Data Box 디스크의 유효성 검사 문제를 해결 하려면

이 문서에서는 Microsoft Azure Data Box 디스크에 적용 됩니다. 문서에는이 솔루션을 배포 하는 경우를 볼 수 있습니다 유효성 검사 문제를 해결 하는 로그를 사용 하는 방법을 설명 합니다.

## <a name="validation-tool-log-files"></a>유효성 검사 도구 로그 파일

유효성을 검사할 때 사용 하 여 디스크에서 데이터를 [유효성 검사 도구](data-box-disk-deploy-copy-data.md#validate-data), *error.xml* 모든 오류를 기록 하기 위해 생성 됩니다. 로그 파일에는 `Drive:\DataBoxDiskImport\logs` 드라이브의 폴더입니다. 유효성 검사를 실행 하는 경우 오류 로그에 대한 링크가 제공 됩니다.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

유효성 검사에 대한 여러 세션을 실행 하는 경우에 세션당 하나의 오류 로그가 생성 됩니다.

- 다음은 오류 로그의 샘플 데이터를 로드 하는 경우는 `PageBlob` 폴더는 512 바이트 정렬 되지 않습니다. PageBlob에 업로드 된 모든 데이터는 512 바이트 정렬 예를 들어, VHD 또는 VHDX를 이어야 합니다. 이 파일에서 오류에는 `<Errors>` 에 및 경고 `<Warnings>`합니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- 컨테이너 이름을 잘못 된 경우 오류 로그의 샘플 다음과 같습니다. 만든 폴더 `BlockBlob`, `PageBlob`, 또는 `AzureFile` 폴더 디스크에 Azure Storage 계정에 컨테이너가 됩니다. 따라 컨테이너의 이름을 지정 해야 합니다 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)합니다.

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>유효성 검사 도구 오류

에 포함 된 오류를 *error.xml* 해당 권장 조치를 사용 하 여 다음 표에 요약 되어 있습니다.

| 오류 코드| 설명                       | 권장 작업               |
|------------|--------------------------|-----------------------------------|
| `None` | 데이터를 확인 했습니다. | 추가적인 조치가 필요하지 않습니다. |
| `InvalidXmlCharsInPath` |파일 경로 잘못 된 문자는 매니페스트 파일을 만들 수 없습니다. | 계속 하려면 이러한 문자를 제거 합니다.  |
| `OpenFileForReadFailed`| 파일을 처리 하지 못했습니다. 액세스 문제 또는 파일 시스템 손상 때문일 수 있습니다.|오류로 인해 파일을 읽지 못했습니다. 오류 세부 정보는 예외에. |
| `Not512Aligned` | 이 파일이 PageBlob 폴더에 대한 올바른 형식이 아닙니다.| 에 512 바이트 데이터 업로드만 정렬 `PageBlob` 폴더입니다. PageBlob 폴더에서 파일을 제거 하거나 BlockBlob 폴더로 이동 합니다. 유효성 검사 다시 시도 하세요.|
| `InvalidBlobPath` | 파일 경로 Azure Blob 명명 규칙에 따라 클라우드에서 유효한 blob 경로에 매핑되지 않습니다.|파일 경로의 이름을 변경 하려면 Azure 명명 지침을 따릅니다. |
| `EnumerationError` | 유효성 검사에 대한 파일을 열거 하지 못했습니다. |이 오류에 대한 여러 원인이 있을 수 있습니다. 가장 가능성이 높은 원인은 파일에 대한 액세스입니다. |
| `ShareSizeExceeded` | 이 파일 때문에 Azure 파일 공유 크기가 5TB Azure 제한을 초과 합니다.|준수 하는 공유에 있는 데이터의 크기를 줄여 합니다 [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)합니다. 유효성 검사 다시 시도 하세요. |
| `AzureFileSizeExceeded` | Azure 파일 크기 제한을 초과 하는 파일 크기입니다.| 준수 하는 파일 또는 데이터의 크기를 줄여 합니다 [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)합니다. 유효성 검사 다시 시도 하세요.|
| `BlockBlobSizeExceeded` | Azure 블록 Blob 크기 제한을 초과 하는 파일 크기입니다. | 준수 하는 파일 또는 데이터의 크기를 줄여 합니다 [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)합니다. 유효성 검사 다시 시도 하세요. |
| `ManagedDiskSizeExceeded` | Azure Managed Disk 크기 제한을 초과 하는 파일 크기입니다. | 준수 하는 파일 또는 데이터의 크기를 줄여 합니다 [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)합니다. 유효성 검사 다시 시도 하세요. |
| `PageBlobSizeExceeded` | Azure Managed Disk 크기 제한을 초과 하는 파일 크기입니다. | 준수 하는 파일 또는 데이터의 크기를 줄여 합니다 [Azure 개체 크기 제한을](data-box-disk-limits.md#azure-object-size-limits)합니다. 유효성 검사 다시 시도 하세요. |
| `InvalidShareContainerFormat`  |디렉터리 이름을 컨테이너 또는 공유에 대한 Azure 명명 규칙에 맞지 않습니다.         |디스크에서 기존 폴더 아래에 생성 하 고 첫 번째 폴더는 저장소 계정의 컨테이너 됩니다. 이 공유 또는 컨테이너 이름은 Azure 명명 규칙에 맞지 않습니다. 준수 하는 파일 이름 바꾸기 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)합니다. 유효성 검사 다시 시도 하세요.   |
| `InvalidBlobNameFormat` | 파일 경로 Azure Blob 명명 규칙에 따라 클라우드에서 유효한 blob 경로에 매핑되지 않습니다.|준수 하는 파일 이름 바꾸기 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)합니다. 유효성 검사 다시 시도 하세요. |
| `InvalidFileNameFormat` | 파일 경로 Azure 파일 명명 규칙에 따라 클라우드에서 올바른 파일 경로에 매핑되지 않습니다. |준수 하는 파일 이름 바꾸기 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)합니다. 유효성 검사 다시 시도 하세요. |
| `InvalidDiskNameFormat` | 파일 경로 Azure Managed Disk 명명 규칙에 따라 클라우드에서 올바른 디스크 이름으로 매핑되지 않습니다. |준수 하는 파일 이름 바꾸기 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)합니다. 유효성 검사 다시 시도 하세요.       |
| `NotPartOfFileShare` | 업로드 파일의 경로를 잘못 되었습니다. Azure Files의 폴더에 파일을 업로드 합니다.   | 오류에서 파일을 제거 하 고 precreated 폴더에 해당 파일을 업로드 합니다. 유효성 검사 다시 시도 하세요. |
| `NonVhdFileNotSupportedForManagedDisk` | 관리 디스크로 아닌 VHD 파일을 업로드할 수 없습니다. |비-VHD 파일을 제거 `ManagedDisk` 이러한 폴더는 지원 되지 않습니다 또는 이러한 파일을 이동할는 `PageBlob` 폴더입니다. 유효성 검사 다시 시도 하세요. |


## <a name="next-steps"></a>다음 단계

- 문제 해결 [데이터 업로드 오류](data-box-disk-troubleshoot-upload.md)합니다.
