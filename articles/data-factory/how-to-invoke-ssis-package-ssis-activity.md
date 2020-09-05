---
title: SSIS 패키지 실행 작업을 사용 하 여 SSIS 패키지 실행
description: 이 문서에서는 SSIS 패키지 실행 작업을 사용 하 여 Azure Data Factory 파이프라인에서 SSIS (SQL Server Integration Services) 패키지를 실행 하는 방법을 설명 합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 901693c512ddfcf5d3c4dafaec71b1606b5dc5f1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077851"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory에서 SSIS 패키지 실행 작업을 사용하여 SSIS 패키지 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 SSIS 패키지 실행 작업을 사용 하 여 Azure Data Factory 파이프라인에서 SSIS (SQL Server Integration Services) 패키지를 실행 하는 방법을 설명 합니다. 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[자습서: 프로 비전 Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)의 단계별 지침을 수행 하 여 아직 없는 경우 AZURE SSIS IR (통합 런타임)을 만듭니다.

## <a name="run-a-package-in-the-azure-portal"></a>Azure Portal에서 패키지 실행
이 섹션에서는 Data Factory UI (사용자 인터페이스) 또는 앱을 사용 하 여 SSIS 패키지를 실행 하는 SSIS 패키지 실행 작업으로 Data Factory 파이프라인을 만듭니다.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기
이 단계에서는 Data Factory UI 또는 응용 프로그램을 사용 하 여 파이프라인을 만듭니다. 파이프라인에 SSIS 패키지 실행 작업을 추가하고 SSIS 패키지를 실행하도록 구성합니다. 

1. Azure Portal Data Factory 개요 또는 홈 페이지에서 **작성자 & 모니터** 타일을 선택 하 여 별도의 탭에서 Data Factory UI 또는 앱을 시작 합니다. 

   ![Data Factory 홈페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **시작** 페이지에서 **파이프라인 만들기**를 선택합니다. 

   ![시작 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. **작업** 도구 상자에서 **일반**을 확장 합니다. 그런 다음 **SSIS 패키지 실행** 작업을 파이프라인 디자이너 화면으로 끌어 옵니다. 

   ![SSIS 패키지 실행 작업을 디자이너 화면으로 끌기](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   SSIS 패키지 실행 작업 개체를 선택 하 여 **일반**, **설정**, **Ssis 매개 변수**, **연결 관리자**및 **속성 재정의** 탭을 구성 합니다.

#### <a name="general-tab"></a>일반 탭

SSIS 패키지 실행 작업의 **일반** 탭에서 다음 단계를 완료 합니다.

![일반 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. **이름**에 SSIS 패키지 실행 작업의 이름을 입력 합니다.

   1. **설명**으로 SSIS 패키지 실행 작업에 대 한 설명을 입력 합니다.

   1. **시간 제한**에서 SSIS 패키지 실행 작업을 실행할 수 있는 최대 시간을 입력 합니다. 기본값은 7 일, 형식은 D. HH: MM: SS입니다.

   1. **다시 시도**하려면 SSIS 패키지 실행 작업에 대 한 최대 재시도 횟수를 입력 합니다.

   1. **다시 시도 간격**에 대해 SSIS 패키지 실행 작업의 각 재시도 사이의 시간 (초)을 입력 합니다. 기본값은 30 초입니다.

   1. **안전한 출력** 확인란을 선택 하 여 SSIS 패키지 실행 작업의 출력을 로깅에서 제외할지 여부를 선택 합니다.

   1. **보안 입력** 확인란을 선택 하 여 SSIS 패키지 실행 작업의 입력을 로깅에서 제외할지 여부를 선택할 수 있습니다.

#### <a name="settings-tab"></a>설정 탭

SSIS 패키지 실행 작업의 **설정** 탭에서 다음 단계를 완료 합니다.

![설정 탭의 속성 설정 - 자동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. **Azure-SSIS IR**에 대해 지정 된 Azure-SSIS IR를 선택 하 여 SSIS 패키지 실행 작업을 실행 합니다.

   1. **설명**으로 SSIS 패키지 실행 작업에 대 한 설명을 입력 합니다.

   1. Windows **인증** 확인란을 선택 하 여 SQL server/파일 공유 온-프레미스 또는 Azure Files와 같은 데이터 저장소에 액세스 하는 데 windows 인증을 사용할지 여부를 선택 합니다.
   
      이 확인란을 선택 하는 경우 **도메인**, **사용자 이름**및 **암호** 상자에 패키지 실행 자격 증명의 값을 입력 합니다. 예를 들어 Azure Files에 액세스 하려면 도메인이이 고 `Azure` 사용자 이름은 이며 `<storage account name>` 암호는 `<storage account key>` 입니다.

      또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택 합니다. 기존 key vault 연결 된 서비스를 선택 하거나 편집 하거나 새로 만듭니다. 그런 다음 값에 대 한 암호 이름 및 버전을 선택 합니다. Key vault 연결 된 서비스를 만들거나 편집할 때 기존 주요 자격 증명 모음을 선택 하거나 편집 하거나 새 자격 증명 모음을 만들 수 있습니다. 아직 수행 하지 않은 경우 키 자격 증명 모음에 대 한 Data Factory 관리 id 액세스 권한을 부여 해야 합니다. 다음과 같은 형식으로 암호를 직접 입력할 수도 있습니다 `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. **32 비트 런타임** 확인란을 선택 하 여 패키지에서 32 비트 런타임을 실행 해야 하는지 여부를 선택 합니다.

   1. **패키지 위치**에서 **SSISDB**, **파일 시스템 (패키지)**, **파일 시스템 (프로젝트)**, **포함 된 패키지**또는 **패키지 저장소**를 선택 합니다. 

##### <a name="package-location-ssisdb"></a>패키지 위치: SSISDB

**Ssisdb** 는 Azure-SSIS IR Azure SQL Database server/Managed Instance에서 호스트 하는 SSISDB (SSIS 카탈로그)를 사용 하 여 프로 비전 된 경우 자동으로 선택 되며, 직접 선택할 수도 있습니다. 선택 된 경우 다음 단계를 완료 합니다.

   1. Azure-SSIS IR 실행 중이 고 **수동 항목** 확인란의 선택을 취소 한 경우 SSISDB에서 기존 폴더, 프로젝트, 패키지 및 환경을 찾아 선택 합니다. 새로 추가 된 폴더, 프로젝트, 패키지 또는 환경을 검색 하 고 선택할 수 있도록 SSISDB에서 새로 추가 **를 선택 합니다** . 패키지 실행을 위한 환경을 탐색 하 고 선택 하려면 해당 환경을 SSISDB 아래의 동일한 폴더에서 참조로 추가 하도록 프로젝트를 미리 구성 해야 합니다. 자세한 내용은 [SSIS 환경 만들기 및 매핑](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)을 참조 하세요.

   1. **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 된 로깅 이름을 대신 입력 하려면 **사용자 지정** 확인란을 선택 합니다. 

   1. Azure-SSIS IR 실행 되 고 있지 않거나 **수동 항목** 확인란이 선택 되어 있는 경우 SSISDB의 패키지 및 환경 경로를 및 형식으로 직접 입력 `<folder name>/<project name>/<package name>.dtsx` `<folder name>/<environment name>` 합니다.

      ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>패키지 위치: 파일 시스템 (패키지)

패키지 위치가 SSISDB 없이 프로 비전 되었거나 직접 선택할 수 있는 Azure-SSIS IR 경우 패키지 위치가 자동으로 선택 되므로 **파일 시스템 (패키지)** 이 자동으로 선택 됩니다. 선택 된 경우 다음 단계를 완료 합니다.

![설정 탭-파일 시스템 (패키지)의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. 패키지 경로 상자에서 패키지 파일 ()에 대 한 UNC (범용 명명 규칙) 경로를 제공 하 여 실행할 패키지를 지정 `.dtsx` 합니다. **Package path** **파일 저장소 찾아보기** 를 선택 하거나 경로를 수동으로 입력 하 여 패키지를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 패키지를 저장 하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` 입니다. 
   
   1. 패키지를 별도의 파일에 구성 하는 경우 구성 경로 상자에서 구성 파일에 대 한 UNC 경로 (포함)도 제공 해야 `.dtsConfig` 합니다. **Configuration path** 찾아보기 **파일 저장소** 를 선택 하거나 경로를 수동으로 입력 하 여 구성을 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 구성을 저장 하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` 입니다.

   1. 패키지 및 구성 파일에 액세스 하는 데 사용할 자격 증명을 지정 합니다. 이전에 패키지 실행 자격 증명 ( **Windows 인증**의 경우)에 대 한 값을 입력 한 경우 **패키지 실행 자격 증명과 동일** 확인란을 선택 하 여 다시 사용할 수 있습니다. 그렇지 않으면 **도메인**, **사용자 이름**및 **암호** 상자에 패키지 액세스 자격 증명 값을 입력 합니다. 예를 들어 Azure Files에 패키지 및 구성을 저장 하는 경우 도메인은이 고 `Azure` 사용자 이름은 이며 `<storage account name>` 암호는 `<storage account key>` 입니다. 

      또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택 합니다. 기존 key vault 연결 된 서비스를 선택 하거나 편집 하거나 새로 만듭니다. 그런 다음 값에 대 한 암호 이름 및 버전을 선택 합니다. Key vault 연결 된 서비스를 만들거나 편집할 때 기존 주요 자격 증명 모음을 선택 하거나 편집 하거나 새 자격 증명 모음을 만들 수 있습니다. 아직 수행 하지 않은 경우 키 자격 증명 모음에 대 한 Data Factory 관리 id 액세스 권한을 부여 해야 합니다. 다음과 같은 형식으로 암호를 직접 입력할 수도 있습니다 `<key vault linked service name>/<secret name>/<secret version>` . 

      이러한 자격 증명은 패키지 실행 태스크에서 패키지에 지정 된 자체 경로 및 기타 구성에 의해 참조 되는 자식 패키지에 액세스 하는 데도 사용 됩니다. 

   1. SQL Server Data Tools (SSDT)를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호 값을 입력 합니다. 또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다 (위 참조).
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 구성 파일 또는 **SSIS 매개 변수**, **연결 관리자**또는 **속성 재정의** 탭에서 중요 한 값을 다시 입력 합니다 (아래 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용 하는 경우 지원 되지 않습니다. SSDT 또는 명령줄 유틸리티를 통해 다른 보호 수준을 사용 하려면 패키지를 다시 구성 해야 `dtutil` 합니다. 

   1. **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 된 로깅 이름을 대신 입력 하려면 **사용자 지정** 확인란을 선택 합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용 하 여 패키지 실행을 기록 하려면 **로깅 경로** 상자에 UNC 경로를 제공 하 여 로그 폴더를 지정 합니다. 찾아보기 **파일 저장소** 를 선택 하거나 경로를 수동으로 입력 하 여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 입니다. SSIS 패키지 실행 작업 실행 ID의 이름을 지정 하 고 5 분 마다 로그 파일이 생성 되는 각 개별 패키지 실행에 대해이 경로에 하위 폴더가 만들어집니다. 
   
   1. 로그 폴더에 액세스 하기 위한 자격 증명을 지정 합니다. 이전에 패키지 액세스 자격 증명 (위 참조)에 대 한 값을 입력 한 경우 **패키지 액세스 자격 증명과 동일** 확인란을 선택 하 여 다시 사용할 수 있습니다. 그렇지 않으면 **도메인**, **사용자 이름**및 **암호** 상자에 로깅 액세스 자격 증명 값을 입력 합니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 도메인은이 고 `Azure` 사용자 이름은 이며 `<storage account name>` 암호는 `<storage account key>` 입니다. 또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다 (위 참조).
   
앞에서 언급 한 모든 UNC 경로에 대해 정규화 된 파일 이름은 260 자 미만 이어야 합니다. 디렉터리 이름은 248 자 미만 이어야 합니다.

##### <a name="package-location-file-system-project"></a>패키지 위치: 파일 시스템 (프로젝트)

패키지 위치로 **파일 시스템 (프로젝트)** 을 선택 하는 경우 다음 단계를 완료 합니다.

![설정 탭-파일 시스템 (프로젝트)의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. 프로젝트 경로 상자에 프로젝트 파일에 대 한 UNC 경로를 제공 하 고 패키지 `.ispac` **Project path** `.dtsx` **이름** 상자에 프로젝트의 패키지 파일 ()을 제공 하 여 실행할 패키지를 지정 합니다. **찾아보기 파일 저장소** 를 선택 하거나 경로를 수동으로 입력 하 여 프로젝트를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 프로젝트를 저장 하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` 입니다.

   1. 프로젝트 및 패키지 파일에 액세스할 자격 증명을 지정 합니다. 이전에 패키지 실행 자격 증명 ( **Windows 인증**의 경우)에 대 한 값을 입력 한 경우 **패키지 실행 자격 증명과 동일** 확인란을 선택 하 여 다시 사용할 수 있습니다. 그렇지 않으면 **도메인**, **사용자 이름**및 **암호** 상자에 패키지 액세스 자격 증명 값을 입력 합니다. 예를 들어 Azure Files에서 프로젝트와 패키지를 저장 하는 경우 도메인은이 고, `Azure` 사용자 이름은이 `<storage account name>` 고, 암호는 `<storage account key>` 입니다. 

      또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택 합니다. 기존 key vault 연결 된 서비스를 선택 하거나 편집 하거나 새로 만듭니다. 그런 다음 값에 대 한 암호 이름 및 버전을 선택 합니다. Key vault 연결 된 서비스를 만들거나 편집할 때 기존 주요 자격 증명 모음을 선택 하거나 편집 하거나 새 자격 증명 모음을 만들 수 있습니다. 아직 수행 하지 않은 경우 키 자격 증명 모음에 대 한 Data Factory 관리 id 액세스 권한을 부여 해야 합니다. 다음과 같은 형식으로 암호를 직접 입력할 수도 있습니다 `<key vault linked service name>/<secret name>/<secret version>` . 

      이러한 자격 증명은 동일한 프로젝트에서 참조 되는 패키지 실행 태스크에서 자식 패키지에 액세스 하는 데도 사용 됩니다. 

   1. SSDT를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호 값을 입력 합니다. 또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다 (위 참조).
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 **SSIS 매개 변수**, **연결 관리자**또는 **속성 재정의** 탭에서 중요 한 값을 다시 입력 합니다 (아래 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용 하는 경우 지원 되지 않습니다. SSDT 또는 명령줄 유틸리티를 통해 다른 보호 수준을 사용 하려면 패키지를 다시 구성 해야 `dtutil` 합니다. 

   1. **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 된 로깅 이름을 대신 입력 하려면 **사용자 지정** 확인란을 선택 합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용 하 여 패키지 실행을 기록 하려면 **로깅 경로** 상자에 UNC 경로를 제공 하 여 로그 폴더를 지정 합니다. 찾아보기 **파일 저장소** 를 선택 하거나 경로를 수동으로 입력 하 여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 입니다. SSIS 패키지 실행 작업 실행 ID의 이름을 지정 하 고 5 분 마다 로그 파일이 생성 되는 각 개별 패키지 실행에 대해이 경로에 하위 폴더가 만들어집니다. 
   
   1. 로그 폴더에 액세스 하기 위한 자격 증명을 지정 합니다. 이전에 패키지 액세스 자격 증명 (위 참조)에 대 한 값을 입력 한 경우 **패키지 액세스 자격 증명과 동일** 확인란을 선택 하 여 다시 사용할 수 있습니다. 그렇지 않으면 **도메인**, **사용자 이름**및 **암호** 상자에 로깅 액세스 자격 증명 값을 입력 합니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 도메인은이 고 `Azure` 사용자 이름은 이며 `<storage account name>` 암호는 `<storage account key>` 입니다. 또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다 (위 참조).
   
앞에서 언급 한 모든 UNC 경로에 대해 정규화 된 파일 이름은 260 자 미만 이어야 합니다. 디렉터리 이름은 248 자 미만 이어야 합니다.

##### <a name="package-location-embedded-package"></a>패키지 위치: 포함 된 패키지

패키지 위치로 **포함 된 패키지** 를 선택 하는 경우 다음 단계를 완료 합니다.

![설정 탭에 포함 된 패키지의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. 패키지 파일 ()을 끌어 놓거나 `.dtsx` 파일 폴더의 제공 된 상자에 **업로드** 합니다. 패키지가 자동으로 압축 되 고 활동 페이로드에 포함 됩니다. 포함 된 후 나중에 편집을 위해 패키지를 **다운로드할** 수 있습니다. 또한 여러 작업에서 사용할 수 있는 파이프라인 매개 변수에 할당 하 여 포함 된 패키지를 **매개 변수화** 할 수 있습니다. 따라서 파이프라인 페이로드의 크기를 최적화 합니다. 프로젝트 파일 (포함)을 포함 하 `.ispac` 는 기능은 현재 지원 되지 않으므로 포함 된 패키지의 프로젝트 수준 범위에서 SSIS 매개 변수/연결 관리자를 사용할 수 없습니다.
   
   1. 포함 된 패키지가 모두 암호화 되지 않은 경우 패키지 실행 태스크 (EPT) 사용을 감지 하면 **패키지 실행 태스크** 확인란이 자동으로 선택 되 고 파일 시스템 경로에서 참조 하는 자식 패키지가 자동으로 추가 되므로 이러한 항목을 포함할 수도 있습니다.
   
      EPT 사용을 검색할 수 없는 경우 수동으로 **패키지 실행 태스크** 확인란을 선택 하 고 파일 시스템 경로에서 참조 하는 자식 패키지를 하나씩 추가 해야 합니다. 자식 패키지가 MSDB (SQL Server 데이터베이스)에 저장 되어 있는 경우에는 해당 패키지를 포함할 수 없으므로 Azure-SSIS IR에서 SQL Server 참조를 사용 하 여 해당 패키지를 인출 하기 위해 MSDB에 액세스할 수 있는지 확인 해야 합니다. 프로젝트 파일 (포함 `.ispac` )은 현재 지원 되지 않으므로 자식 패키지에 대 한 프로젝트 기반 참조를 사용할 수 없습니다.
   
   1. SSDT를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호 값을 입력 합니다. 
   
      또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택 합니다. 기존 key vault 연결 된 서비스를 선택 하거나 편집 하거나 새로 만듭니다. 그런 다음 값에 대 한 암호 이름 및 버전을 선택 합니다. Key vault 연결 된 서비스를 만들거나 편집할 때 기존 주요 자격 증명 모음을 선택 하거나 편집 하거나 새 자격 증명 모음을 만들 수 있습니다. 아직 수행 하지 않은 경우 키 자격 증명 모음에 대 한 Data Factory 관리 id 액세스 권한을 부여 해야 합니다. 다음과 같은 형식으로 암호를 직접 입력할 수도 있습니다 `<key vault linked service name>/<secret name>/<secret version>` .
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 구성 파일 또는 **SSIS 매개 변수**, **연결 관리자**또는 **속성 재정의** 탭에서 중요 한 값을 다시 입력 합니다 (아래 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용 하는 경우 지원 되지 않습니다. SSDT 또는 명령줄 유틸리티를 통해 다른 보호 수준을 사용 하려면 패키지를 다시 구성 해야 `dtutil` 합니다.

   1. **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 된 로깅 이름을 대신 입력 하려면 **사용자 지정** 확인란을 선택 합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용 하 여 패키지 실행을 기록 하려면 **로깅 경로** 상자에 UNC 경로를 제공 하 여 로그 폴더를 지정 합니다. 찾아보기 **파일 저장소** 를 선택 하거나 경로를 수동으로 입력 하 여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 입니다. SSIS 패키지 실행 작업 실행 ID의 이름을 지정 하 고 5 분 마다 로그 파일이 생성 되는 각 개별 패키지 실행에 대해이 경로에 하위 폴더가 만들어집니다. 
   
   1. **도메인**, **사용자 이름**및 **암호** 상자에 해당 값을 입력 하 여 로그 폴더에 액세스 하기 위한 자격 증명을 지정 합니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 도메인은이 고 `Azure` 사용자 이름은 이며 `<storage account name>` 암호는 `<storage account key>` 입니다. 또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다 (위 참조).
   
앞에서 언급 한 모든 UNC 경로에 대해 정규화 된 파일 이름은 260 자 미만 이어야 합니다. 디렉터리 이름은 248 자 미만 이어야 합니다.

##### <a name="package-location-package-store"></a>패키지 위치: 패키지 저장소

패키지 **저장소** 를 패키지 위치로 선택 하는 경우 다음 단계를 완료 합니다.

![설정 탭-패키지 저장소에서 속성을 설정 합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. **패키지 저장소 이름**에서 Azure-SSIS IR에 연결 된 기존 패키지 저장소를 선택 합니다.

   1. `.dtsx` **패키지 경로** 상자에서 선택한 패키지 저장소의 경로 (제외)를 제공 하 여 실행할 패키지를 지정 합니다. 선택한 패키지 저장소가 파일 시스템/Azure Files 맨 위에 있는 경우 **찾아보기 파일 저장소**를 선택 하 여 패키지를 찾아보고 선택할 수 있습니다. 그렇지 않으면 형식의 경로를 입력할 수 있습니다 `<folder name>\<package name>` . 또한 [기존 SSIS 패키지 저장소](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)와 유사한 SQL SERVER MANAGEMENT STUDIO (SSMS)를 통해 선택한 패키지 저장소로 새 패키지를 가져올 수 있습니다. 자세한 내용은 [Azure-SSIS IR 패키지 저장소를 사용하여 SSIS 패키지 관리](https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-package-store)를 참조하세요.

   1. 패키지를 별도의 파일에 구성 하는 경우 구성 경로 상자에서 구성 파일 ()에 대 한 UNC 경로를 제공 해야 `.dtsConfig` 합니다. **Configuration path** 찾아보기 **파일 저장소** 를 선택 하거나 경로를 수동으로 입력 하 여 구성을 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 구성을 저장 하는 경우 해당 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` 입니다.

   1. 구성 파일에 별도로 액세스 하기 위한 자격 증명을 지정할 것인지 여부를 선택 하려면 **구성 액세스 자격 증명** 확인란을 선택 합니다. 이는 선택한 패키지 저장소가 Azure SQL Managed Instance에서 호스트 되는 MSDB (SQL Server 데이터베이스) 위에 있거나 구성 파일을 저장 하지 않는 경우에 필요 합니다.
   
      이전에 패키지 실행 자격 증명 ( **Windows 인증**의 경우)에 대 한 값을 입력 한 경우 **패키지 실행 자격 증명과 동일** 확인란을 선택 하 여 다시 사용할 수 있습니다. 그렇지 않으면 **도메인**, **사용자 이름**및 **암호** 상자에 구성 액세스 자격 증명 값을 입력 합니다. 예를 들어 Azure Files에 구성을 저장 하는 경우 도메인은이 고, `Azure` 사용자 이름은이 `<storage account name>` 고, 암호는 `<storage account key>` 입니다. 

      또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택 합니다. 기존 key vault 연결 된 서비스를 선택 하거나 편집 하거나 새로 만듭니다. 그런 다음 값에 대 한 암호 이름 및 버전을 선택 합니다. Key vault 연결 된 서비스를 만들거나 편집할 때 기존 주요 자격 증명 모음을 선택 하거나 편집 하거나 새 자격 증명 모음을 만들 수 있습니다. 아직 수행 하지 않은 경우 키 자격 증명 모음에 대 한 Data Factory 관리 id 액세스 권한을 부여 해야 합니다. 다음과 같은 형식으로 암호를 직접 입력할 수도 있습니다 `<key vault linked service name>/<secret name>/<secret version>` .

   1. SSDT를 통해 패키지를 만들 때 **EncryptAllWithPassword** 또는 **EncryptSensitiveWithPassword** 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호 값을 입력 합니다. 또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다 (위 참조).
      
      **EncryptSensitiveWithUserKey** 보호 수준을 사용한 경우 구성 파일 또는 **SSIS 매개 변수**, **연결 관리자**또는 **속성 재정의** 탭에서 중요 한 값을 다시 입력 합니다 (아래 참조).
      
      **EncryptAllWithUserKey** 보호 수준을 사용 하는 경우 지원 되지 않습니다. SSDT 또는 명령줄 유틸리티를 통해 다른 보호 수준을 사용 하려면 패키지를 다시 구성 해야 `dtutil` 합니다. 

   1. **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 된 로깅 이름을 대신 입력 하려면 **사용자 지정** 확인란을 선택 합니다. 
   
   1. 패키지에 지정할 수 있는 표준 로그 공급자를 사용 하 여 패키지 실행을 기록 하려면 **로깅 경로** 상자에 UNC 경로를 제공 하 여 로그 폴더를 지정 합니다. 찾아보기 **파일 저장소** 를 선택 하거나 경로를 수동으로 입력 하 여 로그 폴더를 찾아보고 선택할 수 있습니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 로깅 경로는 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 입니다. SSIS 패키지 실행 작업 실행 ID의 이름을 지정 하 고 5 분 마다 로그 파일이 생성 되는 각 개별 패키지 실행에 대해이 경로에 하위 폴더가 만들어집니다. 
   
   1. **도메인**, **사용자 이름**및 **암호** 상자에 해당 값을 입력 하 여 로그 폴더에 액세스 하기 위한 자격 증명을 지정 합니다. 예를 들어 Azure Files에 로그를 저장 하는 경우 도메인은이 고 `Azure` 사용자 이름은 이며 `<storage account name>` 암호는 `<storage account key>` 입니다. 또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다 (위 참조).
   
앞에서 언급 한 모든 UNC 경로에 대해 정규화 된 파일 이름은 260 자 미만 이어야 합니다. 디렉터리 이름은 248 자 미만 이어야 합니다.

#### <a name="ssis-parameters-tab"></a>SSIS 매개 변수 탭

SSIS 패키지 실행 작업의 **Ssis 매개 변수** 탭에서 다음 단계를 완료 합니다.

![SSIS 매개 변수 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Azure-SSIS IR 실행 중인 경우 패키지 위치로 **SSISDB** 를 선택 하 고 **설정** 탭의 **수동 항목** 확인란의 선택을 취소 한 후 선택한 프로젝트의 기존 SSIS 매개 변수와 SSISDB의 패키지를 표시 하 여 값을 할당할 수 있습니다. 그렇지 않으면 값을 하나씩 입력 하 여 수동으로 값을 할당할 수 있습니다. 패키지 실행이 성공적으로 실행 되 고 올바르게 입력 되었는지 확인 합니다. 
   
   1. SSDT 및 **파일 시스템 (패키지)** 을 통해 패키지를 만들 때 **EncryptSensitiveWithUserKey** 보호 수준을 사용 하는 경우 **파일 시스템 (프로젝트)**, **포함 된 패키지**또는 **패키지 저장소** 를 패키지 위치로 선택한 경우이 탭에서 값을 할당 하기 위해 중요 한 매개 변수를 다시 입력 해야 합니다. 
   
매개 변수에 값을 할당할 때 식, 함수, Data Factory 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용 하 여 동적 콘텐츠를 추가할 수 있습니다.

또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택 합니다. 기존 key vault 연결 된 서비스를 선택 하거나 편집 하거나 새로 만듭니다. 그런 다음 값에 대 한 암호 이름 및 버전을 선택 합니다. Key vault 연결 된 서비스를 만들거나 편집할 때 기존 주요 자격 증명 모음을 선택 하거나 편집 하거나 새 자격 증명 모음을 만들 수 있습니다. 아직 수행 하지 않은 경우 키 자격 증명 모음에 대 한 Data Factory 관리 id 액세스 권한을 부여 해야 합니다. 다음과 같은 형식으로 암호를 직접 입력할 수도 있습니다 `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>연결 관리자 탭

SSIS 패키지 실행 작업의 **연결 관리자** 탭에서 다음 단계를 완료 합니다.

![연결 관리자 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Azure-SSIS IR 실행 중인 경우 패키지 위치로 **SSISDB** 를 선택 하 고 **설정** 탭의 **수동 항목** 확인란의 선택을 취소 한 후 선택한 프로젝트의 기존 연결 관리자와 ssisdb의 패키지를 표시 하 여 해당 속성에 값을 할당할 수 있습니다. 그렇지 않으면 값을 하나씩 입력 하 여 속성에 수동으로 값을 할당할 수 있습니다. 패키지 실행이 성공적으로 실행 되 고 올바르게 입력 되었는지 확인 합니다. 
   
      SSDT에서 해당 파일을 포함 하는 패키지를 열어 모든 연결 관리자에 대 한 올바른 **범위**, **이름**및 **속성** 이름을 가져올 수 있습니다. 패키지를 연 후에는 SSDT의 **속성** 창에 있는 모든 속성의 이름과 값을 표시 하는 관련 연결 관리자를 선택 합니다. 이 정보를 사용 하 여 런타임에 연결 관리자 속성의 값을 재정의할 수 있습니다. 

      ![SSDT에서 연결 관리자 속성 가져오기](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      예를 들어 SSDT에서 원래 패키지를 수정 하지 않으면 런타임에 기존 연결 관리자에서 **Connectbyproxy**, **ConnectionString**및 **ConnectUsingManagedIdentity** 속성의 값을 재정의 하 여 SQL Server에서 실행 되는 온-프레미스-온-프레미스 데이터 흐름을 ADF의 SSIS IR에서 실행 되는 온-프레미스-클라우드 데이터 흐름으로 변환할 수 있습니다.
      
      이러한 런타임 재정의는 온-프레미스 데이터에 액세스할 때 SSIS IR에 대 한 프록시로 SHIR (자체 호스팅 IR)을 사용 하도록 설정할 수 있습니다. MSOLEDBSQL 드라이버를 사용 하 여 [SSIS ir에 대 한 프록시로 Shir 구성](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)Managed Instance AZURE SQL DATABASE 및 adf 관리 id를 사용 하 여 aad (Azure Active Directory) 인증을 사용 하는 [aad 인증 구성](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager?view=sql-server-ver15#managed-identities-for-azure-resources-authentication)을 참조 하세요.

      ![연결 관리자 탭에서 SSDT의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. SSDT 및 **파일 시스템 (패키지)** 을 통해 패키지를 만들 때 **EncryptSensitiveWithUserKey** 보호 수준을 사용 하는 경우 **파일 시스템 (프로젝트)**, **포함 된 패키지**또는 **패키지 저장소** 를 패키지 위치로 선택한 경우이 탭에서 값을 할당 하려면 중요 한 연결 관리자 속성을 다시 입력 해야 합니다. 

연결 관리자 속성에 값을 할당할 때 식, 함수, Data Factory 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용 하 여 동적 콘텐츠를 추가할 수 있습니다. 

또는 Azure Key Vault에 저장 된 암호를 해당 값으로 사용할 수 있습니다. 이렇게 하려면 옆에 있는 **AZURE KEY VAULT** 확인란을 선택 합니다. 기존 key vault 연결 된 서비스를 선택 하거나 편집 하거나 새로 만듭니다. 그런 다음 값에 대 한 암호 이름 및 버전을 선택 합니다. Key vault 연결 된 서비스를 만들거나 편집할 때 기존 주요 자격 증명 모음을 선택 하거나 편집 하거나 새 자격 증명 모음을 만들 수 있습니다. 아직 수행 하지 않은 경우 키 자격 증명 모음에 대 한 Data Factory 관리 id 액세스 권한을 부여 해야 합니다. 다음과 같은 형식으로 암호를 직접 입력할 수도 있습니다 `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>속성 재정의 탭

SSIS 패키지 실행 작업의 **속성 재정의** 탭에서 다음 단계를 완료 합니다.

![속성 재정의 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. 선택한 패키지의 기존 속성 경로를 하나씩 입력 하 여 수동으로 값을 할당 합니다. 패키지 실행이 성공적으로 실행 되 고 올바르게 입력 되었는지 확인 합니다. 예를 들어 사용자 변수의 값을 재정의 하려면 다음 형식으로 해당 경로를 입력 `\Package.Variables[User::<variable name>].Value` 합니다. 

      SSDT에 해당 패키지를 포함 하는 패키지를 열어 패키지 속성에 대 한 올바른 **속성 경로** 를 가져올 수 있습니다. 패키지를 연 후 SSDT의 **속성** 창에서 해당 제어 흐름 및 **구성** 속성을 선택 합니다. 그런 다음 **구성** 속성 옆에 있는 줄임표 (**...**) 단추를 선택 하 여 패키지 [배포 모델에서 패키지 구성을 만드는](https://docs.microsoft.com/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations)데 일반적으로 사용 되는 **패키지 구성 도우미** 를 엽니다. 

      ![SSDT 속성에서 패키지 속성 가져오기](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      패키지 구성 **도우미**에서 패키지 구성 **사용** 확인란을 선택 하 고 **추가 ...** 단추를 선택 하 여 **패키지 구성 마법사**를 엽니다. 
      
      **패키지 구성 마법사**의 **구성 유형** 드롭다운 메뉴에서 **XML 구성 파일** 항목을 선택 하 고 구성 **설정 직접 지정** 단추를 입력 한 다음 구성 파일 이름을 입력 하 고 **다음 >** 단추를 선택 합니다. 

      ![SSDT 구성 도우미에서 패키지 속성 가져오기](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      마지막으로 원하는 경로를 가진 패키지 속성을 선택 하 고 **다음 >** 단추를 선택 합니다.  이제 원하는 패키지 속성 경로를 복사 & 붙여넣고 구성 파일에 저장할 수 있습니다. 이 정보를 사용 하 여 런타임 시 패키지 속성의 값을 재정의할 수 있습니다. 

      ![SSDT에서 패키지 속성 가져오기-구성 마법사](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. SSDT 및 **파일 시스템 (패키지)** 을 통해 패키지를 만들 때 **EncryptSensitiveWithUserKey** 보호 수준을 사용 하는 경우 **파일 시스템 (프로젝트)**, **포함 된 패키지**또는 **패키지 저장소** 를 패키지 위치로 선택한 경우이 탭에서 값을 할당 하려면 중요 패키지 속성을 다시 입력 해야 합니다. 
   
패키지 속성에 값을 할당할 때 식, 함수, Data Factory 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용 하 여 동적 콘텐츠를 추가할 수 있습니다.

구성 파일 및 **SSIS 매개 변수** 탭에서 할당 된 값은 **연결 관리자** 또는 **속성 재정의** 탭을 사용 하 여 재정의할 수 있습니다. **속성 재정의** 탭을 사용 하 여 **연결 관리자** 탭에서 할당 된 값을 재정의할 수도 있습니다.

파이프라인 구성의 유효성을 검사 하려면 도구 모음에서 **유효성 검사** 를 선택 합니다. **파이프라인 유효성 검사 보고서**를 닫으려면를 선택 **>>** 합니다.

Data Factory에 파이프라인을 게시 하려면 **모두 게시**를 선택 합니다. 

### <a name="run-the-pipeline"></a>파이프라인 실행
이 단계에서는 파이프라인 실행을 트리거합니다. 

1. 파이프라인 실행을 트리거하려면 도구 모음에서 **트리거** 를 선택 하 고 **지금 트리거**를 선택 합니다. 

   ![지금 트리거](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **파이프라인 실행** 창에서 **마침**을 선택합니다. 

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. 파이프라인 실행 및 해당 상태를 **실행 시작** 시간과 같은 다른 정보와 함께 볼 수 있습니다. 보기를 새로 고치려면 **새로 고침**을 선택합니다.

   ![파이프라인 실행](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에는 하나의 활동만 있으므로 하나의 활동 실행만 표시 됩니다. SSIS 패키지 실행 작업입니다.

   ![작업 실행](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. SQL server의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행 하 여 패키지가 실행 되었는지 확인 합니다. 

   ```sql
   select * from catalog.executions
   ```

   ![패키지 실행 확인](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 파이프라인 작업 실행의 출력에서 SSISDB 실행 ID를 가져오고 ID를 사용 하 여 SQL Server Management Studio에서 보다 포괄적인 실행 로그 및 오류 메시지를 확인할 수도 있습니다.

   ![실행 ID를 가져옵니다.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약

파이프라인이 일정에 따라 실행 되도록 파이프라인에 대해 예약 된 트리거를 만들 수도 있습니다 (예: 매시간 또는 매일). 예를 들어 [데이터 팩터리 만들기 - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

## <a name="run-a-package-with-powershell"></a>PowerShell을 사용하여 패키지 실행
이 섹션에서는 Azure PowerShell를 사용 하 여 SSIS 패키지를 실행 하는 SSIS 패키지 실행 작업을 사용 하 여 Data Factory 파이프라인을 만듭니다. 

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-az-ps)의 단계별 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Azure-SSIS IR를 사용 하 여 데이터 팩터리 만들기
이미 Azure-SSIS IR 프로 비전 된 기존 데이터 팩터리를 사용 하거나 Azure-SSIS IR를 사용 하 여 새 데이터 팩터리를 만들 수 있습니다. [자습서: PowerShell을 통해 Azure에 SSIS 패키지 배포](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)의 단계별 지침을 따릅니다.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기 
이 단계에서는 SSIS 패키지 실행 작업으로 파이프라인을 만듭니다. 이 작업은 SSIS 패키지를 실행합니다. 

1. `RunSSISPackagePipeline.json` `C:\ADF\RunSSISPackage` 다음 예제와 유사한 내용이 포함 된 폴더에 이라는 JSON 파일을 만듭니다.

   > [!IMPORTANT]
   > 파일을 저장 하기 전에 개체 이름, 설명 및 경로, 속성 또는 매개 변수 값, 암호 및 기타 변수 값을 바꿉니다. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   파일 시스템/Azure Files에 저장 된 패키지를 실행 하려면 다음과 같이 패키지 및 로그 위치 속성에 대 한 값을 입력 합니다.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   파일 시스템/Azure Files에 저장 된 프로젝트 내에서 패키지를 실행 하려면 다음과 같이 패키지 위치 속성에 대 한 값을 입력 합니다.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   포함 된 패키지를 실행 하려면 다음과 같이 패키지 위치 속성에 대 한 값을 입력 합니다.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   패키지 저장소에 저장 된 패키지를 실행 하려면 다음과 같이 패키지 및 구성 위치 속성에 대 한 값을 입력 합니다.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Azure PowerShell에서 `C:\ADF\RunSSISPackage` 폴더로 전환합니다.

3. **RunSSISPackagePipeline**파이프라인을 만들려면 **AzDataFactoryV2Pipeline** cmdlet을 실행 합니다.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   샘플 출력은 다음과 같습니다.

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>파이프라인 실행
**AzDataFactoryV2Pipeline** cmdlet을 사용 하 여 파이프라인을 실행 합니다. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사 하거나 붙여넣고 Enter 키를 선택 합니다. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Azure Portal를 사용 하 여 파이프라인을 모니터링할 수도 있습니다. 단계별 지침은 [파이프라인 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약
이전 단계에서는 요청 시 파이프라인을 실행 했습니다. 일정에 따라 일정에 따라 파이프라인을 실행 하는 일정 트리거를 만들 수도 있습니다 (예: 매시간 또는 매일).

1. `MyTrigger.json`다음 콘텐츠를 사용 하 여 폴더에 이라는 JSON 파일을 만듭니다 `C:\ADF\RunSSISPackage` . 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Azure PowerShell에서 `C:\ADF\RunSSISPackage` 폴더로 전환합니다.
1. 트리거를 만드는 **AzDataFactoryV2Trigger** cmdlet을 실행 합니다. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 기본적으로 트리거는 중지된 상태입니다. **AzDataFactoryV2Trigger** cmdlet을 실행 하 여 트리거를 시작 합니다. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. **AzDataFactoryV2Trigger** cmdlet을 실행 하 여 트리거가 시작 되었는지 확인 합니다. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. 한 시간 후에 다음 명령을 실행합니다. 예를 들어 현재 시간이 오후 3:25(UTC)인 경우 오후 4시(UTC)에 명령을 실행합니다. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   SQL server의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행 하 여 패키지가 실행 되었는지 확인 합니다. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>다음 단계
다음 블로그 게시물을 참조하십시오.
- [Azure Data Factory 파이프라인에서 SSIS 작업을 사용 하 여 ETL/ELT 워크플로 현대화 및 확장](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
