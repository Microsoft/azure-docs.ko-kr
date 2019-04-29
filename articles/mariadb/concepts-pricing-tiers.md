---
title: Azure Database for MariaDB의 가격 책정 계층
description: 이 문서에서는 Azure Database for MariaDB의 가격 책정 계층을 설명합니다.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5eb2ba509983918a55370ae0deafd019e03f53d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740287"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB 가격 책정 계층

Azure Database for MariaDB 서버는 기본, 범용 및 메모리 최적화의 세 가지 가격 책정 계층 중 하나에서 만들 수 있습니다. 가격 책정 계층은 프로비전할 수 있는 계산의 vCore 수, vCore당 메모리 및 데이터를 저장하는 데 사용되는 스토리지 기술로 구분됩니다. 모든 리소스는 MariaDB 서버 수준에서 프로비전됩니다. 서버는 하나 이상의 데이터베이스를 갖출 수 있습니다.

|    | **Basic** | **범용** | **메모리 최적화** |
|:---|:----------|:--------------------|:---------------------|
| 계산 세대 | 5세대 |5세대 | 5세대 |
| vCore 수 | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| vCore 메모리 | 2GB | 5GB | 10 GB |
| 저장소 크기 | 5GB-1TB | 5GB-4TB | 5GB-4TB |
| 저장소 유형 | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| 데이터베이스 백업 보존 기간 | 7-35일 | 7-35일 | 7-35일 |

가격 책정 계층을 선택하려면 시작 지점으로 다음 표를 사용합니다.

| 가격 책정 계층  | 대상 워크로드 |
|:-------------|:-----------------|
| Basic | 간단한 계산 및 I/O 성능이 필요한 워크로드. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 애플리케이션에 사용되는 서버가 이에 해당합니다. |
| 범용 | 확장 가능한 I/O 처리량을 갖춘 부하 분산된 컴퓨팅 및 메모리가 필요한 대부분의 비즈니스 워크로드. 예를 들어 웹 및 모바일 앱을 호스트하는 서버와 기타 엔터프라이즈 애플리케이션이 있습니다.|
| 메모리 최적화 | 빠른 트랜잭션 처리와 높은 동시성을 위해 메모리 내 성능이 필요한 고성능 데이터베이스 워크로드. 예를 들어 실시간 데이터를 처리하는 서버 및 고성능 트랜잭션 또는 분석 앱이 있습니다.|

서버를 만든 후 vCore 수 및 가격 책정 계층(기본 제외)은 몇 초 이내로 늘리거나 줄일 수 있습니다. 또한 애플리케이션 중단 시간 없이 독립적으로 스토리지 용량을 늘리거나 백업 보존 기간을 늘리거나 줄일 수 있습니다. 서버가 만들어진 후 백업 저장소 유형은 변경할 수 없습니다. 자세한 내용은 [리소스 크기 조정](#scale-resources) 섹션을 참조하세요.

## <a name="compute-generations-and-vcores"></a>세대 및 vCore 수 계산

계산 리소스는 기본 하드웨어의 논리적 CPU를 나타내는 vCore 수로 제공됩니다. 5세대 논리적 CPU는 Intel E5-2673 v4(Broadwell) 2.3GHz 프로세서를 기반으로 합니다.

## <a name="storage"></a>Storage

프로비전하는 저장소는 Azure Database for MariaDB 서버에 사용할 수 있는 저장소 용량입니다. 저장소는 데이터베이스 파일, 임시 파일, 트랜잭션 로그 및 MariaDB 서버 로그에 사용됩니다. 프로비전하는 총 저장소 용량도 서버에 사용할 수 있는 I/O 용량을 정의합니다.

|    | **Basic** | **범용** | **메모리 최적화** |
|:---|:----------|:--------------------|:---------------------|
| 저장소 유형 | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| 저장소 크기 | 5GB-1TB | 5GB-4TB | 5GB-4TB |
| 저장소 증분 크기 | 1 GB | 1GB | 1 GB |
| IOPS | 변수 |3IOPS/GB<br/>최소 100IOPS<br/>최대 6000IOPS | 3IOPS/GB<br/>최소 100IOPS<br/>최대 6000IOPS |

추가 저장소 용량은 서버를 만드는 동안 및 그 후에 추가할 수 있습니다. 기본 계층에서는 IOPS 보장을 제공하지 않습니다. 범용 및 메모리 최적화 가격 책정 계층에서 IOPS의 크기는 프로비전된 저장소 크기와 3:1 비율로 조정됩니다.

Azure Portal 또는 Azure CLI 명령을 사용하여 I/O 사용량을 모니터링할 수 있습니다. 모니터링할 관련 메트릭은 [저장소 제한, 저장소 비율, 저장소 사용됨 및 IO 백분율](concepts-monitoring.md)입니다.

### <a name="reaching-the-storage-limit"></a>저장소 제한에 도달

여유 저장 공간 용량이 프로비전된 저장소의 5% 또는 5GB 미만 중에서 더 작은 용량이 되면 서버는 읽기 전용으로 표시됩니다. 예를 들어 100GB의 저장소를 프로비전하고 실제 활용이 95GB를 넘어서는 경우 서버는 읽기 전용으로 표시됩니다. 또는 5GB 저장소를 프로비전하는 경우 서버는 여유 저장 공간이 250MB 미만이 되면 읽기 전용으로 표시됩니다.  

서비스가 서버를 읽기 전용으로 만들려고 하는 동안 모든 새 쓰기 트랜잭션 요청은 차단되고 기존 활성 트랜잭션은 계속 실행됩니다. 서버가 읽기 전용으로 설정되면 모든 후속 쓰기 작업 및 트랜잭션 커밋은 실패합니다. 읽기 쿼리는 중단 없이 계속 작동합니다. 프로비전된 저장소를 늘린 후 서버는 다시 쓰기 트랜잭션을 허용할 준비를 갖춥니다.

서버 저장소가 임계값에 근접하는 경우 읽기 전용 상태로 들어가는 것을 방지할 수 있도록 알림 경고를 설정하는 것이 좋습니다. 

자세한 내용은 [경고 설정 방법](howto-alert-metric.md)에 관한 설명서를 참조하세요.

## <a name="backup"></a>Backup

서비스에서 서버 백업을 자동으로 수행합니다. 최소 백업 보존 기간은 7일입니다. 보존 기간은 최대 35일까지 설정할 수 있습니다. 보존 기간은 서버 수명 기간 동안 언제든지 조정할 수 있습니다. 로컬 중복 백업과 지역 중복 백업 중에서 선택할 수 있습니다. 지역 중복 백업은 서버가 만들어진 지역의 [쌍으로 연결된 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)에도 저장됩니다. 이 중복성은 재해 발생 시 일정 수준의 보호를 제공합니다. 또한 지역 중복 백업을 통해 서비스를 사용할 수 있는 다른 Azure 지역으로 서버를 복원할 수 있습니다. 서버가 만들어진 후 두 개의 백업 저장소 옵션 간에 변경할 수 없습니다.

## <a name="scale-resources"></a>리소스 크기 조정

서버를 만든 후 vCore 수, 가격 책정 계층(기본 제외), 저장소 크기 및 백업 보존 기간을 독립적으로 변경할 수 있습니다. 서버가 만들어진 후 백업 저장소 유형은 변경할 수 없습니다. vCore 수는 늘리거나 줄일 수 있습니다. 백업 보존 기간은 7~35일 범위에서 늘리거나 줄일 수 있습니다. 저장소 크기는 늘릴 수 있습니다. 리소스의 크기 조정은 포털 또는 Azure CLI를 통해 수행할 수 있습니다. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

vCore 수 또는 가격 책정 계층을 변경하면 새 계산 할당을 사용하여 원본 서버의 복사본이 만들어집니다. 새 서버가 시작되고 실행된 후 새 서버에 대한 연결로 전환됩니다. 시스템이 새 서버로 전환되면 잠시 동안 새 연결을 설정할 수 없으며, 커밋되지 않은 모든 트랜잭션이 롤백됩니다. 이 기간은 다양하지만, 대부분의 경우 1분 미만입니다.

저장소 크기 조정 및 백업 보존 기간 변경은 온라인 작업입니다. 가동 중지 시간이 없으며 애플리케이션은 영향을 받지 않습니다. IOPS가 프로비전된 저장소 크기로 조정되면 저장소를 확장하여 서버에서 사용할 수 있는 IOPS를 늘릴 수 있습니다.

## <a name="pricing"></a>가격

최신 가격 책정 정보는 서비스 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/mariadb/)를 참조하세요. 원하는 구성 비용을 확인하려면 [Azure Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer)에서 선택한 옵션에 따라 **가격 책정 계층** 탭에 월별 비용이 표시됩니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상 가격을 구할 수 있습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트에서 **항목 추가**를 선택하고, **데이터베이스** 범주를 확장하고, **Azure Database for MariaDB**를 선택하여 옵션을 사용자 지정합니다.

## <a name="next-steps"></a>다음 단계
- [서비스 제한 사항](concepts-limits.md)에 대해 알아봅니다.
- [Azure Portal에서 MariaDB 서버를 만드는 방법](quickstart-create-mariadb-server-database-using-azure-portal.md)을 알아봅니다.

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
