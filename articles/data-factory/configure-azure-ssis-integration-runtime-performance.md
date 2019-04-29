---
title: Azure-SSIS Integration Runtime에 대한 성능 구성 | Microsoft Docs
description: 고성능을 위해 Azure-SSIS Integration Runtime의 속성을 구성하는 방법을 알아봅니다.
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 271da0a6ff443fcee28bc870821f4222b3018c91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262239"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>고성능을 위해 Azure-SSIS Integration Runtime 구성

이 문서는 고성능을 위해 Azure-SSIS IR(Integration Runtime)을 구성하는 방법을 설명합니다. Azure-SSIS IR을 사용하면 Azure에서 SSIS(SQL Server Integration Services) 패키지를 배포하고 실행할 수 있습니다. Azure-SSIS IR에 대한 자세한 내용은 [통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime)을 참조하세요. Azure에서 SSIS 패키지를 배포하고 실행하는 데 대한 자세한 내용은 [SQL Server Integration Services 워크로드를 클라우드로 이동](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)을 참조하세요.

> [!IMPORTANT]
> 이 문서에는 SSIS 개발 팀 멤버가 수행한 사내 테스트의 관찰 내용과 성능 결과가 포함되어 있습니다. 결과는 다양할 수 있습니다. 비용과 성능에 영향을 주는 구성 설정을 마무리하기 전에 직접 테스트를 수행하십시오.

## <a name="properties-to-configure"></a>구성할 속성

구성 스크립트의 다음 부분은 Azure-SSIS Integration Runtime을 만들 때 구성할 수 있는 속성을 보여줍니다. 전체 PowerShell 스크립트 및 설명은 [Azure에 SQL Server Integration Services 패키지 배포](tutorial-deploy-ssis-packages-azure-powershell.md)를 참조하세요.

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
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
-   Standard\_D4\_v2.

SSIS 엔지니어링 팀의 비공식 사내 테스트에 따르면 D 시리즈가 A 시리즈보다 SSIS 패키지 실행에 더 적합합니다.

-   D 시리즈의 성능/가격 비율은 A 시리즈보다 높습니다.
-   D 시리즈의 처리량은 같은 가격으로 A 시리즈보다 높습니다.

### <a name="configure-for-execution-speed"></a>실행 속도 구성
실행할 패키지가 많지 않고 패키지를 신속하게 실행하려면 다음 차트의 정보를 사용하여 시나리오에 적합한 가상 머신 유형을 선택합니다.

이 데이터는 단일 작업자 노드에서 단일 패키지 실행을 나타냅니다. 이 패키지는 Azure Blob Storage의 성 및 이름 열이 있는 1,000만개의 레코드를 로드하여 전체 이름 열을 생성하고 전체 이름이 20자 이상인 레코드를 Azure Blob Storage에 씁니다.

![SSIS Integration Runtime 패키지 실행 속도](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>전체 처리량에 대한 구성

실행할 패키지가 많고 전체 처리량을 가장 중요하게 생각하는 경우 다음 차트의 정보를 사용하여 시나리오에 적합한 가상 머신 유형을 선택합니다.

![SSIS Integration Runtime 최대 전체 처리량](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber**는 통합 런타임의 확장성을 조정합니다. 통합 런타임의 처리량은 **AzureSSISNodeNumber**에 비례합니다. 우선 **AzureSSISNodeNumber**를 작은 값으로 설정하고 통합 런타임의 처리량을 모니터링한 다음 시나리오에 맞게 값을 조정합니다. 작업자 노드 수를 다시 구성하려면 [Azure-SSIS 통합 런타임 관리](manage-azure-ssis-integration-runtime.md)를 참조하세요.

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

이미 강력한 작업자 노드를 사용하여 패키지를 실행 중인 경우 **AzureSSISMaxParallelExecutionsPerNode**를 늘리면 통합 런타임의 전체 처리량이 증가할 수 있습니다. Standard_D1_v2 노드의 경우 노드당 1-4개의 병렬 실행이 지원되며, 다른 모든 유형의 노드의 경우 노드당 1-8개의 병렬 실행이 지원됩니다.
패키지 비용과 작업자 노드에 대한 다음 구성을 기반으로 적절한 값을 예측할 수 있습니다. 자세한 내용은 [범용 가상 머신 크기](../virtual-machines/windows/sizes-general.md)를 참조하세요.

| 크기             | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 / 예상 네트워크 성능(Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |

다음은 **AzureSSISMaxParallelExecutionsPerNode** 속성에 올바른 값을 설정하는 지침입니다. 

1. 처음에는 작은 값으로 설정합니다.
2. 소량을 늘려 가면서 전체 처리량이 향상되는지 여부를 확인합니다.
3. 전체 처리량이 최대값에 도달하면 값 늘리기를 중지합니다.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier**는 Azure SQL 데이터베이스의 SSISDB(SSIS 카탈로그 데이터베이스)에 대한 가격 책정 계층입니다. 이 설정은 IR 인스턴스의 최대 작업자 수, 패키지 실행을 큐에 넣는 속도 및 실행 로그를 로드하는 속도에 영향을 줍니다.

-   패키지 실행을 큐에 넣는 속도 및 실행 로그를 로드하는 속도가 중요하지 않은 경우 가장 낮은 데이터베이스 가격 책정 계층을 선택할 수 있습니다. 기본 가격을 사용하는 Azure SQL Database는 통합 런타임 인스턴스에 8명의 작업자를 지원합니다.

-   작업자 수가 8 이상이거나 코어 수가 50을 넘는 경우 기본보다 더 강력한 데이터베이스를 선택합니다. 그렇지 않으면 데이터베이스가 통합 런타임 인스턴스의 병목 상태가 되어 전반적인 성능에 부정적인 영향을 미칩니다.

Azure Portal에서 사용할 수 있는 DTU([데이터베이스 트랜잭션 단위](../sql-database/sql-database-what-is-a-dtu.md)) 사용량 정보를 기반으로 데이터베이스 가격 책정 계층을 조정할 수도 있습니다.

## <a name="design-for-high-performance"></a>고성능을 위한 디자인
Azure에서 실행할 SSIS 패키지를 설계하는 것은 온-프레미스 실행을 위한 패키지를 설계하는 것과 다릅니다. 동일한 패키지에서 여러 개의 독립적인 작업을 결합하는 대신 Azure-SSIS IR에서 보다 효율적인 실행을 위해 여러 패키지로 분리합니다. 서로 완료될 때까지 기다릴 필요가 없도록 각 패키지에 대한 패키지 실행을 만듭니다. 이러한 방식은 Azure-SSIS 통합 런타임의 확장성을 활용하여 전반적인 처리량을 향상시킵니다.

## <a name="next-steps"></a>다음 단계
Azure-SSIS Integration Runtime에 대해 알아봅니다. [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime)을 참조하세요.
