---
title: Azure-SSIS 통합 런타임 프로비전
description: Azure에 SSIS 패키지를 배포하고 실행할 수 있도록 Azure Data Factory에서 Azure-SSIS 통합 런타임을 프로비전하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 08/11/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 840ccb00fdc91cc44fee46500bbc7237fe55ff2a
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185522"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory에서 Azure-SSIS 통합 런타임 프로비저닝

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 ADF(Azure Data Factory)에서 Azure Portal을 사용하여 Azure-SSIS(SQL Server Integration Services) IR(Integration Runtime)을 프로비저닝하는 단계를 설명합니다. Azure-SSIS IR에서 지원하는 작업은 다음과 같습니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

Azure-SSIS IR이 프로비저닝되면 익숙한 도구를 사용하여 Azure에서 패키지를 배포하고 실행할 수 있습니다. 이러한 도구는 이미 Azure를 사용하며 SSDT(SQL Server Data Tools), SSMS(SQL Server Management Studio) 및 명령줄 유틸리티(예: [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 및 [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec))를 포함하고 있습니다.

Azure-SSIS IR의 개념 정보는 [Azure-SSIS 통합 런타임 개요](concepts-integration-runtime.md#azure-ssis-integration-runtime)를 참조하세요.

이 자습서에서는 다음 단계를 완료합니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Azure-SSIS 통합 런타임 프로비전

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

- **Azure SQL Database 서버(선택 사항)** 데이터베이스 서버가 아직 없는 경우 시작하기 전에 Azure Portal에서 이 서버를 만듭니다. 그러면 Data Factory에서 SSISDB 인스턴스를 이 데이터베이스 서버에 만듭니다. 

  Integration Runtime과 동일한 Azure 지역에 데이터베이스 서버를 만드는 것이 좋습니다. 이 구성을 사용하면 통합 런타임에서 Azure 지역을 벗어나지 않고 SSISDB에 실행 로그를 쓸 수 있습니다.

  다음 사항에 유의하세요.

  - 선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버의 유형을 선택하는 방법에 대한 지침은 [SQL Database 및 SQL Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조하세요. 
  
    IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

  - 데이터베이스 서버에 대해 **Azure 서비스 방문 허용** 설정을 사용하도록 설정되어 있는지 확인합니다. IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하여 SSISDB를 호스팅하는 경우에는 이 설정이 적용되지 않습니다. 자세한 내용은 [Azure SQL Database 보호](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)를 참조하세요. PowerShell을 사용하여 이 설정을 사용하려면 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)을 참조하세요.

  - 클라이언트 머신의 IP 주소 또는 이러한 주소가 포함된 IP 주소의 범위를 데이터베이스 서버에 대한 방화벽 설정의 클라이언트 IP 주소 목록에 추가합니다. 자세한 내용은 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](../sql-database/sql-database-firewall-configure.md)을 참조하세요.

  - 서버 관리자 자격 증명을 통한 SQL 인증을 사용하거나 데이터베이스 서버에 연결하거나 데이터 팩터리의 관리 ID를 통한 Azure AD 인증을 사용하여 데이터베이스 서버에 연결할 수 있습니다. 후자의 경우 데이터 팩터리의 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure AD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

  - 데이터베이스 서버에 SSISDB 인스턴스가 아직 없는지 확인합니다. Azure-SSIS IR 프로비저닝은 기존 SSISDB 인스턴스 사용을 지원하지 않습니다.

> [!NOTE]
> Data Factory 및 Azure-SSIS IR을 현재 사용할 수 있는 Azure 지역의 목록은 [지역별 사용 가능한 Data Factory 및 SSIS IR](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)을 참조하세요. 

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

Azure Portal을 통해 데이터 팩터리를 만들려면 [UI를 통해 데이터 팩터리 만들기](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)의 단계별 지침을 따릅니다. 만든 후에 빠르게 액세스할 수 있도록 하려면 이 작업을 수행하면서 **대시 보드에 고정**을 선택합니다. 

데이터 팩터리가 만들어지면 Azure Portal에서 개요 페이지를 엽니다. **작성자 및 모니터링** 타일을 선택하여 별도의 탭에서 **같이 시작해 볼까요?** 페이지를 엽니다. 여기서 Azure-SSIS IR을 계속 만들 수 있습니다.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 만들기

### <a name="from-the-data-factory-overview"></a>Data Factory 개요에서

1. **시작** 페이지에서 **SSIS Integration Runtime 구성** 타일을 선택합니다. 

   !["SSIS Integration Runtime 구성" 타일](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

### <a name="from-the-authoring-ui"></a>작성 UI에서

1. Azure Data Factory UI에서 **편집** 탭으로 전환하여 **연결**을 선택합니다. 그런 다음, **통합 런타임** 탭으로 전환하여 데이터 팩터리의 기존 통합 런타임을 확인합니다. 

   ![기존 IR 보기 선택](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. **새로 만들기**를 선택하여 Azure-SSIS IR을 만들고, **통합 런타임 설치** 창을 엽니다. 

   ![메뉴를 통한 통합 런타임](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **통합 런타임 설치** 패널에서 **Lift-and-shift existing SSIS packages to execute in Azure**(Azure에서 실행할 기존 SSIS 패키지를 리프트 앤 시프트) 타일을 선택하고, **다음**을 선택합니다.

   ![통합 런타임 유형 지정](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR을 설정하기 위한 나머지 단계는 [Azure SSIS 통합 런타임 프로비전](#provision-an-azure-ssis-integration-runtime) 섹션을 참조하세요. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비전

**통합 런타임 설치** 창에는 일반, 배포 및 고급 설정을 연속으로 구성하는 세 개의 페이지가 있습니다.

### <a name="general-settings-page"></a>일반 설정 페이지

**통합 런타임 설치** 창의 **일반 설정** 페이지에서 다음 단계를 완료합니다. 

   ![일반 설정](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. **이름**에는 통합 런타임의 이름을 입력합니다. 

   1. **설명**에는 통합 런타임에 대한 설명을 입력합니다. 

   1. **위치**에는 통합 런타임의 위치를 선택합니다. 지원되는 위치만 표시됩니다. SSISDB를 호스트하는 데이터베이스의 서버와 동일한 위치를 선택하는 것이 좋습니다. 

   1. **노드 크기**로는 통합 런타임 클러스터의 노드 크기를 선택합니다. 지원되는 노드 크기만 표시됩니다. 여러 개의 계산 집약적 또는 메모리 집약적 패키지를 실행하려면 큰 노드 크기(강화)를 선택합니다. 

   1. **노드 수**로는 통합 런타임 클러스터의 노드 수를 선택합니다. 지원되는 노드 수만 표시됩니다. 여러 패키지를 동시에 실행하려면 노드 수가 많은 대형 클러스터(규모 확장)를 선택합니다. 

   1. **버전/라이선스**의 경우 통합 런타임에 대한 SQL Server 버전, 즉 Standard 또는 Enterprise를 선택합니다. 통합 런타임에서 고급 기능을 사용하려면 Enterprise를 선택합니다. 

   1. **비용 절감**의 경우 통합 런타임에 대한 AHB(Azure 하이브리드 혜택) 옵션에서 **예** 또는 **아니요**를 선택합니다. Software Assurance를 통해 사용자 고유의 SQL Server 라이선스를 가져와서 하이브리드를 사용함으로써 비용을 절감하려면 **예**를 선택합니다. 

   1. **다음**을 선택합니다. 

### <a name="deployment-settings-page"></a>배포 설정 페이지

**통합 런타임 설치** 창의 **배포 설정** 페이지에서 SSISDB 및 또는 Azure-SSIS IR 패키지 저장소를 만드는 옵션이 있습니다.

#### <a name="creating-ssisdb"></a>SSISDB 생성

**통합 런타임 설정** 창의 **배포 설정** 페이지에서 SSISDB(프로젝트 배포 모델)에 패키지를 배포하려면 **프로젝트/패키지/환경/실행 로그를 저장하기 위해 Azure SQL Database 서버/Managed Instance가 호스트하는 SSIS 카탈로그(SSISDB) 만들기** 확인란을 선택합니다. 또는 Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 패키지를 배포하려는 경우에는 SSISDB를 만들거나 확인란을 선택할 필요가 없습니다.

배포 모델에 관계없이 Azure SQL Managed Instance가 호스트하는 SQL Server 에이전트를 사용하여 패키지 실행을 오케스트레이션/예약하려는 경우 SSISDB에서 사용하도록 설정되어 있으므로 이 확인란을 선택합니다. 자세한 내용은 [Azure SQL Managed Instance 에이전트를 통해 SSIS 패키지 실행 예약](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-managed-instance-agent)을 참조하세요.
   
확인란을 선택한 경우 다음 단계를 완료하여 사용자를 대신하여 만들고 관리할 SSISDB를 호스트하는 사용자 고유의 데이터베이스 서버를 가져옵니다.

   ![SSISDB의 배포 설정](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. **구독**에서는 SSISDB를 호스트하는 데이터베이스 서버가 있는 Azure 구독을 선택합니다. 

   1. **위치**에서는 SSISDB를 호스트하는 데이터베이스 서버의 위치를 선택합니다. 통합 런타임과 동일한 위치를 선택하는 것이 좋습니다.

   1. **카탈로그 데이터베이스 서버 엔드포인트**로는 SSISDB를 호스트하는 데이터베이스 서버의 엔드포인트를 선택합니다. 
   
      선택한 데이터베이스 서버에 따라 사용자를 대신하여 SSISDB 인스턴스를 단일 데이터베이스, 탄력적 풀의 일부 또는 관리형 인스턴스로 만들 수 있습니다. 이는 공용 네트워크에서 액세스하거나 가상 네트워크에 조인하여 액세스할 수 있습니다. SSISDB를 호스트할 데이터베이스 서버의 유형을 선택하는 방법에 대한 지침은 [SQL Database 및 SQL Managed Instance 비교](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조하세요.   

      IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버 또는 프라이빗 엔드포인트가 있는 관리형 인스턴스를 선택하여 SSISDB를 호스팅하거나 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 Azure-SSIS IR을 가상 네트워크에 조인해야 합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.

   1. SSISDB를 호스팅할 데이터베이스 서버의 인증 방법을 선택하려면 **ADF의 관리 ID를 통한 Azure AD 인증 사용** 확인란을 선택합니다. 데이터 팩터리의 관리 ID를 통한 SQL 인증 또는 Azure AD 인증을 선택합니다.

      확인란을 선택하면 데이터 팩터리의 관리 ID를 데이터베이스 서버에 대한 액세스 권한이 있는 Azure AD 그룹에 추가해야 합니다. 자세한 내용은 [Azure AD 인증을 사용하여 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요.
   
   1. **관리 사용자 이름**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 SQL 인증 사용자 이름을 입력합니다. 

   1. **관리자 암호**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 SQL 인증 암호를 입력합니다. 

   1. **카탈로그 데이터베이스 서비스 계층**에 대해 SSISDB를 호스팅할 데이터베이스 서버의 서비스 계층을 선택합니다. 기본, 표준 또는 프리미엄 계층을 선택하거나 탄력적 풀 이름을 선택합니다.

**연결 테스트**를 선택하고(해당하는 경우), 테스트가 성공하면 **다음**을 선택합니다.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR 패키지 저장소 만들기

**통합 런타임 설정** 창의 **배포 설정** 페이지에서 Azure-SSIS IR 패키지 저장소를 통해 MSDB, 파일 시스템 또는 Azure Files(패키지 배포 모델)에 배포된 패키지를 관리하려면 **Azure SQL Managed Instance가 호스트하는 파일 시스템/Azure Files/SQL Server 데이터베이스(MSDB)에 배포되는 패키지를 관리할 패키지 저장소 만들기** 확인란을 선택합니다.
   
Azure-SSIS IR 패키지 저장소를 사용하면 [레거시 SSIS 패키지 저장소](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)와 비슷하게 SSMS를 통해 패키지를 가져오고/내보내고/삭제하고/실행하고 실행 중인 패키지를 모니터링/중지할 수 있습니다. 자세한 내용은 [Azure-SSIS IR 패키지 저장소를 사용하여 SSIS 패키지 관리](https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-package-store)를 참조하세요.
   
이 확인란을 선택하면 **새로 만들기**를 선택하여 Azure-SSIS IR에 여러 패키지 저장소를 추가할 수 있습니다. 반대로, 패키지 저장소 하나를 여러 Azure SSIS IR이 공유할 수 있습니다.

![MSDB/파일 시스템/Azure Files의 배포 설정](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

**패키지 저장소 추가** 창에서 다음 단계를 완료합니다.
   
   1. **패키지 저장소 이름**으로 패키지 저장소의 이름을 입력합니다. 

   1. **패키지 저장소 연결된 서비스**로는 패키지가 배포되는 파일 시스템/Azure Files/Azure SQL Managed Instance 대한 액세스 정보를 저장하는 기존의 연결된 서비스를 선택하거나, **새로 만들기**를 선택하여 새로 만듭니다. **새 연결된 서비스** 창에서 다음 단계를 완료합니다. 

      ![연결된 서비스의 배포 설정](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. **이름**에는 연결된 서비스의 이름을 입력합니다. 
         
      1. **설명**에는 연결된 서비스에 대한 설명을 입력합니다. 
         
      1. **형식**으로는 **Azure File Storage**, **Azure SQL Managed Instance** 또는 **파일 시스템**을 선택합니다.

      1. 항상 Azure-SSIS IR를 사용하여 패키지 저장소에 대한 액세스 정보를 가져오기 때문에 **통합 런타임을 통해 연결**을 무시해도 됩니다.

      1. **Azure File Storage**를 선택하는 경우 다음 단계를 완료합니다. 

         1. **계정 선택 방법**으로는 **Azure 구독에서 선택** 또는 **수동으로 입력**을 선택합니다.
         
         1. **Azure 구독에서 선택**을 선택하는 경우 관련된 **Azure 구독**, **스토리지 계정 이름** 및 **파일 공유**를 선택합니다.
            
         1. **수동으로 입력**을 선택하는 경우 **호스트**로 `\\<storage account name>.file.core.windows.net\<file share name>`을 입력하고, **사용자 이름**으로 `Azure\<storage account name>`을 입력하고, **암호**로 `<storage account key>`를 입력하거나 비밀로 저장된 **Azure Key Vault**를 선택합니다.

      1. **Azure SQL Managed Instance**를 선택하는 경우 다음 단계를 완료합니다. 

         1. **연결 문자열**을 선택하여 수동으로 입력하거나 비밀로 저장된 **Azure Key Vault**를 선택합니다.
         
         1. **연결 문자열**을 선택하는 경우 다음 단계를 완료합니다. 

            1. **정규화된 도메인 이름**으로 Azure SQL Managed Instance의 프라이빗 엔드포인트인 `<server name>.<dns prefix>.database.windows.net` 또는 퍼블릭 엔드포인트인 `<server name>.public.<dns prefix>.database.windows.net,3342`를 입력합니다. 프라이빗 엔드포인트를 입력하면 ADF UI가 연결할 수 없기 때문에 **연결 테스트**를 사용할 수 없습니다.

            1. **데이터베이스 이름**으로 `msdb`를 입력합니다.
               
            1. **인증 형식**으로 **SQL 인증**, **관리 ID** 또는 **서비스 주체**를 선택합니다.

            1. **SQL 인증**을 선택하는 경우 관련 **사용자 이름** 및 **암호**를 입력하거나 비밀로 저장된 **Azure Key Vault**를 선택합니다.

            1. **관리 ID**를 선택하는 경우 Azure SQL Managed Instance에 대한 액세스 권한을 ADF 관리 ID에 부여합니다.

            1. **서비스 주체**를 선택하는 경우 관련 **서비스 주체 ID** 및 **서비스 주체 키**를 입력하거나 비밀로 저장된 **Azure Key Vault**를 선택합니다.

      1. **파일 시스템**을 선택하는 경우 패키지가 배포되는 폴더의 UNC 경로를 **호스트**로 입력하고, 관련 **사용자 이름** 및 **암호**을 입력하거나, 비밀로 저장된 **Azure Key Vault**를 선택합니다.

      1. **연결 테스트**를 선택하고(해당하는 경우), 테스트가 성공하면 **만들기**를 선택합니다.

   1. 추가된 패키지 저장소가 **배포 설정** 페이지에 표시됩니다. 저장소를 제거하려면 해당 확인란을 선택한 다음, **삭제**를 선택합니다.

**연결 테스트**를 선택하고(해당하는 경우), 테스트가 성공하면 **다음**을 선택합니다.

### <a name="advanced-settings-page"></a>고급 설정 페이지

**통합 런타임 설치** 창의 **고급 설정** 페이지에서 다음 단계를 완료합니다. 

   ![고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **노드당 최대 병렬 실행 수**에 대해 통합 런타임 클러스터에서 노드당 동시에 실행할 최대 패키지 수를 선택합니다. 지원되는 패키지 수만 표시됩니다. 둘 이상의 코어를 사용하여 계산 집약적이거나 메모리 집약적인 하나의 대형 패키지를 실행하려면 낮은 숫자를 선택합니다. 하나의 코어에서 하나 이상의 소형 패키지를 실행하려면 높은 숫자를 선택합니다. 

   1. **Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations**(추가 시스템 구성/구성 요소 설치를 통해 Azure-SSIS Integration Runtime 사용자 지정) 확인란을 선택하여 Azure-SSIS IR에 표준/빠른 사용자 지정 설치를 추가할지 여부를 선택합니다. 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)를 참조하세요.
   
   1. **조인할 Azure-SSIS Integration Runtime에 대한 VNet을 선택하고 ADF가 특정 네트워크 리소스를 만들고 선택적으로 자체 고정 공용 IP 주소를 가져오도록 허용** 확인란을 선택하여 Azure-SSIS IR을 가상 네트워크에 조인할지 여부를 선택합니다.

      IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database 서버를 사용하거나 SSISDB를 호스팅하기 위해 프라이빗 엔드포인트가 있는 관리형 인스턴스를 사용하는 경우 또는 자체 호스팅 IR을 구성하지 않고 온-프레미스 데이터에 액세스해야 하는 경우 선택합니다. 자세한 내용은 [가상 네트워크에서 Azure-SSIS IR 만들기](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)를 참조하세요. 
   
   1. **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime**(자체 호스팅 통합 런타임을 Azure-SSIS Integration Runtime의 프록시로 설정) 확인란을 선택하여 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성할지 여부를 선택합니다. 자세한 내용은 [자체 호스팅 IR을 프록시로 설정](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)을 참조하세요.   

   1. **계속**을 선택합니다. 

**통합 런타임 설치** 창의 **요약** 페이지에서 모든 프로비저닝 설정을 검토하고, 추천 설명서 링크에 책갈피를 설정하고, **마침**을 선택하여 통합 런타임 만들기를 시작합니다. 

   > [!NOTE]
   > 이 프로세스는 사용자 지정 설치 시간을 제외하고 5분 이내에 완료됩니다.
   >
   > SSISDB를 사용하는 경우 Data Factory 서비스에서 데이터베이스 서버에 연결하여 SSISDB를 준비합니다. 
   > 
   > Azure-SSIS IR을 프로비저닝하는 경우 Access 재배포 가능 패키지 및 Azure Feature Pack for SSIS도 설치됩니다. 이러한 구성 요소는 기본 제공 구성 요소에서 이미 지원하는 데이터 원본 외에도 Excel 파일, Access 파일 및 다양한 Azure 데이터 원본에 대한 연결을 제공합니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime)를 참조하세요. 설치할 수 있는 추가 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR 사용자 지정 설치](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)를 참조하세요.

### <a name="connections-pane"></a>[연결] 창

**관리** 허브의 **연결** 창에서 **통합 런타임** 페이지로 전환하여 **새로 고침**을 선택합니다. 

   ![[연결] 창](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR의 이름을 선택하여 편집/재구성할 수 있습니다. 또한 관련된 단추를 선택하여 Azure-SSIS IR을 모니터링/시작/중지/삭제하고, SSIS 패키지 실행 작업을 사용하여 Azure-SSIS IR에서 실행할 ADF 파이프라인을 자동으로 생성하고, Azure-SSIS IR의 JSON 코드/페이로드를 볼 수 있습니다.  Azure-SSIS IR이 중지된 상태에서만 편집/삭제할 수 있습니다.

## <a name="deploy-ssis-packages"></a>SSIS 패키지 배포

SSISDB를 사용하는 경우 Azure 지원 SSDT 또는 SSMS 도구를 사용하여 SSISDB에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 이러한 도구는 다음과 같은 서버 엔드포인트를 통해 데이터베이스 서버에 연결됩니다. 

- Azure SQL Database 서버의 경우 서버 엔드포인트 형식은 `<server name>.database.windows.net`입니다.
- 프라이빗 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.<dns prefix>.database.windows.net`입니다.
- 퍼블릭 엔드포인트가 있는 관리형 인스턴스의 경우 서버 엔드포인트 형식은 `<server name>.public.<dns prefix>.database.windows.net,3342`입니다. 

SSISDB를 사용하지 않는 경우 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 및 [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec) 명령줄 유틸리티를 사용하여 Azure SQL Managed Instance가 호스트하는 파일 시스템, Azure Files 또는 MSDB에 패키지를 배포하고 Azure-SSIS IR에서 실행할 수 있습니다. 

자세한 내용은 [SSIS 프로젝트/패키지 배포](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-ver15)를 참조하세요.

또한 두 경우 모두 Data Factory 파이프라인에서 SSIS 패키지 실행 활동을 사용하여 배포된 패키지를 Azure-SSIS IR에서 실행할 수도 있습니다. 자세한 내용은 [SSIS 패키지 실행을 Data Factory 첫 번째 클래스 활동으로 호출](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조하세요.

다음 SSIS 설명서도 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure에서 SSISDB에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>다음 단계

Azure-SSIS 통합 런타임을 사용자 지정하는 방법에 대해 알아보려면 다음 문서로 이동하세요. 

> [!div class="nextstepaction"]
> [Azure-SSIS IR 사용자 지정](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)