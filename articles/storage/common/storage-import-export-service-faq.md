---
title: Azure Import/Export 서비스에 대한 FAQ | Microsoft Docs
description: Azure Import/Export 서비스에 대한 질문과 대답을 참고합니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/13/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ee2917c64843c8ab137e0122d63a328d6c19fedb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478575"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure Import/Export 서비스: 질문과 대답 
Azure Import/Export 서비스를 사용하여 Azure Storage에 데이터를 전송할 때 발생할 수 있는 질문 및 답변은 다음과 같습니다. 질문과 대답은 다음과 같은 범주로 정렬됩니다.

- Import/Export 서비스 정보
- Import/Export할 디스크 준비
- Import/Export 작업
- 디스크 운송
- 기타 

## <a name="about-importexport-service"></a>Import/Export 서비스 정보

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Azure Import/Export 서비스를 사용하여 Azure File Storage를 복사할 수 있나요?

예. Azure Import/Export 서비스는 Azure File Storage로 가져오기를 지원합니다. 지금은 Azure 파일의 내보내기는 지원하지 않습니다.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Azure Import/Export 서비스를 CSP 구독에 사용할 수 있나요?

예. Azure Import/Export 서비스는 CSP(클라우드 솔루션 공급자) 구독을 지원합니다.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-o365"></a>Azure Import/Export 서비스를 사용하여 PST 사서함 및 SharePoint 데이터를 O365에 복사할 수 있나요?

예. 자세한 내용은 [Office 365에 PST 파일 또는 SharePoint 데이터 가져오기](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx)로 이동합니다.

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Azure Import/Export 서비스를 사용하여 오프라인 백업을 Azure Backup 서비스에 복사할 수 있나요?

예. 자세한 내용은 [Azure Backup의 오프라인 백업 워크플로](../../backup/backup-azure-backup-import-export.md)로 이동합니다.

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>가져오기/내보내기 작업에 사용할 드라이브를 Microsoft에서 구입할 수 있습니까?

아니요. 가져오기 및 내보내기 작업에 대해 사용자가 자체 드라이브를 발송해야 합니다.


## <a name="preparing-disks-for-importexport"></a>Import/Export할 디스크 준비

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>가져오기 작업에서 드라이브 준비 단계를 건너뛸 수 있나요? 복사하지 않고 드라이브를 준비할 수 있나요?

아니요. 데이터를 가져오는 데 사용되는 드라이브는 Azure WAImportExport 도구를 사용하여 준비되어야 합니다. 도구를 사용하여 드라이브에 데이터를 복사합니다.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>내보내기 작업을 만들 때 디스크를 준비해야 하나요?

아니요. 일부 사전 검사를 수행하는 것이 좋습니다. 필요한 디스크 수를 확인하려면 WAImportExport 도구의 PreviewExport 명령을 사용합니다. 자세한 내용은 [내보내기 작업에 대한 드라이브 사용량 미리 보기](https://msdn.microsoft.com/library/azure/dn722414.aspx)를 참조하세요. 명령을 통해 사용하고자 하는 드라이브의 크기에 따라 선택한 Blob의 드라이브 사용량을 미리 볼 수 있습니다. 또한 내보내기 작업에 대해 제공될 하드 드라이브에서 읽고 쓸 수 있는지 확인합니다.

## <a name="importexport-jobs"></a>Import/Export 작업

### <a name="can-i-cancel-my-job"></a>작업을 취소할 수 있나요?
예. 작업 상태가 **만드는 중** 또는 **운송 중**인 경우 작업을 취소할 수 있습니다. 이러한 단계를 넘으면 작업을 취소할 수 없고 최종 단계까지 계속됩니다.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Azure Portal에서 완료된 작업의 상태는 얼마나 오랫동안 볼 수 있나요?
완료된 작업의 상태는 최대 90일 동안 볼 수 있습니다. 90일이 지나면 완료된 작업이 삭제됩니다.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>10개가 넘는 드라이브를 가져오거나 내보내려면 어떻게 해야 합니까?
하나의 가져오기 또는 내보내기 작업은 단일 작업에서 10개의 드라이브만을 참조할 수 있습니다. 10개가 넘는 드라이브를 운송하려면 여러 개의 작업을 만들어야 합니다. 동일한 작업과 연결된 드라이브는 동일한 패키지에 함께 발송되어야 합니다. 데이터 용량이 여러 디스크 가져오기 작업에 걸쳐 있는 경우 정보 및 지침은 bulkimport@microsoft.com의 Microsoft에 문의합니다. 

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>업로드된 Blob은 상태를 “임대 만료”로 표시합니다. 어떻게 해야 하나요?
"임대 만료" 필드는 무시해도 됩니다. Import/Export 기능은 업로드 중에 Blob을 임대하여 다른 프로세스가 동시에 Blob을 업데이트할 수 없도록 합니다. 임대 만료는 Import/Export 기능이 더 이상 해당 Blob에 업로드하지 않으므로 사용자가 Blob을 사용할 수 있음을 의미합니다. 

## <a name="shipping-disks"></a>디스크 운송

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>각 운송에 허용되는 최대 HDD 수는 몇 개인가요?
하나의 운송에서 HDD 수는 제한되지 않습니다. 디스크가 여러 작업에 속하는 경우 다음을 수행하는 것이 좋습니다. 
- 해당하는 작업 이름 사용하여 디스크의 레이블을 지정합니다.
- 추적 번호에 접미사 -1, -2 등을 추가하여 작업을 업데이트합니다.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>HDD 외에도 패키지에 포함해야 하는 다른 항목이 있나요?
운송 패키지에서는 하드 드라이브만을 운송합니다. 전원 공급 장치 케이블이나 USB 케이블과 같은 품목은 포함하지 마세요.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>FedEx 또는 DHL을 사용하여 드라이브를 운송해야 하나요?
FedEx, DHL, UPS 또는 US 우편 서비스와 같이 알려진 모든 운송업체를 사용하여 Azure 데이터 센터에 드라이브를 발송할 수 있습니다. 그러나 데이터 센터 간에 드라이브를 반송하는 경우 다음을 제공해야 합니다.

- 미국 및 유럽의 FedEx 계정 번호 또는
- 아시아 및 오스트레일리아 지역의 DHL 계정 번호

> [!NOTE]
> 인도 데이터 센터에 드라이브를 반환 하 여 편지지 (배달 challan)의 선언 문자를 필요 합니다. 필요한 항목 패스를 정렬 하려면 선택한 통신사 된 선택을 예약 하 고 데이터 센터를 사용 하 여 세부 정보를 공유도 해야 합니다.

### <a name="are-there-any-restrictions-with-shipping-my-drive-internationally"></a>국제적으로 드라이브를 운송하는 데 제한 사항이 있나요?
배송하는 실제 미디어는 국경을 지나야 할 수 있습니다. 실제 미디어와 데이터를 관련 법률에 따라 가져오거나 내보내도록 해야 합니다. 실제 미디어를 배송하기 전에 관리자에게 미디어 및 데이터를 확인된 데이터 센터에 합법적으로 배송할 수 있는지 확인하세요. 이렇게 하면 해당 품목이 시기 적절하게 Microsoft에 도착될 수 있습니다.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>데이터 센터 내 디스크를 제공 하기 위한 특별 한 요구 사항이 있습니까?

요구 사항을 Azure 데이터 센터를 특정 제한에 따라 달라 집니다.
- Microsoft 데이터 센터 보안상의 이유로 소포에 쓸 인바운드 ID 번호를 필요로 하는 몇 가지 사이트가 있습니다. 드라이브 또는 디스크를 데이터 센터에 발송 하기 전에 Azure DataBox Operations에 게 문의 (adbops@microsoft.com)를 가져올 수 있습니다. 이 수 없이 패키지는 거부 됩니다.
- 인도 데이터 센터 필요 예: 정부 ID 카드 또는 증명 아니요. 드라이버의 개인 정보 (예를 들어 팬, AADHAR DL), 이름, 연락처 및 자동차는 게이트 항목 패스를 구할 수를 판을 합니다. 배달 지연 되지 않도록, 통신사 이러한 요구 사항을 알려 줍니다.


### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>작업을 만들 때 배송지 주소가 저장소 계정 위치와는 다른 위치입니다. 어떻게 해야 하나요?

일부 저장소 계정 위치는 대체 배송 위치로 매핑됩니다. 이전에 사용 가능했던 배송 위치 또한 일시적으로 대체 위치로 매핑될 수 있습니다. 드라이브를 발송하기 전에 작업을 만들 때 제공한 배송지 주소를 항상 확인합니다.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>드라이브를 운송할 때 운송업체에서 데이터 센터 연락처 주소 및 전화 번호를 요청합니다. 무엇을 제공해야 하나요?

전화 번호와 DC 주소는 작업을 만드는 동안 제공됩니다.


## <a name="miscellaneous"></a>기타

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>지원 요구 사항에 맞지 않는 HDD를 실수로 보내면 어떻게 됩니까?

Azure 데이터 센터에서는 지원 요구 사항에 맞지 않는 드라이브를 사용자에게 반환합니다. 패키지의 드라이브 중 일부만 지원 요구 사항에 맞는 경우 해당 드라이브는 처리되며 요구 사항에 맞지 않는 드라이브는 사용자에게 반환됩니다.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>서비스가 드라이브를 반환하기 전에 포맷하나요?

아니요. 모든 드라이브는 BitLocker로 암호화되어 있습니다.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>이 서비스를 통해 가져오는 데이터에 액세스하려면 어떻게 하나요?

Azure Portal 또는 [스토리지 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)를 사용하여 Azure Storage 계정에서 데이터에 액세스합니다.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>가져오기 작업을 완료한 후에 저장소 계정에서 데이터는 어떻게 표시되나요? 디렉터리 계층 구조가 유지되나요?

가져오기 작업을 위해 하드 드라이브를 준비할 때 대상은 데이터 세트 CSV의 DstBlobPathOrPrefix 필드로 지정됩니다. 이는 하드 드라이브로부터 데이터가 복사되는 저장소 계정의 대상 컨테이너입니다. 이 대상 컨테이너 내에 하드 드라이브의 폴더에 대해 가상 디렉터리가 만들어지며 파일에 대해 Blob이 만들어집니다. 

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>저장소 계정에 이미 있는 파일이 드라이브에 있는 경우 서비스가 기존 Blob 또는 파일을 덮어쓰나요?

경우에 따라 다릅니다. 드라이브를 준비할 때 데이터 세트 CSV 파일에 있는 Disposition:&lt;rename|no-overwrite|overwrite&gt;라는 필드를 사용하여 대상 파일을 덮어쓰거나 무시해야 하는지를 지정할 수 있습니다. 기본적으로 서비스는 기존 Blob 또는 파일을 덮어쓰지 않고 새 파일의 이름을 변경합니다.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>WAImportExport 도구는 32비트 운영 체제와 호환되나요?
아니요. WAImportExport 도구는 64비트 Windows 운영 체제에서만 호환됩니다. 지원되는 OS의 전체 목록은 [지원되는 운영 체제](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements)로 이동합니다. 


### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Azure Import/Export에서 지원하는 최대 블록 Blob 및 페이지 Blob 크기는 무엇인가요?

최대 블록 Blob 크기는 약 4.768TB 또는 5,000,000MB입니다.
최대 페이지 Blob 크기는 8TB입니다.


### <a name="does-azure-importexport-support-aes-256-encryption"></a>Azure Import/Export는 AES-256 암호화를 지원하나요?
Azure Import/Export 서비스는 기본적으로 AES-128 bitlocker 암호화를 사용합니다. 데이터를 복사하기 전에 bitlocker로 수동으로 암호화하여 AES-256으로 변경할 수 있습니다. 

- [WAImportExport V1](https://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip)을 사용하는 경우 샘플 명령은 아래와 같습니다.
    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
    ```
- [WAImportExport V2](https://www.microsoft.com/download/details.aspx?id=55280)를 사용하는 경우 "AlreadyEncrypted"를 지정하고 드라이브 집합 CSV에 키를 제공합니다.
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Import/Export란?](storage-import-export-service.md)


