---
title: Azure Import/Export를 사용하여 Azure Blob으로 데이터 전송 | Microsoft Docs
description: Azure Portal에서 가져오기 및 내보내기 작업을 만들어 Azure Blob 간에 데이터를 전송하는 방법을 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e60a58a8d2f1c69728a2d049fe1414ca1997893e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479306"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Azure Import/Export 서비스를 사용하여 Azure Blob Storage로 데이터 가져오기

이 문서에서는 Azure Import/Export 서비스를 사용하여 Azure Blob Storage로 많은 양의 데이터를 안전하게 가져오는 방법에 대한 단계별 지침을 제공합니다. 데이터를 Azure Blob으로 가져오려면 서비스를 사용하여 데이터가 포함된 암호화된 디스크 드라이브를 Azure 데이터 센터로 배송해야 합니다.  

## <a name="prerequisites"></a>필수 조건

가져오기 작업을 만들어 Azure Blob Storage로 데이터를 전송하기 전에 이 서비스에 대한 다음 필수 조건 목록을 신중하게 검토하고 완료해야 합니다. 다음이 필요합니다.

- Import/Export 서비스에 사용할 수 있는 활성 Azure 구독이 있어야 합니다.
- 스토리지 컨테이너가 있는 Azure Storage 계정이 하나 이상 있어야 합니다. [Import/Export 서비스에 지원되는 저장소 계정 및 저장소 형식](storage-import-export-requirements.md) 목록을 참조하세요. 
    - 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](storage-quickstart-create-account.md)(영문)을 참조하세요. 
    - 저장소 컨테이너에 대한 자세한 내용은 [저장소 컨테이너 만들기](../blobs/storage-quickstart-blobs-portal.md#create-a-container)로 이동하세요.
- [지원되는 유형](storage-import-export-requirements.md#supported-disks)에 속한 적절한 개수의 디스크가 있어야 합니다. 
- [지원되는 OS 버전](storage-import-export-requirements.md#supported-operating-systems)을 실행하는 Windows 시스템이 있어야 합니다. 
- Windows 시스템에서 BitLocker를 사용하도록 설정합니다. [BitLocker를 사용하도록 설정하는 방법](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)을 참조하세요.
- Windows 시스템에서 [WAImportExport 버전 1을 다운로드](https://aka.ms/waiev1)합니다. `waimportexportv1` 기본 폴더에 압축을 풉니다. 예: `C:\WaImportExportV1`.
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

1.  SATA 커넥터를 통해 디스크 드라이브를 Windows 시스템에 연결합니다.
1.  각 드라이브에 단일 NTFS 볼륨을 만듭니다. 볼륨에 드라이브 문자를 할당합니다. 탑재 지점은 사용하지 마세요.
2.  NTFS 볼륨에서 BitLocker 암호화를 사용하도록 설정합니다. Windows Server 시스템을 사용하는 경우 [Windows Server 2012 R2에서 BitLocker를 사용하도록 설정하는 방법](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)의 지침을 따르세요.
3.  데이터를 암호화된 볼륨으로 복사합니다. 끌어서 놓기, Robocopy 또는 이러한 복사 도구를 사용합니다.
4.  관리 권한이 있는 PowerShell 또는 명령줄 창을 엽니다. 압축을 푼 폴더로 디렉터리를 변경하려면 다음 명령을 실행합니다.
    
    `cd C:\WaImportExportV1`
5.  드라이브의 BitLocker 키를 가져오려면 다음 명령을 실행합니다.
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  디스크를 준비하려면 다음 명령을 실행합니다. **데이터 크기에 따라 몇 시간에서 며칠이 걸릴 수 있습니다.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /sk:<Storage account key> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /skipwrite 
    ```
    저널 파일이 도구를 실행한 폴더와 동일한 폴더에 만들어집니다. *.xml* 파일(도구를 실행하는 폴더)과 *drive-manifest.xml* 파일(데이터가 있는 폴더)의 두 개의 다른 파일도 만들어집니다.
    
    다음 표에는 사용되는 매개 변수가 나와 있습니다.

    |옵션  |설명  |
    |---------|---------|
    |/j:     |확장명이 .jrn인 저널 파일의 이름입니다. 저널 파일은 드라이브마다 생성됩니다. 디스크 일련 번호를 저널 파일 이름으로 사용하는 것이 좋습니다.         |
    |/id:     |세션 ID입니다. 명령의 각 인스턴스마다 고유한 세션 번호를 사용합니다.      |
    |/sk:     |Azure Storage 계정 키입니다.         |
    |/t:     |배송할 디스크의 드라이브 문자입니다. 예: `D` 드라이브         |
    |/bk:     |드라이브의 BitLocker 키입니다. `manage-bde -protectors -get D:` 출력의 숫자 암호입니다.      |
    |/srcdir:     |`:\` 다음에 나오는 배송될 디스크의 드라이브 문자입니다. 예: `D:\`.         |
    |/dstdir:     |Azure Storage에 있는 대상 컨테이너 이름입니다.         |
    |/skipwrite:     |복사하는 데 필요한 새 데이터가 없고 디스크의 기존 데이터를 준비하도록 지정하는 옵션입니다.          |
7. 배송해야 하는 각 디스크에 대해 이전 단계를 반복합니다. 명령줄을 실행할 때마다 제공된 이름의 저널 파일이 만들어집니다.
    
    > [!IMPORTANT]
    > - 저널 파일과 함께 `<Journal file name>_DriveInfo_<Drive serial ID>.xml` 파일도 도구가 있는 폴더와 동일한 폴더에 만들어집니다. 저널 파일이 너무 큰 경우 작업을 만들 때는 .xml 파일이 저널 파일 대신 사용됩니다. 

## <a name="step-2-create-an-import-job"></a>2단계: 가져오기 작업 만들기

다음 단계를 수행하여 Azure Portal에서 가져오기 작업을 만듭니다.

1. https://portal.azure.com/에 로그온합니다.
2. **모든 서비스 > 저장소 > 작업 가져오기/내보내기**로 차례로 이동합니다. 
    
    ![작업 가져오기/내보내기로 이동](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. **가져오기/내보내기 작업 만들기**를 클릭합니다.

    ![가져오기/내보내기 작업 만들기 클릭](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. **기본 사항**에서

   - **Azure로 가져오기**를 선택합니다.
   - 가져오기 작업을 설명하는 이름을 입력합니다. 이름을 사용하여 작업 진행 상황을 추적합니다.
       - 이름에는 소문자, 숫자 및 하이픈만 포함할 수 있습니다.
       - 이름은 문자로 시작해야 하며, 공백은 포함할 수 없습니다.
   - 구독을 선택합니다.
   - 리소스 그룹을 입력하거나 선택합니다.  

     ![가져오기 작업 만들기 - 1단계](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. **작업 세부 정보**에서:

    - 드라이브 준비 단계에서 얻은 드라이브 저널 파일을 업로드합니다. `waimportexport.exe version1`이 사용된 경우 준비한 각 드라이브에 대해 파일을 하나씩 업로드합니다. 저널 파일 크기가 2MB를 초과하면 저널 파일을 사용하여 만든 `<Journal file name>_DriveInfo_<Drive serial ID>.xml`도 사용할 수 있습니다. 
    - 데이터가 저장될 대상 저장소 계정을 선택합니다. 
    - 하차 위치는 선택한 저장소 계정의 지역을 기반으로 자동으로 채워집니다.
   
   ![가져오기 작업 만들기 - 2단계](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. **반송 정보**에서:

   - 드롭다운 목록에서 운송업체를 선택합니다. 이외의 FedEx/DHL 운송 업체를 사용 하려는 경우 드롭다운 목록에서 기존 옵션을 선택 합니다. 연락처의 Azure 데이터 상자 작업 팀에서 `adbops@microsoft.com` 사용 하려는 운송 업체에 대 한 정보를 사용 하 여 합니다.
   - 운송업체에서 만든 유효한 운송업체 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다. 계정 번호가 없는 경우 [FedEx](https://www.fedex.com/us/oadr/) 또는 [DHL](http://www.dhl.com/) 운송업체 계정을 만듭니다.
   - 완전하고 유효한 연락처 이름, 전화 번호, 이메일, 주소, 구/군/시, 우편 번호, 시/도 및 국가/지역을 제공합니다. 
        
       > [!TIP] 
       > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

     ![가져오기 작업 만들기 - 3단계](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. **요약**에서:

   - 요약에 제공된 직업 정보를 검토합니다. 작업 이름과 디스크를 Azure로 반송할 Azure 데이터 센터 배송 주소를 적어 둡니다. 이 정보는 나중에 운송 레이블에 사용됩니다.
   - **확인**을 클릭하여 가져오기 작업을 만듭니다.

     ![가져오기 작업 만들기 - 4단계](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>3단계: 드라이브 배송 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>4단계: 추적 정보를 사용하여 작업 업데이트

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5단계: Azure에 대한 데이터 업로드 확인

완료될 때까지 작업을 추적합니다. 작업이 완료되면 데이터가 Azure에 업로드되었는지 확인합니다. 업로드가 성공했음을 확인한 후에만 온-프레미스 데이터를 삭제합니다.

## <a name="next-steps"></a>다음 단계

* [작업 및 드라이브 상태 보기](storage-import-export-view-drive-status.md)
* [Import/Export 요구 사항 검토](storage-import-export-requirements.md)


