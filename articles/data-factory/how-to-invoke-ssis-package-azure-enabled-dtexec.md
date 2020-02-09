---
title: Azure 지원 dtexec 유틸리티를 사용 하 여 SQL Server Integration Services (SSIS) 패키지 실행
description: Azure 지원 dtexec 유틸리티를 사용 하 여 SSIS (SQL Server Integration Services) 패키지를 실행 하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: a5540eea91937319a6ac947b50698ccaa8b25847
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931696"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Azure 지원 dtexec 유틸리티를 사용 하 여 SQL Server Integration Services 패키지 실행
이 문서에서는 Azure 지원 dtexec (AzureDTExec) 명령 프롬프트 유틸리티에 대해 설명 합니다. Azure Data Factory에서 IR (Azure-SSIS Integration Runtime)에 SQL Server Integration Services (SSIS) 패키지를 실행 하는 데 사용 됩니다.

기존 dtexec 유틸리티는 SQL Server와 함께 제공 됩니다. 자세한 내용은 [dtexec utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)를 참조 하세요. 온-프레미스에서 SSIS 패키지를 실행 하기 위해 ActiveBatch 및 Control-M과 같은 타사 orchestrator 또는 스케줄러에 의해 호출 되는 경우가 많습니다. 

최신 AzureDTExec 유틸리티는 SSMS (SQL Server Management Studio) 도구와 함께 제공 됩니다. Azure에서 SSIS 패키지를 실행 하기 위해 타사 orchestrator 또는 스케줄러에 의해 호출 될 수도 있습니다. SSIS 패키지를 클라우드로 전환 하거나 마이그레이션하는 것을 용이 하 게 합니다. 마이그레이션 후 일상 작업에서 타사 orchestrator 또는 스케줄러를 계속 사용 하려는 경우 이제 dtexec 대신 AzureDTExec를 호출할 수 있습니다.

AzureDTExec는 Data Factory 파이프라인에서 SSIS 패키지 실행 작업으로 패키지를 실행 합니다. 자세한 내용은 [Azure Data Factory 작업으로 SSIS 패키지 실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조 하세요. 

SSMS를 통해 AzureDTExec를 구성 하 여 데이터 팩터리에 파이프라인을 생성 하는 Azure Active Directory (Azure AD) 응용 프로그램을 사용할 수 있습니다. 또한 패키지를 저장 하는 파일 시스템, 파일 공유 또는 Azure Files에 액세스 하도록 구성할 수 있습니다. 사용자가 호출 옵션에 대해 지정한 값에 따라 AzureDTExec는 SSIS 패키지 실행 작업을 사용 하 여 고유한 Data Factory 파이프라인을 생성 하 고 실행 합니다. 해당 옵션에 대해 동일한 값을 사용 하 여 AzureDTExec를 호출 하면 기존 파이프라인이 다시 실행 됩니다.

## <a name="prerequisites"></a>전제 조건
AzureDTExec를 사용 하려면 최신 버전의 SSMS (버전 18.3 이상)를 다운로드 하 여 설치 합니다. [이 웹 사이트](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)에서 다운로드합니다.

## <a name="configure-the-azuredtexec-utility"></a>AzureDTExec 유틸리티 구성
로컬 컴퓨터에 SSMS를 설치 하면 AzureDTExec도 설치 됩니다. 설정을 구성 하려면 **관리자 권한으로 실행** 옵션을 사용 하 여 SSMS를 시작 합니다. 그런 다음 azure **로 마이그레이션을** > **도구** 를 선택 하 > **azure 지원 DTExec를 구성**합니다.

![Azure 사용 dtexec 메뉴 구성](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

이 작업을 수행 하면 *Azuredtexec. settings* 파일에 쓰기 위해 관리자 권한으로 열어야 하는 **Azuredtexecconfig** 창이 열립니다. SSMS를 관리자로 실행 하지 않은 경우 UAC (사용자 계정 컨트롤) 창이 열립니다. 관리자 암호를 입력 하 여 권한을 상승 시킵니다.

![Azure 사용 dtexec 설정 구성](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

**Azuredtexecconfig** 창에서 다음과 같이 구성 설정을 입력 합니다.

- **ApplicationId**: 데이터 팩터리에 파이프라인을 생성 하기 위한 올바른 권한으로 만든 Azure AD 앱의 고유 식별자를 입력 합니다. 자세한 내용은 [Azure Portal를 통해 AZURE AD 앱 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)를 참조 하세요.
- **Authenticationkey**: Azure AD 앱에 대한 인증 키를 입력 합니다.
- **TenantId**: azure ad 앱이 생성 되는 azure ad 테넌트의 고유 식별자를 입력 합니다.
- **SubscriptionId**: 데이터 팩터리가 만들어진 Azure 구독의 고유 식별자를 입력 합니다.
- **ResourceGroup**: 데이터 팩터리가 생성 된 Azure 리소스 그룹의 이름을 입력 합니다.
- **DataFactory**: azuredtexec를 호출할 때 제공 되는 옵션의 값을 기반으로 하 여 SSIS 패키지 실행 작업을 사용 하는 고유 파이프라인이 생성 되는 데이터 팩터리의 이름을 입력 합니다.
- **Irname**: azuredtexec를 호출할 때 UNC (범용 명명 규칙) 경로에 지정 된 패키지가 실행 되는 데이터 팩터리에 Azure-SSIS IR 이름을 입력 합니다.
- **Packageaccessdomain**: azuredtexec를 호출할 때 지정 된 UNC 경로에서 패키지에 액세스 하려면 도메인 자격 증명을 입력 합니다.
- **Packageaccessusername**: azuredtexec를 호출할 때 지정 된 UNC 경로에서 패키지에 액세스 하는 사용자 이름 자격 증명을 입력 합니다.
- **PackageAccessPassword**: azuredtexec를 호출할 때 지정 된 UNC 경로에서 패키지에 액세스 하려면 암호 자격 증명을 입력 합니다.
- **LogPath**: 로그 폴더의 UNC 경로를 입력 합니다 .이 경로에는 Azure-SSIS IR에서 패키지 실행의 로그 파일이 기록 됩니다.
- **LogLevel**: Azure-SSIS IR에서 패키지 실행에 대한 미리 정의 된 **null**, **기본**, **자세한 정보**또는 **성능** 옵션에서 선택한 로깅 범위를 입력 합니다.
- **Logaccessdomain**: 로그 파일을 작성할 때 ( **LogPath** 을 지정 하 고 **LogLevel** 이 **null**이 아닌 경우 필요 함) 로그 폴더에 액세스할 도메인 자격 증명을 입력 합니다.
- **Logaccessusername**: 로그 파일을 작성할 때 사용자 이름 자격 증명을 입력 하 여 로그 폴더에 액세스 합니다. 로그 파일은 **LogPath** 을 지정 하 고 **LogLevel** 은 **null**이 아닌 경우에 필요 합니다.
- **LogAccessPassword**: 로그 파일을 작성할 때 암호 자격 증명을 입력 하 여 해당 UNC 경로에 액세스 합니다 .이는 **LogPath** 가 지정 되 고 **LogLevel** 이 **null**이 아닌 경우에 필요 합니다.
- **PipelineNameHashStrLen**: azuredtexec를 호출할 때 제공 하는 옵션 값에서 생성할 해시 문자열의 길이를 입력 합니다. 문자열은 Azure-SSIS IR에서 패키지를 실행 하는 Data Factory 파이프라인에 대한 고유 이름을 구성 하는 데 사용 됩니다. 일반적으로 32 문자 길이가 충분 합니다.

패키지 및 로그 파일을 온-프레미스 파일 시스템 또는 파일 공유에 저장 하려면 패키지를 가져오고 로그 파일을 쓸 수 있도록 온-프레미스 네트워크에 연결 된 가상 네트워크에 Azure-SSIS IR를 조인 합니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 가입](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)을 참조 하세요.

일반 텍스트로 *Azuredtexec. 설정* 파일에 기록 된 중요 한 값이 표시 되지 않도록 하려면 Base64 인코딩의 문자열로 인코딩합니다. AzureDTExec를 호출 하면 모든 Base64 인코딩 문자열이 원래 값으로 다시 디코딩됩니다. 액세스할 수 있는 계정을 제한 하 여 *Azuredtexec 설정* 파일의 보안을 강화할 수 있습니다.

## <a name="invoke-the-azuredtexec-utility"></a>AzureDTExec 유틸리티 호출
명령줄 프롬프트에서 AzureDTExec를 호출 하 고 사용 사례 시나리오에서 특정 옵션에 대한 관련 값을 제공할 수 있습니다.

유틸리티는 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`에 설치 됩니다. ' PATH ' 환경 변수에 해당 경로를 추가 하 여 어디에서 나 호출할 수 있습니다.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

AzureDTExec를 호출 하면 dtexec 호출과 비슷한 옵션이 제공 됩니다. 자세한 내용은 [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)를 참조하세요. 현재 지원 되는 옵션은 다음과 같습니다.

- **/F [파일 이름]** : 파일 시스템, 파일 공유 또는 Azure Files에 저장 된 패키지를 로드 합니다. 이 옵션의 값으로 파일 시스템, 파일 공유 또는 package.dtsx 확장명을 사용 하 여 Azure Files 패키지 파일에 대한 UNC 경로를 지정할 수 있습니다. 지정 된 UNC 경로에 공백이 있으면 전체 경로 주위에 따옴표를 추가 합니다.
- **/Arys [igFile]** : 값을 추출할 구성 파일을 지정 합니다. 이 옵션을 사용 하면 디자인 타임에 지정 된 것과 다른 패키지에 대한 런타임 구성을 설정할 수 있습니다. XML 구성 파일에 다른 설정을 저장 한 다음 패키지를 실행 하기 전에 로드할 수 있습니다. 자세한 내용은 [SSIS 패키지 구성](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)을 참조 하세요. 이 옵션의 값을 지정 하려면 파일 시스템, 파일 공유 또는 Ssistutorial.dtsconfig 확장을 사용 하 여 Azure Files 구성 파일에 대한 UNC 경로를 사용 합니다. 지정 된 UNC 경로에 공백이 있으면 전체 경로 주위에 따옴표를 추가 합니다.
- **/Conn [사용자 이름]** : 패키지의 기존 연결 관리자에 대한 연결 문자열을 지정 합니다. 이 옵션을 사용 하면 디자인 타임에 지정 된 연결 관리자와 다른 패키지의 기존 연결 관리자에 대한 런타임 연결 문자열을 설정할 수 있습니다. 이 옵션의 값을 다음과 같이 지정 합니다. `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.
- **/Set**: 패키지에서 매개 변수, 변수, 속성, 컨테이너, 로그 공급자, Foreach 열거자 또는 연결의 구성을 재정의 합니다. 이 옵션은 여러 번 지정할 수 있습니다. 이 옵션의 값을 다음과 같이 지정 합니다. `property_path;value`. 예를 들어 `\package.variables[counter].Value;1`은 `counter` 변수 값을 1로 재정의 합니다. 패키지 **구성** 마법사를 사용 하 여 패키지에서 값을 재정의 하려는 항목에 대한 `property_path` 값을 찾고 복사 하 고 붙여넣을 수 있습니다. 자세한 내용은 [패키지 구성 마법사](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)를 참조 하세요.
- **/De [** /**EncryptAllWithPassword** ]: **EncryptSensitiveWithPassword** 보호 수준으로 구성 된 패키지에 대한 암호 해독 암호를 설정 합니다.

> [!NOTE]
> 해당 옵션에 대한 새 값을 사용 하 여 AzureDTExec를 호출 하면 옵션 **/De [c)]** 를 제외 하 고 새 파이프라인이 생성 됩니다.

## <a name="next-steps"></a>다음 단계

SSIS 패키지 실행 작업이 포함 된 고유 파이프라인이 생성 되 고 AzureDTExec를 호출한 후에 실행 되 면 Data Factory 포털에서 모니터링할 수 있습니다. 자세한 내용은 [Data Factory 작업으로 SSIS 패키지 실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조 하세요.

> [!WARNING]
> 생성 된 파이프라인은 AzureDTExec 에서만 사용 해야 합니다. 해당 속성 또는 매개 변수는 나중에 변경 될 수 있으므로 다른 용도로 수정 하거나 다시 사용 하지 마세요. 수정은 AzureDTExec를 중단할 수 있습니다. 이 문제가 발생 하면 파이프라인을 삭제 합니다. AzureDTExec는 다음에 호출 될 때 새 파이프라인을 생성 합니다.
