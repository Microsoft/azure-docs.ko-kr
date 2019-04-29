---
title: Azure Import/Export를 사용하여 Azure Files로 데이터 전송 | Microsoft Docs
description: Azure Portal에서 가져오기 작업을 만들어 Azure Files로 데이터를 전송하는 방법에 대해 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 28026a429643c62434ddfd7591126169857a7371
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479073"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export 서비스를 사용하여 Azure Files로 데이터 가져오기

이 아티클에서는 Azure Import/Export 서비스를 사용하여 Azure Files로 많은 양의 데이터를 안전하게 가져오는 방법에 대한 단계별 지침을 제공합니다. 데이터를 가져오려면 서비스를 사용하여 데이터가 포함된 지원되는 디스크 드라이브를 Azure 데이터 센터로 운송해야 합니다.  

Import/Export 서비스는 Azure Storage로 Azure Files의 가져오기만을 지원합니다. Azure Files의 내보내기는 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

가져오기 작업을 만들어 Azure Files로 데이터를 전송하기 전에 다음 필수 조건 목록을 신중하게 검토하고 완료해야 합니다. 다음이 필요합니다.

- Import/Export 서비스에 사용할 활성 Azure 구독이 있어야 합니다.
- Azure Storage 계정이 하나 이상 있어야 합니다. [Import/Export 서비스에 지원되는 저장소 계정 및 저장소 형식](storage-import-export-requirements.md) 목록을 참조하세요. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](storage-quickstart-create-account.md)(영문)을 참조하세요.
- [지원되는 형식](storage-import-export-requirements.md#supported-disks)에 속한 적절한 개수의 디스크가 있어야 합니다. 
- [지원되는 OS 버전](storage-import-export-requirements.md#supported-operating-systems)을 실행하는 Windows 시스템이 있어야 합니다.
- Windows 시스템에서 [WAImportExport 버전 2를 다운로드](https://aka.ms/waiev2)합니다. `waimportexport` 기본 폴더에 압축을 풉니다. 예: `C:\WaImportExport`.
- FedEx/DHL 계정이 있습니다. 이외의 FedEx/DHL 운송 업체를 사용 하려는 경우 Azure 데이터 상자 작업 팀에 문의 `adbops@microsoft.com`합니다.  
    - 계정은 유효해야 하고, 잔액이 있어야 하며, 반품 기능이 있어야 합니다.
    - 내보내기 작업의 추적 번호를 생성합니다.
    - 모든 작업에는 별도의 추적 번호가 있어야 합니다. 추적 번호가 동일한 여러 작업은 지원되지 않습니다.
    - 운송업체 계정이 없는 경우, 다음으로 이동합니다.
        - [FedEX 계정 만들기](https://www.fedex.com/en-us/create-account.html) 또는 
        - [DHL 계정 만들기](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>1단계: 드라이브 준비

이 단계에서는 업무 일지 파일을 생성합니다. 업무 일지 파일에는 드라이브 일련 번호, 암호화 키 및 저장소 계정 세부 정보와 같은 기본 정보가 저장됩니다.

다음 단계를 수행하여 드라이브를 준비합니다.

1. SATA 커넥터를 통해 디스크 드라이브를 Windows 시스템에 연결합니다.
2. 각 드라이브에 단일 NTFS 볼륨을 만듭니다. 볼륨에 드라이브 문자를 할당합니다. 탑재 지점은 사용하지 마세요.
3. 도구가 있는 루트 폴더에서 *dataset.csv* 파일을 수정합니다. 파일 또는 폴더 중 하나 또는 둘 다를 가져올지에 따라 다음 예제와 비슷한 *dataset.csv* 파일에 항목을 추가합니다.  

   - **파일을 가져오려면**: 다음 예제에서 C: 드라이브에 복사할 데이터가 있습니다. *MyFile1.txt* 파일을 *MyAzureFileshare1*의 루트에 복사합니다. *MyAzureFileshare1*이 존재하지 않는 경우 Azure Storage 계정에 생성됩니다. 폴더 구조는 유지됩니다.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **폴더를 가져오려면**: *MyFolder2* 아래의 모든 파일과 폴더가 fileshare에 반복적으로 복사됩니다. 폴더 구조는 유지됩니다.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     가져온 폴더 또는 파일에 해당하는 같은 파일에 여러 항목을 만들 수 있습니다. 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     [데이터 세트 CSV 파일 준비](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file)에 대해 자세히 알아보세요.
    

4. 도구가 있는 루트 폴더에서 *driveset.csv* 파일을 수정합니다. 다음 예제와 비슷한 *driveset.csv* 파일에 항목을 추가합니다. 드라이브 집합 파일에는 디스크 및 해당하는 드라이브 문자 목록이 있으므로 도구는 준비해야 할 디스크 목록을 올바르게 선택할 수 있습니다.

    이 예제에서는 두 개의 디스크가 연결되어 있고 기본 NTFS 볼륨 G:\ 및 H:\가 생성되었다고 가정합니다. G:가 이미 암호화된 반면 H:\는 암호화되지 않았습니다. 도구는 H:\만을 호스트하는 디스크를 포맷하고 암호화합니다(G:\) 제외).

   - **암호화되지 않은 디스크의 경우**: *Encrypt*를 지정하여 디스크에서 BitLocker 암호화를 사용합니다.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **이미 암호화되어 있는 디스크의 경우**: *AlreadyEncrypted*를 지정하고 BitLocker 키를 제공합니다.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     여러 드라이브에 해당하는 같은 파일에 여러 항목을 만들 수 있습니다. [드라이브 집합 CSV 파일 준비](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file)에 대해 자세히 알아보세요. 

5. `PrepImport` 옵션을 사용하여 디스크 드라이브에 대한 데이터를 복사하고 준비합니다. 새 복사 세션을 사용하여 디렉터리 및/또는 파일을 복사하는 첫 번째 복사 세션의 경우 다음과 같은 명령을 실행합니다.

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   아래에 가져오기 예제가 나와 있습니다.
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. 명령줄을 실행할 때마다 `/j:` 매개 변수와 함께 제공된 이름의 업무 일지 파일이 만들어집니다. 준비한 각 드라이브에는 가져오기 작업을 만들 때 업로드해야 하는 업무 일지 파일이 있습니다. 업무 일지 파일이 없는 드라이브는 처리되지 않습니다.

    > [!IMPORTANT]
    > - 디스크 준비를 완료한 후 디스크 드라이브 또는 업무 일지 파일에 있는 데이터를 수정하지 마세요.

추가 예제는 [업무 일지 파일에 대한 샘플](#samples-for-journal-files)로 이동합니다.

## <a name="step-2-create-an-import-job"></a>2단계: 가져오기 작업 만들기 

다음 단계를 수행하여 Azure Portal에서 가져오기 작업을 만듭니다.
1. https://portal.azure.com/에 로그온합니다.
2. **모든 서비스 > 저장소 > 작업 가져오기/내보내기**로 차례로 이동합니다. 

    ![가져오기/내보내기로 이동](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. **가져오기/내보내기 작업 만들기**를 클릭합니다.

    ![작업 가져오기/내보내기 클릭](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **기본 사항**에서

    - **Azure로 가져오기**를 선택합니다.
    - 가져오기 작업을 설명하는 이름을 입력합니다. 작업이 진행 중인 동안 그리고 작업이 완료되면 이 이름을 사용하여 작업을 추적합니다.
        -  이 이름에는 소문자, 숫자, 하이픈 및 밑줄만 사용할 수 있습니다.
        -  이름은 문자로 시작해야 하며, 공백은 포함할 수 없습니다. 
    - 구독을 선택합니다.
    - 리소스 그룹을 선택합니다. 

        ![가져오기 작업 만들기 - 1단계](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. **작업 세부 정보**에서:
    
    - 이전 [1단계: 드라이브 준비](#step-1-prepare-the-drives) 중에 만든 업무 일지 파일을 업로드합니다. 
    - 데이터를 가져올 수 있는 저장소 계정을 선택합니다. 
    - 하차 위치는 선택한 저장소 계정의 지역을 기반으로 자동으로 채워집니다.
   
       ![가져오기 작업 만들기 - 2단계](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **반송 정보**에서:

    - 드롭다운 목록에서 운송업체를 선택합니다. 이외의 FedEx/DHL 운송 업체를 사용 하려는 경우 드롭다운 목록에서 기존 옵션을 선택 합니다. 연락처의 Azure 데이터 상자 작업 팀에서 `adbops@microsoft.com` 사용 하려는 운송 업체에 대 한 정보를 사용 하 여 합니다.
    - 운송업체에서 만든 유효한 운송업체 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다. 
    - 완전하고 유효한 연락처 이름, 전화 번호, 이메일, 주소, 구/군/시, 우편 번호, 시/도 및 국가/지역을 제공합니다.

        > [!TIP] 
        > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

       ![가져오기 작업 만들기 - 3단계](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. **요약**에서:

    - Azure로의 반송 디스크에 Azure 데이터 센터 운송 주소를 입력합니다. 작업 이름 및 전체 주소가 배송 레이블에 언급되어 있는지 확인합니다.
    - **확인**을 클릭하여 가져오기 작업 만들기를 완료합니다.

        ![가져오기 작업 만들기 - 4단계](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3단계: Azure 데이터 센터에 드라이브 배송 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4단계: 추적 정보를 사용하여 작업 업데이트

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5단계: Azure에 대한 데이터 업로드 확인

완료될 때까지 작업을 추적합니다. 작업이 완료되면 데이터가 Azure에 업로드되었는지 확인합니다. 업로드가 성공했음을 확인한 후에만 온-프레미스 데이터를 삭제합니다.

## <a name="samples-for-journal-files"></a>업무 일지 파일에 대한 샘플

**드라이브를 추가**하려면 새 드라이브 집합 파일을 만들고 아래 명령을 실행합니다. 

*InitialDriveset .csv* 파일에 지정된 것과 다른 디스크 드라이브에 대한 후속 복사 세션의 경우 새 드라이브 집합 *.csv* 파일을 지정하고 `AdditionalDriveSet` 매개 변수에 값으로 제공합니다. **동일한 저널 파일** 이름을 사용하고 **새 세션 ID**를 제공합니다. AdditionalDriveset CSV 파일의 형식은 InitialDriveSet 형식과 같습니다.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

아래에 가져오기 예제가 나와 있습니다.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


추가 데이터를 동일한 드라이브 집합에 추가하려면 후속 복사 세션에 PrepImport 명령을 사용하여 추가 파일/디렉터리를 복사합니다.

*InitialDriveset.csv* 파일에서 지정된 동일한 하드 디스크 드라이브에 대한 후속 복사 세션의 경우 **동일한 업무 일지 파일** 이름을 지정하고, **새 세션 ID**를 제공합니다. 저장소 계정 키를 제공하지 않아도 됩니다.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

아래에 가져오기 예제가 나와 있습니다.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>다음 단계

* [작업 및 드라이브 상태 보기](storage-import-export-view-drive-status.md)
* [Import/Export 요구 사항 검토](storage-import-export-requirements.md)


