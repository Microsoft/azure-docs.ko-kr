---
title: Data lake 및 data warehouse에서 Azure로 데이터 마이그레이션
description: Azure Data Factory를 사용 하 여 data lake 및 데이터 웨어하우스의 데이터를 Azure로 마이그레이션합니다.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: aaf1593cc049e8b23f8ebe36fea022b3029ccd04
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930794"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory를 사용 하 여 data lake 또는 데이터 웨어하우스의 데이터를 Azure로 마이그레이션

Data lake 또는 EDW (엔터프라이즈 데이터 웨어하우스)를 Microsoft Azure로 마이그레이션하려면 Azure Data Factory를 사용 하는 것이 좋습니다. Azure Data Factory는 다음과 같은 시나리오에 적합 합니다.

- Amazon S3 (Amazon Simple Storage Service) 또는 HDFS (온-프레미스 Hadoop 분산 파일 시스템)에서 Azure로 빅 데이터 워크 로드 마이그레이션
- Oracle Exadata, Netezza, Teradata 또는 Amazon Redshift에서 Azure로의 EDW 마이그레이션

Data lake migration의 경우 페타바이트 (PB) 데이터를 이동 하 고 데이터 웨어하우스 마이그레이션을 위해 수십 테라바이트의 데이터를 이동할 수 있습니다. Azure Data Factory

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>데이터 마이그레이션에 Azure Data Factory를 사용할 수 있는 이유

- 성능, 복원 력 및 확장성이 뛰어난 서버를 사용 하지 않는 방식으로 데이터를 이동 하는 처리 능력의 양을 쉽게 확장할 수 Azure Data Factory. 사용한 만큼만 요금을 지불 하면 됩니다. 그리고 다음 사항도 참조하세요. 
  - Azure Data Factory에는 데이터 볼륨 또는 파일 수에 대한 제한이 없습니다.
  - Azure Data Factory는 네트워크 및 저장소 대역폭을 완벽 하 게 사용 하 여 사용자 환경에서 가장 높은 데이터 이동 처리량을 달성할 수 있습니다.
  - Azure Data Factory는 종 량 제 방법을 사용 하므로 Azure로의 데이터 마이그레이션을 실행 하는 데 실제로 사용한 시간에 대해서만 비용을 지불 합니다.  
- Azure Data Factory는 일회성 기록 로드와 예약 된 증분 로드를 모두 수행할 수 있습니다.
- Azure Data Factory는 Azure IR (통합 런타임)을 사용 하 여 공개적으로 액세스할 수 있는 data lake 및 웨어하우스 엔드포인트 간에 데이터를 이동 합니다. 또한 VNet (Azure Virtual Network) 내에서 data lake 및 웨어하우스 엔드포인트에 대한 데이터를 이동 하거나 방화벽 뒤에 있는 자체 호스팅 IR을 사용할 수 있습니다.
- 엔터프라이즈급 보안 Azure Data Factory: 보안 서비스 간 통합을 위해 MSI (Windows Installer) 또는 서비스 Id를 사용 하거나 자격 증명 관리에 Azure Key Vault를 사용할 수 있습니다.
- Azure Data Factory은 코드 없는 제작 환경과 풍부한 기본 제공 모니터링 대시보드를 제공 합니다.  

## <a name="online-vs-offline-data-migration"></a>온라인 및 오프 라인 데이터 마이그레이션

Azure Data Factory는 네트워크 (인터넷, ER 또는 VPN)를 통해 데이터를 전송 하는 표준 온라인 데이터 마이그레이션 도구입니다. 오프 라인 데이터 마이그레이션을 사용 하는 경우 사용자는 조직에서 Azure 데이터 센터로 데이터 전송 장치를 실제로 제공 합니다.  

온라인 및 오프 라인 마이그레이션 방법 중에서 선택할 때 세 가지 주요 고려 사항이 있습니다.  

- 마이그레이션할 데이터 크기
- 네트워크 대역폭
- 마이그레이션 창

예를 들어 Azure Data Factory를 사용 하 여 2 주 ( *마이그레이션 창*) 내에서 데이터 마이그레이션을 완료할 계획인 경우를 가정 합니다. 다음 표에서 분홍색/파랑 줄을 확인 합니다. 지정 된 열에 대한 가장 낮은 분홍색 셀은 마이그레이션 기간이 2 주 미만이 가장 가까운 데이터 크기/네트워크 대역폭 페어링을 표시 합니다. 파랑 셀의 모든 크기/대역폭 페어링에는 2 주 이상의 온라인 마이그레이션 기간이 있습니다. 

온라인 및 오프 라인](media/data-migration-guidance-overview/online-offline.png) ![이 표를 사용 하 여 데이터 크기 및 사용 가능한 네트워크 대역폭을 기반으로 하는 온라인 마이그레이션 (Azure Data Factory)을 통해 원하는 마이그레이션 기간을 충족할 수 있는지 여부를 확인할 수 있습니다. 온라인 마이그레이션 기간이 2 주 이상 이면 오프 라인 마이그레이션을 사용 하는 것이 좋습니다.

> [!NOTE]
> 온라인 마이그레이션을 사용 하면 단일 도구를 통해 기록 데이터 로드 및 증분 피드를 모두 달성할 수 있습니다.  이 방법을 통해 전체 마이그레이션 기간 동안 기존 저장소와 새 저장소 간에 데이터를 동기화 된 상태로 유지할 수 있습니다. 즉, 새로 고쳐진 데이터를 사용 하 여 새 저장소에서 ETL 논리를 다시 빌드할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [AWS S3에서 Azure로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)
- [온-프레미스 hadoop 클러스터에서 Azure로 데이터 마이그레이션](data-migration-guidance-hdfs-azure-storage.md)
- [온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션](data-migration-guidance-netezza-azure-sqldw.md)
