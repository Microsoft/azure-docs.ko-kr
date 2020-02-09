---
title: Azure-SSIS Integration Runtime에 대한 성능 구성
description: 고성능을 위해 Azure-SSIS Integration Runtime의 속성을 구성하는 방법을 알아봅니다.
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: 15aac35a7ebc505e76ddfd0c538c4fddb7b2d9ff
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930552"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>고성능을 위해 Azure-SSIS Integration Runtime 구성

이 문서는 고성능을 위해 Azure-SSIS IR(Integration Runtime)을 구성하는 방법을 설명합니다. Azure-SSIS IR을 사용하면 Azure에서 SSIS(SQL Server Integration Services) 패키지를 배포하고 실행할 수 있습니다. Azure-SSIS IR에 대한 자세한 내용은 [통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime)을 참조하세요. Azure에서 SSIS 패키지를 배포하고 실행하는 데 대한 자세한 내용은 [SQL Server Integration Services 워크로드를 클라우드로 이동](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)을 참조하세요.

> [!IMPORTANT]
> 이 문서에는 SSIS 개발 팀 멤버가 수행한 사내 테스트의 관찰 내용과 성능 결과가 포함되어 있습니다. 결과는 다양할 수 있습니다. 비용과 성능에 영향을 주는 구성 설정을 마무리하기 전에 직접 테스트를 수행하십시오.

## <a name="properties-to-configure"></a>구성할 속성

구성 스크립트의 다음 부분은 Azure-SSIS Integration Runtime을 만들 때 구성할 수 있는 속성을 보여줍니다. 전체 PowerShell 스크립트 및 설명은 [Azure에 SQL Server Integration Services 패키지 배포](tutorial-deploy-ssis-packages-azure-powershell.md)를 참조하세요.

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation**은 통합 런타임 작업자 노드의 위치입니다. 작업자 노드는 Azure SQL 데이터베이스의 SSISDB(SSIS 카탈로그 데이터베이스)와 지속적으로 연결을 유지 관리합니다. **AzureSSISLocation**을 SSISDB를 호스트하는 SQL Database 서버와 동일한 위치로 설정하면 통합 런타임이 최대한 효율적으로 작동합니다.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Azure-SSIS IR을 포함하여 Data Factory는 다음 옵션을 지원합니다.
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   표준\_D2\_v3
-   표준\_D4\_v3
-   표준\_D8\_v3
-   표준\_D16\_v3
-   표준\_D32\_v3
-   표준\_D64\_v3
-   표준\_E2\_v3
-   표준\_E4\_v3
-   표준\_E8\_v3
-   표준\_E16\_v3
-   표준\_E32\_v3
-   표준\_E64\_v3

SSIS 엔지니어링 팀의 비공식 사내 테스트에 따르면 D 시리즈가 A 시리즈보다 SSIS 패키지 실행에 더 적합합니다.

-   D 시리즈의 성능/가격은 시리즈 보다 높고 v3 시리즈의 성능/가격 비율은 v2 시리즈 보다 높습니다.
-   D 시리즈에 대한 처리량은 동일한 가격의 시리즈 보다 더 높고 v3 시리즈의 처리량은 동일한 가격의 v2 시리즈 보다 높습니다.
-   Azure-SSIS IR의 v2 시리즈 노드가 사용자 지정 설치에 적합 하지 않으므로 v3 시리즈 노드를 대신 사용 하세요. 이미 v2 시리즈 노드를 사용 하는 경우 가능한 한 빨리 v3 시리즈 노드를 사용 하도록 전환 하세요.
-   E 시리즈는 다른 컴퓨터 보다 메모리와 CPU의 비율을 높이는 메모리 액세스에 최적화 된 VM 크기입니다. 패키지에 많은 메모리가 필요한 경우 E 시리즈 VM을 선택 하는 것을 고려할 수 있습니다.

### <a name="configure-for-execution-speed"></a>실행 속도 구성
실행할 패키지가 많지 않고 패키지를 신속하게 실행하려면 다음 차트의 정보를 사용하여 시나리오에 적합한 가상 머신 유형을 선택합니다.

이 데이터는 단일 작업자 노드에서 단일 패키지 실행을 나타냅니다. 패키지는 Azure Blob Storage에서 이름 및 성 열이 포함 된 300만 레코드를 로드 하 고, 전체 이름 열을 생성 하 고, 전체 이름이 20 자 보다 긴 레코드를 Azure Blob Storage에 기록 합니다.

![SSIS Integration Runtime 패키지 실행 속도](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>전체 처리량에 대한 구성

실행할 패키지가 많고 전체 처리량을 가장 중요하게 생각하는 경우 다음 차트의 정보를 사용하여 시나리오에 적합한 가상 머신 유형을 선택합니다.

![SSIS Integration Runtime 최대 전체 처리량](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber**는 통합 런타임의 확장성을 조정합니다. 통합 런타임의 처리량은 **AzureSSISNodeNumber**에 비례합니다. 우선 **AzureSSISNodeNumber**를 작은 값으로 설정하고 통합 런타임의 처리량을 모니터링한 다음 시나리오에 맞게 값을 조정합니다. 작업자 노드 수를 다시 구성하려면 [Azure-SSIS 통합 런타임 관리](manage-azure-ssis-integration-runtime.md)를 참조하세요.

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

이미 강력한 작업자 노드를 사용하여 패키지를 실행 중인 경우 **AzureSSISMaxParallelExecutionsPerNode**를 늘리면 통합 런타임의 전체 처리량이 증가할 수 있습니다. Standard_D1_v2 노드의 경우 노드당 1-4개의 병렬 실행이 지원되며, 다른 모든 유형의 노드에 대해 1-최대 (2 x 코어 수, 8) 노드당 병렬 실행이 지원 됩니다. **AzureSSISMaxParallelExecutionsPerNode** 에서 지 원하는 최대값을 초과 하는 경우 지원 티켓을 열 수 있으며, 최대 값을 늘릴 수 있으며 Azure Powershell을 사용 하 여 **AzureSSISMaxParallelExecutionsPerNode**를 업데이트 해야 합니다.
패키지 비용과 작업자 노드에 대한 다음 구성을 기반으로 적절한 값을 예측할 수 있습니다. 자세한 내용은 [범용 가상 머신 크기](../virtual-machines/windows/sizes-general.md)를 참조하세요.

| 크기             | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 성능(Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| 표준\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| 표준\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| 표준\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| 표준\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| 표준\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| 표준\_D64\_v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32 / 192x500                      | 8 / 30000                                      |
| 표준\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1000                                       |
| 표준\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| 표준\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| 표준\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| 표준\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| 표준\_E64\_v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32 / 192x500                      | 8 / 30000                                      |

다음은 **AzureSSISMaxParallelExecutionsPerNode** 속성에 올바른 값을 설정하는 지침입니다. 

1. 처음에는 작은 값으로 설정합니다.
2. 소량을 늘려 가면서 전체 처리량이 향상되는지 여부를 확인합니다.
3. 전체 처리량이 최대값에 도달하면 값 늘리기를 중지합니다.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier**는 Azure SQL 데이터베이스의 SSISDB(SSIS 카탈로그 데이터베이스)에 대한 가격 책정 계층입니다. 이 설정은 IR 인스턴스의 최대 작업자 수, 패키지 실행을 큐에 넣는 속도 및 실행 로그를 로드하는 속도에 영향을 줍니다.

-   패키지 실행을 큐에 넣는 속도 및 실행 로그를 로드하는 속도가 중요하지 않은 경우 가장 낮은 데이터베이스 가격 책정 계층을 선택할 수 있습니다. 기본 가격을 사용하는 Azure SQL Database는 통합 런타임 인스턴스에 8명의 작업자를 지원합니다.

-   작업자 수가 8 이상이거나 코어 수가 50을 넘는 경우 기본보다 더 강력한 데이터베이스를 선택합니다. 그렇지 않으면 데이터베이스가 통합 런타임 인스턴스의 병목 상태가 되어 전반적인 성능에 부정적인 영향을 미칩니다.

-   로깅 수준이 자세히로 설정 된 경우 s3과 같은 더 강력한 데이터베이스를 선택 합니다. 비공식 사내 테스트에 따르면 s3 가격 책정 계층은 2 개 노드, 128 병렬 개수 및 자세한 로깅 수준으로 SSIS 패키지 실행을 지원할 수 있습니다.

Azure Portal에서 사용할 수 있는 DTU([데이터베이스 트랜잭션 단위](../sql-database/sql-database-what-is-a-dtu.md)) 사용량 정보를 기반으로 데이터베이스 가격 책정 계층을 조정할 수도 있습니다.

## <a name="design-for-high-performance"></a>고성능을 위한 디자인
Azure에서 실행할 SSIS 패키지를 설계하는 것은 온-프레미스 실행을 위한 패키지를 설계하는 것과 다릅니다. 동일한 패키지에서 여러 개의 독립적인 작업을 결합하는 대신 Azure-SSIS IR에서 보다 효율적인 실행을 위해 여러 패키지로 분리합니다. 서로 완료될 때까지 기다릴 필요가 없도록 각 패키지에 대한 패키지 실행을 만듭니다. 이러한 방식은 Azure-SSIS 통합 런타임의 확장성을 활용하여 전반적인 처리량을 향상시킵니다.

## <a name="next-steps"></a>다음 단계
Azure-SSIS Integration Runtime에 대해 알아봅니다. [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime)을 참조하세요.
