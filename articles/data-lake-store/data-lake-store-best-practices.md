---
title: Azure Data Lake Storage Gen1을 사용하는 모범 사례 | Microsoft Docs
description: Azure Data Lake Storage Gen1(이전에 Azure Data Lake Store라고 함) 사용과 관련된 데이터 수집, 날짜 보안 및 성능에 대한 모범 사례를 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 50d0ed644b5afa744e8bce478199079fd4fb7432
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878961"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1을 사용하는 모범 사례

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

이 문서에서는 Azure Data Lake Storage Gen1 작업에 대한 모범 사례와 고려 사항에 대해 알아 봅니다. 여기서는 Data Lake Storage Gen1의 보안, 성능, 복원력 및 모니터링에 대해 설명합니다. Data Lake Storage Gen1 이전에는 Azure HDInsight와 같은 서비스에서 진정한 빅 데이터를 사용하는 것이 복잡했습니다. 여러 Blob Storage 계정에서 데이터를 분할하여 페타바이트 스토리지와 최적의 성능을 얻을 수 있도록 해야 했습니다. Data Lake Storage Gen1을 사용하면 크기와 성능에 대한 대부분의 엄격한 한도가 제거됩니다. 그러나 이 문서에서 다루는 몇 가지 고려 사항은 Data Lake Storage Gen1에서 최상의 성능을 얻을 수 있도록 하기 위한 것입니다.

## <a name="security-considerations"></a>보안 고려 사항

Azure Data Lake Storage Gen1은 Azure AD(Azure Active Directory) 사용자, 그룹 및 서비스 사용자에 대한 POSIX 액세스 제어 및 자세한 감사 기능을 제공합니다. 이러한 액세스 제어는 기존 파일 및 폴더에 설정할 수 있습니다. 액세스 제어를 사용하여 새 파일이나 폴더에 적용할 수 있는 기본값을 만들 수도 있습니다. 권한이 기존 폴더 및 자식 개체에 설정되면 권한을 각 개체에 재귀적으로 전파해야 합니다. 파일 수가 많은 경우 사용 권한을 전파하는 데 오랜 시간이 걸릴 수 있습니다. 소요 시간은 초당 처리되는 30-50개 개체 사이의 범위일 수 있습니다. 따라서 폴더 구조와 사용자 그룹을 적절하게 계획합니다. 그렇지 않으면 데이터를 사용할 때 예기치 않은 지연과 문제가 발생할 수 있습니다.

100,000개의 자식 개체가 있는 폴더가 있다고 가정합니다. 초당 처리되는 30개 개체의 하한 값을 사용하면 전체 폴더에 대한 권한을 업데이트하는 데 1시간이 걸릴 수 있습니다. Data Lake Storage Gen1 ACL에 대한 자세한 내용은 [Azure Data Lake Storage Gen1의 액세스 제어](data-lake-store-access-control.md)에서 확인할 수 있습니다. ACL을 재귀적으로 할당할 때 성능을 향상시키기 위해 Azure Data Lake 명령줄 도구를 사용할 수 있습니다. 이 도구는 다중 스레드와 재귀 탐색 논리를 만들어 수백만 개의 파일에 ACL을 빠르게 적용합니다. 이 도구는 Linux 및 Windows에서 사용할 수 있으며, 이 도구에 대한 [설명서](https://github.com/Azure/data-lake-adlstool) 및 [다운로드](https://aka.ms/adlstool-download)는 GitHub에서 찾을 수 있습니다. Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) 및 [Java](data-lake-store-get-started-java-sdk.md) SDK로 작성된 고유한 도구에서 이러한 동일한 성능 개선을 설정할 수 있습니다.

### <a name="use-security-groups-versus-individual-users"></a>보안 그룹 및 개별 사용자 사용

Data Lake Storage Gen1에서 빅 데이터로 작업할 때는 대부분의 경우 Azure HDInsight와 같은 서비스가 데이터 작업을 수행할 수 있도록 서비스 사용자가 사용됩니다. 그러나 개별 사용자가 데이터에 액세스해야 하는 경우도 있을 수 있습니다. 이 경우 개별 사용자를 폴더 및 파일에 할당하는 대신 Azure Active Director [보안 그룹](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory)을 사용해야 합니다.

보안 그룹에 권한이 할당되면 그룹에서 사용자를 추가하거나 제거할 때 Data Lake Storage Gen1을 업데이트할 필요가 없습니다. 이렇게 하면 [32개의 액세스 및 기본 ACL](../azure-subscription-service-limits.md#data-lake-store-limits) 제한을 초과하지 않는 데 도움이 됩니다(여기에는 항상 모든 파일 및 폴더과 연결된 4개의 POSIX 스타일 ACL, 즉 [소유 사용자](data-lake-store-access-control.md#the-owning-user), [소유 그룹](data-lake-store-access-control.md#the-owning-group), [마스크](data-lake-store-access-control.md#the-mask) 및 기타가 포함됨).

### <a name="security-for-groups"></a>그룹에 대한 보안

설명한 대로 사용자가 Data Lake Storage Gen1에 액세스해야 하는 경우 Azure Active Directory 보안 그룹을 사용하는 것이 가장 좋습니다. 시작하는 데 권장되는 몇 가지 그룹은 계정의 루트에 대해 **ReadOnlyUsers**, **WriteAccessUsers** 및 **FullAccessUsers**이고, 키 하위 폴더에 대해서는 별도의 그룹일 수도 있습니다. 나중에 추가될 수 있지만 아직은 확인되지 않은 것으로 예상되는 다른 사용자 그룹이 있는 경우, 특정 폴더에 대한 액세스 권한이 있는 더미 보안 그룹을 만드는 것을 고려할 수 있습니다. 보안 그룹을 사용하면 나중에 수천 개의 파일에 새 권한을 할당하는 데 긴 처리 시간이 필요하지 않습니다.

### <a name="security-for-service-principals"></a>서비스 사용자에 대한 보안

Azure Active Directory 서비스 사용자는 일반적으로 Azure HDInsight와 같은 서비스에서 Data Lake Storage Gen1의 데이터에 액세스하는 데 사용됩니다. 여러 작업 간의 액세스 요구 사항에 따라 조직 내부 및 외부의 보안을 보장하기 위한 몇 가지 고려 사항이 있을 수 있습니다. 많은 고객에게 단일 Azure Active Directory 서비스 사용자가 적합할 수 있으며 Data Lake Storage Gen1 계정의 루트에서 모든 권한을 가질 수 있습니다. 한 클러스터에는 데이터에 대한 모든 액세스 권한이 있고 다른 클러스터에는 읽기 전용 액세스 권한이 있는 다른 고객에게는 서로 다른 서비스 사용자가 있는 여러 클러스터가 필요할 수 있습니다. 보안 그룹과 마찬가지로 Data Lake Storage Gen1 계정이 만들어지면 예상되는 각 시나리오(읽기, 쓰기, 모두)별로 서비스 사용자를 만드는 것이 좋습니다.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Azure 서비스 액세스 권한으로 Data Lake Storage Gen1 방화벽 사용

Data Lake Storage Gen1은 방화벽을 설정하고 액세스를 Azure 서비스만으로 제한하는 옵션을 지원합니다. 이 옵션은 외부 침입으로 인한 공격 벡터가 더 작은 경우에 사용하는 것이 좋습니다. Azure Portal의 **방화벽** > **방화벽 사용(켜기)** > **Azure 서비스에 대한 액세스 허용** 옵션을 통해 Data Lake Storage Gen1 계정에서 방화벽을 사용하도록 설정할 수 있습니다.

![Data Lake Storage Gen1의 방화벽 설정](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Data Lake Storage Gen1의 방화벽 설정")

방화벽을 사용하도록 설정하면 HDInsight, Data Factory, SQL Data Warehouse 등과 같은 Azure 서비스만 Data Lake Storage Gen1에 액세스할 수 있습니다. Azure에서 사용하는 내부 네트워크 주소 변환으로 인해 Data Lake Storage Gen1 방화벽에서는 IP를 통해 특정 서비스를 제한할 수 없으며, 온-프레미스와 같은 Azure 외부 엔드포인트에 대한 제한에만 사용됩니다.

## <a name="performance-and-scale-considerations"></a>성능 및 크기 조정 고려 사항

Data Lake Storage Gen1의 가장 강력한 기능 중 하나는 데이터 처리량에 대한 엄격한 한도를 제거한다는 것입니다. 제한을 제거하면 고객이 데이터를 분할하지 않고도 데이터 크기 및 수반되는 성능 요구 사항을 늘릴 수 있습니다. Data Lake Storage Gen1 성능 최적화와 관련해서 가장 중요한 고려 사항 중 하나는 병렬 처리를 제공할 때 성능이 극대화된다는 것입니다.

### <a name="improve-throughput-with-parallelism"></a>병렬 처리로 처리량 향상

최적의 읽기/쓰기 처리량을 위해 코어당 8-12개 스레드를 제공하는 것이 좋습니다. 이는 단일 스레드에서 읽기/쓰기를 차단하고 더 많은 스레드에서 더 높은 동시성을 VM에 허용할 수 있기 때문입니다. 수준이 정상이고 병렬 처리를 늘릴 수 있도록 하려면 VM의 CPU 사용률을 모니터링해야 합니다.

### <a name="avoid-small-file-sizes"></a>작은 파일 크기 방지

Data Lake Storage Gen1의 POSIX 권한 및 감사에는 수많은 작은 파일을 사용할 때 명백하게 발생하는 오버헤드가 있습니다. Data Lake Storage Gen1에 수천 또는 수백만 개의 작은 파일을 쓰는 대신, 더 큰 파일에 데이터를 일괄적으로 처리하는 것이 좋습니다. 작은 파일 크기를 방지하면 다음과 같은 여러 이점이 있습니다.

* 여러 파일에 대한 인증 검사 감소
* 열린 파일 연결 감소
* 더 빠른 복사/복제
* Data Lake Storage Gen1 POSIX 권한을 업데이트할 때 더 적은 수의 파일 처리

데이터를 사용하는 서비스 및 워크로드에 따라 파일에 고려해야 할 적절한 크기는 256MB 이상입니다. Data Lake Storage Gen1에 연결될 때 파일 크기를 일괄 처리할 수 없는 경우 이러한 파일을 더 큰 파일로 결합하는 별도의 압축 작업을 수행할 수 있습니다. Data Lake Storage Gen1의 파일 크기 및 데이터 구성에 대한 자세한 내용 및 권장 사항은 [데이터 집합 구성](data-lake-store-performance-tuning-guidance.md#structure-your-data-set)을 참조하세요.

### <a name="large-file-sizes-and-potential-performance-impact"></a>큰 파일 크기 및 성능에 대한 잠재적 영향

Data Lake Storage Gen1은 최대 페타바이트 크기의 큰 파일을 지원하지만 최적의 성능을 위해 데이터 읽기 프로세스에 따라 평균 2GB를 초과하지 않는 것이 좋을 수 있습니다. 예를 들어 **Distcp**를 사용하여 위치 또는 다른 저장소 계정 간에 데이터를 복사하는 경우 파일은 매핑 작업을 결정하는 데 사용되는 최상의 세분성 수준입니다. 따라서 각각 1TB인 파일 10개를 복사하는 경우 최대 10개의 매퍼가 할당됩니다. 또한 매퍼가 할당된 많은 파일이 있는 경우 처음에는 매퍼가 병렬로 작동하여 큰 파일을 이동합니다. 그러나 작업이 종료되기 시작하면 몇 개의 매퍼만 할당된 상태로 유지되고 큰 파일에 할당된 단일 매퍼를 사용하여 멈출 수 있습니다. Microsoft는 이후의 Hadoop 버전에서 이 문제를 해결하기 위해 Distcp에 대한 향상된 기능을 제출했습니다.

고려해야 할 또 다른 예는 Data Lake Storage Gen1에서 Azure Data Lake Analytics를 사용하는 경우입니다. 추출기에서 수행되는 처리에 따라 분할할 수 없는 일부 파일(예: XML, JSON)은 2GB보다 큰 경우 성능이 저하될 수 있습니다. 추출기에서 파일(예: CSV)을 분할할 수 있는 경우 큰 파일을 사용하도록 기본 설정됩니다.

### <a name="capacity-plan-for-your-workload"></a>작업에 대한 용량 계획

Azure Data Lake Storage Gen1은 Blob Storage 계정에 적용된 하드 IO 제한 한도를 제거합니다. 그러나 고려해야 할 소프트 제한이 여전히 있습니다. 기본 수신/송신 제한은 대부분의 시나리오의 요구 사항을 충족합니다. 작업에 대한 제한을 더 늘려야 하는 경우 Microsoft 지원을 사용합니다. 또한 프로덕션 중에 IO 제한에 도달하지 않도록 개념 증명(proof-of-concept) 단계에서 제한을 살펴봅니다. 이러한 경우 Microsoft 엔지니어링 팀에서 수동으로 늘릴 때까지 기다려야 합니다. IO 제한이 발생하면 Azure Data Lake Storage Gen1에서 429 오류 코드를 반환하며 적절한 지수 백오프 정책을 사용하여 다시 시도해야 합니다.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Data Lake Storage Gen1 드라이버 버퍼로 “쓰기” 최적화

Hadoop에서 Data Lake Storage Gen1에 쓸 때 성능을 최적화하고 IOPS를 줄이려면 Data Lake Storage Gen1 드라이버 버퍼 크기에 최대한 가깝게 쓰기 작업을 수행합니다. Apache Storm 또는 Spark 스트리밍 작업을 사용하여 스트리밍하는 경우와 같이 플러시하기 전에 버퍼 크기를 초과하지 않도록 합니다. HDInsight/Hadoop에서 Data Lake Storage Gen1에 쓰는 경우 4MB 버퍼가 있는 드라이버가 Data Lake Storage Gen1에 있음을 알고 있어야 합니다. 많은 파일 시스템 드라이버와 마찬가지로, 이 버퍼는 4MB 크기에 도달하기 전에 수동으로 플러시할 수 있습니다. 그렇지 않으면 다음 쓰기에서 버퍼의 최대 크기를 초과하면 저장소에 즉시 플러시됩니다. 가능한 경우 개수 또는 시간 범위에 따라 정책을 동기화/플러시할 때 버퍼의 오버런 또는 중요한 언더런을 방지해야 합니다.

## <a name="resiliency-considerations"></a>복원력 고려 사항

Data Lake Storage Gen1 또는 클라우드 서비스를 사용하여 시스템을 설계할 때는 가용성 요구 사항과 잠재적인 서비스 중단에 대응하는 방법을 고려해야 합니다. 문제는 특정 인스턴스 또는 지역 전체에 국한될 수 있으므로 둘 모두에 대한 계획이 있어야 합니다. 작업에 대한 **복구 시간 목표** 및 **복구 지점 목표** SLA에 따라 고가용성 및 재해 복구에 대해 다소 공격적인 전략을 선택할 수 있습니다.

### <a name="high-availability-and-disaster-recovery"></a>고가용성 및 재해 복구

HA(고가용성)와 DR(재해 복구)은 서로 결합될 수 있는 경우도 있지만, 특히 데이터에 관해서는 다소 다른 전략을 사용합니다. Data Lake Storage Gen1은 국지적인 하드웨어 오류를 방지하기 위해 이미 내부적으로 3배의 복제를 처리하고 있습니다. 그러나 지역 간 복제가 기본 제공되지 않으므로 직접 관리해야 합니다. HA에 대한 계획을 만들 때 서비스 중단이 발생하는 경우 작업은 로컬 또는 새 지역에서 별도로 복제된 인스턴스로 전환하여 가능한 한 빨리 최신 데이터에 액세스해야 합니다.

DR 전략에서는 지역에서 치명적인 장애가 드물게 발생하는 경우에 대비하기 위해 데이터를 다른 지역으로 복제해야 합니다. 이 데이터는 처음에 복제된 HA 데이터와 동일할 수 있습니다. 그러나 데이터 손상과 같은 위기 상황에 대한 요구 사항을 고려해야 하며, 이 경우 대체할 스냅숏을 정기적으로 만들어야 할 수도 있습니다. 데이터의 중요도와 크기 및 위험 허용 오차에 따라 1시간, 6시간 및 24시간 기간의 델타 스냅숏을 로컬 및/또는 보조 저장소에서 롤링하는 것이 좋습니다.

Data Lake Storage Gen1을 사용하여 데이터를 복원하는 경우 HA/DR 요구 사항을 충족하는 빈도(이상적으로 매시간)로 별도의 영역에 데이터를 지리적으로 복제하는 것이 좋습니다. 이 복제 빈도는 주 시스템과 더 나은 RPO(복구 지점 목표)와 경쟁하는 처리량 요구 사항이 있을 수 있는 대규모 데이터 이동을 최소화합니다. 또한 애플리케이션에서 Data Lake Storage Gen1을 사용하여 트리거 또는 실패한 시도의 길이를 모니터링하거나 적어도 수동으로 개입하도록 관리자에게 알림을 보내어 자동으로 보조 계정으로 장애 조치(failover)하는 방법을 고려해야 합니다. 서비스가 온라인 상태로 돌아올 때까지 기다리는 것과 비교하여 장애 조치에 대한 절충점이 있음을 명심하세요. 데이터 복제가 완료되지 않은 경우 장애 조치를 수행하면 잠재적인 데이터 손실, 불일치 또는 복잡한 데이터 병합이 발생할 수 있습니다.

다음은 Data Lake Storage Gen1 계정 간의 복제를 오케스트레이션하는 데 권장되는 상위 세 가지 옵션과 각각의 주요 차이점입니다.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**크기 조정 제한**     | 작업자 노드로 제한됨        | 최대 클라우드 데이터 이동 단위로 제한됨        | 분석 단위로 제한됨        |
|**델타 복사 지원**     |   예      | 아니오         | 아닙니다.         |
|**기본 제공 오케스트레이션**     |  아니요(Oozie Airflow 또는 cron 작업 사용)       | 예        | 아니요(Azure Automation 또는 Windows 작업 스케줄러 사용)         |
|**지원되는 파일 시스템**     | ADL, HDFS, WASB, S3, GS, CFS        |많음, [커넥터](../data-factory/connector-azure-blob-storage.md) 참조         | ADL 간, WASB 및 ADL 간(동일한 지역에만 해당)        |
|**OS 지원**     |Hadoop을 실행하는 모든 OS         | N/A          | 윈도우 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>두 위치 간 데이터 이동에 Distcp 사용

Distcp(Distributed Copy의 약자)는 Hadoop과 함께 제공되고 두 위치 간에 분산된 데이터 이동을 제공하는 Linux 명령줄 도구입니다. 두 위치는 Data Lake Storage Gen1, HDFS, WASB 또는 S3일 수 있습니다. 이 도구는 Hadoop 클러스터(예: HDInsight)에서 MapReduce 작업을 사용하여 모든 노드의 크기를 확장합니다. Distcp는 특별한 네트워크 압축 어플라이언스 없이 빅 데이터를 이동하는 가장 빠른 방법으로 간주됩니다. 또한 Distcp는 두 위치 간의 델타만 업데이트하고 자동 재시도 및 계산의 동적 크기 조정을 처리하는 옵션을 제공합니다. 이 방법은 하나의 디렉터리에 많은 큰 파일을 포함할 수 있는 Hive/Spark 테이블과 같은 것을 복제하고 수정된 데이터만 복사하려는 경우에 매우 효율적입니다. 이러한 이유로 Distcp는 빅 데이터 저장소 간에 데이터를 복사하는 데 가장 많이 권장되는 도구입니다.

복사 작업은 Linux cron 작업뿐만 아니라 빈도 또는 데이터 트리거를 사용하는 Apache Oozie 워크플로를 통해서도 트리거될 수 있습니다. 집약적인 복제 작업의 경우 복사 작업을 위해 특별히 튜닝 및 크기 조정할 수 있는 별도의 HDInsight Hadoop 클러스터를 작동하는 것이 좋습니다. 이렇게 하면 복사 작업이 중요한 작업을 방해하지 않습니다. 매우 충분한 빈도로 복제를 실행하는 경우 각 작업 간에 클러스터가 중단될 수도 있습니다. 보조 지역으로 장애 조치(failover)하는 경우 백업할 때 보조 지역에서 또 다른 클러스터가 작동되어 새 데이터를 기본 Data Lake Storage Gen1 계정으로 다시 복제해야 합니다. Distcp 사용 예는 [Distcp를 사용하여 Azure Storage Blob과 Data Lake Storage Gen1 간에 데이터 복사](data-lake-store-copy-data-wasb-distcp.md)를 참조하세요.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory를 사용하여 복사 작업 예약

Azure Data Factory는 **복사 활동**을 사용하여 복사 작업을 예약하는 데 사용할 수 있으며 **복사 마법사**를 통해 빈도에 설정할 수도 있습니다. Azure Data Factory에는 클라우드 DMU(데이터 이동 단위) 제한이 있으며, 결국에는 대규모 데이터 작업에 대한 처리량/계산을 제한한다는 것을 명심하세요. 또한 Azure Data Factory는 현재 Data Lake Storage Gen1 계정 간에 델타 업데이트를 제공하지 않으므로 Hive 테이블과 같은 폴더에는 복제하는 데 전체 복사본이 필요합니다. Data Factory를 사용하여 복사하는 방법에 대한 자세한 내용은 [복사 활동 튜닝 가이드](../data-factory/copy-activity-performance.md)를 참조하세요.

### <a name="adlcopy"></a>AdlCopy

AdlCopy는 동일한 지역 내에서만 두 Data Lake Storage Gen1 계정 간에 데이터를 복사할 수 있는 Windows 명령줄 도구입니다. AdlCopy 도구는 독립 실행형 옵션 또는 Azure Data Lake Analytics 계정을 사용하여 복사 작업을 실행하는 옵션을 제공합니다. 원래는 강력한 복제와 달리 주문형 복사본용으로 빌드되었지만, 동일한 지역 내의 Data Lake Storage Gen1 계정에 분산 복사를 수행하는 또 다른 옵션을 제공합니다. 안정성을 위해 모든 프로덕션 작업에 대해 프리미엄 Data Lake Analytics 옵션을 사용하는 것이 좋습니다. 독립 실행형 버전은 사용 중 응답을 반환할 수 있으며 크기 조정과 모니터링이 제한됩니다.

Distcp와 마찬가지로, AdlCopy는 Azure Automation 또는 Windows 작업 스케줄러와 같은 것으로 오케스트레이션되어야 합니다. Data Factory와 마찬가지로 AdlCopy는 업데이트된 파일만 복사하는 것을 지원하지 않지만 기존 파일을 다시 복사하고 덮어씁니다. AdlCopy를 사용하는 방법에 대한 자세한 내용 및 예제는 [Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)를 참조하세요.

## <a name="monitoring-considerations"></a>모니터링 고려 사항

Data Lake Storage Gen1은 자세한 진단 로그 및 감사 기능을 제공합니다. Data Lake Storage Gen1은 Azure Portal의 Data Lake Storage Gen1 계정 및 Azure Monitor에서 몇 가지 기본 메트릭을 제공합니다. Data Lake Storage Gen1의 가용성은 Azure Portal에 표시됩니다. 그러나 이 메트릭은 7분마다 새로 고쳐지고 공개적으로 노출된 API를 통해 쿼리할 수 없습니다. Data Lake Storage Gen1 계정의 최신 가용성을 확인하려면 사용자 고유의 가상 테스트를 실행하여 가용성의 유효성을 검사해야 합니다. 총 저장소 사용률, 읽기/쓰기 요청 수 및 수신/송신과 같은 다른 메트릭은 새로 고치는 데 최대 24시간이 걸릴 수 있습니다. 따라서 최신 메트릭은 Hadoop 명령줄 도구 또는 로그 정보 집계를 통해 수동으로 계산해야 합니다. 가장 최근의 저장소 사용률을 가져오는 가장 빠른 방법은 Hadoop 클러스터 노드(예: 헤드 노드)에서 이 HDFS 명령을 실행하는 것입니다.

    hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Data Lake Storage Gen1 진단 내보내기

Data Lake Storage Gen1에서 검색 가능한 로그에 액세스하는 가장 빠른 방법 중 하나는 Data Lake Storage Gen1 계정에 대한 **진단** 블레이드 아래에서 **Log Analytics**로 로그를 전달하도록 설정하는 것입니다. 이렇게 하면 15분 간격으로 트리거되는 경고 옵션(이메일/웹후크)과 함께 시간 및 콘텐츠 필터를 사용하여 들어오는 로그에 즉시 액세스할 수 있습니다. 지침은 [Azure Data Lake Storage Gen1에 대한 진단 로그 액세스](data-lake-store-diagnostic-logs.md)를 참조하세요.

실시간으로 더 많이 경고하고 로그를 기록할 위치를 더 많이 제어하려면 개별적으로 또는 시간 범위에 대해 콘텐츠를 분석하여 실시간 알림을 큐에 전송할 수 있는 Azure EventHub로 로그를 내보내는 것이 좋습니다. [Logic App](../connectors/connectors-create-api-azure-event-hubs.md)과 같은 별도의 애플리케이션에서 적절한 채널로 경고를 사용하고 전달할 수 있을 뿐만 아니라 NewRelic, Datadog 또는 AppDynamics와 같은 모니터링 도구에 메트릭을 제출할 수도 있습니다. 또는 ElasticSearch와 같은 타사 도구를 사용하는 경우, 로그를 Blob Storage로 내보내고 [Azure Logstash 플러그 인](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)을 사용하여 데이터를 ELK(Elasticsearch, Kibana 및 Logstash) 스택으로 사용할 수 있습니다.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>HDInsight에서 디버그 수준 로깅 설정

Data Lake Storage Gen1 로그 전달이 켜져 있지 않으면 Azure HDInsight는 log4j를 통해 [Data Lake Storage Gen1에 대한 클라이언트 쪽 로깅](data-lake-store-performance-tuning-mapreduce.md)을 사용하도록 설정하는 방법을 제공합니다. **Ambari** > **YARN** > **Config** > **고급 yarn-log4j 구성**에서 다음 속성을 설정해야 합니다.

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG

속성이 설정되고 노드가 다시 시작되면 Data Lake Storage Gen1 진단이 노드의 YARN 로그(/tmp/\<user\>/yarn.log)에 기록되고 오류 또는 제한(HTTP 429 오류 코드)과 같은 중요한 세부 정보를 모니터링할 수 있습니다. Azure Monitor 로그 또는 로그에 전달 되는 위치에서이 동일한 정보를 모니터링할 수도 있습니다는 [진단](data-lake-store-diagnostic-logs.md) 데이터 레이크 저장소 Gen1 계정 블레이드입니다. 최소한 클라이언트 쪽 로깅을 설정하거나 Data Lake Storage Gen1을 통해 로그 전달 옵션을 활용하여 운영 가시성을 확보하고 더 쉽게 디버그하는 것이 좋습니다.

### <a name="run-synthetic-transactions"></a>가상 트랜잭션 실행

현재 Azure Portal의 Data Lake Storage Gen1에 대한 서비스 가용성 메트릭에는 7분 단위의 새로 고침 기간이 있습니다. 또한 공개적으로 노출된 API를 사용하여 쿼리할 수 없습니다. 따라서 최대 분 단위의 가용성을 제공할 수 있는 Data Lake Storage Gen1에 대해 가상 트랜잭션을 수행하는 기본 애플리케이션을 빌드하는 것이 좋습니다. 예를 들어 WebJob, Logic App 또는 Azure Function App을 만들어 Data Lake Storage Gen1에 대한 읽기, 만들기 및 업데이트를 수행하고 결과를 모니터링 솔루션에 보낼 수 있습니다. 작업은 임시 폴더에서 수행한 다음, 테스트 후 삭제할 수 있으며, 요구 사항에 따라 30-60초마다 실행할 수 있습니다.

## <a name="directory-layout-considerations"></a>디렉터리 레이아웃 고려 사항

Data Lake에 데이터를 연결할 때 보안, 분할 및 처리를 효과적으로 활용할 수 있도록 데이터 구조를 미리 계획해야 합니다. Azure Data Lake Storage Gen1, Blob Storage 또는 HDFS 중 어느 것을 사용하든 다음 권장 사항을 대부분 사용할 수 있습니다. 작업마다 데이터를 사용하는 방법에 대한 요구 사항이 다르지만, IoT 및 일괄 처리 시나리오로 작업할 때 고려해야 할 몇 가지 일반적인 레이아웃은 다음과 같습니다.

### <a name="iot-structure"></a>IoT 구조

IoT 작업에서는 수많은 제품, 디바이스, 조직 및 고객에 걸쳐 있는 데이터 저장소에 많은 양의 데이터가 연결될 수 있습니다. 다운스트림 소비자를 위해 데이터의 조직, 보안 및 효율적인 처리를 수행할 수 있도록 디렉터리 레이아웃을 미리 계획해야 합니다. 고려해야 할 일반적인 템플릿은 다음과 같은 레이아웃일 수 있습니다.

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

예를 들어 비행기 엔진에 대한 영국 내 착륙 원격 분석은 다음 구조와 같이 표시될 수 있습니다.

    UK/Planes/BA1293/Engine1/2017/08/11/12/

폴더 구조의 끝 부분에 날짜를 배치하는 중요한 이유가 있습니다. 특정 지역이나 주제를 사용자/그룹으로 한정하려면 POSIX 권한으로 쉽게 수행할 수 있습니다. 그렇지 않고, 앞부분에 나오는 날짜 구조를 사용하여 영국 데이터 또는 특정 비행기만 볼 수 있도록 특정 보안 그룹을 제한해야 하는 경우 모든 시간 폴더 아래의 수많은 폴더에 대해 별도의 권한이 필요합니다. 또한 앞부분에 나오는 날짜 구조를 사용하면 시간이 지남에 따라 폴더 수가 기하급수적으로 늘어납니다.

### <a name="batch-jobs-structure"></a>일괄 처리 작업 구조

일반적으로 사용되는 높은 수준의 일괄 처리 방식은 데이터를 "내부" 폴더에 저장하는 것입니다. 그런 다음, 데이터가 처리되면 다운스트림 프로세스에서 사용할 새 데이터를 "외부" 폴더에 저장합니다. 개별 파일에 대한 처리가 필요하지만 큰 데이터 세트에 대한 대규모 병렬 처리가 필요하지 않을 수 있는 작업에서 이 디렉터리 구조가 종종 나타납니다. 위에서 권장하는 IoT 구조와 마찬가지로, 좋은 디렉터리 구조에는 지역 및 주제(예: 조직, 제품/생산자)와 같은 항목에 대한 부모 수준 폴더가 있습니다. 이 구조는 조직 전체의 데이터를 보호하고 작업의 데이터를 더 효율적으로 관리하는 데 유용합니다. 또한 처리에서 더 나은 구성, 필터링된 검색, 보안 및 자동화를 허용하는 구조에서 날짜 및 시간을 고려해야 합니다. 날짜 구조의 세분성 수준은 데이터가 업로드되거나 처리되는 간격(예: 시간별, 일별 또는 월별)에 따라 결정됩니다.

데이터 손상 또는 예기치 않은 형식으로 인해 파일 처리가 실패하는 경우가 있습니다. 이러한 경우 디렉터리 구조에서 **/bad** 폴더를 지원하여 추가 검사를 위해 파일을 이동할 수 있습니다. 또한 일괄 처리 작업은 이러한 *불량* 파일에 대한 보고 또는 알림을 처리하여 수동으로 개입할 수 있습니다. 다음과 같은 템플릿 구조를 고려합니다.

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

예를 들어 북아메리카 지역의 고객으로부터 고객 업데이트의 일일 데이터 추출 결과를 받는 마케팅 회사는 처리 전과 후에 다음 코드 조각처럼 표시될 수 있습니다.

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

일괄 처리 데이터가 일반적으로 Hive 또는 기존 SQL 데이터베이스와 같은 데이터베이스에 직접 처리되는 경우, 출력이 이미 Hive 테이블 또는 외부 데이터베이스에 대한 별도의 폴더로 이동하기 때문에 **/in** 또는 **/out** 폴더가 필요하지 않습니다. 예를 들어 고객으로부터의 일일 추출 결과가 해당 폴더에 저장되고, Azure Data Factory, Apache Oozie 또는 Apache Airflow 등을 통한 오케스트레이션에서 매일 Hive 또는 Spark 작업을 트리거하여 데이터를 처리하고 Hive 테이블에 씁니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
* [Azure Data Lake Storage Gen1의 액세스 제어](data-lake-store-access-control.md)
* [Azure Data Lake Storage Gen1의 보안](data-lake-store-security-overview.md)
* [Azure Data Lake Storage Gen1의 성능 조정](data-lake-store-performance-tuning-guidance.md)
* [Azure Data Lake Storage Gen1에서 HDInsight Spark를 사용하기 위한 성능 조정 지침](data-lake-store-performance-tuning-spark.md)
* [Azure Data Lake Storage Gen1에서 HDInsight Hive를 사용하기 위한 성능 조정 지침](data-lake-store-performance-tuning-hive.md)
* [Data Lake Storage Gen1로 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)
