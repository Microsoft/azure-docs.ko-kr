---
title: SSIS integration runtime에서 패키지 실행 문제 해결
description: 이 문서에서는 SSIS integration runtime에서 SSIS 패키지 실행에 대 한 문제 해결 지침을 제공 합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 1c2db107302e4851641ef430db61ec9b29ee151f
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187472"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>SSIS integration runtime에서 패키지 실행 문제 해결

이 문서에는 ssis Integration runtime에서 SSIS (SQL Server Integration Services) 패키지를 실행할 때 발견할 수 있는 가장 일반적인 오류가 포함 되어 있습니다. 오류를 해결 하는 잠재적인 원인과 작업을 설명 합니다.

## <a name="where-to-find-logs-for-troubleshooting"></a>문제 해결을 위한 로그를 찾을 수 있는 위치

Azure Data Factory 포털을 사용 하 여 SSIS 패키지 실행 작업의 출력을 확인 합니다. 출력에는 실행 결과, 오류 메시지 및 작업 ID가 포함 됩니다. 자세한 내용은 [파이프라인 모니터링](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)을 참조 하세요.

SSIS 카탈로그 (SSISDB)를 사용 하 여 실행에 대 한 세부 정보 로그를 확인 합니다. 자세한 내용은 [실행 중인 패키지 및 기타 작업 모니터링](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)을 참조 하세요.

## <a name="common-errors-causes-and-solutions"></a>일반적인 오류, 원인 및 해결 방법

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>오류 메시지: "연결 제한 시간이 만료 되었습니다." 또는 "서비스에서 요청을 처리 하는 동안 오류가 발생 했습니다. 다시 시도하세요”와 같은 오류로 인해 실패합니다.

가능한 원인 및 권장 되는 작업은 다음과 같습니다.
* 데이터 원본 또는 대상이 오버 로드 됩니다. 데이터 원본 또는 대상에 대 한 부하를 확인 하 고 용량이 충분 한지 확인 합니다. 예를 들어 Azure SQL Database 사용 하는 경우 데이터베이스가 시간 초과 될 가능성이 있는 경우 확장 하는 것이 좋습니다.
* 특히 연결이 지역 간 이거나 온-프레미스와 Azure 사이에 있는 경우 SSIS 통합 런타임과 데이터 원본 또는 대상 간의 네트워크가 불안정 합니다. 다음 단계를 수행 하 여 SSIS 패키지에서 재시도 패턴을 적용 합니다.
  * SSIS 패키지가 부작용 (예: 데이터 손실 또는 데이터 중복) 없이 실패 시 다시 실행할 수 있는지 확인 합니다.
  * **일반** 탭에서 **SSIS 패키지 실행** 작업의 **다시 시도** 및 **재시도 간격** 을 구성 합니다. ![일반 탭에서 속성을 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * ADO.NET 및 OLE DB 원본 또는 대상 구성 요소에 대해 SSIS 패키지 또는 SSIS 작업에서 연결 관리자의 **ConnectRetryCount** 및 **ConnectRetryInterval** 를 설정 합니다.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>오류 메시지: "ADO NET 원본에서 연결을 획득 하지 못했습니다. '" "SQL Server에 대 한 연결을 설정 하는 동안 네트워크 관련 또는 인스턴스 관련 오류가 발생 했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. "

이 이슈는 일반적으로 SSIS 통합 런타임에서 데이터 원본 또는 대상에 액세스할 수 없음을 의미합니다. 원인은 다양합니다. 다음과 같이 작업해 보세요.
* 데이터 원본 또는 대상 이름/i d를 올바르게 전달 하 고 있는지 확인 합니다.
* 방화벽이 올바르게 설정 되어 있는지 확인 합니다.
* 데이터 원본 또는 대상이 온-프레미스 인 경우 가상 네트워크가 올바르게 구성 되어 있는지 확인 합니다.
  * 동일한 가상 네트워크에서 Azure VM을 프로 비전 하 여 가상 네트워크 구성에서 문제가 발생 했는지 여부를 확인할 수 있습니다. 그런 다음 Azure VM에서 데이터 원본 또는 대상을 액세스할 수 있는지 여부를 확인 합니다.
  * 가상 네트워크를 사용 하는 방법에 대 한 자세한 내용은 [AZURE ssis integration runtime을 가상 네트워크에 가입](join-azure-ssis-integration-runtime-virtual-network.md)에서 ssis integration runtime을 참조 하세요.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>오류 메시지: "ADO NET 원본에서 연결을 획득 하지 못했습니다. '" "관리 되는 연결 관리자를 만들 수 없습니다."

패키지에 사용 되는 ADO.NET 공급자가 SSIS 통합 런타임에 설치 되지 않았기 때문일 수 있습니다. 사용자 지정 설치를 사용 하 여 공급자를 설치할 수 있습니다. 사용자 지정 설치에 대 한 자세한 내용은 [AZURE SSIS 통합 런타임의 설정 사용자 지정](how-to-configure-azure-ssis-ir-custom-setup.md)에서 확인할 수 있습니다.

### <a name="error-message-the-connection--is-not-found"></a>오류 메시지: "연결 ' ... ' 찾을 수 없음 "

이 오류는 이전 버전의 SSMS(SQL Server Management Studio)에서 알려진 이슈로 인해 발생할 수 있습니다. 패키지에 SSMS를 사용하여 배포를 수행하는 컴퓨터에 설치되지 않은 사용자 지정 구성 요소(예: SSIS Azure Feature Pack 또는 파트너 구성 요소)가 포함된 경우 SSMS는 구성 요소를 제거하고 오류를 발생시킵니다. 이슈가 해결된 최신 버전으로 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 업그레이드합니다.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>오류 메시지: "SSIS Executor 종료 코드:-1073741819"

* 잠재적인 원인 & 권장 조치:
  * 여러 Excel 원본 또는 대상이 다중 스레드에서 병렬로 실행 되는 경우 Excel 원본 및 대상에 대 한 제한으로 인해이 오류가 발생할 수 있습니다. 이러한 제한을 해결 하려면 순서 대로 실행 되도록 Excel 구성 요소를 변경 하거나, ExecuteOutOfProcess 속성을 True로 설정 하 여 "패키지 실행 태스크"를 통해 서로 다른 패키지를 실행 합니다.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>오류 메시지: "디스크에 공간이 부족 합니다."

이 오류는 SSIS 통합 런타임 노드에서 로컬 디스크가 사용 됨을 의미 합니다. 패키지나 사용자 지정 설치에서 디스크 공간을 많이 사용 하 고 있는지 확인 합니다.
* 패키지에서 디스크를 사용 하는 경우 패키지 실행이 완료 된 후 해제 됩니다.
* 사용자 지정 설치 프로그램에서 디스크를 사용 하는 경우 SSIS 통합 런타임을 중지 하 고, 스크립트를 수정 하 고, 통합 런타임을 다시 시작 해야 합니다. 사용자 지정 설치에 대해 지정한 전체 Azure blob 컨테이너는 SSIS integration runtime 노드에 복사 되므로 해당 컨테이너 아래에 불필요 한 콘텐츠가 있는지 확인 합니다.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>오류 메시지: "마스터에서 리소스를 검색 하지 못했습니다. Microsoft.sqlserver.management.integrationservices: 300004. ScaleoutContract. MasterResponseFailedException:. 설명: 로드 파일 "* * *"이 (가) 실패 했습니다. "

* 잠재적인 원인 & 권장 조치:
  * SSIS 작업이 파일 시스템 (패키지 파일 또는 프로젝트 파일)에서 패키지를 실행 하는 경우 SSIS 작업에서 제공한 패키지 액세스 자격 증명을 사용 하 여 프로젝트, 패키지 또는 구성 파일에 액세스할 수 없는 경우이 오류가 발생 합니다.
    * Azure 파일을 사용 하는 경우:
      * 파일 경로는 \\\\\<저장소 계정 이름\>으로 시작 해야 합니다. file.core.windows.net\\\<파일 공유 경로\>
      * 도메인은 "Azure" 여야 합니다.
      * 사용자 이름은 저장소 계정 이름 \<이어야 합니다\>
      * 암호는 저장소 액세스 키 \<이어야 합니다\>
    * 온-프레미스 파일을 사용 하는 경우 Azure SSIS 통합 런타임에서 온-프레미스 파일 공유에 액세스할 수 있도록 VNet, 패키지 액세스 자격 증명 및 권한이 올바르게 구성 되어 있는지 확인 하세요.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>오류 메시지: "파일 이름 ' ... ' 연결에 지정 된가 잘못 되었습니다. "

* 잠재적인 원인 & 권장 조치:
  * 잘못 된 파일 이름이 지정 되었습니다.
  * 연결 관리자에서 짧은 시간 대신 FQDN (정규화 된 도메인 이름)을 사용 하 고 있는지 확인 합니다.

### <a name="error-message-cannot-open-file-"></a>오류 메시지: "파일을 열 수 없습니다."

이 오류는 패키지 실행이 SSIS 통합 런타임의 로컬 디스크에서 파일을 찾을 수 없을 때 발생 합니다. 다음과 같이 작업해 보세요.
* SSIS 통합 런타임에 실행 되는 패키지의 절대 경로를 사용 하지 마세요. 현재 실행 작업 디렉터리 (.) 또는 임시 폴더 (% TEMP%)를 사용 합니다. 대신.
* SSIS 통합 런타임 노드에 일부 파일을 유지 해야 하는 경우 [설치 사용자 지정](how-to-configure-azure-ssis-ir-custom-setup.md)에 설명 된 대로 파일을 준비 합니다. 작업 디렉터리의 모든 파일은 실행이 완료 된 후 정리 됩니다.
* SSIS integration runtime 노드에 파일을 저장 하는 대신 Azure Files를 사용 합니다. 자세한 내용은 [Azure 파일 공유 사용](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)을 참조 하세요.

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>오류 메시지: "' SSISDB ' 데이터베이스가 크기 할당량에 도달 했습니다."

Azure SQL 데이터베이스에 만든 SSISDB 데이터베이스 또는 SSIS 통합 런타임을 만들 때 관리되는 인스턴스가 할당량에 도달했기 때문일 수 있습니다. 다음과 같이 작업해 보세요.
* 데이터베이스의 DTU를 늘리는 것이 좋습니다. 자세한 내용은 [Azure SQL Database 서버의 SQL Database 리소스 한도](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)를 참조하세요.
* 패키지에서 많은 로그를 생성할지 여부를 확인합니다. 많은 로그를 생성할 경우 이러한 로그를 정리하도록 탄력적 작업을 구성할 수 있습니다. 자세한 내용은 [Azure Elastic Database 작업을 사용하여 SSISDB 로그 정리](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)를 참조하세요.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>오류 메시지: "데이터베이스에 대 한 요청 제한은 ... 및에 도달 했습니다. "

SSIS integration runtime에서 많은 패키지가 병렬로 실행 되는 경우이 오류는 SSISDB가 요청 제한에 도달 했기 때문에 발생할 수 있습니다. 이 문제를 해결 하려면 SSISDB의 DTC를 늘려야 합니다. 자세한 내용은 [Azure SQL Database 서버의 SQL Database 리소스 한도](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)를 참조하세요.

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>오류 메시지: "예기치 않은 작업 상태를 포함 하 여 SSIS 작업이 실패 했습니다."

오류는 일반적으로 일시적인 문제로 인해 발생 하므로 패키지 실행을 다시 실행 해 보십시오. 다음 단계를 수행 하 여 SSIS 패키지에서 재시도 패턴을 적용 합니다.

* SSIS 패키지가 부작용 (예: 데이터 손실 또는 데이터 중복) 없이 실패 시 다시 실행할 수 있는지 확인 합니다.
* **일반** 탭에서 **SSIS 패키지 실행** 작업의 **다시 시도** 및 **재시도 간격** 을 구성 합니다. ![일반 탭에서 속성을 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* ADO.NET 및 OLE DB 원본 또는 대상 구성 요소에 대해 SSIS 패키지 또는 SSIS 작업에서 연결 관리자의 **ConnectRetryCount** 및 **ConnectRetryInterval** 를 설정 합니다.

### <a name="error-message-there-is-no-active-worker"></a>오류 메시지: "활성 작업자는 없습니다."

이 오류는 일반적으로 SSIS 통합 런타임이 비정상 상태임을 의미 합니다. 상태 및 자세한 오류에 대 한 Azure Portal를 확인 합니다. 자세한 내용은 [AZURE SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)을 참조 하세요.

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>오류 메시지: "사용자 지정 설치를 위해 제공한 Azure Blob 컨테이너에 액세스할 수 없으므로 통합 런타임을 업그레이드할 수 없으며 결국 작동이 중지 됩니다."

이 오류는 SSIS 통합 런타임에서 사용자 지정 설치를 위해 구성 된 저장소에 액세스할 수 없을 때 발생 합니다. 제공 된 SAS (공유 액세스 서명) URI가 유효 하 고 만료 되지 않았는지 확인 합니다.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>오류 메시지: "Microsoft OLE DB Provider for Analysis Services. ' Hresult: 0x80004005 설명: ' COM 오류: COM 오류: mscorlib; 호출 대상이 예외를 throw 했습니다. "

한 가지 가능한 원인은 Azure Multi-Factor Authentication 사용 하도록 설정 된 사용자 이름 또는 암호가 Azure Analysis Services 인증에 대해 구성 되어 있기 때문입니다. 이 인증은 SSIS integration runtime에서 지원 되지 않습니다. Azure Analysis Services 인증을 위해 서비스 주체를 사용 하려고 합니다.

1. [서비스 주체를 사용 하 여 자동화](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)에 설명 된 대로 서비스 주체를 준비 합니다.
2. 연결 관리자에서 **특정 사용자 이름 및 암호 사용**구성: **AppID** 를 username으로 설정 하 고 **clientSecret** 를 암호로 설정 합니다.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>오류 메시지: "ADONET Source가 연결 {GUID}을 (를) 가져오지 못했습니다. 오류 메시지: 관리 id를 사용 하는 경우 ' NT AUTHORITY\ANONYMOUS LOGON ' 사용자에 대 한 로그인이 실패 했습니다.

*ConnectUsingManagedIdentity* 매개 변수가 **True**인 경우 **Active Directory 암호 인증** 으로 연결 관리자의 인증 방법을 구성 하지 않아야 합니다. 대신 *ConnectUsingManagedIdentity* 가 설정 된 경우 무시 되는 **SQL 인증** 으로 구성할 수 있습니다.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>오류 메시지: "0xC020801F at ..., OData 원본 [...]: 런타임 연결 관리자에서 관리 되는 연결을 가져올 수 없습니다."

하나는 TLS (전송 계층 보안)가 OData 원본에 필요한 SSIS 통합 런타임에 사용 하도록 설정 되지 않았기 때문입니다. SSIS integration runtime에서 설치 프로그램 사용자 지정을 사용 하 여 TLS를 사용 하도록 설정할 수 있습니다. 자세한 내용은 [ssis에서 Project Online Odata에 연결할 수 없음](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) 및 [Azure ssis 통합 런타임에 대 한 설정 사용자 지정](how-to-configure-azure-ssis-ir-custom-setup.md)에서 확인할 수 있습니다.

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>오류 메시지: "작업 guid를 사용 하 여 준비 작업 요청 ... 오류 발생 후 실패: 다음 오류 메시지와 함께 준비 작업을 디스패치하지 못했습니다. Microsoft.sqlserver.management.integrationservices. "데이터 프록시를 로드 하지 못했습니다."

Azure SSIS 통합 런타임이 자체 호스팅 통합 런타임을 사용 하 여 구성 되어 있는지 확인 합니다. 자세한 내용은 [ADF의 Azure-SSIS IR에 대 한 프록시로 자체 호스팅 IR 구성](self-hosted-integration-runtime-proxy-ssis.md)에서 찾을 수 있습니다.

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>오류 메시지: "준비 작업 상태: 실패 준비 작업 오류: ErrorCode: 2010, ErrorMessage: 자체 호스팅 Integration Runtime ... 오프 라인 상태 "

자체 호스팅 integration runtime이 설치 및 시작 되었는지 확인 합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](create-self-hosted-integration-runtime.md) 에서 찾을 수 있습니다.

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>오류 메시지: "준비 작업 오류: ErrorCode: 2906, ErrorMessage: 패키지를 실행 하지 못했습니다., 출력: {" OperationErrorMessages ":" 오류: 요청 된 OLE DB 공급자 ... 이 등록 되지 않은 경우 64 비트 드라이버가 설치 되지 않은 경우 32 비트 모드에서 패키지를 실행 합니다.

패키지의 OLE DB 커넥터에서 사용 하는 해당 공급자가 자체 호스팅 통합 런타임 컴퓨터에 제대로 설치 되어 있는지 확인 합니다. 자세한 내용은 [ADF의 Azure-SSIS IR에 대 한 프록시로 자체 호스팅 IR 구성](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir) 에서 찾을 수 있습니다.

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>오류 메시지: "준비 작업 오류: ErrorCode: 2906, ErrorMessage: 패키지를 실행 하지 못했습니다., 출력: {" OperationErrorMessages ":" 오류: FileLoadException: 파일이 나 어셈블리 ' Windowsazure.servicebus, Version = ...,을 (를) 로드할 수 없습니다. Culture = 중립, PublicKeyToken = 31bf3856ad364e35 ' 또는 해당 종속성 중 하나입니다. 찾은 어셈블리의 매니페스트 정의가 어셈블리 참조와 일치 하지 않습니다. ' ..."

자체 호스팅 통합 런타임이 제대로 설치 또는 업그레이드 되지 않는 한 가지 잠재적인 원인이 있습니다. 최신 자체 호스팅 통합 런타임을 다운로드 하 고 다시 설치 하는 것이 좋습니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](create-self-hosted-integration-runtime.md#installation-best-practices) 에서 찾을 수 있습니다.

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>오류 메시지: "메타 데이터를 요청할 때 연결이 필요 합니다. 오프 라인으로 작업 하는 경우 SSIS 메뉴에서 오프 라인으로 작업을 선택 취소 하 여 연결을 설정 합니다.

* 잠재적인 원인 & 권장 조치:
  * "구성 요소에서 연결 관리자를 사용 하 여 연결 관리자의 연결 관리자 사용을 지원 하지 않습니다." 라는 경고 메시지가 나타나면 "ConnectByProxy"를 아직 지원 하지 않는 구성 요소에서 연결 관리자를 사용 하는 것을 의미 합니다. 지원 되는 구성 요소는 [ADF의 Azure-SSIS IR에 대 한 프록시로 자체 호스팅 IR 구성](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy) 에서 찾을 수 있습니다.
  * 실행 로그는 [SSMS 보고서](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) 또는 SSIS 패키지 실행 작업에서 지정한 로그 폴더에서 찾을 수 있습니다.
  * vNet은 다른 방법으로 온-프레미스 데이터에 액세스 하는 데도 사용할 수 있습니다. 자세한 내용은 [AZURE SSIS integration runtime을 가상 네트워크에 가입](join-azure-ssis-integration-runtime-virtual-network.md) 에서 찾을 수 있습니다.

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>오류 메시지: "준비 작업 상태: 실패 준비 태스크 오류: ErrorCode: 2906, ErrorMessage: 패키지를 실행 하지 못했습니다., 출력: {"OperationErrorMessages": "SSIS Executor 종료 코드:-1. \ n", "LogLocation": "...\\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "Executionlog": ..., "durationInQueue": {"integrationRuntimeQueue": ...}} "

자체 호스팅 integration C++ runtime 컴퓨터에 Visual runtime이 설치 되어 있는지 확인 합니다. 자세한 내용은 [ADF의 Azure-SSIS IR에 대 한 프록시로 자체 호스팅 IR 구성](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir) 에서 찾을 수 있습니다.

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>여러 패키지 실행이 예기치 않게 트리거됨

* 잠재적인 원인 & 권장 조치:
  * ADF 저장 프로시저 작업 또는 조회 작업은 SSIS 패키지 실행을 트리거하는 데 사용 됩니다. T-sql 명령은 일시적인 문제를 발생 시킬 수 있으며 여러 패키지를 실행 하는 다시 실행을 트리거할 수 있습니다.
  * 사용자가 작업에서 다시 시도 횟수를 설정 하지 않으면 패키지 실행이 다시 실행 되지 않도록 하는 ExecuteSSISPackage 작업을 대신 사용 합니다. 자세한 내용은 [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 를 참조 하십시오.
  * 실행이 이미 트리거 되었는지 확인 하 여 다시 실행할 수 있도록 t-sql 명령을 구체화 합니다.

### <a name="package-execution-takes-too-long"></a>패키지 실행 시간이 너무 오래 걸립니다.

가능한 원인 및 권장 되는 작업은 다음과 같습니다.

* SSIS 통합 런타임에 너무 많은 패키지 실행이 예약 되었습니다. 이러한 모든 실행은 큐에서 차례로 대기 합니다.
  * 다음 수식을 사용 하 여 최대값을 결정 합니다.

    IR 당 최대 병렬 실행 수 = 노드 수 * 노드당 최대 병렬 실행 수
  * 노드당 노드 수와 최대 병렬 실행을 설정 하는 방법에 대 한 자세한 내용은 [Azure Data Factory에서 AZURE SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md)를 참조 하세요.
* SSIS 통합 런타임이 중지 되었거나 비정상 상태입니다. SSIS 통합 런타임 상태 및 오류를 확인 하는 방법에 대 한 자세한 내용은 [AZURE ssis integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)을 참조 하세요.

[일반] 탭의 [일반 **] 탭에서** 속성 ![설정 하 고](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)에 시간 제한을 설정 하는 것이 좋습니다.

### <a name="poor-performance-in-package-execution"></a>패키지 실행의 성능 저하

다음과 같이 작업해 보세요.

* SSIS 통합 런타임이 데이터 원본 및 대상과 동일한 지역에 있는지 확인 합니다.

* 패키지 실행의 로깅 수준을 **성능** 으로 설정 하 여 실행의 각 구성 요소에 대 한 기간 정보를 수집 합니다. 자세한 내용은 [Integration Services (SSIS) 로깅](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)을 참조 하세요.

* Azure Portal IR 노드 성능을 확인 합니다.
  * SSIS 통합 런타임을 모니터링 하는 방법에 대 한 자세한 내용은 [AZURE ssis integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)을 참조 하세요.
  * Azure Portal에서 데이터 팩터리의 메트릭을 확인 하 여 SSIS 통합 런타임에 대 한 CPU/메모리 기록을 찾을 수 있습니다.
    ![SSIS 통합 런타임의 메트릭을 모니터링](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
