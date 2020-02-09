---
title: Amazon S3에서 Azure Storage로 데이터 마이그레이션
description: Azure Data Factory를 사용 하 여 Amazon s 3에서 Azure Storage 데이터를 마이그레이션합니다.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 6f2db91a35573bc2cbdd0df2cb1ac09914cc956b
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122647"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Azure Data Factory를 사용 하 여 Amazon s 3에서 Azure Storage로 데이터를 마이그레이션합니다. 

Azure Data Factory는 Amazon s 3에서 Azure Blob Storage 또는 Azure Data Lake Storage Gen2으로 대규모로 데이터를 마이그레이션하는 데 성능과 강력 하 고 비용 효율적인 메커니즘을 제공 합니다.  이 문서에서는 데이터 엔지니어와 개발자에 대한 다음 정보를 제공 합니다. 

> [!div class="checklist"]
> * 성능 중심 
> * 복원 력
> * 네트워크 보안
> * 개략적인 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능 중심

ADF는 서로 다른 수준에서 병렬 처리를 허용 하는 서버 리스 아키텍처를 제공 합니다 .이 아키텍처를 통해 개발자는 네트워크 대역폭과 저장소 IOPS 및 대역폭을 완벽 하 게 활용 하 여 환경에 대한 데이터 이동 처리량을 최대화할 수 있습니다. 

고객은 Amazon s 3에서 Azure Blob Storage로 구성 된 수백 개의 파일로 구성 된 데이터를 페타바이트 하 고 처리량이 2gbps 이상인 데이터를 지속적으로 마이그레이션 했습니다. 

![성능](media/data-migration-guidance-s3-to-azure-storage/performance.png)

위의 그림은 다양 한 병렬 처리 수준을 통해 우수한 데이터 이동 속도를 달성할 수 있는 방법을 보여 줍니다.
 
- 단일 복사 작업은 확장 가능한 계산 리소스를 활용할 수 있습니다. Azure Integration Runtime를 사용 하는 경우 서버를 사용 하지 않는 방식으로 각 복사 작업에 대해 [최대 256 DIUs를](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) 지정할 수 있습니다. 자체 호스트 된 Integration Runtime를 사용 하는 경우 수동으로 컴퓨터를 확장 하거나 여러 컴퓨터로 확장할 수 있습니다 ([최대 4 개 노드](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). 단일 복사 작업은 모든 노드에 걸쳐 해당 파일 집합을 분할 합니다. 
- 단일 복사 작업은 여러 스레드를 사용 하 여 데이터 저장소에서 읽고 씁니다. 
- ADF 제어 흐름은 [For each 루프](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)를 사용 하는 등의 여러 복사 작업을 병렬로 시작할 수 있습니다. 

## <a name="resilience"></a>복원력

단일 복사 작업 실행 내에서 ADF에는 기본 제공 재시도 메커니즘이 있으므로 데이터 저장소나 기본 네트워크에서 특정 수준의 일시적인 오류를 처리할 수 있습니다. 

S 3에서 Blob으로 이진 복사를 수행 하 고 s 3에서 ADLS Gen2로 복사 하는 경우 ADF는 검사점을 자동으로 수행 합니다.  복사 작업 실행이 실패 하거나 시간이 초과 되 면 이후 다시 시도 시 복사가 처음부터 시작 하는 대신 마지막 오류 지점에서 다시 시작 됩니다. 

## <a name="network-security"></a>네트워크 보안 

기본적으로 ADF는 Amazon s 3에서 Azure Blob Storage 또는 Azure Data Lake Storage Gen2 HTTPS 프로토콜을 통한 암호화 된 연결을 사용 하 여 데이터를 전송 합니다.  HTTPS는 전송 중인 데이터 암호화를 제공 하며 도청 및 메시지 가로채기 (man-in-the-middle) 공격을 방지 합니다. 

또는 공용 인터넷을 통해 데이터를 전송 하지 않으려면 AWS Direct Connect와 Azure Express 경로 간의 개인 피어 링 링크를 통해 데이터를 전송 하 여 더 높은 수준의 보안을 달성할 수 있습니다.  이를 달성할 수 있는 방법은 아래의 솔루션 아키텍처를 참조 하세요. 

## <a name="solution-architecture"></a>솔루션 아키텍처

공용 인터넷을 통해 데이터 마이그레이션:

![솔루션-아키텍처-공용-네트워크](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- 이 아키텍처에서 데이터는 공용 인터넷을 통해 HTTPS를 사용 하 여 안전 하 게 전송 됩니다. 
- 원본 Amazon S3 뿐만 아니라 대상 Azure Blob Storage 또는 Azure Data Lake Storage Gen2 모두 모든 네트워크 IP 주소의 트래픽을 허용 하도록 구성 되어 있습니다.  특정 IP 범위에 대한 네트워크 액세스를 제한 하는 방법에 대한 아래의 두 번째 아키텍처를 참조 하세요. 
- 사용자 환경에 가장 적합 한 처리량을 얻을 수 있도록 네트워크 및 저장소 대역폭을 완벽 하 게 활용 하기 위해 서버 리스 방식의 처리량을 쉽게 확장할 수 있습니다. 
- 초기 스냅숏 마이그레이션과 델타 데이터 마이그레이션은 모두이 아키텍처를 사용 하 여 달성할 수 있습니다. 

개인 링크를 통해 데이터 마이그레이션: 

![해결 방법-개인-네트워크](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- 이 아키텍처에서 데이터 마이그레이션은 공용 인터넷을 통해 통과 하지 않도록 AWS Direct Connect와 Azure Express 경로 간의 개인 피어 링 링크를 통해 수행 됩니다.  AWS VPC 및 Azure Virtual network를 사용 해야 합니다. 
- 이 아키텍처를 구현 하려면 Azure virtual network 내의 Windows VM에 ADF 자체 호스팅 통합 런타임을 설치 해야 합니다.  자체 호스팅 IR Vm을 수동으로 확장 하거나 여러 Vm (최대 4 개 노드)으로 확장 하 여 네트워크 및 저장소 IOPS/대역폭을 완벽 하 게 활용할 수 있습니다. 
- HTTPS를 통해 데이터를 전송 하는 것이 허용 되지만, 원본 s 3에 대한 네트워크 액세스를 특정 IP 범위로 잠가야 하는 경우 AWS VPC를 제거 하 고 개인 링크를 HTTPS로 대체 하 여이 아키텍처의 변형을 채택할 수 있습니다.  Azure VM에서 Azure 가상 및 자체 호스팅 IR을 유지 하 여 허용 목록 목적의 정적 공용 라우팅할 수 있는 IP를 사용할 수 있습니다. 
- 초기 스냅숏 데이터 마이그레이션과 델타 데이터 마이그레이션은 모두이 아키텍처를 사용 하 여 달성할 수 있습니다. 

## <a name="implementation-best-practices"></a>구현 모범 사례 

### <a name="authentication-and-credential-management"></a>인증 및 자격 증명 관리 

- Amazon S3 계정에 인증 하려면 [IAM 계정에 대한 액세스 키](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties)를 사용 해야 합니다. 
- Azure Blob Storage에 연결 하기 위해 여러 인증 유형이 지원 됩니다.  [Azure 리소스에 관리](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) 되는 id를 사용 하는 것이 좋습니다. azure AD에서 자동으로 관리 되는 ADF 식별을 기반으로 구축 된 연결 된 서비스 정의에서 자격 증명을 제공 하지 않고 파이프라인을 구성할 수 있습니다.  또는 [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [공유 액세스 서명](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)를 사용 하 여 Azure Blob Storage에 인증할 수 있습니다. 
- Azure Data Lake Storage Gen2에 연결 하는 데에도 여러 인증 형식이 지원 됩니다.  [서비스 주체](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) 또는 [저장소 계정 키](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 를 사용할 수도 있지만 [Azure 리소스에 관리](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) 되는 id를 사용 하는 것이 좋습니다. 
- Azure 리소스에 관리 되는 id를 사용 하지 않는 경우 [에는 Azure Key Vault에 자격 증명을 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 하는 것이 좋습니다. ADF 연결 된 서비스를 수정 하지 않고도 키를 중앙에서 중앙에서 관리 하 고 회전할 수 있습니다.  [CI/CD에 대한 모범 사례](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd)중 하나 이기도 합니다. 

### <a name="initial-snapshot-data-migration"></a>초기 스냅숏 데이터 마이그레이션 

데이터 파티션은 100 TB 이상의 데이터를 마이그레이션할 때 특히 권장 됩니다.  데이터를 분할 하려면 ' 접두사 ' 설정을 활용 하 여 Amazon s 3의 폴더와 파일을 이름별로 필터링 한 다음 각 ADF 복사 작업에서 한 번에 하나의 파티션을 복사할 수 있습니다.  더 나은 처리량을 위해 여러 ADF 복사 작업을 동시에 실행할 수 있습니다. 

네트워크 또는 데이터 저장소의 일시적인 문제로 인해 복사 작업이 실패 하는 경우 실패 한 복사 작업을 다시 실행 하 여 AWS S3에서 특정 파티션을 다시 다시 로드할 수 있습니다.  다른 파티션을 로드 하는 다른 모든 복사 작업에는 영향을 주지 않습니다. 

### <a name="delta-data-migration"></a>델타 데이터 마이그레이션 

AWS s 3에서 새로운 파일 또는 변경 된 파일을 식별 하는 가장 효율적인 방법은 시간 분할 명명 규칙을 사용 하는 것입니다. 즉, AWS S3의 데이터가 파일 또는 폴더 이름 (예:/yyyy/mm/dd/file.csv)의 시간 조각 정보를 사용 하 여 분할 된 경우 파이프라인은 증분 복사할 파일/폴더를 쉽게 식별할 수 있습니다. 

또는 AWS S3의 데이터가 시간 분할 되지 않은 경우 ADF는 새 파일 또는 변경 된 파일을 LastModifiedDate에서 식별할 수 있습니다.   ADF는 AWS S3의 모든 파일을 검색 하 고 마지막으로 수정 된 타임 스탬프가 특정 값 보다 큰 새 파일 및 업데이트 된 파일만 복사 하는 방식으로 작동 합니다.  S 3에 많은 파일이 있는 경우 초기 파일 검색은 필터 조건과 일치 하는 파일 수에 관계 없이 시간이 오래 걸릴 수 있습니다.  이 경우 초기 스냅숏 마이그레이션에 동일한 ' 접두사 ' 설정을 사용 하 여 데이터를 먼저 분할 하는 것이 좋습니다. 따라서 파일 검색은 병렬로 수행 될 수 있습니다.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Azure VM에서 자체 호스팅 통합 런타임이 필요한 시나리오 

개인 링크를 통해 데이터를 마이그레이션하거나 Amazon S3 방화벽에서 특정 IP 범위를 허용 하려면 Azure Windows VM에 자체 호스팅 Integration runtime을 설치 해야 합니다. 

- 각 Azure VM에 대해 시작 하는 권장 구성은 32 vCPU 및 128 GB 메모리로 Standard_D32s_v3 있습니다.  데이터 마이그레이션 중에 IR VM의 CPU 및 메모리 사용률을 계속 모니터링 하 여 더 나은 성능을 위해 VM을 확장 하거나 비용을 절약 하기 위해 VM을 확장할 필요가 있는지 확인할 수 있습니다. 
- 단일 자체 호스팅 IR을 사용 하 여 최대 4 개의 VM 노드를 연결 하 여 규모를 확장할 수도 있습니다.  자체 호스팅 IR에 대해 실행 되는 단일 복사 작업은 자동으로 파일 집합을 분할 하 고 모든 VM 노드를 활용 하 여 파일을 병렬로 복사 합니다.  고가용성을 위해 데이터 마이그레이션 중 단일 실패 지점을 방지 하기 위해 2 개의 VM 노드로 시작 하는 것이 좋습니다. 

### <a name="rate-limiting"></a>속도 제한 

모범 사례로, 대표적인 샘플 데이터 집합을 사용 하 여 성능 POC를 수행 하 여 적절 한 파티션 크기를 결정할 수 있습니다. 

단일 파티션과 기본 DIU 설정을 사용 하는 단일 복사 작업으로 시작 합니다.  네트워크의 대역폭 제한 또는 데이터 저장소의 IOPS/대역폭 제한에 도달 하거나 단일 복사 작업에서 허용 되는 최대 256 DIU에 도달할 때까지 DIU 설정을 점차적으로 늘립니다. 

다음으로, 사용자 환경의 제한에 도달할 때까지 동시 복사 작업의 수를 점진적으로 증가 시킵니다. 

ADF 복사 작업에서 보고 하는 제한 오류가 발생 하는 경우 ADF에서 동시성 또는 DIU 설정을 줄이거나 네트워크 및 데이터 저장소의 대역폭/IOPS 제한을 늘려야 합니다.  

### <a name="estimating-price"></a>가격 예측 

> [!NOTE]
> 이는 가상의 가격 책정 예입니다.  실제 가격은 사용자 환경의 실제 처리량에 따라 달라 집니다.

S 3에서 Azure Blob Storage로 데이터를 마이그레이션하기 위해 생성 된 다음 파이프라인을 고려 하십시오. 

![가격-파이프라인](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

다음을 가정해 보세요. 

- 총 데이터 볼륨 2 PB 
- 첫 번째 솔루션 아키텍처를 사용 하 여 HTTPS를 통해 데이터 마이그레이션 
- 2 PB는 1 K 파티션으로 나뉘어 각 복사본은 하나의 파티션으로 이동 합니다. 
- 각 복사본은 DIU = 256으로 구성 되 고 1Gbps 처리량을 달성 합니다. 
- ForEach 동시성은 2로 설정 되 고 집계 처리량은 2Gbps입니다. 
- 마이그레이션을 완료 하는 데는 총 292 시간이 소요 됩니다. 

다음은 위의 가정에 따라 예상 되는 가격입니다. 

![가격 책정-테이블](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>추가 참조 
- [Amazon Simple Storage Service connector](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Azure Blob Storage 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 커넥터](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [복사 작업 성능 조정 가이드](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [자체 호스팅 Integration Runtime 만들기 및 구성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [자체 호스팅 통합 런타임 HA 및 확장성](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Azure Key Vault에 자격 증명 저장](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [분할 된 시간 파일 이름에 따라 증분 방식으로 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [LastModifiedDate에 따라 새 파일 및 변경 된 파일 복사](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [ADF 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>템플릿

Amazon s 3에서 Azure Data Lake Storage Gen2로 구성 된 수백만 개의 파일로 구성 된 페타바이트 데이터를 마이그레이션하기 위해 시작 하는 [템플릿은](solution-template-migration-s3-azure.md) 다음과 같습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용 하 여 여러 컨테이너의 파일 복사](solution-template-copy-files-multiple-containers.md)
