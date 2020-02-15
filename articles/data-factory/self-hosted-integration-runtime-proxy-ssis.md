---
title: 자체 호스팅 통합 런타임을 SSIS에 대 한 프록시로 구성
description: 자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime 프록시로 구성 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/06/2020
ms.openlocfilehash: 5f9e15b83c36c6c19fbe93c5f1df365f6f763c81
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187677"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Azure Data Factory에서 Azure-SSIS IR에 대 한 프록시로 자체 호스팅 IR 구성

이 문서에서는 프록시로 구성 된 자체 호스팅 통합 런타임 (자체 호스팅 IR)과 Azure Data Factory의 Azure-SSIS Integration Runtime (Azure-SSIS IR)에서 SSIS (SQL Server Integration Services) 패키지를 실행 하는 방법을 설명 합니다. 

이 기능을 사용 하면 [Azure-SSIS IR를 가상 네트워크에 연결](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)하지 않고도 온-프레미스 데이터에 액세스할 수 있습니다. 이 기능은 회사 네트워크에 Azure-SSIS IR을 삽입할 수 있을 정도로 너무 복잡 하거나 정책이 너무 제한적 인 경우에 유용 합니다.

이 기능은 온-프레미스 데이터 원본을 사용 하는 데이터 흐름 태스크를 포함 하는 패키지를 두 개의 준비 작업으로 분할 합니다. 
* 자체 호스팅 IR에서 실행 되는 첫 번째 작업은 먼저 온-프레미스 데이터 원본의 데이터를 Azure Blob storage의 준비 영역으로 이동 합니다.
* Azure-SSIS IR에서 실행 되는 두 번째 작업은 준비 영역에서 원하는 데이터 대상으로 데이터를 이동 합니다.

이 기능의 다른 이점과 기능을 사용 하면 예를 들어, Azure-SSIS IR에서 아직 지원 하지 않는 지역에 자체 호스팅 IR을 설정 하 고, 데이터 원본의 방화벽에서 자체 호스팅 IR의 공용 고정 IP 주소를 허용할 수 있습니다.

## <a name="prepare-the-self-hosted-ir"></a>자체 호스팅 IR 준비

이 기능을 사용 하려면 먼저 데이터 팩터리를 만들고 여기에 Azure-SSIS IR을 설정 합니다. 아직 수행 하지 않은 경우 [Azure-SSIS IR 설정](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)의 지침을 따르세요.

그런 다음 Azure-SSIS IR 설정 된 것과 동일한 data factory에서 자체 호스팅 IR을 설정 합니다. 이렇게 하려면 [자체 호스팅 IR 만들기](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)를 참조 하세요.

마지막으로, 다음과 같이 온-프레미스 컴퓨터 또는 Azure VM (가상 머신)에서 추가 드라이버 및 런타임 뿐만 아니라 자체 호스팅 IR의 최신 버전을 다운로드 하 여 설치 합니다.
- [자체 호스팅 IR](https://www.microsoft.com/download/details.aspx?id=39717)의 최신 버전을 다운로드 하 여 설치 합니다.
- 패키지에 개체 연결 및 OLEDB (데이터베이스 포함) 커넥터를 사용 하는 경우에는 자체 호스팅 IR이 설치 된 컴퓨터에 관련 OLEDB 드라이버를 다운로드 하 여 설치 합니다 (아직 수행 하지 않은 경우).  

  SQL Server (SQL Server Native Client [SQLNCLI]) 용 OLEDB 드라이버의 이전 버전을 사용 하는 경우 [64 비트 버전을 다운로드](https://www.microsoft.com/download/details.aspx?id=50402)합니다.  

  최신 버전의 OLEDB driver for SQL Server (MSOLEDBSQL)를 사용 하는 경우 [64 비트 버전을 다운로드](https://www.microsoft.com/download/details.aspx?id=56730)합니다.  
  
  PostgreSQL, MySQL, Oracle 등의 다른 데이터베이스 시스템에 OLEDB 드라이버를 사용 하는 경우 해당 웹 사이트에서 64 비트 버전을 다운로드할 수 있습니다.
- 아직 수행 하지 않은 경우 자체 호스팅 IR이 설치 된 컴퓨터에 [64 비트 버전의 C++ Visual (VC) 런타임을 다운로드 하 여 설치](https://www.microsoft.com/download/details.aspx?id=40784) 합니다.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>스테이징을 위해 Azure Blob 저장소 연결 된 서비스 준비

아직 수행 하지 않은 경우 Azure-SSIS IR 설정 된 동일한 데이터 팩터리에 Azure Blob 저장소 연결 된 서비스를 만듭니다. 이렇게 하려면 [Azure data factory 연결 된 서비스 만들기](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)를 참조 하세요. 다음을 수행 해야 합니다.
- **데이터 저장소**에서 **Azure Blob Storage**를 선택 합니다.  
- **Integration runtime을 통한 연결**의 경우 **AutoResolveIntegrationRuntime**를 선택 합니다.  
- **인증 방법**에 대해 **계정 키**, **SAS URI**또는 **서비스 주체**를 선택 합니다.  

    >[!TIP]
    > **서비스 주체**를 선택 하는 경우 적어도 *저장소 Blob 데이터 참가자* 역할을 부여 합니다. 자세한 내용은 [Azure Blob storage 커넥터](connector-azure-blob-storage.md#linked-service-properties)를 참조 하세요.

![스테이징을 위해 Azure Blob 저장소 연결 된 서비스 준비](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>자체 호스팅 IR을 프록시로 사용 하 여 Azure-SSIS IR 구성

스테이징을 위해 자체 호스팅 IR 및 Azure Blob storage 연결 된 서비스를 준비한 후 이제 data factory 포털 또는 앱에서 자체 호스팅 IR을 사용 하 여 새 또는 기존 Azure-SSIS IR를 구성할 수 있습니다. 이렇게 하기 전에 기존 Azure-SSIS IR 이미 실행 중인 경우에는 중지 한 다음 다시 시작 합니다.

1. **Integration runtime 설정** 창에서 **다음**을 선택 하 여 **일반 설정** 및 **SQL 설정** 섹션을 건너뜁니다. 

1. **고급 설정** 섹션에서 다음을 수행 합니다.

   1. **Azure-SSIS Integration Runtime에 대 한 프록시로 자체 호스트 된 Integration Runtime 설정** 확인란을 선택 합니다. 

   1. **자체 호스팅 Integration Runtime** 드롭다운 목록에서 기존 자체 호스팅 IR을 Azure-SSIS IR 프록시로 선택 합니다.

   1. **스테이징 저장소 연결 된 서비스** 드롭다운 목록에서 기존 Azure Blob storage 연결 된 서비스를 선택 하거나 스테이징을 위한 새 서비스를 만듭니다.

   1. **준비 경로** 상자에서 선택한 Azure blob storage 계정에 blob 컨테이너를 지정 하거나, 스테이징에 기본 항목을 사용 하도록 비워 둡니다.

   1. **계속**을 선택합니다.

   ![자체 호스팅 IR을 사용 하는 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

PowerShell을 사용 하 여 자체 호스트 IR을 사용 하 여 새 또는 기존 Azure-SSIS IR를 프록시로 구성할 수도 있습니다.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>프록시를 사용 하 여 SSIS 패키지 연결

Visual Studio 또는 독립 실행형 설치 관리자에 대 한 최신 SSDT SSIS 프로젝트 확장을 사용 하 여 OLEDB 또는 플랫 파일 연결 관리자에 추가 된 새 `ConnectByProxy` 속성을 찾을 수 있습니다.
* [Visual Studio 용 SSIS 프로젝트 확장을 사용 하 여 SSDT 다운로드](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [독립 실행형 설치 관리자 다운로드](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

온-프레미스 데이터베이스 또는 파일에 액세스할 수 있는 OLEDB 또는 플랫 파일 원본을 사용 하 여 데이터 흐름 태스크를 포함 하는 새 패키지를 디자인할 경우 관련 연결 관리자의 **속성** 창에서이 속성을 *True* 로 설정 하 여이 속성을 설정할 수 있습니다.

![ConnectByProxy 속성 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

기존 패키지를 실행 하는 경우이 속성을 수동으로 변경할 필요 없이이 속성을 사용 하도록 설정할 수도 있습니다.  옵션에는
- **옵션 A**: Azure-SSIS IR에서 실행할 최신 SSDT를 사용 하 여 패키지를 포함 하는 프로젝트를 열고 다시 빌드하고 다시 배포 합니다. 그런 다음 관련 연결 관리자에 대해 *True* 로 설정 하 여 속성을 설정할 수 있습니다. SSMS에서 패키지를 실행 하는 경우 이러한 연결 관리자는 **패키지 실행** 팝업 창의 **연결 관리자** 탭에 나타납니다.

  ![ConnectByProxy property2 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  또한 Data Factory 파이프라인에서 패키지를 실행 하는 경우 [SSIS 패키지 실행 작업](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 의 **연결 관리자** 탭에 표시 되는 관련 연결 관리자에 대해이 속성을 *True* 로 설정 하 여 속성을 설정할 수 있습니다.
  
  ![ConnectByProxy property3 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **옵션 B:** SSIS IR에서 실행할 패키지를 포함 하는 프로젝트를 다시 배포 합니다. 그런 다음 SSMS에서 패키지를 실행할 때 **패키지 실행** 팝업 창의 **고급** 탭에서 속성 경로를 제공 하 고, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`하 고, 속성을 *True* 로 설정 하 여 속성을 사용 하도록 설정할 수 있습니다.

  ![ConnectByProxy property4 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Data Factory 파이프라인에서 패키지 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`를 실행 하는 경우 [SSIS 패키지 실행 작업](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) **의 속성 재정의 탭에서** 속성 재정의로 설정 하 고 속성 경로를 *True* 로 설정 하 여 속성을 설정할 수도 있습니다.
  
  ![ConnectByProxy property5 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>첫 번째와 두 번째 준비 작업을 디버깅 합니다.

자체 호스팅 IR에서 *C:\ProgramData\SSISTelemetry* 폴더의 런타임 로그와 *C:\ProgramData\SSISTelemetry\ExecutionLog* 폴더에 있는 첫 번째 준비 태스크의 실행 로그를 찾을 수 있습니다.  Ssisdb 또는 파일 시스템에 패키지를 저장 하는지, 파일 공유에 저장 하는지 또는 Azure Files에서 패키지를 저장 하는지에 따라 SSISDB 또는 지정 된 로깅 경로에서 두 번째 준비 작업의 실행 로그를 찾을 수 있습니다. 두 번째 준비 태스크의 실행 로그에서 첫 번째 준비 태스크의 고유 Id를 찾을 수도 있습니다. 

![첫 번째 준비 태스크의 고유 ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>준비 작업에서 Windows 인증 사용

자체 호스팅 IR에서 준비 작업에 Windows 인증이 필요한 경우 [동일한 windows 인증을 사용 하도록 SSIS 패키지를 구성](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)합니다. 

스테이징 작업은 자체 호스팅 IR 서비스 계정 (기본적으로*NT SERVICE\DIAHostService*)을 사용 하 여 호출 되 고, 데이터 저장소는 Windows 인증 계정을 사용 하 여 액세스 됩니다. 두 계정 모두에 특정 보안 정책을 할당 해야 합니다. 자체 호스팅 IR 컴퓨터에서 로컬 **보안 정책** > **로컬 정책** > **사용자 권한 할당**으로 이동한 후 다음을 수행 합니다.

1. *프로세스에 대 한 메모리 할당량 조정* 을 할당 하 고 *프로세스 수준 토큰* 정책을 자체 호스팅 IR 서비스 계정으로 바꿉니다. 이는 기본 서비스 계정으로 자체 호스팅 IR을 설치할 때 자동으로 발생 합니다. 다른 서비스 계정을 사용 하는 경우 동일한 정책을 할당 합니다.

1. Windows 인증 계정에 *서비스로 로그온* 정책을 할당 합니다.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>첫 번째와 두 번째 준비 작업에 대 한 청구

자체 호스팅 ir에서 실행 되는 첫 번째 스테이징 작업은 자체 호스팅 IR에서 실행 되는 데이터 이동 작업에 대 한 요금이 청구 되는 것 처럼 별도로 청구 됩니다. 이는 [Azure Data Factory 데이터 파이프라인 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 문서에 지정 되어 있습니다.

Azure-SSIS IR에서 실행 되는 두 번째 준비 작업은 별도로 청구 되지 않지만 실행 중인 Azure-SSIS IR는 [Azure-SSIS IR 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/ssis/) 문서에 지정 된 대로 청구 됩니다.

## <a name="current-limitations"></a>현재 제한 사항

- ODBC (Open Database Connectivity), OLEDB 또는 플랫 파일 연결 관리자와 ODBC, OLEDB 및 플랫 파일 원본이 나 OLEDB 대상이 있는 데이터 흐름 태스크만 현재 지원 됩니다. 
- *계정 키*, *SAS (공유 액세스 서명) URI*또는 *서비스 주체* 인증을 사용 하 여 구성 된 Azure Blob 저장소 연결 서비스만 현재 지원 됩니다.

## <a name="next-steps"></a>다음 단계

자체 호스팅 IR을 Azure-SSIS IR에 대 한 프록시로 구성한 후에는 Data Factory 파이프라인에서 SSIS 패키지 실행 작업으로 온-프레미스 데이터에 액세스 하기 위해 패키지를 배포 하 고 실행할 수 있습니다. 방법에 대 한 자세한 내용은 [Data Factory 파이프라인에서 ssis 패키지를 실행 하 여 Ssis 패키지 실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조 하세요.
