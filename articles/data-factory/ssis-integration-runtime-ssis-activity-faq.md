---
title: SSIS integration runtime에서 패키지 실행 문제 해결 | Microsoft Docs
description: 이 문서에서는 SSIS Integration Runtime에서 SSIS 패키지 실행에 대 한 문제 해결 지침을 제공
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: a018a383de855a05b14aa6e1f1c465f8868f672d
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312161"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>SSIS integration runtime에서 패키지 실행 문제 해결

이 문서에서는 SSIS Integration Runtime에서 잠재적 원인 및 작업 오류를 해결 하기 위해 패키지를 SSIS를 실행 하는 경우 발생할 수 있는 가장 일반적인 오류를 포함 합니다.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>문제 해결에 대 한 로그는 어디서 찾을 수 있습니까

* 실행 결과, 오류 메시지 및 작업 ID를 포함 하 여 SSIS 패키지 실행 작업의 출력을 확인 하는 ADF 포털을 사용할 수 있습니다. 세부 정보에서 찾을 수 있습니다 [파이프라인 모니터링](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* 실행에 대 한 세부 정보 로그를 확인 하는 SSIS 카탈로그 (SSISDB)를 사용할 수 있습니다. 세부 정보에서 찾을 수 있습니다 [모니터 실행 중인 패키지 및 기타 작업](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>일반적인 오류, 원인 및 솔루션

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>오류 메시지: `"Connection Timeout Expired."` 또는 `"The service has encountered an error processing your request. Please try again."`

* 잠재적 원인 및 권장된 작업:
  * 데이터 원본/대상 오버 로드 됩니다. 데이터 원본/대상에서 부하를 확인 하 고 충분 한 용량이 있는지 확인 합니다. 예를 들어, Azure SQL을 사용 하는 경우 데이터베이스 시간 초과 될 가능성이 규모를 고려해 야 제안 됩니다.
  * SSIS Integration Runtime에서 데이터 원본/대상 사이의 네트워크 연결이 지역 간 또는 온-프레미스와 azure 간에 하는 경우에 특히 안정적이 고 아닙니다. 다음 단계에서 SSIS 패키지에서 다시 시도 패턴을 적용할 제안한:
    * SSIS 패키지를 다시 실행할 수 부작용 없이 실패 한 경우 (예: 있는지 확인 데이터가 손실 될 데이터 dup....)
    * 구성 합니다 **다시 시도** 및 **재시도 간격** 일반 탭에서 SSIS 패키지 작업 실행의 ![일반 탭의 속성을 설정 합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * ADO.NET 및 OLEDB 원본/대상 구성 요소에 대 한 ConnectRetryCount 및 ConnectRetryInterval에서 설정할 수 있습니다 SSIS 패키지에서 연결 관리자 또는 SSIS 작업

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>오류 메시지: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* 잠재적 원인 및 권장된 작업:
  * 이 문제 일반적으로 데이터 원본/대상 의미에서에서 액세스할 수 없는 SSIS Integration Runtime 다양 한 이유로 인해 발생할 수 있습니다.
    * 데이터 원본/대상 이름/i P 제대로 전달 되었는지 확인
    * 방화벽을 제대로 설정 되어 있는지 확인
    * VNet은 온-프레미스에서 데이터 원본/대상 경우 올바르게 구성 되어 있는지 확인 합니다.
      * 동일한 vNet에 Azure VM을 프로 비전 하 여 vNet 구성에서 문제 인지 여부를 확인할 수 있습니다. 데이터 원본/대상 Azure VM에서 액세스할 수 있는지 여부를 확인 하 고
      * SSIS Integration Runtime을 사용 하 여 vNet을 사용 하는 방법에 대 한 자세한 내용을 볼 수 있습니다 [가상 네트워크에 AZURE-SSIS 통합 런타임을 조인](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>오류 메시지: "`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`"

* 잠재적 원인 및 권장된 작업:
  * 패키지에서 사용 하는 ADO.NET 공급자는 SSIS Integration Runtime에 설치 되지 않았습니다. 사용자 지정 설치를 사용 하 여 공급자를 설치할 수 있습니다. 사용자 지정 설치에 대 한 자세한 세부 정보를 찾을 수 있습니다 [AZURE-SSIS integration runtime에 대 한 설치를 사용자 지정](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>오류 메시지: "`The connection '...' is not found`"

* 잠재적 원인 및 권장된 작업:
  * 이 오류 때문일 수 있습니다 이전 SSMS 버전의 알려진된 문제입니다. 사용자 지정 구성 요소 (예: SSIS Azure Feature Pack 또는 타사 구성 요소) SSMS 배포를 위해 여기서는 컴퓨터에 설치 되지 않는 포함 된 패키지, 구성 요소 SSMS에서 제거 되 고 오류가 발생 합니다. 업그레이드 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 고정 하는 문제가 있는 최신 버전으로 합니다.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>오류 메시지: "공간이 충분 한 디스크에"

* 잠재적 원인 및 권장된 작업:
  * 이 오류는 SSIS Integration Runtime 노드에서 로컬 디스크는 의미 합니다. 에 패키지 또는 사용자 지정 설치 디스크 공간을 많이 소비 합니다 있는지 여부를 확인 합니다.
    * 디스크에 패키지에서 사용 되는 경우 해당 인해 확보 될 패키지 실행이 완료 된 후입니다.
    * 디스크에 사용자 지정 설치 프로그램에서 사용 되는 경우, SSIS Integration Runtime을 중지 하 고, 스크립트를 수정 하 고, SSIS Integration Runtime을 다시 시작 하는 것이 해야 합니다. 사용자 지정 설치에 대 한 지정 된 전체 Azure Blob 컨테이너 SSIS IR 노드로 복사 됩니다, 그리고 따라서 해당 컨테이너에서 불필요 한 내용이 있는지 여부를 확인 합니다.

### <a name="error-message-cannot-open-file-"></a>오류 메시지: "파일 열 수 없습니다..."

* 잠재적 원인 및 권장된 작업:
  * 이 오류는 패키지 실행 SSIS Integration Runtime에서 로컬 디스크의 파일을 찾을 수 없을 때 발생 합니다.
    * 하지 SSIS Integration Runtime에서 실행 하는 패키지의 절대 경로 사용 하는 것이 좋습니다. 현재 실행 작업 디렉터리 (.) 또는 임시 폴더 (% TEMP %)를 사용 합니다. 대신 합니다.
    * SSIS Integration Runtime 노드에서 일부 파일을 유지 하는 데 필요한, 것이 좋습니다 통해 파일을 준비 하려면 [사용자 지정 설치](how-to-configure-azure-ssis-ir-custom-setup.md)합니다. 실행 완료 된 후 실행 작업 디렉터리의 모든 파일을 정리할 수 됩니다.
    * SSIS Integration Runtime 노드에서 파일을 저장 하는 대신 Azure 파일을 사용 하는 방법도 있습니다. 자세한 세부 정보를 찾을 수 있습니다 [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)합니다.

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>오류 메시지: "'SSISDB' 데이터베이스 할당량에 도달한 크기"

* 잠재적 원인 및 권장된 작업:
  * SSIS Integration Runtime을 만들 때 관리 되는 인스턴스를 Azure SQL에서 만든 SSISDB에는 할당량에 도달 했습니다.
    * 이 문제를 해결 하려면 데이터베이스의 DTU를 늘려 보십시오. 세부 정보에서 찾을 수 있습니다. [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * 패키지는 많은 로그를 생성 하는지 여부를 확인 합니다. 그렇다면 이러한 로그를 정리 하려면 탄력적 작업을 구성할 수 있습니다. 가리킵니다 [Azure Elastic Database 작업을 사용 하 여 SSISDB 로그 정리](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) 세부 정보에 대 한 합니다.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>오류 메시지: "데이터베이스에 대 한 요청 제한이 됩니다... 및에 도달 했습니다. "

* 잠재적 원인 및 권장된 작업:
  * 많은 패키지를 SSIS Integration Runtime에서 병렬로 실행 하는 SSISDB에 대 한 요청 제한에 도달 하기 때문에이 오류가 발생할 수 있습니다. 이 문제를 해결 하 여 SSISDB의 DTC를 늘리는 것이 좋습니다. 세부 정보에서 찾을 수 있습니다. [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>오류 메시지: "SSIS 작업이 실패 했습니다. 예기치 않은 작업 상태:..."

* 잠재적 원인 및 권장된 작업:
  * 일시적인 오류로 인해 주로 오류의 원인은, 따라서 패키지 실행을 다시 실행 하려고 합니다. 다음 단계에서 SSIS 패키지에서 다시 시도 패턴을 적용할 제안한:
    * SSIS 패키지 부작용 (예: 데이터 손실, 데이터 dup....) 없이 실패 시 다시 실행할 수 있는지 확인
    * 구성 합니다 **다시 시도** 및 **재시도 간격** 일반 탭에서 SSIS 패키지 작업 실행의 ![일반 탭의 속성을 설정 합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * ADO.NET 및 OLEDB 원본/대상 구성 요소에 대 한 ConnectRetryCount 및 ConnectRetryInterval에서 설정할 수 있습니다 SSIS 패키지에서 연결 관리자 또는 SSIS 작업

### <a name="error-message-there-is-no-active-worker"></a>오류 메시지: "이 활성 작업 자가 없습니다."

* 잠재적 원인 및 권장된 작업:
  * 이 오류는 일반적으로 비정상 상태에서는 SSIS Integration Runtime을 의미 합니다. 오류 상태 및 세부 정보에 대 한 Azure portal을 확인 합니다. [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>오류 메시지: "Integration runtime 업그레이드할 수 없습니다 및 작업을 사용자 지정 설치에 대해 제공한 Azure Blob 컨테이너에 액세스할 수 없습니다 때문 결국 중지 됩니다."

* SSIS 통합 런타임 사용자 지정 설치에 대 한 구성 저장소에 액세스할 수 없습니다.이 오류가 발생 합니다. 제공 된 SAS Uri를 올바르고 만료 되지 않았는지 여부를 확인 합니다.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>오류 메시지: "Microsoft OLE DB Provider for Analysis Services입니다. ' Hresult: 0x80004005 설명:' COM 오류: COM 오류: mscorlib; 호출 대상이 예외를 throw 했습니다 "

* 잠재적 원인 및 권장된 작업:
  * 잠재적 원인은 사용 하도록 설정 하는 MFA 사용 하 여 해당 사용자 이름/암호 하나가 지원 되지 않는 SSIS integration runtime에서 아직 Azure Analysis Services 인증에 대해 구성 되었습니다. Azure Analysis Service 인증에 대 한 서비스 주체를 사용 하려고 합니다.
    1. AAS에 대 한 서비스 주체를 준비 합니다. [https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
    2. 연결 관리자 구성 "특정 사용자 이름 및 암호를 사용 하 여": "AppID" 사용자 이름 및 암호와 "clientSecret"으로 설정

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-managed-identity"></a>오류 메시지: "ADONET 원본이 다음과 같은 오류 메시지가 {GUID} 연결을 설정 하지 못했습니다. 사용자 ' NT AUTHORITY\ANONYMOUS LOGON' 로그인 하지 못했습니다 "관리 되는 id를 사용 하는 경우

* 잠재적 원인 및 권장된 작업:
  * 매개 변수 "ConnectUsingManagedIdentity"가 true 인 경우 "Active Directory 암호 인증"으로 연결 관리자의 인증 방법을 구성 하지 있는지 확인 합니다. 구성할 수 있습니다 "SQL 인증" 대신 "ConnectUsingManagedIdentity" 설정 된 경우는 무시 됩니다. 됩니다.

### <a name="package-takes-unexpected-long-time-to-execute"></a>패키지는 예기치 않은 데 오래 걸리는 실행

* 잠재적 원인 및 권장된 작업:
  * SSIS Integration Runtime에서 너무 많은 패키지 실행 예약 되었습니다. 이 경우 이러한 모든 실행 실행 하려면 해당 설정에 대 한 큐에서 대기 수 됩니다.
    * 최대 병렬 실행 개수 IR 노드 수 = * 노드당 최대 병렬 실행
    * 가리킵니다 [Azure Data Factory에서 AZURE-SSIS Integration Runtime 만들기](create-azure-ssis-integration-runtime.md) 노드 수 및 노드당 최대 병렬 실행을 설정 하는 방법에 대 한 합니다.
  * SSIS Integration Runtime은 중지 또는 비정상 상태입니다. 확인할 [AZURE-SSIS 통합 런타임을](monitor-integration-runtime.md#azure-ssis-integration-runtime) SSIS 통합 런타임 상태 및 오류를 확인 하는 방법에 대 한 합니다.
  * 패키지 실행을 완료 해야 하는 특정 시간에 확실 한 경우 제한 시간을 설정 하는 것이 좋습니다. ![일반 탭의 속성을 설정 합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>패키지 실행에서 성능 저하

* 잠재적 원인 및 권장된 작업:

  * SSIS Integration Runtime 데이터 원본 및 대상으로 동일한 지역에 있는지 확인 합니다.

  * "Performance" 로깅 수준을 사용 하도록 설정

      실행의 각 구성 요소에 대 한 자세한 정보 기간을 수집 하려면 "성능" 패키지 실행의 로깅 수준을 설정할 수 있습니다. 세부 정보에서 찾을 수 있습니다. [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Azure portal의 모니터 페이지 IR에서에서 IR 노드 성능을 검사 합니다.
    * SSIS Integration Runtime을 모니터링 하는 방법: [Azure SSIS 통합 런타임](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * Azure portal에서 기록을 CPU/메모리 사용량의 SSIS Integration Runtime은 데이터 팩터리의 메트릭을 사용할 수 있는 ![SSIS Integration Runtime의 메트릭 모니터링](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
