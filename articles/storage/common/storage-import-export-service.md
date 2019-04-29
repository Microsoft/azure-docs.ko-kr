---
title: Azure Import/Export를 사용하여 Azure Storage 간에 데이터 전송 | Microsoft Docs
description: Azure Portal에서 가져오기 및 내보내기 작업을 만들어 Azure Storage 간에 데이터를 전송하는 방법에 대해 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 02/14/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 4850dd82ca52a060c921569433035256f5b74cce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478798"
---
# <a name="what-is-azure-importexport-service"></a>Azure Import/Export 서비스란?

Azure Import/Export 서비스를 사용하면 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure Blob Storage 및 Azure Files로 가져올 수 있습니다. 이 서비스를 사용하여 데이터를 Azure Blob Storage에서 디스크 드라이브로 전송하고 온-프레미스 사이트로 발송할 수도 있습니다. 하나 이상의 디스크 드라이브에 있는 데이터를 Azure Blob Storage 또는 Azure Files로 가져올 수 있습니다. 

자신의 디스크 드라이브를 제공하고 Azure Import/Export 서비스로 데이터를 전송합니다. Microsoft에서 제공하는 디스크 드라이브를 사용할 수도 있습니다. 

Microsoft에서 제공하는 디스크 드라이브를 사용하여 데이터를 전송하려는 경우 [Azure Data Box Disk](../../databox/data-box-disk-overview.md)를 사용하여 Azure로 데이터를 가져올 수 있습니다. Microsoft는 주문당 총 40TB 용량의 암호화된 SSD(solid-state disk) 드라이브를 5개까지 지역 이동 통신 사업자를 통해 데이터 센터에 제공합니다. 신속하게 디스크 드라이브를 구성하고, USB 3.0 연결을 통해 디스크 드라이브에 데이터를 복사하고, Azure에 다시 디스크 드라이브를 제공할 수 있습니다. 자세한 내용은 [Azure Data Box Disk 개요](../../databox/data-box-disk-overview.md)로 이동합니다.

## <a name="azure-importexport-usecases"></a>Azure Import/Export 사용 사례

네트워크를 통한 데이터 업로드 또는 다운로드가 너무 느리거나 추가 네트워크 대역폭 비용이 너무 비싼 경우 Azure Import/Export 서비스 사용을 고려할 수 있습니다. 다음 시나리오에서 이 서비스를 사용하세요.

* **클라우드로의 데이터 마이그레이션**: 빠르고 경제적인 방식을 통해 많은 양의 데이터를 Azure로 이동합니다.
* **콘텐츠 배포**: 고객 사이트로 데이터를 빠르게 전송합니다.
* **백업**: 온-프레미스 데이터의 백업을 가져와 Azure Storage에 저장합니다.
* **데이터 복구**: 스토리지에 저장된 많은 양의 데이터를 복구하여 온-프레미스 위치에 전달합니다.

## <a name="importexport-components"></a>Import/Export 구성 요소

Import/Export 서비스는 다음 구성 요소를 사용합니다.

- **가져오기/내보내기 서비스**: Azure Portal에서 제공되는 이 서비스를 사용하면 데이터 가져오기(업로드) 및 내보내기(다운로드) 작업을 만들고 추적할 수 있습니다.  

- **WAImportExport 도구**: 다음 작업을 수행하는 명령줄 도구입니다. 
    - 가져오기용으로 발송할 디스크 드라이브를 준비합니다.
    - 드라이브에 데이터를 편리하게 복사할 수 있도록 합니다.
    - BitLocker를 사용하여 드라이브의 데이터를 암호화합니다.
    - 가져오기 생성 동안 사용되는 드라이브 저널 파일을 생성합니다.
    - 내보내기 작업에 필요한 드라이브 수를 식별하는 데 도움이 됩니다.
    
> [!NOTE]
> WAImportExport 도구는 버전 1 및 버전 2의 두 버전으로 사용할 수 있습니다. 다음과 같이 사용하길 권장합니다.
> - Azure Blob Storage로 가져오기/내보내기를 수행하는 경우 버전 1을 사용합니다. 
> - Azure 파일로 데이터를 가져오는 경우 버전 2를 사용합니다.
>
> WAImportExport 도구는 64비트 Windows 운영 체제에서만 호환됩니다. 지원되는 특정 운영 체제 버전을 확인하려면 [Azure Import/Export 요구 사항](storage-import-export-requirements.md#supported-operating-systems)을 참조하세요.

- **디스크 드라이브**: SSD(반도체 드라이브) 또는 HDD(하드 디스크 드라이브)를 Azure 데이터 센터로 발송할 수 있습니다. 가져오기 작업을 만들 때는 사용자 데이터가 포함된 디스크 드라이브를 발송합니다. 내보내기 작업을 만들 때는 Azure 데이터 센터로 빈 드라이브를 발송합니다. 특정 디스크 유형에 대해 알아보려면 [지원되는 디스크 유형](storage-import-export-requirements.md#supported-hardware)을 참조하세요.

## <a name="how-does-importexport-work"></a>Import/Export 작업 작동 방식

Azure Import/Export 서비스를 사용하면 작업을 만들어 Azure Blob 및 Azure Files로 데이터를 전송할 수 있습니다. Azure Portal 또는 Azure Resource Manager REST API를 사용하여 작업을 만듭니다. 각 작업은 단일 저장소 계정과 연결됩니다. 

작업은 내보내기 또는 가져오기 작업일 수 있습니다. 가져오기 작업을 사용하면 Azure Blob 또는 Azure Files로 데이터를 가져올 수 있고, 내보내기 작업을 사용하면 Azure Blob에서 파일을 내보낼 수 있습니다. 가져오기 작업의 경우 사용자 데이터가 포함된 드라이브를 발송합니다. 내보내기 작업을 만들 때는 Azure 데이터 센터로 빈 드라이브를 발송합니다. 각 경우에 작업당 최대 10개의 디스크 드라이브를 발송할 수 있습니다.

### <a name="inside-an-import-job"></a>가져오기 작업 내부

가져오기 작업은 개략적으로 다음 단계를 포함합니다.

1. Azure Storage의 데이터에 대해 가져올 데이터, 필요한 드라이브 수, 대상 blob 위치를 결정합니다.
2. WAImportExport 도구를 사용하여 데이터를 드라이브에 복사합니다. BitLocker로 디스크 드라이브를 암호화합니다.
3. Azure Portal에서 대상 저장소 계정에 가져오기 작업을 만듭니다. 드라이브 저널 파일을 업로드합니다.
4. 드라이브를 다시 발송하기 위한 반송 주소 및 운송업체 계정 번호를 제공합니다.
5. 작업을 만드는 동안 제공된 배송지 주소로 디스크 드라이브를 발송합니다.
6. 가져오기 작업 세부 정보에서 배송 추적 번호를 업데이트하고 가져오기 작업을 제출합니다.
7. 드라이브는 Azure 데이터 센터에서 수신 및 처리됩니다.
8. 드라이브는 운송업체 계정을 사용하여 가져오기 작업에 제공된 반송 주소로 배송됩니다.

> [!NOTE]
> 로컬(데이터 센터 국가 내) 배송의 경우 국내 운송업체 계정 공유 
>
> (외부 데이터 센터 국가) abroad 배송에 대 한 공유는 국제 운송 업체 계정

 ![그림 1: 가져오기 작업 흐름](./media/storage-import-export-service/importjob.png)

데이터 가져오기에 대한 단계별 지침을 보려면 다음으로 이동합니다.

- [Azure Blob로 데이터 가져오기](storage-import-export-data-to-blobs.md)
- [Azure Files로 데이터 가져오기](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>내보내기 작업 내부

> [!IMPORTANT]
> 이 서비스는 Azure Blob의 내보내기만 지원합니다. Azure 파일 내보내기는 지원되지 않습니다.

내보내기 작업은 개략적으로 다음 단계를 포함합니다.

1. Blob Storage의 데이터에 대해 내보낼 데이터, 필요한 드라이브 수, 원본 Blob 또는 컨테이너 경로를 결정합니다.
3. Azure Portal에서 원본 저장소 계정에 내보내기 작업을 만듭니다.
4. 내보낼 데이터에 대해 원본 Blob 또는 컨테이너 경로를 지정합니다.
5. 드라이브를 다시 발송하기 위한 반송 주소 및 운송업체 계정 번호를 제공합니다.
6. 작업을 만드는 동안 제공된 배송지 주소로 디스크 드라이브를 발송합니다.
7. 내보내기 작업 세부 정보에서 배송 추적 번호를 업데이트하고 내보내기 작업을 제출합니다.
8. 드라이브는 Azure 데이터 센터에서 수신 및 처리됩니다.
9. 드라이브는 BitLocker로 암호화하고, 키는 Azure Portal을 통해 사용할 수 있습니다.  
10. 드라이브는 운송업체 계정을 사용하여 가져오기 작업에 제공된 반송 주소로 배송됩니다.

> [!NOTE]
> 로컬(데이터 센터 국가 내) 배송의 경우 국내 운송업체 계정 공유 
>
> (외부 데이터 센터 국가) abroad 배송에 대 한 공유는 국제 운송 업체 계정
  
 ![그림 2: 내보내기 작업 흐름](./media/storage-import-export-service/exportjob.png)

데이터 내보내기에 대한 단계별 지침을 알아보려면 [Azure Blob에서 데이터 내보내기](storage-import-export-data-from-blobs.md)를 참조하세요.

## <a name="region-availability"></a>지역 가용성 

Azure Import/Export 서비스는 모든 Azure 저장소 계정으로의 데이터 복사를 지원합니다. 나열된 위치 중 하나로 디스크 드라이브를 발송할 수 있습니다. 저장소 계정이 여기에 지정되지 않은 Azure 위치에 있는 경우 작업을 만들 때 대체 발송 위치가 제공됩니다.

### <a name="supported-shipping-locations"></a>지원되는 발송 위치


|국가  |국가  |국가  |국가  |
|---------|---------|---------|---------|
|미국 동부    | 북유럽        | 인도 중부        |미국 정부 아이오와         |
|미국 서부     |서유럽         | 인도 남부        | 미국 국방부 동부        |
|미국 동부 2    | 동아시아        |  인도 서부        | 미국 국방부 중부        |
|미국 서부 2     | 동남아시아        | 캐나다 중부        | 중국 동부         |
|미국 중부     | 오스트레일리아 동부        | 캐나다 동부        | 중국 북부        |
|미국 중북부     |  오스트레일리아 남동부       | 브라질 남부        | 영국 남부        |
|미국 중남부     | 일본 서부        |한국 중부         | 독일 중부        |
|미국 중서부     |  일본 동부       | 미국 정부 버지니아        | 독일 북동부        |


## <a name="security-considerations"></a>보안 고려 사항

드라이브의 데이터는 BitLocker 드라이브 암호화를 사용하여 암호화됩니다. 이렇게 암호화하면 전송 중 데이터가 보호됩니다.

가져오기 작업의 경우 드라이브는 두 가지 방법으로 암호화됩니다.  


- 드라이브 준비 중에 WAImportExport 도구를 실행하면서 *dataset.csv* 파일을 사용할 때 옵션을 지정합니다. 

- 드라이브에 대해 BitLocker 암호화를 수동으로 사용하도록 설정합니다. 드라이브 준비 중에 WAImportExport 도구 명령줄을 실행할 때 *driveset.csv*에 암호화 키를 지정합니다.


내보내기 작업의 경우 데이터가 드라이브에 복사된 후 서비스에서 이를 다시 사용자에게 발송하기 전에 BitLocker를 사용하여 드라이브를 암호화합니다. 암호화 키는 Azure Portal을 통해 제공됩니다.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>가격

**드라이브 취급 수수료**

가져오기 또는 내보내기 작업의 일부로 처리되는 각 드라이브에 대해 드라이브 취급 수수료가 있습니다. [Azure Import/Export 가격 책정](https://azure.microsoft.com/pricing/details/storage-import-export/)에서 자세한 내용을 확인하세요.

**발송 비용**

Azure에 드라이브를 발송하는 경우 운송업체에 발송 비용을 지불합니다. Microsoft에서 드라이브를 반환할 때 발송 비용은 작업을 만들 때 제공한 운송업체 계정으로 청구됩니다.

**트랜잭션 비용**

데이터를 Azure Storage로 가져올 때 표준 스토리지 트랜잭션 비용 이외에 필요한 트랜잭션 비용은 없습니다. Blob Storage에서 데이터를 내보낼 때는 표준 송신 요금이 적용됩니다. 트랜잭션 비용에 대한 자세한 내용은 [데이터 전송 가격 책정](https://azure.microsoft.com/pricing/details/data-transfers/)을 참조하세요.



## <a name="next-steps"></a>다음 단계

Import/Export 서비스를 사용하여 다음 작업을 수행하는 방법을 알아봅니다.
* [Azure Blob에 데이터 가져오기](storage-import-export-data-to-blobs.md)
* [Azure Blob에서 데이터 내보내기](storage-import-export-data-from-blobs.md)
* [Azure Files에 데이터 가져오기](storage-import-export-data-to-files.md)

