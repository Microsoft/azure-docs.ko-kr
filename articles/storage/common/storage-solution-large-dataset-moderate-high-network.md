---
title: 보통-높은 네트워크 대역폭에서 대용량 데이터 세트를 위한 Azure 데이터 전송 옵션 | Microsoft Docs
description: 사용자 환경의 네트워크 대역폭이 보통-높은 수준이며 대용량 데이터 세트를 전송하려는 경우 데이터 전송을 위한 Azure 솔루션을 선택하는 방법을 알아봅니다.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 8dd55032c933cdc31b848addfdac991550376dcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729236"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>보통-높은 네트워크 대역폭에서 대용량 데이터 세트의 데이터 전송
 
이 문서에서는 사용자 환경의 네트워크 대역폭이 보통-높은 수준이며 대용량 데이터 세트를 전송하려는 경우의 데이터 전송 솔루션에 대해 간략하게 설명합니다. 이 시나리오에서 권장되는 데이터 전송 옵션 및 각각의 주요 기능 매트릭스에 대해서도 설명합니다.

사용 가능한 모든 데이터 전송 옵션에 대한 개요를 보려면 [Azure 데이터 전송 솔루션 선택](storage-choose-data-transfer-solution.md)으로 이동하세요.

## <a name="scenario-description"></a>시나리오 설명

대용량 데이터 세트는 TB에서 PB 단위의 데이터 크기를 가리킵니다. 보통-높은 네트워크 대역폭은 100Mbps~10Gbps를 가리킵니다.

## <a name="recommended-options"></a>권장 옵션

이 시나리오에서 권장되는 옵션은 보통 네트워크 대역폭을 사용하는지 또는 높은 네트워크 대역폭을 사용하는지에 따라 달라집니다.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>보통 네트워크 대역폭(100Mbps~1Gbps)

보통 네트워크 대역폭을 사용하는 경우 네트워크를 통한 데이터 전송 시간을 예상해야 합니다.

다음 표를 사용하여 시간을 예상하고, 이 시간에 따라 오프라인 전송 또는 네트워크를 통한 전송 중에서 선택합니다. 이 표에는 사용 가능한 다양한 네트워크 대역폭(90% 사용률 가정)에서 네트워크 데이터 전송의 예상 시간이 나와 있습니다.  

![네트워크 전송 또는 오프라인 전송](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- 네트워크 전송이 너무 느릴 것으로 예상되는 경우 물리적 디바이스를 사용해야 합니다. 이 경우 권장되는 옵션은 Azure Data Box 제품군의 오프라인 전송 디바이스 또는 사용자 고유의 디스크를 사용한 Azure Import/Export입니다.

    - **오프라인 전송을 위한 Azure Data Box 제품군** - 시간, 네트워크 가용성 또는 비용으로 제한되는 경우 Microsoft에서 제공하는 Data Box 디바이스를 사용하여 대량 데이터를 Azure로 이동할 수 있습니다. Robocopy와 같은 도구를 사용하여 온-프레미스 데이터를 복사합니다. 전송하려는 데이터 크기에 따라 Data Box Disk, Data Box 또는 Data Box Heavy 중에서 선택할 수 있습니다.
    - **Azure Import/Export** – 사용자 고유의 디스크 드라이브를 배송하여 Azure Import/Export 서비스를 사용하면 대량 데이터를 Azure Blob Storage 및 Azure Files로 안전하게 가져올 수 있습니다. 이 서비스를 사용하여 데이터를 Azure Blob Storage에서 디스크 드라이브로 전송하고 온-프레미스 사이트로 발송할 수도 있습니다.

- 네트워크 전송이 적절한 것으로 예상되는 경우 [높은 네트워크 대역폭](#high-network-bandwidth)에서 자세히 설명하는 다음 도구를 사용할 수 있습니다.


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>높은 네트워크 대역폭(1Gbps~100Gbps)

사용 가능한 네트워크 대역폭이 높은 수준인 경우 다음 도구 중 하나를 사용합니다.

- **AzCopy** - 이 명령줄 도구를 사용하여 Azure Blob, Files 및 Table Storage에서 최적 성능으로 데이터를 쉽게 복사할 수 있습니다. AzCopy는 동시성 및 병렬 처리 기능과 중단된 복사 작업을 다시 시작하는 기능을 지원합니다.
- **Azure Storage REST API/SDK** - 애플리케이션을 빌드할 때 Azure Storage REST API에 대해 애플리케이션을 개발하고 여러 언어로 제공되는 Azure SDK를 사용할 수 있습니다.
- **온라인 전송을 위한 Azure Data Box 제품군** - Data Box Edge 및 Data Box Gateway는 Azure에(서) 데이터를 이동할 수 있는 온라인 네트워크 디바이스입니다. 지속적인 수집 및 업로드 전에 데이터 사전 처리가 동시에 필요한 경우 Data Box Edge 물리적 디바이스를 사용합니다. Data Box Gateway는 동일한 데이터 전송 기능을 제공하는 가상 디바이스 버전입니다. 각 경우에 데이터 전송은 디바이스에서 관리됩니다.
- **Azure Data Factory** - Data Factory는 전송 작업을 확장하거나 오케스트레이션 및 엔터프라이즈급 모니터링 기능이 필요한 경우에 사용해야 합니다. Data Factory를 사용하여 여러 Azure 서비스, 온-프레미스 또는 둘 다의 조합 간에 정기적으로 파일을 전송할 수 있습니다. Data Factory를 통해 개별 데이터 저장소의 데이터를 수집하고 데이터 이동 및 데이터 변환을 자동화하는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 예약할 수 있습니다.

## <a name="comparison-of-key-capabilities"></a>주요 기능 비교

다음 표에서는 권장 옵션의 주요 기능 차이점을 요약해서 보여 줍니다.

### <a name="moderate-network-bandwidth"></a>보통 네트워크 대역폭

오프라인 데이터 전송을 사용하는 경우 다음 표를 사용하여 주요 기능의 차이점을 파악합니다.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
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


온라인 데이터 전송을 사용하는 경우 높은 네트워크 대역폭에 대한 다음 섹션의 표를 사용합니다.

### <a name="high-network-bandwidth"></a>높은 네트워크 대역폭

|                                     |    도구 AzCopy, <br>Azure PowerShell, <br>Azure CLI             |    Azure Storage REST API, SDK                   |    데이터 상자 게이트웨이나 가장자리가 상자의 데이터          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    데이터 형식                  |    Azure Blob, Azure Files, Azure 테이블    |    Azure Blob, Azure Files, Azure 테이블    |    Azure Blob, Azure Files                           |   데이터 저장소 및 형식을 위해 70개 이상의 데이터 커넥터 지원    |
|    폼 팩터                |    명령줄 도구                        |    프로그래밍 인터페이스                    |    Microsoft에서 가상 <br>또는 물리적 디바이스 제공     |    Azure Portal의 서비스                                            |
|    초기 일회성 설치     |    쉬움               |    보통                       |    쉬움(30분 미만)~보통(1~2시간)            |    광범위                                                          |
|    데이터 사전 처리              |    아닙니다.                                        |    아닙니다.                                        |    예(Edge 컴퓨팅 사용)                               |    예                                                                |
|    다른 클라우드에서 전송       |    아닙니다.                                        |    아니요                                        |    아니요                                                    |    예                                                                |
|    사용자 유형                        |    IT 전문가 또는 개발자                                       |    개발                                       |    IT 전문가                                                |    IT 전문가                                                             |
|    가격                          |    무료, 데이터 송신 요금 적용         |    무료, 데이터 송신 요금 적용         |    [가격](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [가격](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>다음 단계

- [Import/Export를 사용하여 데이터를 전송하는 방법 알아보기](/azure/storage/common/storage-import-export-data-to-blobs)
- 다음 작업을 수행하는 방법을 이해합니다.

    - [Data Box Disk를 사용하여 데이터 전송](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)
    - [Data Box를 사용하여 데이터 전송](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)
- [AzCopy를 사용하여 데이터 전송](/azure/storage/common/storage-use-azcopy-v10)
- 다음 작업을 수행하는 방법을 이해합니다.
    - [Data Box Gateway를 통해 데이터 전송](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Azure로 보내기 전에 Data Box Edge를 사용하여 데이터 변환](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute)
- [Azure Data Factory를 사용하여 데이터를 전송하는 방법 알아보기](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)
- REST API를 사용하여 데이터 전송

    - [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
