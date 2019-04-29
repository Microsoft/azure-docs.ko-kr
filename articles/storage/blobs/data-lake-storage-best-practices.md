---
title: Azure Data Lake Storage Gen2 사용에 대한 모범 사례 | Microsoft Docs
description: Azure Data Lake Storage Gen2(이전의 Azure Data Lake Store) 사용과 관련된 데이터 수집, 날짜 보안 및 성능에 대한 모범 사례를 알아봅니다.
services: storage
author: sachinsbigdata
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: article
ms.date: 12/06/2018
ms.author: sachins
ms.openlocfilehash: e371ac848eff0e66390fe17bc23934725fca35f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708535"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 사용에 대한 모범 사례

이 문서에서는 Azure Data Lake Storage Gen2 사용에 대한 모범 사례와 고려 사항에 대해 알아봅니다. 여기서는 Data Lake Storage Gen2의 보안, 성능, 복원력 및 모니터링에 대한 정보를 제공합니다. Data Lake Storage Gen2 이전에는 Azure HDInsight와 같은 서비스에서 진정한 빅 데이터를 사용하는 것이 복잡했습니다. 여러 Blob Storage 계정에서 데이터를 분할하여 페타바이트 스토리지와 최적의 성능을 얻을 수 있도록 해야 했습니다. Data Lake Storage Gen2를 사용하면 크기와 성능에 대한 대부분의 엄격한 제한이 제거됩니다. 그러나 이 문서에는 Data Lake Storage Gen2를 통해 최상의 성능을 얻기 위한 몇 가지 고려 사항도 있습니다.

## <a name="security-considerations"></a>보안 고려 사항

Azure Data Lake Storage Gen2는 Azure AD(Azure Active Directory) 사용자, 그룹 및 서비스 사용자에 대한 POSIX 액세스 제어를 제공합니다. 이러한 액세스 제어는 기존 파일 및 디렉터리에 설정할 수 있습니다. 또한 액세스 제어를 사용하여 새 파일 또는 디렉터리에 자동으로 적용할 수 있는 기본 권한을 만들 수도 있습니다. Data Lake Storage Gen2 ACL에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](storage-data-lake-storage-access-control.md)에서 확인할 수 있습니다.

### <a name="use-security-groups-versus-individual-users"></a>보안 그룹 및 개별 사용자 사용

Data Lake Storage Gen2에서 빅 데이터를 사용할 때는 서비스 사용자를 통해 Azure HDInsight와 같은 서비스에서 데이터를 사용하도록 허용할 가능성이 높습니다. 그러나 개별 사용자가 데이터에 액세스해야 하는 경우도 있을 수 있습니다. 모든 경우에서 개별 사용자를 폴더 및 디렉터리에 할당하는 대신 Azure Active Director [보안 그룹](../common/storage-auth-aad.md)을 사용하는 것이 좋습니다.

보안 그룹에 권한이 할당되면 그룹에서 사용자를 추가하거나 제거할 때 Data Lake Storage Gen2를 업데이트할 필요가 없습니다. 그러면 ACL(액세스 제어 목록)당 액세스 제어 항목의 최대 수를 초과하지 않도록 하는 데도 도움이 됩니다. 현재 이 개수는 32개입니다(모든 파일 및 디렉터리와 항상 연결되는 소유 사용자, 소유 그룹, 마스크 및 기타의 4가지 POSIX 스타일 ACL 포함). 각 디렉터리에는 총 64개의 액세스 제어 항목에 대한 두 가지 ACL 유형, 즉 액세스 ACL과 기본 ACL이 있을 수 있습니다. 이러한 ACL에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)를 참조하세요.

### <a name="security-for-groups"></a>그룹에 대한 보안

관리자 또는 사용자가 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정의 데이터에 액세스해야 하는 경우 Azure Active Directory 보안 그룹을 사용하는 것이 가장 좋습니다. 몇 가지 권장 그룹 시작 될 수 있습니다 **ReadOnlyUsers**하십시오 **WriteAccessUsers**, 및 **FullAccessUsers** 루트 파일 시스템의 한도 대 한 별도에 대 한 키의 하위 디렉터리입니다. 나중에 추가될 수 있지만 아직은 확인되지 않은 것으로 예상되는 다른 사용자 그룹이 있는 경우, 특정 폴더에 대한 액세스 권한이 있는 더미 보안 그룹을 만드는 것을 고려할 수 있습니다. 보안 그룹을 사용하면 수천 개의 파일에 새 권한을 할당할 때 처리 시간이 오래 걸리지 않도록 방지할 수 있습니다.

### <a name="security-for-service-principals"></a>서비스 사용자에 대한 보안

Azure Active Directory 서비스 사용자는 일반적으로 Azure Databricks와 같은 서비스에서 Data Lake Storage Gen2의 데이터에 액세스하는 데 사용됩니다. 많은 고객에 게는 단일 Azure Active Directory 서비스 사용자가 적합할 수 및 Data Lake 저장소 Gen2 파일 시스템의 루트에 전체 권한을 가질 수 있습니다 것입니다. 한 클러스터에는 데이터에 대한 모든 액세스 권한이 있고 다른 클러스터에는 읽기 전용 액세스 권한이 있는 다른 고객에게는 서로 다른 서비스 사용자가 있는 여러 클러스터가 필요할 수 있습니다. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Azure 서비스 액세스 권한으로 Data Lake Storage Gen2 방화벽 사용

Data Lake Storage Gen2는 외부 공격의 벡터를 제한하는 데 추천되는 Azure 서비스에만 방화벽을 설정하고 액세스를 제한하는 옵션을 지원합니다. 방화벽은 Azure Portal에서 **방화벽** > **방화벽 사용(켜기)** > **Azure 서비스 방문 허용** 옵션을 통해 스토리지 계정에 사용하도록 설정할 수 있습니다.

스토리지 방화벽을 통해 액세스할 수 있는 가상 네트워크에 Azure Databricks 클러스터를 추가하려면 Databricks의 미리 보기 기능을 사용해야 합니다. 이 기능을 사용하려면 지원 요청을 제출하세요.

## <a name="resiliency-considerations"></a>복원력 고려 사항

Data Lake Storage Gen2 또는 클라우드 서비스를 사용하여 시스템을 설계하는 경우 가용성 요구 사항과 잠재적인 서비스 중단에 대응하는 방법을 고려해야 합니다. 문제는 특정 인스턴스 또는 지역 전체에 국한될 수 있으므로 둘 모두에 대한 계획이 있어야 합니다. 워크로드에 대한 복구 시간 목표 및 복구 지점 목표 SLA에 따라 고가용성 및 재해 복구에 대한 다소 공격적인 전략을 선택할 수 있습니다.

### <a name="high-availability-and-disaster-recovery"></a>고가용성 및 재해 복구

HA(고가용성)와 DR(재해 복구)은 서로 결합될 수 있는 경우도 있지만, 특히 데이터에 관해서는 다소 다른 전략을 사용합니다. Data Lake Storage Gen2는 지역화된 하드웨어 오류를 방지하기 위해 이미 내부적으로 3배의 복제를 처리하고 있습니다. 또한 ZRS와 같은 다른 복제 옵션은 HA를 향상시키는 반면, GRS 및 RA-GRS는 DR을 향상시킵니다. HA에 대한 계획을 만들 때 서비스 중단이 발생하는 경우 작업은 로컬 또는 새 지역에서 별도로 복제된 인스턴스로 전환하여 가능한 한 빨리 최신 데이터에 액세스해야 합니다.

DR 전략에서는 지역에서 치명적인 장애가 드물게 발생하는 경우에 대비하기 위해 GRS 또는 RA-GRS 복제도 사용하여 데이터를 다른 지역으로 복제해야 합니다. 또한 데이터 손상과 같은 위기 상황에 대한 요구 사항도 고려해야 하며, 이 경우 대체할 스냅숏을 정기적으로 만들어야 할 수 있습니다. 데이터의 중요도와 크기 및 위험 허용 오차에 따라 1시간, 6시간 및 24시간 기간의 델타 스냅숏을 롤링하는 것이 좋습니다.

Data Lake Storage Gen2를 사용하여 데이터를 복원하는 경우 HA/DR 요구 사항을 충족하는 GRS 또는 RA-GRS를 통해 데이터를 지리적으로 복제하는 것이 좋습니다. 또한 애플리케이션에서 Data Lake Storage Gen2를 사용하여 트리거 또는 실패한 시도의 길이를 모니터링하거나 적어도 수동으로 개입하도록 관리자에게 알림을 보내어 자동으로 보조 지역으로 장애 조치하는 방법을 고려해야 합니다. 서비스가 온라인 상태로 돌아올 때까지 기다리는 것과 비교하여 장애 조치에 대한 절충점이 있음을 명심하세요.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>두 위치 간 데이터 이동에 Distcp 사용

DistCp(Distributed Copy의 약자)는 Hadoop과 함께 제공되고 두 위치 간에 분산된 데이터 이동을 제공하는 Linux 명령줄 도구입니다. 두 위치는 Data Lake Storage Gen2, HDFS 또는 S3일 수 있습니다. 이 도구는 Hadoop 클러스터(예: HDInsight)에서 MapReduce 작업을 사용하여 모든 노드의 크기를 확장합니다. Distcp는 특별한 네트워크 압축 어플라이언스 없이 빅 데이터를 이동하는 가장 빠른 방법으로 간주됩니다. 또한 Distcp는 두 위치 간의 델타만 업데이트하고 자동 재시도 및 계산의 동적 크기 조정을 처리하는 옵션을 제공합니다. 이 방법은 하나의 디렉터리에 많은 큰 파일을 포함할 수 있는 Hive/Spark 테이블과 같은 것을 복제하고 수정된 데이터만 복사하려는 경우에 매우 효율적입니다. 이러한 이유로 Distcp는 빅 데이터 저장소 간에 데이터를 복사하는 데 가장 많이 권장되는 도구입니다.

복사 작업은 Linux cron 작업뿐만 아니라 빈도 또는 데이터 트리거를 사용하는 Apache Oozie 워크플로를 통해서도 트리거될 수 있습니다. 집약적인 복제 작업의 경우 복사 작업을 위해 특별히 튜닝 및 크기 조정할 수 있는 별도의 HDInsight Hadoop 클러스터를 작동하는 것이 좋습니다. 이렇게 하면 복사 작업이 중요한 작업을 방해하지 않습니다. 매우 충분한 빈도로 복제를 실행하는 경우 각 작업 간에 클러스터가 중단될 수도 있습니다. 보조 지역으로 장애 조치하는 경우 백업이 시작되면 보조 지역에서 또 다른 클러스터가 작동되어 새 데이터를 기본 Data Lake Storage Gen2 계정으로 다시 복제해야 합니다. DistCp 사용에 대한 예제는 [DistCp를 사용하여 Azure Storage Blob과 Data Lake Storage Gen2 간에 데이터 복사](../blobs/data-lake-storage-use-distcp.md)를 참조하세요.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory를 사용하여 복사 작업 예약

Azure Data Factory는 복사 활동을 사용하여 복사 작업을 예약하는 데 사용할 수도 있으며, 복사 마법사를 통해 빈도에 설정할 수도 있습니다. Azure Data Factory에는 클라우드 DMU(데이터 이동 단위) 제한이 있으며, 결국에는 대규모 데이터 작업에 대한 처리량/계산을 제한한다는 것을 명심하세요. 또한 Azure Data Factory는 현재 Data Lake Storage Gen2 계정 간의 델타 업데이트를 제공하지 않으므로 Hive 테이블과 같은 디렉터리에는 복제하는 데 전체 복사본이 필요합니다. Data Factory를 사용하여 복사하는 방법에 대한 자세한 내용은 [Data Factory 문서](../../data-factory/load-azure-data-lake-storage-gen2.md)를 참조하세요.

## <a name="monitoring-considerations"></a>모니터링 고려 사항

Data Lake Storage Gen2는 Azure Portal의 Data Lake Storage Gen2 계정 및 Azure Monitor에서 메트릭을 제공합니다. Data Lake Storage Gen2의 가용성은 Azure Portal에 표시됩니다. Data Lake Storage Gen2 계정의 최신 가용성을 가져오려면 사용자 고유의 가상 테스트를 실행하여 가용성의 유효성을 검사해야 합니다. 총 스토리지 사용률, 읽기/쓰기 요청 수 및 수신/송신과 같은 다른 메트릭은 애플리케이션을 모니터링하여 활용할 수 있으며, 임계값(예: 평균 지연 시간 또는 분당 오류 수)이 초과되면 경고를 트리거할 수도 있습니다.

## <a name="directory-layout-considerations"></a>디렉터리 레이아웃 고려 사항

Data Lake에 데이터를 연결할 때 보안, 분할 및 처리를 효과적으로 활용할 수 있도록 데이터 구조를 미리 계획해야 합니다. 다음 추천 사항 중 대다수는 모든 빅 데이터 워크로드에 적용할 수 있습니다. 작업마다 데이터를 사용하는 방법에 대한 요구 사항이 다르지만, IoT 및 일괄 처리 시나리오로 작업할 때 고려해야 할 몇 가지 일반적인 레이아웃은 다음과 같습니다.

### <a name="iot-structure"></a>IoT 구조

IoT 작업에서는 수많은 제품, 디바이스, 조직 및 고객에 걸쳐 있는 데이터 저장소에 많은 양의 데이터가 연결될 수 있습니다. 다운스트림 소비자를 위해 데이터의 조직, 보안 및 효율적인 처리를 수행할 수 있도록 디렉터리 레이아웃을 미리 계획해야 합니다. 고려해야 할 일반적인 템플릿은 다음과 같은 레이아웃일 수 있습니다.

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

예를 들어 비행기 엔진에 대한 영국 내 착륙 원격 분석은 다음 구조와 같이 표시될 수 있습니다.

    UK/Planes/BA1293/Engine1/2017/08/11/12/

디렉터리 구조의 끝 부분에 날짜를 배치하는 중요한 이유가 있습니다. 특정 지역이나 주제를 사용자/그룹으로 한정하려면 POSIX 권한으로 쉽게 수행할 수 있습니다. 그렇지 않은 경우 앞에 있는 날짜 구조를 사용하여 영국 데이터 또는 특정 비행기만 볼 수 있도록 특정 보안 그룹을 제한해야 하는 경우 모든 시간 디렉터리 아래의 수많은 디렉터리에 대해 별도의 권한이 필요합니다. 또한 앞에 있는 날짜 구조를 사용하면 시간이 지남에 따라 디렉터리 수가 기하급수적으로 늘어납니다.

### <a name="batch-jobs-structure"></a>일괄 처리 작업 구조

높은 수준에서 일반적으로 사용되는 일괄 처리 방식은 데이터를 "내부" 디렉터리로 이동하는 것입니다. 그런 다음, 데이터가 처리되면 다운스트림 프로세스에서 사용할 새 데이터를 "외부" 디렉터리에 배치합니다. 개별 파일에 대한 처리가 필요하지만 큰 데이터 세트에 대한 대규모 병렬 처리가 필요하지 않을 수 있는 작업에서 이 디렉터리 구조가 종종 나타납니다. 위에서 추천하는 IoT 구조와 마찬가지로, 좋은 디렉터리 구조에는 지역 및 주체(예: 조직, 제품/생산자)와 같은 항목에 대한 부모 수준 디렉터리가 있습니다. 이 구조는 조직 전체의 데이터를 보호하고 작업의 데이터를 더 효율적으로 관리하는 데 유용합니다. 또한 처리에서 더 나은 구성, 필터링된 검색, 보안 및 자동화를 허용하는 구조에서 날짜 및 시간을 고려해야 합니다. 날짜 구조의 세분성 수준은 데이터가 업로드되거나 처리되는 간격(예: 시간별, 일별 또는 월별)에 따라 결정됩니다.

데이터 손상 또는 예기치 않은 형식으로 인해 파일 처리가 실패하는 경우가 있습니다. 이러한 경우 디렉터리 구조에서 **/bad** 폴더를 지원하여 추가 검사를 위해 파일을 이동할 수 있습니다. 또한 일괄 처리 작업은 이러한 *불량* 파일에 대한 보고 또는 알림을 처리하여 수동으로 개입할 수 있습니다. 다음과 같은 템플릿 구조를 고려합니다.

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

예를 들어 북아메리카 지역의 고객으로부터 고객 업데이트의 일일 데이터 추출 결과를 받는 마케팅 회사는 처리 전과 후에 다음 코드 조각처럼 표시될 수 있습니다.

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

일괄 처리 데이터가 일반적으로 Hive 또는 기존 SQL 데이터베이스와 같은 데이터베이스에 직접 처리되는 경우, 출력이 이미 Hive 테이블 또는 외부 데이터베이스에 대한 별도의 폴더로 이동하기 때문에 **/in** 또는 **/out** 폴더가 필요하지 않습니다. 예를 들어 고객으로부터의 일일 추출 결과가 해당 폴더에 저장되고, Azure Data Factory, Apache Oozie 또는 Apache Airflow 등을 통한 오케스트레이션에서 매일 Hive 또는 Spark 작업을 트리거하여 데이터를 처리하고 Hive 테이블에 씁니다.
