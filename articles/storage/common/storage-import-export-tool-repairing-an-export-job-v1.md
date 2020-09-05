---
title: Azure Import/Export 내보내기 작업 복구 - v1 | Microsoft Docs
description: Azure Import/Export 서비스를 사용하여 생성 및 실행된 내보내기 작업을 복구하는 방법을 알아봅니다.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 0731848e1ff187afb6e9f607516dd74b6c16de9b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520484"
---
# <a name="repairing-an-export-job"></a>내보내기 작업 복구
내보내기 작업이 완료된 후에 온-프레미스에서 Microsoft Azure Import/Export 도구를 실행하여 다음을 수행할 수 있습니다.  
  
1.  Azure Import/Export 서비스로 내보낼 수 없는 모든 파일을 다운로드합니다.  
  
2.  드라이브의 파일이 제대로 내보내졌는지 확인합니다.  
  
이 기능을 사용하려면 Azure Storage에 연결되어 있어야 합니다.  
  
가져오기 작업을 복구하는 명령은 **RepairExport**입니다.

## <a name="repairexport-parameters"></a>RepairExport 매개 변수

**RepairExport**와 함께 다음 매개 변수를 지정할 수 있습니다.  
  
|매개 변수|설명|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|필수 사항입니다. 복구의 진행 상황을 추적하고 중단된 복구를 다시 시작할 수 있도록 하는 복구 파일의 경로입니다. 각 드라이브에는 하나의 복구 파일만 있어야 합니다. 지정 된 드라이브에 대 한 복구를 시작할 때 복구 파일에 경로를 전달 합니다 .이 파일은 아직 존재 하지 않습니다. 중단된 복구를 다시 시작하려면 기존 복구 파일의 이름을 제공해야 합니다. 대상 드라이브에 해당 하는 복구 파일을 항상 지정 합니다.|  
|**/logdir:<LogDirectory\>**|선택 사항입니다. 로그 디렉터리입니다. 이 디렉터리에 자세한 로그 파일이 기록됩니다. 로그 디렉터리를 지정하지 않는 경우 현재 디렉터리가 로그 디렉터리로 사용됩니다.|  
|**/d:<TargetDirectory\>**|필수 사항입니다. 유효성을 검사하고 복구할 디렉터리입니다. 이 디렉터리는 일반적으로 내보내기 드라이브의 루트 디렉터리 이지만 내보낸 파일의 복사본이 포함 된 네트워크 파일 공유 일 수도 있습니다.|  
|**/bk:<BitLockerKey\>**|선택 사항입니다. 내보낸 파일이 저장 되는 암호화 된의 잠금을 해제 하려는 경우 BitLocker 키를 지정 합니다.|  
|**/wsn: <StorageAccountName\>**|필수 사항입니다. 내보내기 작업에 대한 스토리지 계정의 이름입니다.|  
|**/StorageAccountKey: <\>**|컨테이너 SAS가 지정 되지 않은 경우에만 **필수** 입니다. 내보내기 작업에 대한 스토리지 계정의 계정 키입니다.|  
|**/csas: <S a s\>**|저장소 계정 키가 지정 되지 않은 경우에만 **필요** 합니다. 내보내기 작업과 연결된 blob에 액세스하기 위한 컨테이너 SAS입니다.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|필수 사항입니다. 드라이브 복사 로그 파일의 경로입니다. 이 파일은 Microsoft Azure Import/Export 서비스에 의해 생성되고 작업과 연결된 Blob Storage에서 다운로드할 수 있습니다. 복사 로그 파일에는 복구 해야 하는 실패 한 blob 또는 파일에 대 한 정보가 포함 되어 있습니다.|  
|**/ManifestFile:<DriveManifestFile\>**|선택 사항입니다. 내보내기 드라이브의 매니페스트 파일 경로입니다. 이 파일은 Microsoft Azure Import/Export 서비스에 의해 생성 되 고 내보내기 드라이브에 저장 됩니다. 필요에 따라 작업과 연결 된 저장소 계정의 blob에서<br /><br /> 내보내기 드라이브에 있는 파일의 내용은 이 파일에 포함된 MD5 해시를 사용해서 확인됩니다. 손상 된 파일이 다운로드 되 고 대상 디렉터리에 다시 작성 됩니다.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>RepairExport 모드를 사용하여 실패한 내보내기 수정  
Azure Import/Export 도구를 사용하여 내보내기에 실패한 파일을 다운로드할 수 있습니다. 복사 로그 파일에는 내보내기에 실패한 파일 목록이 포함됩니다.  
  
내보내기 실패의 원인에는 다음 가능성이 포함됩니다.  
  
-   손상된 드라이브  
  
-   전송 프로세스 중에 변경된 스토리지 계정 키  
  
**RepairExport** 모드에서 이 도구를 실행하려면 컴퓨터로 내보낸 파일이 포함된 드라이브에 먼저 연결해야 합니다. 다음에는 Azure Import/Export 도구를 실행하고, `/d` 매개 변수를 사용하여 해당 드라이브의 경로를 지정합니다. 또한 다운로드한 드라이브의 복사 로그 파일 경로를 지정해야 합니다. 다음 명령줄 예제에서는 도구를 실행 하 여 내보내기에 실패 한 모든 파일을 복구 합니다.  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
다음 예는 blob의 한 블록을 내보내지 못했음을 보여 주는 복사 로그 파일입니다.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
이 복사 로그 파일은 Microsoft Azure Import/Export 서비스가 blob 블록 중 하나를 내보내기 드라이브의 파일에 다운로드하는 동안 오류가 발생했음을 나타냅니다. 파일의 다른 구성 요소는 성공적으로 다운로드되었으며 파일 길이도 올바르게 설정되었습니다. 이 경우 도구는 드라이브의 파일을 열고 스토리지 계정에서 블록을 다운로드한 후 길이가 65536이고 오프셋 65536부터 시작하는 파일 범위에 씁니다.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>RepairExport를 사용하여 드라이브 내용의 유효성 검사  
**RepairExport** 옵션과 함께 Azure 가져오기/내보내기를 사용하여 드라이브의 내용이 올바른지 확인할 수도 있습니다. 각 내보내기 드라이브에 있는 매니페스트 파일에는 드라이브 내용에 대한 MD5가 포함되어 있습니다.  
  
Azure Import/Export 서비스는 내보내기 프로세스 동안 스토리지 계정에 이 매니페스트 파일을 저장할 수도 있습니다. 매니페스트 파일의 위치는 작업이 완료될 때 [Get Job](/rest/api/storageimportexport/jobs) 작업을 통해 사용할 수 있습니다. 드라이브 매니페스트 파일의 형식에 대 한 자세한 내용은 [Import/Export 서비스 매니페스트 파일 형식](storage-import-export-file-format-metadata-and-properties.md)을 참조 하세요.  
  
다음 예제에서는 **/ManifestFile** 및 **/CopyLogFile** 매개 변수를 사용하여 Azure Import/Export 도구를 실행하는 방법을 보여 줍니다.  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
다음 예제에서는 매니페스트 파일을 보여 줍니다.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
복구 프로세스를 마친 후 이 도구는 매니페스트 파일에 참조된 각 파일을 읽고 MD5 해시를 사용하여 파일 무결성을 확인합니다. 위에 있는 매니페스트의 경우 다음 구성 요소가 확인됩니다.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

확인에 실패한 모든 구성 요소는 도구를 통해 다운로드된 후 드라이브의 동일한 파일에 다시 써집니다.  
  
## <a name="next-steps"></a>다음 단계
 
* [Azure Import/Export 도구 설정](storage-import-export-tool-setup-v1.md)   
* [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [복사 로그 파일을 사용하여 작업 상태 검토](storage-import-export-tool-reviewing-job-status-v1.md)   
* [가져오기 작업 복구](storage-import-export-tool-repairing-an-import-job-v1.md)
