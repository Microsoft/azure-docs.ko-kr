---
title: SSDT에서 SSIS 패키지 실행
description: SSDT에서 Azure에서 SSIS 패키지를 실행 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 5f21623af9b89bbb020063dfb72f7b60e65a6ebe
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927716"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT에서 Azure에서 SSIS 패키지 실행
이 문서에서는 SQL Server Data Tools (SSDT)에서 Azure 사용 가능 SQL Server Integration Services (SSIS) 프로젝트의 기능에 대해 설명 합니다 .이 기능을 사용 하면 ADF (Azure Data Factory Azure-SSIS Integration Runtime)에서 패키지를 실행할 수 있습니다.  이 기능을 사용 하 여 기존 SSIS 패키지를 테스트 하기 전에 기존 SSIS 패키지를 테스트 하 여 Azure로 이동 & 하거나 Azure에서 실행할 새 SSIS 패키지를 개발할 수 있습니다.

이 기능을 사용 하 여 새 Azure-SSIS IR을 만들거나 SSIS 프로젝트에 기존 항목을 연결한 다음 패키지를 실행할 수 있습니다.  프로젝트 배포 모델의 SSISDB (SSIS 카탈로그)에 배포할 패키지를 실행 하 고 패키지 배포 모델에서 파일 시스템/파일 공유/Azure Files에 배포할 패키지를 지원 합니다. 

## <a name="prerequisites"></a>전제 조건
이 기능을 사용 하려면 [여기](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) 에서 Visual STUDIO 용 SSIS 프로젝트 확장을 사용 하 여 최신 SSDT를 다운로드 하 여 설치 하거나 [여기](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)에서 독립 실행형 설치 관리자로 설치 하세요.

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS 프로젝트 사용
SSDT에서 **Integration Services 프로젝트 (Azure 사용 가능)** 템플릿을 사용 하 여 새 azure 사용 가능 SSIS 프로젝트를 만들 수 있습니다.

![새 Azure 사용 가능 SSIS 프로젝트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

또는 SSDT의 솔루션 탐색기 패널에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 여 기존 SSIS 프로젝트를 사용 하도록 설정 하 여 메뉴를 표시 한 다음, **Azure 사용** 메뉴 항목을 선택 하면 됩니다.

![Azure-기존 SSIS 프로젝트 사용](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure-기존 SSIS 프로젝트를 사용 하도록 설정 하려면 대상 서버 버전을 Azure-SSIS IR에서 지원 되는 최신 버전으로 설정 해야 합니다 .이는 현재 **2017 SQL Server**이므로 아직 수행 하지 않은 경우이를 위해 대화 상자 창이 나타납니다.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Azure Data Factory에서 SSIS에 Azure 사용 프로젝트 연결
Azure 사용 프로젝트를 ADF의 SSIS에 연결 하면 패키지를 Azure Files로 업로드 하 고 Azure-SSIS IR에서 실행할 수 있습니다.  이렇게 하려면 다음 단계를 수행 하세요.

1. SSDT의 솔루션 탐색기 패널에서 프로젝트 또는 **연결 된 Azure 리소스** 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 하 고 **Azure Data Factory에서 ssis에 연결** 메뉴 항목을 선택 하 여 **ADF 연결 마법사에서 ssis**를 시작 합니다.

   ![ADF에서 SSIS에 연결](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. **ADF의 SSIS 소개** 페이지에서 소개를 검토 하 고 **다음** 단추를 클릭 하 여 계속 합니다.

   ![ADF의 SSIS 소개](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. **Adf에서 SSIS IR 선택** 페이지에서 기존 ADF를 선택 하 고 패키지를 실행 하려면 Azure-SSIS IR 하 고, 없는 경우 새로 만듭니다.
   - 기존 Azure-SSIS IR을 선택 하려면 먼저 관련 Azure 구독 및 ADF를 선택 합니다.
   - Azure-SSIS IR 없는 기존 ADF를 선택 하는 경우에는 **SSIS IR 만들기** 단추를 클릭 하 여 adf 포털/앱에 새 항목을 만듭니다.
   - ADF가 없는 기존 Azure 구독을 선택 하는 경우 **SSIS Ir 만들기** 단추를 클릭 하 여 **Integration Runtime 만들기 마법사**를 시작 합니다 .이 마법사에서는 사용자를 대신 하 여 자동으로 새 Azure 리소스 그룹, Data Factory 및 SSIS IR을 만들 수 있습니다 .이에 대한 자세한 **접두사-RG/DF/IR-YourCreationTime**.
   
   ![ADF에서 SSIS IR 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. **Azure Storage 선택** 페이지에서 기존 Azure Storage 계정을 선택 하 여 Azure Files 패키지를 업로드 하거나, 없는 경우 새로 만듭니다.
   - 기존 Azure Storage 계정을 선택 하려면 먼저 관련 Azure 구독을 선택 합니다.
   - Azure Storage 계정이 없는 Azure-SSIS IR와 동일한 Azure 구독을 선택 하는 경우 **Azure Storage 만들기** 단추를 클릭 하 여 Azure-SSIS IR 이름과 만든 날짜의 접두사를 결합 하 여 Azure-SSIS IR와 동일한 위치에 새 항목을 자동으로 만듭니다.
   - Azure Storage 계정이 없는 다른 Azure 구독을 선택 하는 경우 **Azure Storage 만들기** 단추를 클릭 하 여 Azure Portal에 새 구독을 만듭니다.
   
   ![Azure Storage 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. **연결 단추를** 클릭 하 여 연결을 완료 합니다.  SSDT의 솔루션 탐색기 패널에서 **연결 된 Azure 리소스** 노드 아래에 선택한 Azure-SSIS IR 및 Azure Storage 계정을 표시 합니다.  또한 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음, ADF 포털/앱으로 이동 하 여이 작업을 수행 하는 **시작 메뉴 항목을 선택** 하 여 해당 노드를 마우스 오른쪽 단추로 클릭 하 여 관리할 수 있는 Azure-SSIS IR의 상태를 새로 고칩니다.

## <a name="execute-ssis-packages-in-azure"></a>Azure에서 SSIS 패키지 실행
### <a name="starting-package-executions"></a>패키지 실행 시작
ADF에서 SSIS에 프로젝트를 연결한 후 Azure-SSIS IR에서 패키지를 실행할 수 있습니다.  패키지 실행을 시작 하는 두 가지 옵션이 있습니다.
-  SSDT 도구 모음에서 **시작** 단추를 클릭 하 여 메뉴를 드롭다운 하 고 **Azure에서 실행** 메뉴 항목을 선택 합니다. 

   ![Azure에서 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  SSDT의 솔루션 탐색기 패널에서 패키지 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 팝 하 고 **Azure에서 패키지 실행** 메뉴 항목을 선택 합니다.

   ![Azure에서 패키지 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Azure에서 패키지를 실행 하려면 실행 중인 Azure-SSIS IR 있어야 합니다. 따라서 Azure-SSIS IR 중지 된 경우에는 대화 상자 창에서 해당 작업을 시작 합니다.  사용자 지정 설치 시간을 제외 하 고이 프로세스는 5 분 이내에 완료 되어야 하지만 Azure-SSIS IR 가상 네트워크에 가입 하는 데 약 20-30 분이 걸릴 수 있습니다.  Azure에서 패키지를 실행 한 후에는 SSDT의 솔루션 탐색기 패널에서 해당 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음,이 작업을 수행 하기 위해 ADF 포털/앱으로 이동 하는 시작 메뉴 항목을 선택 하 여 Azure-SSIS IR 실행 비용을 관리 **하는 작업** 을 중지할 수 있습니다.

### <a name="checking-package-execution-logs"></a>패키지 실행 로그를 확인 하는 중
패키지 실행을 시작할 때 SSDT의 진행률 창에 해당 로그를 포맷 하 고 표시 합니다.  장기 실행 패키지의 경우 해당 로그를 분 단위로 정기적으로 업데이트 합니다.  SSDT 도구 모음에서 **중지** 단추를 클릭 하 여 패키지 실행을 중지할 수 있습니다. 그러면 즉시 취소 됩니다.  또한 UNC (범용 명명 규칙) 경로: `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`에서 로그 원시 데이터를 일시적으로 찾을 수 있지만 1 일 후에 정리 합니다.

### <a name="switching-package-protection-level"></a>패키지 보호 수준 전환
Azure에서 SSIS 패키지를 실행 하는 것은 **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 보호 수준을 지원 하지 않습니다.  따라서 패키지가 이러한 패키지를 사용 하 여 구성 된 경우 Azure-SSIS IR에서 실행 하기 위해 패키지를 Azure Files에 업로드할 때 임의로 생성 된 암호를 사용 하 여 **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**로 각각 일시적으로 전환 합니다.

> [!NOTE]
> **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 보호 수준을 사용 하 여 구성 된 다른 패키지를 참조 하는 패키지 실행 태스크가 패키지에 포함 되어 있는 경우 패키지를 실행 하기 전에 각각/**EncryptSensitiveWithPassword** **EncryptAllWithPassword**를 사용 하도록 다른 패키지를 수동으로 다시 구성 해야 합니다.

**EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 보호 수준을 사용 하 여 패키지를 이미 구성한 경우에는 해당 패키지를 변경 되지 않은 상태로 유지 하지만 패키지를 Azure Files에 업로드 하면 Azure-SSIS IR에서 실행 되도록 패키지를 계속 해 서 임의로 생성 합니다.

### <a name="using-package-configuration-file"></a>패키지 구성 파일 사용
패키지 배포 모델의 패키지 구성 파일을 사용 하 여 런타임에 변수 값을 변경 하는 경우 Azure-SSIS IR에서 실행할 수 있도록 패키지를 포함 하는 파일을 자동으로 Azure Files에 업로드 합니다.

### <a name="using-execute-package-task"></a>패키지 실행 태스크 사용
패키지에 로컬 파일 시스템에 저장 된 다른 패키지를 참조 하는 패키지 실행 태스크가 포함 되어 있는 경우 다음과 같은 추가 설정 작업을 수행 해야 합니다.

1. 프로젝트에 연결 된 동일한 Azure Storage 계정으로 Azure Files에 다른 패키지를 업로드 하 고 새 UNC 경로 (예:)를 가져옵니다 `\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 패키지 실행 태스크의 파일 연결 관리자에 있는 다른 패키지의 파일 경로를 새 UNC 경로로 바꿉니다.
   - SSDT를 실행 하는 컴퓨터에서 새 UNC 경로에 액세스할 수 없는 경우 파일 연결 관리자의 속성 패널에서 파일 경로를 변경할 수 있습니다.
   - 또는 런타임에 파일 경로에 대한 변수를 사용 하 여 올바른 값을 할당할 수 있습니다.

패키지에 동일한 프로젝트의 다른 패키지를 참조 하는 패키지 실행 태스크가 포함 되어 있으면 추가 설정이 필요 하지 않습니다.

## <a name="next-steps"></a>다음 단계
SSDT에서 Azure의 패키지를 실행 하는 것이 만족 스 러 우면 ADF 파이프라인에서 ssis 패키지 실행 작업으로 배포 하 고 실행할 수 있습니다. [adf 파이프라인에서](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)ssis 패키지를 실행 하려면 Ssis 패키지 실행을 참조 하세요.
