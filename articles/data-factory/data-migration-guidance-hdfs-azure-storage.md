---
title: 온-프레미스 Hadoop 클러스터에서 Azure Storage로 데이터 마이그레이션
description: Azure Data Factory를 사용 하 여 온-프레미스 Hadoop 클러스터에서 Azure Storage로 데이터를 마이그레이션하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: afccbdbbfd5b8ddeefa621448d6170d937b518f0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931443"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Azure Data Factory를 사용 하 여 온-프레미스 Hadoop 클러스터에서 Azure Storage로 데이터를 마이그레이션합니다. 

Azure Data Factory은 온-프레미스 HDFS에서 Azure Blob storage 또는 Azure Data Lake Storage Gen2로 대규모로 데이터를 마이그레이션하기 위한 강력 하 고, 강력 하며, 비용 효율적인 메커니즘을 제공 합니다. 

Data Factory은 온-프레미스 HDFS에서 Azure로 데이터를 마이그레이션하는 두 가지 기본적인 방법을 제공 합니다. 시나리오에 따라 방법을 선택할 수 있습니다. 

- **Data Factory distcp 모드** (권장): Data Factory에서 [distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (분산 복사)를 사용 하 여 Azure Blob storage (준비 된 복사 포함) Azure Data Lake Store 또는 Gen2 ( [준비 된 복사](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)포함)로 파일을 복사할 수 있습니다. DistCp와 통합 된 Data Factory를 사용 하 여 기존의 강력한 클러스터를 활용 하 여 최상의 복사 처리량을 달성할 수 있습니다. 또한 Data Factory에서 유연한 예약 및 통합 모니터링 환경을 활용 하는 이점을 얻을 수 있습니다. Data Factory 구성에 따라 복사 작업은 DistCp 명령을 자동으로 생성 하 고, 데이터를 Hadoop 클러스터에 제출한 다음, 복사 상태를 모니터링 합니다. 온-프레미스 Hadoop 클러스터에서 Azure로 데이터를 마이그레이션하기 위해 DistCp 모드 Data Factory 하는 것이 좋습니다.
- **Data Factory native integration runtime 모드**: distcp는 모든 시나리오에서 옵션이 아닙니다. 예를 들어 Azure Virtual Networks 환경에서 DistCp 도구는 Azure Storage 가상 네트워크 엔드포인트을 사용 하 여 Azure Express 경로 개인 피어 링을 지원 하지 않습니다. 또한 경우에 따라 기존 Hadoop 클러스터를 데이터 마이그레이션하기 위한 엔진으로 사용 하지 않는 것이 좋습니다. 이렇게 하면 기존 ETL 작업의 성능에 영향을 줄 수 있는 많은 부하가 클러스터에 포함 되지 않습니다. 대신, 온-프레미스 HDFS에서 Azure로 데이터를 복사 하는 엔진으로 Data Factory integration runtime의 기본 기능을 사용할 수 있습니다.

이 문서에서는 두 가지 방법에 대한 다음 정보를 제공 합니다.
> [!div class="checklist"]
> * 성능 중심 
> * 복원 력
> * 네트워크 보안
> * 개략적인 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능 중심

Data Factory DistCp 모드에서 처리량은 DistCp 도구를 독립적으로 사용 하는 경우와 동일 합니다. Data Factory DistCp 모드는 기존 Hadoop 클러스터의 용량을 최대화 합니다. 대량 클러스터 간 또는 클러스터 내 복사를 위해 DistCp를 사용할 수 있습니다. 

DistCp는 MapReduce를 사용 하 여 배포, 오류 처리 및 복구, 보고를 적용 합니다. 작업 매핑을 위한 입력으로 파일 및 디렉터리 목록을 확장 합니다. 각 작업은 원본 목록에 지정 된 파일 파티션을 복사 합니다. DistCp와 통합 된 Data Factory를 사용 하 여 네트워크 대역폭, 저장소 IOPS 및 대역폭을 완벽 하 게 활용 하는 파이프라인을 빌드하여 사용자 환경에 대한 데이터 이동 처리량을 최대화할 수 있습니다.  

Data Factory native integration runtime 모드에서는 서로 다른 수준에서 병렬 처리를 수행할 수도 있습니다. 병렬 처리를 사용 하 여 네트워크 대역폭, 저장소 IOPS 및 대역폭을 완벽 하 게 활용 하 여 데이터 이동 처리량을 최대화할 수 있습니다.

- 단일 복사 작업은 확장 가능한 계산 리소스를 활용할 수 있습니다. 자체 호스팅 통합 런타임을 사용 하면 컴퓨터를 수동으로 확장 하거나 여러 컴퓨터로 확장할 수 있습니다 ([최대 4 개 노드](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). 단일 복사 작업은 모든 노드에 걸쳐 해당 파일 집합을 분할 합니다. 
- 단일 복사 작업은 여러 스레드를 사용 하 여 데이터 저장소에서 읽고 씁니다. 
- Data Factory 제어 흐름은 동시에 여러 복사 작업을 시작할 수 있습니다. 예를 들어 [For each 루프](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)를 사용할 수 있습니다. 

자세한 내용은 [복사 작업 성능 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)를 참조 하세요.

## <a name="resilience"></a>복원력

Data Factory DistCp 모드에서는 다른 DistCp 명령줄 매개 변수를 사용할 수 있습니다. 예를 들어 다른 DistCp 명령줄 매개 변수를 사용할 수 있습니다 (예: `-i`, 오류 무시 또는 `-update`, 원본 파일 및 대상 파일 크기가 다른 경우 데이터 쓰기

Data Factory native integration runtime 모드에서 단일 복사 작업 실행의 Data Factory에는 기본 제공 재시도 메커니즘이 있습니다. 데이터 저장소 또는 기본 네트워크에서 특정 수준의 일시적인 오류를 처리할 수 있습니다. 

온-프레미스 HDFS에서 Blob storage로, 그리고 온-프레미스 HDFS에서 Data Lake Store Gen2로 이진 복사를 수행 하는 경우 Data Factory는 자동으로 검사점을 확장 합니다. 복사 작업 실행이 실패 하거나 시간이 초과 되는 경우 후속 재시도에서 (재시도 횟수가 > 1 인지 확인) 처음부터 시작 하는 대신 마지막 오류 지점에서 복사본이 다시 시작 됩니다.

## <a name="network-security"></a>네트워크 보안 

기본적으로 Data Factory는 HTTPS 프로토콜을 통한 암호화 된 연결을 사용 하 여 온-프레미스 HDFS에서 Blob storage 또는 Azure Data Lake Storage Gen2로 데이터를 전송 합니다. HTTPS는 전송 중인 데이터 암호화를 제공 하며 도청 및 메시지 가로채기 (man-in-the-middle) 공격을 방지 합니다. 

또는 공용 인터넷을 통해 데이터를 전송 하지 않으려는 경우에는 보안을 강화 하기 위해 Express 경로를 통해 개인 피어 링 링크를 통해 데이터를 전송할 수 있습니다. 

## <a name="solution-architecture"></a>솔루션 아키텍처

이 이미지는 공용 인터넷을 통해 데이터를 마이그레이션하는 것을 보여 줍니다.

![공용 네트워크를 통해 데이터를 마이그레이션하기 위한 솔루션 아키텍처를 보여 주는 다이어그램](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- 이 아키텍처에서 데이터는 공용 인터넷을 통해 HTTPS를 사용 하 여 안전 하 게 전송 됩니다. 
- 공용 네트워크 환경에서 Data Factory DistCp 모드를 사용 하는 것이 좋습니다. 강력한 기존 클러스터를 활용 하 여 최상의 복사 처리량을 달성할 수 있습니다. 또한 Data Factory에서 유연 하 게 일정 예약 및 통합 모니터링 환경을 얻을 수 있습니다.
- 이 아키텍처의 경우에는 회사 방화벽 뒤에 있는 Windows 컴퓨터에 Data Factory 자체 호스팅 통합 런타임을 설치 하 여 DistCp 명령을 Hadoop 클러스터에 제출 하 고 복사 상태를 모니터링 해야 합니다. 컴퓨터가 제어 목적 으로만 데이터를 이동 하는 엔진이 아니므로 컴퓨터의 용량은 데이터 이동 처리량에 영향을 주지 않습니다.
- DistCp 명령의 기존 매개 변수가 지원 됩니다.

이 이미지는 개인 링크를 통해 데이터를 마이그레이션하는 것을 보여 줍니다. 

![개인 네트워크를 통해 데이터를 마이그레이션하기 위한 솔루션 아키텍처를 보여 주는 다이어그램](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- 이 아키텍처에서 데이터는 Azure Express 경로를 통해 개인 피어 링 링크를 통해 마이그레이션됩니다. 데이터는 공용 인터넷을 통과 하지 않습니다.
- DistCp 도구는 Azure Storage 가상 네트워크 엔드포인트으로 Express 경로 개인 피어 링을 지원 하지 않습니다. 통합 런타임을 통해 Data Factory의 기본 기능을 사용 하 여 데이터를 마이그레이션하는 것이 좋습니다.
- 이 아키텍처의 경우 Azure 가상 네트워크의 Windows VM에 Data Factory 자체 호스팅 통합 런타임을 설치 해야 합니다. 수동으로 VM을 확장 하거나 여러 Vm으로 확장 하 여 네트워크 및 저장소 IOPS 또는 대역폭을 완벽 하 게 활용할 수 있습니다.
- 각 Azure VM (Data Factory 자체 호스팅 통합 런타임이 설치 된)에 대해 시작 하는 권장 구성은 32 vCPU 및 128 GB의 메모리로 Standard_D32s_v3 됩니다. 데이터 마이그레이션 중 vm의 CPU 및 메모리 사용량을 모니터링 하 여 더 나은 성능을 위해 VM을 확장 하거나 비용을 줄이기 위해 vm을 확장할 필요가 있는지 여부를 확인할 수 있습니다.
- 단일 자체 호스팅 통합 런타임을 사용 하 여 최대 4 개의 VM 노드를 연결 하 여 규모를 확장할 수도 있습니다. 자체 호스팅 통합 런타임에 대해 실행 되는 단일 복사 작업은 자동으로 파일 집합을 분할 하 고 모든 VM 노드를 사용 하 여 파일을 병렬로 복사 합니다. 고가용성을 위해 데이터 마이그레이션 중 단일 오류 시나리오를 방지 하기 위해 두 개의 VM 노드로 시작 하는 것이 좋습니다.
- 이 아키텍처를 사용 하는 경우 초기 스냅숏 데이터 마이그레이션 및 델타 데이터 마이그레이션을 사용할 수 있습니다.

## <a name="implementation-best-practices"></a>구현 모범 사례

데이터 마이그레이션을 구현할 때 이러한 모범 사례를 따르는 것이 좋습니다.

### <a name="authentication-and-credential-management"></a>인증 및 자격 증명 관리 

- HDFS에 인증 하려면 [Windows (Kerberos) 또는 익명](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties)을 사용할 수 있습니다. 
- Azure Blob storage에 연결 하는 데 여러 인증 유형이 지원 됩니다.  [Azure 리소스에 관리 되는 id를](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)사용 하는 것이 좋습니다. Azure Active Directory (Azure AD)에서 자동으로 관리 되는 Data Factory id를 기반으로 구축 된 관리 되는 id를 사용 하면 연결 된 서비스 정의에 자격 증명을 제공 하지 않고 파이프라인을 구성할 수 있습니다. 또는 [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [공유 액세스 서명](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)를 사용 하 여 Blob 저장소에 인증할 수 있습니다. 
- Data Lake Storage Gen2에 연결 하는 경우에도 여러 인증 유형이 지원 됩니다.  [Azure 리소스에 관리 되는 id](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)를 사용 하는 것이 좋지만 [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)를 사용할 수도 있습니다. 
- Azure 리소스에 관리 되는 id를 사용 하지 않는 경우 [Azure Key Vault에 자격 증명을 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 하 여 Data Factory 연결 된 서비스를 수정 하지 않고 키를 중앙에서 보다 쉽게 관리 하 고 회전할 수 있도록 하는 것이 좋습니다. 이는 [CI/CD에 대한 모범 사례](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)이기도 합니다. 

### <a name="initial-snapshot-data-migration"></a>초기 스냅숏 데이터 마이그레이션 

Data Factory DistCp 모드에서는 하나의 복사 작업을 만들어 DistCp 명령을 제출 하 고, 다른 매개 변수를 사용 하 여 초기 데이터 마이그레이션 동작을 제어할 수 있습니다. 

Data Factory native integration runtime 모드에서는 데이터 파티션이 권장 됩니다. 특히 10TB를 초과 하는 데이터를 마이그레이션하는 경우에 적합 합니다. 데이터를 분할 하려면 HDFS에서 폴더 이름을 사용 합니다. 그런 다음 각 Data Factory 복사 작업에서 한 번에 하나의 폴더 파티션을 복사할 수 있습니다. 더 나은 처리량을 위해 여러 Data Factory 복사 작업을 동시에 실행할 수 있습니다.

네트워크 또는 데이터 저장소의 일시적인 문제로 인해 복사 작업이 실패 하는 경우 실패 한 복사 작업을 다시 실행 하 여 HDFS에서 특정 파티션을 다시 로드할 수 있습니다. 다른 파티션을 로드 하는 다른 복사 작업은 영향을 받지 않습니다.

### <a name="delta-data-migration"></a>델타 데이터 마이그레이션 

Data Factory DistCp 모드에서 DistCp 명령줄 매개 변수 `-update`를 사용 하 여 원본 파일 및 대상 파일의 크기가 다른 경우 데이터를 쓸 수 있습니다.

Data Factory 기본 통합 모드에서 HDFS에서 새로운 파일 또는 변경 된 파일을 식별 하는 가장 효율적인 방법은 시간 분할 명명 규칙을 사용 하는 것입니다. HDFS의 데이터가 파일 또는 폴더 이름 (예: */yyyy/mm/dd/file.csv*)의 시간 조각 정보를 사용 하 여 시간 분할 된 경우 파이프라인은 증분 복사할 파일 및 폴더를 쉽게 식별할 수 있습니다.

또는 HDFS의 데이터가 시간 분할 되지 않은 경우 **LastModifiedDate** 값을 사용 하 여 새 파일 또는 변경 된 파일을 식별할 수 Data Factory. Data Factory는 HDFS에서 모든 파일을 검색 하 고, 마지막으로 수정 된 타임 스탬프가 설정 된 값 보다 큰 새 파일 및 업데이트 된 파일만 복사 합니다. 

HDFS에 많은 파일이 있는 경우 필터 조건과 일치 하는 파일 수에 관계 없이 초기 파일 검색에 오랜 시간이 걸릴 수 있습니다. 이 시나리오에서는 초기 스냅숏 마이그레이션에 사용한 것과 동일한 파티션을 사용 하 여 데이터를 먼저 분할 하는 것이 좋습니다. 그런 다음 파일 검색을 병렬로 수행할 수 있습니다.

### <a name="estimate-price"></a>예상 가격 

HDFS에서 Azure Blob storage로 데이터를 마이그레이션하기 위한 다음 파이프라인을 고려 합니다. 

![가격 책정 파이프라인을 보여 주는 다이어그램](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

다음 정보를 가정 하겠습니다. 

- 총 데이터 볼륨은 1 PB입니다.
- Data Factory native integration runtime 모드를 사용 하 여 데이터를 마이그레이션합니다.
- 1 PB는 1000 파티션으로 나뉘어 각 복사본은 하나의 파티션으로 이동 합니다.
- 각 복사 작업은 4 대의 컴퓨터와 연결 되며 500 MBps 처리량을 달성 하는 자체 호스팅 통합 런타임 하나로 구성 됩니다.
- ForEach 동시성은 **4** 로 설정 되 고 집계 처리량은 2gbps입니다.
- 마이그레이션을 완료 하는 데는 총 146 시간이 소요 됩니다.

다음은 가정에 따라 예상 되는 가격입니다. 

![가격 책정 계산을 보여 주는 표](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> 이는 가상의 가격 책정 예입니다. 실제 가격은 사용자 환경의 실제 처리량에 따라 달라 집니다.
> Azure Windows VM (자체 호스팅 통합 런타임이 설치 된)의 가격은 포함 되지 않습니다.

### <a name="additional-references"></a>추가 참조

- [HDFS 커넥터](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob storage 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [복사 작업 성능 조정 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [자체 호스팅 통합 런타임 만들기 및 구성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [자체 호스팅 통합 런타임 고가용성 및 확장성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [시간이 분할 된 파일 이름에 따라 증분 방식으로 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [LastModifiedDate에 따라 새 파일 및 변경 된 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [가격 책정 페이지 Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용 하 여 여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)