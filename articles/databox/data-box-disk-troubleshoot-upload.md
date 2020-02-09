---
title: Azure Data Box 디스크 문제 해결 데이터 로그를 사용 하 여 문제를 업로드 | Microsoft Docs
description: 로그를 사용 하 여 Azure Data Box 디스크에 데이터를 업로드할 때 나타나는 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807521"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Azure Data Box 디스크에 데이터 업로드 문제를 해결 하기 위해 로그 이해

이 문서에서는 Microsoft Azure Data Box 디스크에 적용 되며 Azure에 데이터를 업로드할 때를 표시 하는 문제에 설명 합니다.

## <a name="about-upload-logs"></a>로그 업로드 하는 방법에 대한

데이터는 데이터 센터에서 Azure에 업로드 되 면 `_error.xml` 고 `_verbose.xml` 각 저장소 계정에 대한 파일이 생성 됩니다. 이러한 로그는 데이터를 업로드 하는 데 사용 된 동일한 저장소 계정에 업로드 됩니다. 

로그를 모두 같은 형식 및 Azure Storage 계정에 디스크에서 데이터를 복사 하는 동안 발생 한 이벤트의 XML 설명을 포함 합니다.

오류 로그를 blob 또는 파일을 업로드 하는 동안 오류가 발생 했습니다.에 대한 정보만 포함 하는 반면 모든 blob 또는 파일에 대한 복사 작업의 상태에 대한 전체 정보를 포함 하는 자세한 로그 합니다.

오류 로그는 자세한 로그 있지만 성공적인 작업 필터링으로 동일한 구조를 가집니다.

## <a name="download-logs"></a>로그 다운로드

로그 업로드를 확인 하려면 다음 단계를 수행 합니다.

1. 오류가 있는 경우 모든 데이터를 Azure에 업로드 하는 경우, 포털 진단 로그가 위치한 폴더로 경로 표시 합니다.

    ![포털에서 로그에 연결](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. 로 이동 **waies**합니다.

    ![오류 및 자세한 로그](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

각각의 경우 오류 로그 및 자세한 로그를 볼 수 있습니다. 각 로그를 선택 하 고 로컬 복사본을 다운로드 합니다.

## <a name="sample-upload-logs"></a>샘플 로그 업로드

샘플은 `_verbose.xml` 아래에 표시 됩니다. 이 경우 순서 오류 없이 성공적으로 완료 했습니다.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

같은 순서로의 샘플에 대한는 `_error.xml` 아래에 표시 됩니다.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

샘플은 `_error.xml` 위치 아래에 표시 됩니다 완료 되었으나 오류가 발생 하는 순서입니다. 

오류 파일의이 경우에 `Summary` 섹션과 모든 파일을 포함 하는 다른 섹션에는 오류 수준입니다. 

합니다 `Summary` 포함 된 `ValidationErrors` 및 `CopyErrors`합니다. 이 경우 8 파일 또는 폴더를 Azure에 업로드 된 및 유효성 검사 오류가 없습니다. Azure Storage 계정에 데이터를 복사 하는 경우 5 개의 파일 또는 폴더 업로드 했습니다. 나머지 3 개의 파일 또는 폴더를 Azure 컨테이너 명명 규칙에 따라 변경 했으며 Azure에 성공적으로 업로드 됩니다.

파일 수준 상태는 `BlobStatus` 는 blob을 업로드 하는 데 걸린 모든 작업에 설명 합니다. 이 경우 세 개의 컨테이너를 컨테이너에 대한 Azure 명명 규칙을 사용 하 여 데이터를 복사한 폴더를 준수 하지 않아 바뀝니다. 해당 컨테이너에 업로드 된 blob에 대한 새 컨테이너 이름, Azure에서 blob의 경로, 원래 잘못 된 파일 경로 및 blob 크기가 포함 됩니다.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>데이터 업로드 오류

Azure에 데이터를 업로드 하는 경우 생성 된 오류는 다음 표에 요약 되어 있습니다.

| 오류 코드 | Description                   |
|-------------|------------------------------|
|`None` |  완료 되었습니다.           |
|`Renamed` | Blob을 이름을 바꾸었습니다.   |
|`CompletedWithErrors` | 업로드를 완료 했지만 오류가 발생 합니다. 오류 파일의 세부 정보는 로그 파일에 포함 됩니다.  |
|`Corrupted`|데이터 수집 중에 계산 된 CRC 업로드 중에 계산 된 CRC 일치 하지 않습니다.  |  
|`StorageRequestFailed` | Azure 저장소 요청이 실패 했습니다.   |     
|`LeasePresent` | 이 항목이 임대 및 다른 사용자가 사용 됩니다. |
|`StorageRequestForbidden` |인증 문제로 인해 업로드 하지 못했습니다. |
|`ManagedDiskCreationTerminalFailure` | 관리 되는 디스크로 업로드할 수 없습니다. 페이지 blob으로 파일은 준비 저장소 계정에서 사용할 수 있습니다. Managed disks로 페이지 blob을 수동으로 변환할 수 있습니다.  |
|`DiskConversionNotStartedTierInfoMissing` | VHD 파일을 복사 하 여 precreated 계층 폴더 외부에 있으므로 관리 디스크 생성 되지 않았습니다. 파일 순서를 만드는 동안 지정 된 대로 준비 저장소 계정에 페이지 blob으로 업로드 됩니다. 관리 디스크를 수동으로 변환할 수 있습니다.|
|`InvalidWorkitem` | Azure 명명에 맞지 않습니다 하 고 규칙 제한에 데이터를 업로드 하지 못했습니다.|
|`InvalidPageBlobUploadAsBlockBlob` | 접두사를 사용 하 여 컨테이너에 블록 blob으로 업로드 `databoxdisk-invalid-pb-`합니다.|
|`InvalidAzureFileUploadAsBlockBlob` | 접두사를 사용 하 여 컨테이너에 블록 blob으로 업로드 `databoxdisk-invalid-af`-합니다.|
|`InvalidManagedDiskUploadAsBlockBlob` | 접두사를 사용 하 여 컨테이너에 블록 blob으로 업로드 `databoxdisk-invalid-md`-합니다.|
|`InvalidManagedDiskUploadAsPageBlob` |접두사를 사용 하 여 컨테이너에 페이지 blob으로 업로드 `databoxdisk-invalid-md-`합니다. |
|`MovedToOverflowShare` |원래 공유 크기와 새 공유로 업로드 된 파일에는 최대 Azure 크기 제한을 초과 했습니다. 새 파일 공유 이름에 원래 이름을 붙음 `-2`합니다.   |
|`MovedToDefaultAzureShare` |기본 공유 폴더에 속하지 않았던 업로드 된 파일입니다. 공유 이름은 시작 `databox-`합니다. |
|`ContainerRenamed` |이러한 파일의 컨테이너는 Azure 명명 규칙을 준수 하지 않은 하 고 이름이 바뀝니다. 새 이름을 사용 하 여 시작 `databox-` 원래 이름의 SHA1 해시 붙습니다 및 |
|`ShareRenamed` |이러한 파일에 대한 공유 Azure 명명 규칙을 준수 하지 않은 하 고 이름이 바뀝니다. 새 이름을 사용 하 여 시작 `databox-` 및 원래 이름의 SHA1 해시 붙습니다. |
|`BlobRenamed` |이러한 Azure 명명 규칙을 준수 하지 않은 파일과 이름이 바뀌었습니다. 확인 된 `BlobPath` 새 이름에 대한 필드입니다. |
|`FileRenamed` |이러한 Azure 명명 규칙을 준수 하지 않은 파일과 이름이 바뀌었습니다. 확인 된 `FileStoragePath` 새 이름에 대한 필드입니다. |
|`DiskRenamed` |이러한 Azure 명명 규칙을 준수 하지 않은 파일과 이름이 바뀌었습니다. 확인 된 `BlobPath` 새 이름에 대한 필드입니다. |


## <a name="next-steps"></a>다음 단계

- [Data Box 디스크 문제에 대한 지원 티켓을 열어](data-box-disk-contact-microsoft-support.md)합니다.
