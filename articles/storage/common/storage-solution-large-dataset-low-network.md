---
title: 네트워크 대역폭이 부족하거나 없을 경우 대용량 데이터 세트를 위한 Azure 데이터 전송 옵션 | Microsoft Docs
description: 사용자 환경의 네트워크 대역폭이 제한적이거나 없으며 대용량 데이터 세트를 전송하려는 경우 데이터 전송을 위한 Azure 솔루션을 선택하는 방법을 알아봅니다.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4c4ac9489b9613b2eeaf26a3df9f4cbc664a1026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730693"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>네트워크 대역폭이 부족하거나 없을 경우 대용량 데이터 세트의 데이터 전송
 
이 문서에서는 사용자 환경의 네트워크 대역폭이 제한적이거나 없으며 대용량 데이터 세트를 전송하려는 경우의 데이터 전송 솔루션에 대해 간략하게 설명합니다. 이 시나리오에서 권장되는 데이터 전송 옵션 및 각각의 주요 기능 매트릭스에 대해서도 설명합니다.

사용 가능한 모든 데이터 전송 옵션에 대한 개요를 보려면 [Azure 데이터 전송 솔루션 선택](storage-choose-data-transfer-solution.md)으로 이동하세요.

## <a name="offline-transfer-or-network-transfer"></a>오프라인 전송 또는 네트워크 전송

대용량 데이터 세트는 TB에서 PB 단위의 데이터가 있음을 의미합니다. 네트워크 대역폭이 제한적이거나 없고, 네트워크 속도가 느리거나 불안정합니다. 추가 정보:

- ISP(인터넷 서비스 공급자)의 네트워크 전송 비용에 의해 제한됩니다.
- 보안 또는 조직 정책이 중요한 데이터를 처리할 때 아웃바운드 연결을 허용하지 않는 경우

이러한 모든 경우에는 물리적 디바이스를 사용하여 일회성 대량 데이터 전송을 수행합니다. Microsoft에서 제공하는 Data Box Disk, Data Box, Data Box Heavy 디바이스 중에서 선택하거나 사용자 고유의 디스크를 사용한 Import/Export를 선택합니다.

물리적 디바이스가 올바른 옵션인지 확인하려면 다음 표를 사용합니다. 이 표에는 사용 가능한 다양한 대역폭(90% 사용률 가정)에서 네트워크 데이터 전송의 예상 시간이 나와 있습니다. 네트워크 전송이 너무 느릴 것으로 예상되는 경우 물리적 디바이스를 사용해야 합니다.  

![네트워크 전송 또는 오프라인 전송](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>권장 옵션

이 시나리오에서 사용할 수 있는 옵션은 Azure Data Box 오프라인 전송용 디바이스 또는 Azure Import/Export입니다.

- **오프라인 전송을 위한 Azure Data Box 제품군** - 시간, 네트워크 가용성 또는 비용으로 제한되는 경우 Microsoft에서 제공하는 Data Box 디바이스를 사용하여 대량 데이터를 Azure로 이동할 수 있습니다. Robocopy와 같은 도구를 사용하여 온-프레미스 데이터를 복사합니다. 전송하려는 데이터 크기에 따라 Data Box Disk, Data Box 또는 Data Box Heavy 중에서 선택할 수 있습니다.
- **Azure Import/Export** – 사용자 고유의 디스크 드라이브를 배송하여 Azure Import/Export 서비스를 사용하면 대량 데이터를 Azure Blob Storage 및 Azure Files로 안전하게 가져올 수 있습니다. 이 서비스를 사용하여 데이터를 Azure Blob Storage에서 디스크 드라이브로 전송하고 온-프레미스 사이트로 발송할 수도 있습니다.

## <a name="comparison-of-key-capabilities"></a>주요 기능 비교

다음 표에서는 주요 기능의 차이점을 요약해서 보여 줍니다.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    데이터 크기                        |    최대 35TB                 |    디바이스당 최대 80TB                       |    디바이스당 최대 800TB               |    변수                            |
|    데이터 형식                        |    Azure Blob                  |    Azure Blob<br>Azure 파일                    |    Azure Blob<br>Azure 파일            |    Azure Blob<br>Azure 파일          |
|    폼 팩터                      |    주문당 5개 SSD             |    1 X 50lbs 주문당 데스크톱 크기 디바이스    |    1 X ~500lbs 주문당 대형 디바이스    |    주문당 최대 10개 HDD/SSD        |
|    초기 설치 시간               |    낮음 <br>(15분)            |    낮음-보통 <br> (30분 미만)               |    보통<br>(1~2시간)               |    보통-어려움<br>(가변적) |
|    Azure에 데이터 보내기               |    예                          |    예                                           |    예                                   |    예                                 |
|    Azure에서 데이터 가져오기           |    아닙니다.                           |    아니요                                            |    아니요                                    |    예                                 |
|    암호화                       |    AES 128비트                  |    AES 256비트                                   |    AES 256비트                           |    AES 128비트                         |
|    하드웨어                         |     Microsoft 제공          |    Microsoft 제공                            |    Microsoft 제공                    |    고객 제공                   |
|    Linux                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    파트너 통합              |    일부                         |    [높음](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [높음](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    일부                                |
|    발송                         |    Microsoft 관리            |    Microsoft 관리                             |    Microsoft 관리                     |    고객 관리                    |
| 데이터 이동 시 사용         |상거래 경계 이내|상거래 경계 이내|상거래 경계 이내|지리적 경계 간(예: 미국에서 EU로 이동)|
|    가격                          |    [가격](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [가격](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [가격](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [가격](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>다음 단계

- 다음 작업을 수행하는 방법을 이해합니다.

    - [Data Box Disk를 사용하여 데이터 전송](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)
    - [Data Box를 사용하여 데이터 전송](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)
    - [Import/Export를 사용하여 데이터 전송](/azure/storage/common/storage-import-export-data-to-blobs)
