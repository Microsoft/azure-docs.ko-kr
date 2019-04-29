---
title: Azure Import/Export 도구 v1 설정 | Microsoft Docs
description: Azure Import/Export 서비스를 위한 드라이브 준비 및 복구 도구 설정 방법을 알아봅니다. Import/Export 도구 v1을 나타냅니다.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: aafff208b9de2ae485fb84be428cf77f237e022e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477572"
---
# <a name="setting-up-the-azure-importexport-tool"></a>Azure Import/Export 도구 설정
Microsoft Azure Import/Export 도구는 Microsoft Azure Import/Export 서비스에서 사용할 수 있는 드라이브 준비 및 복구 도구입니다. 다음 기능을 위해 이 도구를 사용할 수 있습니다.  
  
-   가져오기 작업을 만들기 전에 이 도구를 사용하여 Windows Azure 데이터 센터에 배송하려는 하드 드라이브에 데이터를 복사할 수 있습니다.  
  
-   가져오기 작업이 완료된 후에는 이 도구를 사용하여 손상되거나 누락되거나 다른 Blob과 충돌한 Blob을 복구할 수 있습니다.  
  
-   완료된 내보내기 작업에서 드라이브를 받은 후에는 이 도구를 사용하여 드라이브에서 손상되거나 누락된 파일을 복구할 수 있습니다.  
  
## <a name="prerequisites"></a>필수 조건  
가져오기 작업을 위해 드라이브를 준비하는 경우 다음과 같은 필수 조건을 충족해야 합니다.  
  
-   활성 Azure 구독이 있어야 합니다.  
  
-   구독에는 가져올 파일을 저장할 수 있는 충분한 공간이 있는 저장소 계정이 있어야 합니다.  
  
-   저장소 계정에 대한 계정 키 중 하나 이상이 필요합니다.  
  
-   Windows 7, Windows Server 2008 R2 또는 최신 Windows 운영 체제가 설치된 컴퓨터("복사 컴퓨터")가 필요합니다.  
  
-   .NET Framework 4가 복사 컴퓨터에 설치되어야 합니다.  
  
-   복사 컴퓨터에서 BitLocker를 사용할 수 있어야 합니다.  
  
-   가져올 데이터가 있는 하나 이상의 드라이브 또는 빈 3.5인치 SATA 하드 드라이브가 복사 컴퓨터에 연결되어 있는 하나 이상의 드라이브가 필요합니다.  
  
-   가져오려는 파일은 네트워크 공유 또는 로컬 하드 드라이브에 상관없이 복사 컴퓨터에서 액세스할 수 있어야 합니다. 
  
부분적으로 실패한 가져오기를 복구하려는 경우 다음이 필요합니다.  
  
- 복사 로그 파일  
  
- 저장소 계정 키입니다.  
  
  부분적으로 실패한 내보내기를 복구하려는 경우 다음이 필요합니다.  
  
- 복사 로그 파일  
  
- 매니페스트 파일(선택 사항)  
  
- 저장소 계정 키입니다.  
  
## <a name="installing-the-azure-importexport-tool"></a>Azure Import/Export 도구 설치  
 Azure Import/Export 도구는 다음 파일로 구성됩니다.  
  
- WAImportExport.exe  
  
- WAImportExport.exe.config  
  
- WAImportExportCore.dll  
  
- WAImportExportRepair.dll  
  
- Microsoft.WindowsAzure.Storage.dll  
  
- Hddid.dll  
  
  이 파일을 작업 디렉터리(예: `c:\WAImportExport`)에 복사합니다. 그런 다음 관리자 모드에서 명령줄 창을 열고 위의 디렉터리를 현재 디렉터리로 설정합니다.  
  
  명령에 대한 도움말을 출력하려면 매개 변수 없이 도구를 실행합니다.  
  
```  
WAImportExport, a client tool for Microsoft Azure Import/Export service. Microsoft (c) 2013, 2014  
  
Copy a Directory:  
    WAImportExport.exe PrepImport  
        /j:<JournalFile> [/logdir:<LogDirectory>] [/id:<SessionId>] [/resumesession]  
        [/abortsession] [/sk:<StorageAccountKey>] [/csas:<ContainerSas>]  
        [/t:<TargetDriveLetter>] [/format] [/silentmode] [/encrypt]  
        [/bk:<BitLockerKey>] [/Disposition:<Disposition>] [/BlobType:<BlobType>]  
        [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]  
        /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory>  
  
Copy a File:  
    WAImportExport.exe PrepImport  
        /j:<JournalFile> [/logdir:<LogDirectory>] [/id:<SessionId>] [/resumesession]  
        [/abortsession] [/sk:<StorageAccountKey>] [/csas:<ContainerSas>]  
        [/t:<TargetDriveLetter>] [/format] [/silentmode] [/encrypt]  
        [/bk:<BitLockerKey>] [/Disposition:<Disposition>] [/BlobType:<BlobType>]  
        [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]  
        /srcfile:<SourceFilePath> /dstblob:<DestinationBlobPath>  
  
Repair a Drive:  
    WAImportExport.exe RepairImport | RepairExport  
        /r:<RepairFile> [/logdir:<LogDirectory>]  
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]  
        /sn:<StorageAccountName> [/sk:<StorageAccountKey> | /csas:<ContainerSas>]  
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]  
        [/PathMapFile:<DrivePathMapFile>]  
  
Preview an Export Job:  
    WAImportExport.exe PreviewExport  
        [/logdir:<LogDirectory>]  
        /sn:<StorageAccountName> [/sk:<StorageAccountKey> | /csas:<ContainerSas>]  
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>  
  
Parameters:  
  
    /j:<JournalFile>  
        - Required. Path to the journal file. Each drive must have one and only one  
          journal file. The journal file corresponding to the target drive must always  
          be specified.  
    /logdir:<LogDirectory>  
        - Optional. The log directory. Verbose log files as well as some temporary  
          files will be written to this directory. If not specified, current directory  
          will be used as the log directory.  
    /id:<SessionId>  
        - Required. The session Id is used to identify a copy session. It is used to  
          ensure accurate recovery of an interrupted copy session. In addition, files  
          that are copied in a copy session are stored in a directory named after the  
          session Id on the target drive.  
    /resumesession  
        - Optional. If the last copy session was terminated abnormally, this parameter  
          can be specified to resume the session.  
    /abortsession  
        - Optional. If the last copy session was terminated abnormally, this parameter  
          can be specified to abort the session.  
    /sn:<StorageAccountName>  
        - Required. Only applicable for RepairImport and RepairExport. The name of  
          the storage account.  
    /sk:<StorageAccountKey>  
        - Optional. The key of the storage account. One of /sk: and /csas: must be  
          specified.  
    /csas:<ContainerSas>  
        - Optional. A container SAS, in format of <ContainerName>?<SasString>, to be  
          used for import the data. One of /sk: and /csas: must be specified.  
    /t:<TargetDriveLetter>  
        - Required. Drive letter of the target drive.  
    /r:<RepairFile>  
        - Required. Only applicable for RepairImport and RepairExport.  
          Path to the file for tracking repair progress. Each drive must have one  
          and only one repair file.  
    /d:<TargetDirectories>  
        - Required. Only applicable for RepairImport and RepairExport.  
          For RepairImport, one or more semicolon-separated directories to repair;  
          For RepairExport, one directory to repair, e.g. root directory of the drive.  
    /format  
        - Optional. If specified, the target drive will be formatted. DO NOT specify  
          this parameter if you do not want to format the drive.  
    /silentmode  
        - Optional. If not specified, the /format parameter will require a confirmation  
          from console before the tool formats the drive. If this parameter is specified,  
          not confirmation will be given for formatting the drive.  
    /encrypt  
        - Optional. If specified, the target drive will be encrypted with BitLocker.  
          If the drive has already been encrypted with BitLocker, do not specify this  
          parameter and instead specify the BitLocker key using the "/k" parameter.  
    /bk:<BitLockerKey>  
        - Optional. The current BitLocker key if the drive has already been encrypted  
          with BitLocker.  
    /Disposition:<Disposition>  
        - Optional. Specifies the behavior when a blob with the same path as the one  
          being imported already exists. Valid values are: rename, no-overwrite and  
          overwrite (case-sensitive). If not specified, "rename" will be used as the  
          default value.  
    /BlobType:<BlobType>  
        - Optional. The blob type for the imported blob(s). Valid values are BlockBlob  
          and PageBlob. If not specified, BlockBlob will be used as the default value.  
    /PropertyFile:<PropertyFile>  
        - Optional. Path to the property file for the file(s) to be imported.  
    /MetadataFile:<MetadataFile>  
        - Optional. Path to the metadata file for the file(s) to be imported.  
    /CopyLogFile:<DriveCopyLogFile>  
        - Required. Only applicable for RepairImport and RepairExport. Path to the  
          drive copy log file (verbose or error).  
    /ManifestFile:<DriveManifestFile>  
        - Required. Only applicable for RepairExport. Path to the drive manifest file.  
    /PathMapFile:<DrivePathMapFile>  
        - Optional. Only applicable for RepairImport. Path to the file containing  
          mappings of file paths relative to the drive root to locations of actual files  
          (tab-delimited). When first specified, it will be populated with file paths  
          with empty targets, which means either they are not found in TargetDirectories,  
          access denied, with invalid name, or they exist in multiple directories. The  
          path map file can be manually edited to include the correct target paths and  
          specified again for the tool to resolve the file paths correctly.  
    /ExportBlobListFile:<ExportBlobListFile>  
        - Required. Path to the XML file containing list of blob paths or blob path  
          prefixes for the blobs to be exported. The file format is the same as the  
          blob list blob format in the Put Job operation of the Import/Export service  
          REST API.  
    /DriveSize:<DriveSize>  
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.  
          Note: 1 GB = 1,000,000,000 bytes  
                1 TB = 1,000,000,000,000 bytes  
    /srcdir:<SourceDirectory>  
        - Required. Source directory that contains files to be copied to the  
          target drives.  
    /dstdir:<DestinationBlobVirtualDirectory>  
        - Required. Destination blob virtual directory to which the files will  
          be imported.  
    /srcfile:<SourceFilePath>  
        - Required. Path to the source file to be imported.  
    /dstblob:<DestinationBlobPath>  
        - Required. Destination blob path for the file to be imported.  
    /skipwrite
        - Optional. To skip write process. Used for inplace data drive preparation.
          Be sure to reserve enough space (3 GB per 7TB) for drive manifest file!
Examples:  
  
    Copy a source directory to a drive:  
    WAImportExport.exe PrepImport  
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL  
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:x /format /encrypt /srcdir:d:\movi  
        es\drama /dstdir:movies/drama/  
  
    Copy another directory to the same drive following the above command:  
    WAImportExport.exe PrepImport  
        /j:9WM35C2V.jrn /id:session#2 /srcdir:d:\movies\action /dstdir:movies/action/  
  
    Copy another file to the same drive following the above commands:  
    WAImportExport.exe PrepImport  
        /j:9WM35C2V.jrn /id:session#3 /srcfile:d:\movies\dvd.vhd /dstblob:movies/dvd.vhd /BlobType:PageBlob  
  
    Preview how many 1.5 TB drives are needed for an export job:  
    WAImportExport.exe PreviewExport  
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K  
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml  
        /DriveSize:1.5TB  
  
    Repair an finished import job:  
    WAImportExport.exe RepairImport  
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795  
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94  
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log 

    Skip write process, inplace data drive preparation:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movi
        es\drama /dstdir:movies/drama/ /skipwrite
```  
  
## <a name="next-steps"></a>다음 단계

* [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [내보내기 작업에 대한 드라이브 사용량 미리 보기](../storage-import-export-tool-previewing-drive-usage-export-v1.md)   
* [복사 로그 파일을 사용하여 작업 상태 검토](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [가져오기 작업 복구](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [내보내기 작업 복구](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Azure Import/Export 도구 문제 해결](storage-import-export-tool-troubleshooting-v1.md)
