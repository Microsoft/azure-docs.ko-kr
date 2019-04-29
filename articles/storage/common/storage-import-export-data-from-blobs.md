---
title: Azure Import/Export를 사용하여 Azure Blob에서 데이터 내보내기 | Microsoft Docs
description: Azure Portal에서 내보내기 작업을 만들어 Azure Blob에서 데이터를 전송하는 방법을 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: fc02e830953f8612a077fb219c7fef4e86bc3827
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479293"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Azure Import/Export 서비스를 사용하여 Azure Blob Storage에서 데이터 내보내기
이 문서에서는 Azure Import/Export 서비스를 사용하여 Azure Blob Storage에서 많은 양의 데이터를 안전하게 내보내는 방법에 대한 단계별 지침을 제공합니다. 서비스를 사용하려면 빈 드라이브를 Azure 데이터 센터에 배송해야 합니다. 서비스에서 저장소 계정의 데이터를 드라이브로 내보낸 다음, 드라이브를 다시 배송합니다.

## <a name="prerequisites"></a>필수 조건

내보내기 작업을 만들어 Azure Blob Storage에서 데이터를 전송하기 전에 이 서비스에 대한 다음 필수 조건 목록을 신중하게 검토하고 완료해야 합니다. 다음이 필요합니다.

- Import/Export 서비스에 사용할 수 있는 활성 Azure 구독이 있어야 합니다.
- Azure Storage 계정이 하나 이상 있어야 합니다. [Import/Export 서비스에 지원되는 저장소 계정 및 저장소 형식](storage-import-export-requirements.md) 목록을 참조하세요. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](storage-quickstart-create-account.md)(영문)을 참조하세요.
- [지원되는 형식](storage-import-export-requirements.md#supported-disks)에 속한 적절한 개수의 디스크가 있어야 합니다.
- FedEx/DHL 계정이 있습니다. 이외의 FedEx/DHL 운송 업체를 사용 하려는 경우 Azure 데이터 상자 작업 팀에 문의 `adbops@microsoft.com`합니다. 
    - 계정은 유효해야 하고, 잔액이 있어야 하며, 반품 기능이 있어야 합니다.
    - 내보내기 작업의 추적 번호를 생성합니다.
    - 모든 작업에는 별도의 추적 번호가 있어야 합니다. 추적 번호가 동일한 여러 작업은 지원되지 않습니다. 
    - 운송업체 계정이 없는 경우, 다음으로 이동합니다.
        - [FedEX 계정 만들기](https://www.fedex.com/en-us/create-account.html) 또는 
        - [DHL 계정 만들기](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1단계: 내보내기 작업 만들기

다음 단계를 수행하여 Azure Portal에서 내보내기 작업을 만듭니다.

1. https://portal.azure.com/에 로그온합니다.
2. **모든 서비스 > 저장소 > 작업 가져오기/내보내기**로 차례로 이동합니다. 

    ![작업 가져오기/내보내기로 이동](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. **가져오기/내보내기 작업 만들기**를 클릭합니다.

    ![작업 가져오기/내보내기 클릭](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. **기본 사항**에서
    
    - **Azure에서 내보내기**를 선택합니다. 
    - 내보내기 작업에 대한 설명이 포함된 이름을 입력합니다. 선택한 이름을 사용하여 작업 진행 상황을 추적합니다. 
        - 이름에는 소문자, 숫자, 하이픈 및 밑줄만 사용할 수 있습니다.
        - 이름은 문자로 시작해야 하며, 공백은 포함할 수 없습니다. 
    - 구독을 선택합니다.
    - 리소스 그룹을 입력하거나 선택합니다.

        ![기본 사항](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. **작업 세부 정보**에서:

    - 내보낼 데이터가 있는 저장소 계정을 선택합니다. 현재 위치에 가까운 저장소 계정을 사용합니다.
    - 하차 위치는 선택한 저장소 계정의 지역을 기반으로 자동으로 채워집니다. 
    - 저장소 계정에서 빈 드라이브로 내보내려는 Blob 데이터를 지정합니다. 
    - 저장소 계정의 Blob 데이터를 모두 내보내도록 선택합니다(**모두 내보내기**).
    
         ![모두 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - 내보낼 컨테이너와 Blob을 지정할 수 있습니다.
        - **내보낼 Blob을 지정하려면** **같음** 선택기를 사용합니다. 컨테이너 이름으로 시작하는 Blob에 대한 상대 경로를 지정합니다. 루트 컨테이너를 지정하려면 *$root* 를 사용합니다.
        - **접두사로 시작하는 모든 Blob을 지정하려면** **시작 단어** 선택기를 사용합니다. 슬래시('/')로 시작하는 접두사를 지정합니다. 접두사는 컨테이너 이름의 접두사, 완전한 컨테이너 이름 또는 Blob 이름 접두사가 뒤에 오는 완전한 컨테이너 이름일 수 있습니다. 처리 중에 오류가 발생하지 않도록 방지하려면 다음 스크린샷과 같이 유효한 형식의 Blob 경로를 제공해야 합니다. 자세한 내용은 [유효한 Blob 경로의 예](#examples-of-valid-blob-paths)를 참조하세요. 
   
           ![선택한 컨테이너 및 Blob 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - Blob 목록 파일에서 내보낼 수 있습니다.

        ![Blob 목록 파일에서 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > 내보낼 Blob가 데이터 복사 중에 사용되는 경우 Azure Import/Export 서비스는 Blob의 스냅숏을 가져와 해당 스냅숏을 복사합니다.
 

4. **반송 정보**에서:

    - 드롭다운 목록에서 운송업체를 선택합니다. 이외의 FedEx/DHL 운송 업체를 사용 하려는 경우 드롭다운 목록에서 기존 옵션을 선택 합니다. 연락처의 Azure 데이터 상자 작업 팀에서 `adbops@microsoft.com` 사용 하려는 운송 업체에 대 한 정보를 사용 하 여 합니다.
    - 운송업체에서 만든 유효한 운송업체 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다. 
    - 완전하고 유효한 연락처 이름, 전화 번호, 이메일, 주소, 구/군/시, 우편 번호, 시/도 및 국가/지역을 제공합니다.

        > [!TIP] 
        > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.
   
5. **요약**에서:

    - 작업에 대한 세부 정보를 검토합니다.
    - 작업 이름과 Azure로의 배송 디스크에 제공되는 Azure 데이터 센터 배송 주소를 기록합니다. 

        > [!NOTE] 
        > 항상 Azure Portal에 기록된 데이터 센터로 디스크를 보냅니다. 디스크가 잘못된 데이터 센터로 배송되면 작업이 처리되지 않습니다.

    - **확인**을 클릭하여 내보내기 작업 만들기를 완료합니다.

## <a name="step-2-ship-the-drives"></a>2단계: 드라이브 배송

필요한 드라이브 수를 알지 못하는 경우 [드라이브 수를 확인](#check-the-number-of-drives)합니다. 드라이브 수를 알고 있으면 드라이브 배송을 계속 진행합니다.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3단계: 추적 정보를 사용하여 작업 업데이트

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>4단계: 디스크 받기
대시보드에서 작업이 완료되었다고 보고하면 디스크가 배송되고 포털에서 배송 추적 번호를 사용할 수 있습니다.

1. 내보낸 데이터가 있는 드라이브를 받은 후에는 BitLocker 키를 가져와서 드라이브의 잠금을 해제해야 합니다. Azure Portal에서 내보내기 작업으로 이동합니다. **가져오기/내보내기** 탭을 클릭합니다. 
2. 목록에서 내보내기 작업을 선택하고 클릭합니다. **BitLocker 키**로 이동하여 해당 키를 복사합니다.
   
   ![내보내기 작업의 BitLocker 키 보기](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. BitLocker 키를 사용하여 디스크의 잠금을 해제합니다.

내보내기가 완료되었습니다. 이제는 작업을 삭제하거나 90일 후에 자동으로 삭제할 수 있습니다.


## <a name="check-the-number-of-drives"></a>드라이브 수 확인

이 *선택적인* 단계는 내보내기 작업에 필요한 드라이브 수를 결정하는 데 도움이 됩니다. [지원되는 OS 버전](storage-import-export-requirements.md#supported-operating-systems)을 실행하는 Windows 시스템에서 이 단계를 수행합니다.

1. Windows 시스템에서 [WAImportExport 버전 1을 다운로드](https://aka.ms/waiev1)합니다. 
2. `waimportexportv1` 기본 폴더에 압축을 풉니다. 예: `C:\WaImportExportV1`.
3. 관리 권한이 있는 PowerShell 또는 명령줄 창을 엽니다. 압축을 푼 폴더로 디렉터리를 변경하려면 다음 명령을 실행합니다.
    
    `cd C:\WaImportExportV1`

4. 선택한 Blob에 필요한 디스크 수를 확인하려면 다음 명령을 실행합니다.

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    다음 표에는 매개 변수가 나와 있습니다.
    
    |명령줄 매개 변수|설명|  
    |--------------------------|-----------------|  
    |**/logdir:**|선택 사항입니다. 로그 디렉터리입니다. 이 디렉터리에 자세한 로그 파일이 기록됩니다. 지정하지 않으면 현재 디렉터리가 로그 디렉터리로 사용됩니다.|  
    |**/sn:**|필수 사항입니다. 내보내기 작업에 대한 저장소 계정의 이름입니다.|  
    |**/sk:**|컨테이너 SAS가 지정되지 않은 경우에만 필요합니다. 내보내기 작업에 대한 저장소 계정의 계정 키입니다.|  
    |**/csas:**|저장소 계정 키가 지정되지 않은 경우에만 필요합니다. 내보내기 작업에서 내보낼 Blob을 나열하기 위한 컨테이너 SAS입니다.|  
    |**/ExportBlobListFile:**|필수 사항입니다. 내보낼 Blob에 대한 Blob 경로 또는 Blob 경로 접두사 목록을 포함하고 있는 XML 파일의 경로입니다. Import/Export 서비스 REST API의 [작업 배치](/rest/api/storageimportexport/jobs) 작업에서 `BlobListBlobPath` 요소에 사용되는 파일 형식입니다.|  
    |**/DriveSize:**|필수 사항입니다. 내보내기 작업에 사용할 드라이브의 크기입니다(*예*: 500GB, 1.5TB).|  

    [PreviewExport 명령 예제](#example-of-previewexport-command)를 참조하세요.
 
5. 내보내기 작업을 위해 배송될 드라이브에 읽거나 쓸 수 있는지 확인합니다.

### <a name="example-of-previewexport-command"></a>PreviewExport 명령 예제

다음 예제에서는 `PreviewExport` 명령을 보여 줍니다.  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
내보내기 Blob 목록 파일에는 다음과 같이 Blob 이름과 Blob 접두사가 포함될 수 있습니다.  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Azure Import/Export 도구는 내보낼 모든 Blob을 나열하고 필요한 오버헤드를 고려하여 지정된 크기의 드라이브에 패키지하는 방법을 계산한 다음 Blob 및 드라이브 사용 정보를 보유하는 데 필요한 드라이브 수를 추정합니다.  
  
정보 로그가 생략된 출력의 예제는 다음과 같습니다.  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>유효한 Blob 경로의 예

다음 표에는 유효한 Blob 경로의 예가 있습니다.
   
   | 선택기 | Blob 경로 | 설명 |
   | --- | --- | --- |
   | 시작 단어 |/ |저장소 계정의 모든 Blob을 내보냄 |
   | 시작 단어 |/$root/ |루트 컨테이너의 모든 Blob을 내보냄 |
   | 시작 단어 |/book |접두사 **book** |
   | 시작 단어 |/music/ |컨테이너 **music** |
   | 시작 단어 |/music/love |접두사 **love**로 시작하는 컨테이너 **music**의 모든 Blob을 내보냄 |
   | 같음 |$root/logo.bmp |루트 컨테이너의 Blob **logo.bmp** 를 내보냄 |
   | 같음 |videos/story.mp4 |컨테이너 **videos**의 Blob **story.mp4**를 내보냄 |

## <a name="next-steps"></a>다음 단계

* [작업 및 드라이브 상태 보기](storage-import-export-view-drive-status.md)
* [Import/Export 요구 사항 검토](storage-import-export-requirements.md)


