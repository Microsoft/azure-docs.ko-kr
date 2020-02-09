---
title: 자체 호스팅 통합 런타임을 SSIS에 대한 프록시로 구성
description: 자체 호스팅 Integration Runtime Azure-SSIS Integration Runtime에 대한 프록시로 구성 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: b20a615691d95c04574e2909f69b5a83a97f9d14
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048945"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>ADF의 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR 구성

이 문서에서는 프록시로 구성 된 자체 호스팅 IR을 사용 하 여 Azure Data Factory (ADF)에서 Azure-SSIS Integration Runtime (IR)에 SQL Server Integration Services (SSIS) 패키지를 실행 하는 방법을 설명 합니다.  이 기능을 사용 하면 [Azure-SSIS IR 가상 네트워크에 가입](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)하지 않고도 온-프레미스 데이터에 액세스할 수 있습니다.  회사 네트워크에 Azure-SSIS IR를 삽입 하는 데 과도 하 게 복잡 한 구성/제한적인 정책이 있는 경우 유용 합니다.

이 기능은 온-프레미스 데이터 원본을 사용 하는 데이터 흐름 태스크를 포함 하는 패키지를 두 개의 준비 작업으로 분할 합니다. 즉, 자체 호스팅 IR에서 실행 되는 첫 번째 작업은 먼저 온-프레미스 데이터 원본에서 Azure Blob Storage의 준비 영역으로 데이터를 이동 합니다. 그런 다음 Azure-SSIS IR에서 실행 되는 두 번째 항목은 준비 영역에서 원하는 데이터 대상으로 데이터를 이동 합니다.

또한이 기능은 Azure-SSIS IR에서 아직 지원 되지 않는 지역에서 자체 호스팅 IR을 프로 비전 할 수 있도록 하는 다른 혜택/기능을 제공 하 고, 데이터 원본의 방화벽에서 자체 호스팅 IR의 공용 고정 IP 주소를 허용 합니다.

## <a name="prepare-self-hosted-ir"></a>자체 호스팅 IR 준비

이 기능을 사용 하려면 먼저 [Azure-SSIS IR를 프로 비전 하는 방법](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) 문서에 따라 ADF를 만든 후에 Azure-SSIS IR를 프로 비전 해야 합니다.

그런 다음 [자체 호스팅 ir을 만드는 방법](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) 문서에 따라 Azure-SSIS IR 프로 비전 되는 동일한 ADF에서 자체 호스팅 ir을 프로 비전 해야 합니다.

마지막으로, 다음과 같이 온-프레미스 컴퓨터/Azure VM (가상 머신)에서 추가 드라이버 및 런타임 뿐만 아니라 자체 호스팅 IR의 최신 버전을 다운로드 하 여 설치 해야 합니다.
- [여기](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스트 된 IR의 최신 버전을 다운로드 하 여 설치 합니다.
- 패키지에서 OLEDB 커넥터를 사용 하는 경우 자체 호스팅 IR이 설치 된 컴퓨터에 관련 OLEDB 드라이버를 다운로드 하 여 설치 합니다 (아직 수행 하지 않은 경우).  SQLNCLI (SQL Server 용 OLEDB 드라이버의 이전 버전을 사용 하는 경우 [여기](https://www.microsoft.com/download/details.aspx?id=50402)에서 64 비트 버전을 다운로드할 수 있습니다.  최신 버전의 OLEDB driver for SQL Server (MSOLEDBSQL)를 사용 하는 경우 [여기](https://www.microsoft.com/download/details.aspx?id=56730)에서 64 비트 버전을 다운로드할 수 있습니다.  PostgreSQL, MySQL, Oracle 등의 다른 데이터베이스 시스템에 OLEDB 드라이버를 사용 하는 경우 해당 웹 사이트에서 64 비트 버전을 다운로드할 수 있습니다.
- 아직 수행 하지 않은 C++ 경우 자체 호스팅 IR이 설치 된 컴퓨터에서 Visual (VC) 런타임을 다운로드 하 여 설치 합니다.  [여기](https://www.microsoft.com/download/details.aspx?id=40784)에서 64 비트 버전을 다운로드할 수 있습니다.

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>준비를 위해 연결 된 서비스 Azure Blob Storage 준비

[Adf 연결 된 서비스를 만드는 방법](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) 문서를 참조 하 여 Azure-SSIS IR 프로 비전 되는 동일한 adf에 Azure Blob Storage 연결 된 서비스를 만듭니다.  다음 사항을 확인 하세요.
- **데이터 저장소** 에 대해 **Azure Blob Storage** 선택 됨
- **AutoResolveIntegrationRuntime** 은 **integration Runtime을 통해 연결** 하도록 선택 됩니다.
- **인증 방법** 에 대해 **계정 키**/**SAS URI**/**서비스 주체** 를 선택 합니다.

>[!TIP]
> **서비스 사용자** 를 선택 하는 경우 최소한 **저장소 Blob 데이터 참가자 역할**을 부여 합니다. 자세한 내용은 [Azure Blob Storage 커넥터](connector-azure-blob-storage.md#linked-service-properties)를 참조 하세요.

![준비를 위해 연결 된 서비스 Azure Blob Storage 준비](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>자체 호스팅 IR을 프록시로 Azure-SSIS IR 구성

스테이징을 위해 자체 호스팅 IR 및 Azure Blob Storage 연결 된 서비스를 준비한 후에는 이제 ADF 포털/앱에서 프록시를 사용 하 여 자체 호스팅 IR을 사용 하 여 신규/기존 Azure-SSIS IR을 구성할 수 있습니다.  기존 Azure-SSIS IR 실행 되 고 있는 경우이 작업을 수행 하기 전에 중지 하 고 나중에 다시 시작 합니다.

1. Integration runtime 설정 패널에서 **다음** 단추를 선택 하 여 **일반 설정** 및 **SQL 설정** 섹션을 진행 합니다. 

1. **고급 설정** 섹션에서 다음을 수행 합니다.

   1. **Azure-SSIS Integration Runtime에 대한 프록시로 자체 호스트 된 Integration Runtime 설정** 확인란을 선택 합니다. 

   1. **자체 호스팅 Integration Runtime**의 경우 Azure-SSIS IR의 프록시로 기존 자체 호스팅 IR을 선택 합니다.

   1. **스테이징 저장소 연결 된 서비스**의 경우 기존 Azure Blob Storage 연결 된 서비스를 선택 하거나 준비를 위해 새 Blob storage를 만듭니다.

   1. **준비 경로**의 경우 선택한 Azure blob 저장소 계정에 blob 컨테이너를 지정 하거나, 스테이징에 기본 항목을 사용 하도록 비워 둡니다.

   1. **계속**을 선택합니다.

   ![자체 호스팅 IR을 사용 하는 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

PowerShell을 사용 하 여 자체 호스트 IR을 사용 하 여 새/기존 Azure-SSIS IR를 프록시로 구성할 수도 있습니다.

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

[여기에서](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) 다운로드할 수 있는 Visual STUDIO 용 SSIS 프로젝트 확장의 최신 SSDT를 사용 하 여 [여기](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)에서 다운로드할 수 있는 독립 실행형 설치 관리자를 사용 하면 OLEDB/Flat File 연결 관리자에 추가 된 새 **connectbyproxy** 속성을 찾을 수 있습니다.  

OLEDB/Flat File 원본을 사용 하는 데이터 흐름 태스크가 포함 된 새 패키지를 디자인 하 여 온-프레미스의 데이터베이스/파일에 액세스 하는 경우 관련 연결 관리자의 속성 패널에서이 속성을 **True** 로 설정 하 여이 속성을 설정할 수 있습니다.

![ConnectByProxy 속성 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

수동으로 변경 하지 않고도 기존 패키지를 실행할 때이 속성을 사용 하도록 설정할 수도 있습니다.  두 가지 옵션 중에서 선택할 수 있습니다.
- Azure-SSIS IR에서 실행할 최신 SSDT를 사용 하 여 해당 패키지를 포함 하는 프로젝트 열기, 다시 빌드 및 다시 배포: SSMS에서 패키지를 실행할 때 패키지 실행 팝업 창의 **연결 관리자** 탭에 표시 되는 관련 연결 관리자에 대해 속성을 **True** 로 설정 하 여 속성을 설정할 수 있습니다.

  ![ConnectByProxy property2 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  ADF 파이프라인에서 패키지를 실행할 때 [SSIS 패키지 실행 작업](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 의 **연결 관리자** 탭에 표시 되는 관련 연결 관리자에 대해이 속성을 **True** 로 설정 하 여 속성을 설정할 수도 있습니다.
  
  ![ConnectByProxy property3 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- SSIS IR에서 실행할 패키지를 포함 하는 프로젝트를 다시 배포: 속성 경로를 제공 하 고, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`하 고, SSMS에서 패키지를 실행할 때 패키지 실행 팝업 창의 **고급** 탭에서 속성 재정의로 **True** 로 설정 하 여 속성을 설정할 수 있습니다.

  ![ConnectByProxy property4 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  속성 경로를 제공 하 고, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`하 고, ADF 파이프라인에서 패키지를 실행할 때 [SSIS 패키지 실행 작업](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) **의 속성 재정의 탭에** 속성 재정의로 설정 하 여 속성을 **True** 로 설정 하 여 속성을 설정할 수도 있습니다.
  
  ![ConnectByProxy property5 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>첫 번째와 두 번째 준비 작업을 디버깅 합니다.

자체 호스팅 IR에서 `C:\ProgramData\SSISTelemetry` 폴더의 런타임 로그와 `C:\ProgramData\SSISTelemetry\ExecutionLog` 폴더에 있는 첫 번째 준비 작업의 실행 로그를 찾을 수 있습니다.  두 번째 준비 태스크의 실행 로그는 ssisdb 또는 파일 시스템/파일 공유/Azure Files 각각에 패키지를 저장 하는지에 따라 SSISDB 또는 지정 된 로깅 경로에서 찾을 수 있습니다.  첫 번째 준비 태스크의 고유 Id는 두 번째 준비 작업의 실행 로그 (예:) 에서도 확인할 수 있습니다. 

![첫 번째 준비 태스크의 고유 ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>준비 작업에서 Windows 인증 사용

자체 호스팅 IR에서 준비 작업에 Windows 인증이 필요한 경우 [동일한 windows 인증을 사용 하도록 SSIS 패키지를 구성](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)해야 합니다. 스테이징 작업은 자체 호스팅 IR 서비스 계정 (기본적으로`NT SERVICE\DIAHostService`)을 사용 하 여 호출 되 고, 데이터 저장소는 Windows 인증 계정을 사용 하 여 액세스 됩니다. 두 계정 모두에 특정 보안 정책을 할당 해야 합니다. 따라서 자체 호스팅 IR 컴퓨터에서 `Local Security Policy` -> `Local Policies` -> `User Rights Assignment`을 열고 다음 단계를 완료 합니다.
- **프로세스에 대한 메모리 할당량 조정** 을 할당 하 고 **프로세스 수준 토큰** 정책을 자체 호스팅 IR 서비스 계정으로 바꿉니다. 기본 서비스 계정으로 자체 호스팅 IR을 설치 하는 경우 자동으로 수행 됩니다. 다른 서비스 계정을 사용 하는 경우 동일한 정책을 할당 합니다.
- Windows 인증 계정에 **서비스로 로그온** 정책을 할당 합니다.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>첫 번째와 두 번째 준비 작업에 대한 청구

자체 호스팅 IR에서 실행 되는 첫 번째 스테이징 작업은 [ADF 데이터 파이프라인 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 문서에 지정 된 대로 자체 호스팅 ir에서 실행 되는 데이터 이동 작업의 요금이 청구 되는 것과 동일한 방식으로 별도로 청구 됩니다.

Azure-SSIS IR에서 실행 되는 두 번째 스테이징 작업은 별도로 청구 되지 않지만 실행 중인 Azure-SSIS IR는 [Azure-SSIS IR 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/ssis/) 문서에 지정 된 대로 청구 됩니다.

## <a name="current-limitations"></a>현재 제한 사항

- 현재 ODBC/OLEDB/Flat File 연결 관리자와 ODBC/OLEDB/Flat File 원본 또는 OLEDB 대상이 있는 데이터 흐름 작업만 지원 됩니다. 
- **계정 키**/**SAS URI**/**서비스 주체** 인증을 사용 하 여 구성 된 Azure Blob Storage 연결 된 서비스만 현재 지원 됩니다.

## <a name="next-steps"></a>다음 단계

자체 호스팅 IR을 Azure-SSIS IR에 대한 프록시로 구성 하면 ADF 파이프라인에서 ssis 패키지 실행 작업으로 온-프레미스 데이터에 액세스 하기 위해 패키지를 배포 하 고 실행할 수 있습니다. adf 파이프라인에서 ssis 패키지를 실행 하려면 ssis 패키지 [실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조 하세요.
