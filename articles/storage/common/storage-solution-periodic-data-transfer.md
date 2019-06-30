---
title: 정기적 데이터 전송을 위한 Azure 솔루션 선택 | Microsoft Docs
description: 데이터를 정기적으로 전송하는 경우 데이터 전송을 위한 Azure 솔루션을 선택하는 방법을 알아봅니다.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fb49802adf6242f445b700d06622d7e6aa336b4d
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357032"
---
# <a name="solutions-for-periodic-data-transfer"></a>정기적 데이터 전송을 위한 솔루션
 
이 문서에서는 정기적으로 데이터를 전송하는 경우의 데이터 전송 솔루션에 대해 간략하게 설명합니다. 네트워크를 통한 정기적 데이터 전송은 일정한 간격마다 발생 또는 지속적인 데이터 이동으로 분류될 수 있습니다. 이 시나리오에서 권장되는 데이터 전송 옵션 및 각각의 주요 기능 매트릭스에 대해서도 설명합니다.

사용 가능한 모든 데이터 전송 옵션에 대한 개요를 보려면 [Azure 데이터 전송 솔루션 선택](storage-choose-data-transfer-solution.md)으로 이동하세요.

## <a name="recommended-options"></a>권장 옵션

정기적 데이터 전송에 대한 권장 옵션은 전송이 반복인지 지속적인지에 따라 두 가지 범주로 분류됩니다.

- **스크립팅/프로그래밍 방식 도구** - 일정한 간격마다 발생하는 데이터 전송의 경우 AzCopy, Azure Storage REST API 등의 스크립팅 및 프로그래밍 방식 도구를 사용합니다. 이러한 도구는 IT 전문가 및 개발자를 대상으로 합니다.

    - **AzCopy** - 이 명령줄 도구를 사용하여 Azure Blob, Files 및 Table Storage에서 최적 성능으로 데이터를 쉽게 복사할 수 있습니다. AzCopy는 동시성 및 병렬 처리 기능과 중단된 복사 작업을 다시 시작하는 기능을 지원합니다.
    - **Azure Storage REST API/SDK** - 애플리케이션을 빌드할 때 Azure Storage REST API에 대해 애플리케이션을 개발하고 여러 언어로 제공되는 Azure SDK를 사용할 수 있습니다. 또한 REST API는 Azure와의 데이터 고성능 복사를 위해 특별히 설계된 Azure Storage 데이터 이동 라이브러리를 활용할 수 있습니다.

- **지속적인 데이터 수집 도구** - 지속적인 데이터 수집을 위해 Data Box 온라인 전송 디바이스 또는 Azure Data Factory 중 하나를 선택할 수 있습니다. 이러한 도구는 IT 전문가가 설정하며 데이터 전송을 투명하게 자동화할 수 있습니다.

    - **Azure Data Factory** - Data Factory는 전송 작업을 확장하거나 오케스트레이션 및 엔터프라이즈급 모니터링 기능이 필요한 경우에 사용해야 합니다. Azure Data Factory를 사용하여 여러 Azure 서비스, 온-프레미스 또는 둘 다의 조합 간에 정기적으로 파일을 전송하는 클라우드 파이프라인을 설정할 수 있습니다. Azure Data Factory를 통해 개별 데이터 저장소의 데이터를 수집하고 데이터 이동 및 데이터 변환을 자동화하는 데이터 기반 워크플로를 오케스트레이션할 수 있습니다.
    - **온라인 전송을 위한 Azure Data Box 제품군** - Data Box Edge 및 Data Box Gateway는 Azure에(서) 데이터를 이동할 수 있는 온라인 네트워크 디바이스입니다. Data Box Edge는 AI 지원 Edge 컴퓨팅을 사용하여 업로드 전에 데이터를 사전 처리합니다. Data Box Gateway는 동일한 데이터 전송 기능을 제공하는 가상 디바이스 버전입니다.


## <a name="comparison-of-key-capabilities"></a>주요 기능 비교

다음 표에서는 주요 기능의 차이점을 요약해서 보여 줍니다.

### <a name="scriptedprogrammatic-network-data-transfer"></a>스크립팅/프로그래밍 방식 네트워크 데이터 전송

| 기능                  | AzCopy                                 | Azure Storage REST API       |
|-----------------------------|----------------------------------------|-------------------------------|
| 폼 팩터                 | Microsoft의 명령줄 도구       | 고객이 Storage에 대해 개발함 <br> Azure 클라이언트 라이브러리를 사용하는 REST API |
| 초기 일회성 설치     | 최소                                | 보통, 가변 개발 작업    |
| 데이터 형식                 | Azure Blob, Azure Files, Azure 테이블 | Azure Blob, Azure Files, Azure 테이블   |
| 성능                 | 이미 최적화됨                      | 개발 시 최적화                  |
| 가격                     | 무료, 데이터 송신 요금 적용      | 무료, 데이터 송신 요금 적용        |

### <a name="continuous-data-ingestion-over-network"></a>네트워크를 통한 지속적인 데이터 수집

| 기능                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| 폼 팩터                                   | 가상 디바이스             | 물리적 디바이스          | Azure Portal의 서비스, 온-프레미스의 에이전트                                                            |
| 하드웨어                                      | 하이퍼바이저            | Microsoft 제공    | 해당 없음                                                            |
| 초기 설치 작업                          | 작음(30분 미만)            | 보통(~몇 시간) | 큼(~며칠)                                                 |
| 데이터 형식                                   | Azure Blob, Azure Files   | Azure Blob, Azure Files | [데이터 저장소 및 형식을 위해 70개 이상의 데이터 커넥터 지원](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| 데이터 사전 처리                           | 아닙니다.                         | 예, Edge 컴퓨팅 사용    | 예                                                           |
| 로컬 캐시<br>(온-프레미스 데이터 저장)    | 예                        | 예                      | 아닙니다.                                                            |
| 다른 클라우드에서 전송                    | 아닙니다.                         | 아니요                       | 예                                                           |
| 가격                                       | [가격](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [가격](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [가격](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>다음 단계

- [AzCopy를 사용하여 데이터 전송](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Storage REST API를 사용한 데이터 전송에 대한 자세한 정보](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet)
- 다음 작업을 수행하는 방법을 이해합니다.
    - [Data Box Gateway를 통해 데이터 전송](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Azure로 보내기 전에 Data Box Edge를 사용하여 데이터 변환](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute)
- [Azure Data Factory를 사용하여 데이터를 전송하는 방법 알아보기](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal)
