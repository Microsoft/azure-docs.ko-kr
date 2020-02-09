---
title: Azure Data Factory 문제 해결 | Microsoft Docs
description: Azure Data Factory에서 외부 제어 작업의 문제를 해결 하는 방법을 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 2ae0f3033b88b3229d3dbef35c8bc9a32510c00e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972339"
---
# <a name="troubleshoot-azure-data-factory"></a>문제 해결 Azure Data Factory

이 문서에서는 Azure Data Factory의 외부 제어 작업에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="connector-and-copy-activity"></a>커넥터 및 복사 작업

커넥터 문제 (예: 복사 작업을 사용 하 여 오류 발생)의 경우 [커넥터 Azure Data Factory 문제 해결](connector-troubleshoot-guide.md)을 참조 하세요.
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>오류 코드: 3200

- **메시지**: 오류 403.

- **원인**: `The Databricks access token has expired.`

- **권장 사항**: 기본적으로 Azure Databricks 액세스 토큰은 90 일 동안 유효 합니다. 새 토큰을 만들고 연결 된 서비스를 업데이트 합니다.


### <a name="error-code--3201"></a>오류 코드: 3201

- **메시지**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **원인**: `Bad authoring: Notebook path not specified correctly.`

- **권장 사항**: Databricks 활동에서 노트북 경로를 지정 합니다.

<br/>  

- **메시지**: `Cluster... does not exist.`

- **원인**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **권장 사항**: Databricks 클러스터가 있는지 확인 합니다.

<br/>  

- **메시지**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **원인**: `Bad authoring.`

- **권장 사항**: 작업 영역 주소 지정 구성표의 절대 경로를 지정 하거나 Databricks 파일 시스템에 저장 된 파일에 대해 `dbfs:/folder/subfolder/foo.py` 합니다.

<br/>  

- **메시지**: `{0} LinkedService should have domain and accessToken as required properties.`

- **원인**: `Bad authoring.`

- **권장 사항**: [연결 된 서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인 합니다.

<br/>  

- **메시지**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **원인**: `Bad authoring.`

- **권장 사항**: [연결 된 서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인 합니다.

<br/>  

- **메시지**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **원인**: `Bad authoring.`

- **권장 사항**: 오류 메시지를 참조 하십시오.

<br/>

### <a name="error-code--3202"></a>오류 코드: 3202

- **메시지**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **원인**: `Too many Databricks runs in an hour.`

- **권장 사항**: 작업 생성 속도로이 Databricks 작업 영역을 사용 하는 모든 파이프라인을 선택 합니다.  파이프라인이 너무 많은 Databricks를 시작한 경우에는 일부 파이프라인을 새 작업 영역으로 마이그레이션합니다.

<br/>  

- **메시지**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **원인**: `Authoring error: No value provided for the parameter.`

- **권장 사항**: 파이프라인 JSON을 검사 하 고 baseparameters 노트북의 모든 매개 변수가 비어 있지 않은 값을 지정 하는지 확인 합니다.

<br/>  

- **메시지**: `User: `SimpleUserContext {userId = ..., name =user@company.com, orgId = ...}` is not   authorized to access cluster.`

- **원인**: 액세스 토큰을 생성 한 사용자가 연결 된 서비스에 지정 된 Databricks 클러스터에 액세스할 수 없습니다.

- **권장 사항**: 사용자에 게 작업 영역에서 필요한 권한이 있는지 확인 합니다.


### <a name="error-code--3203"></a>오류 코드: 3203

- **메시지**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **원인**: 클러스터가 종료 되었습니다. 대화형 클러스터의 경우 경합 상태가 될 수 있습니다.

- **권장 사항**:이 오류를 방지 하는 가장 좋은 방법은 작업 클러스터를 사용 하는 것입니다.


### <a name="error-code--3204"></a>오류 코드: 3204

- **메시지**: `Job execution failed.`

- **원인**: 오류 메시지는 예기치 않은 클러스터 상태 또는 특정 활동과 같은 다양 한 문제를 표시 합니다. 대부분 오류 메시지가 표시 되지 않는 경우가 많습니다.

- **권장 사항**: 해당 없음
            

## <a name="azure-data-lake-analytics"></a>Azure 데이터 레이크 분석

다음 표는 U-SQL에 적용 됩니다.
      
### <a name="error-code--2709"></a>오류 코드: 2709

- **메시지**: `The access token is from the wrong tenant.`

- **원인**: 잘못 된 Azure Active Directory (Azure AD) 테넌트가 있습니다.

- **권장 사항**: 잘못 된 Azure Active Directory (Azure AD) 테넌트.

<br/>

- **메시지**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **원인**:이 오류는 Data Lake Analytics에 대한 제한으로 인해 발생 합니다.

- **권장 사항**: 작업에 대한 Data Factory 트리거 및 동시성 설정을 변경 하 여 Data Lake Analytics 전송 된 작업 수를 줄입니다. 또는 Data Lake Analytics에 대한 제한을 늘립니다.

<br/>  

- **메시지**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **원인**:이 오류는 Data Lake Analytics에 대한 제한으로 인해 발생 합니다.

- **권장 사항**: 작업에 대한 Data Factory 트리거 및 동시성 설정을 변경 하 여 Data Lake Analytics 전송 된 작업 수를 줄입니다. 또는 Data Lake Analytics에 대한 제한을 늘립니다.


### <a name="error-code--2705"></a>오류 코드: 2705

- **메시지**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소에 있는 파일에 액세스할 수 없습니다.

- **권장 사항**: Data Lake Analytics 작업을 위해 사용자가 제공 하는 서비스 주체 또는 인증서가 루트 폴더에서 Data Lake Analytics 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인 합니다.


### <a name="error-code--2711"></a>오류 코드: 2711

- **메시지**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소에 있는 파일에 액세스할 수 없습니다.

- **권장 사항**: Data Lake Analytics 작업을 위해 사용자가 제공 하는 서비스 주체 또는 인증서가 루트 폴더에서 Data Lake Analytics 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인 합니다.

<br/>  

- **메시지**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **원인**: U-SQL 파일의 경로가 잘못 되었거나 연결 된 서비스 자격 증명에 액세스 권한이 없습니다.

- **권장 사항**: 연결 된 서비스에서 제공 하는 경로 및 자격 증명을 확인 합니다.


### <a name="error-code--2704"></a>오류 코드: 2704

- **메시지**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소에 있는 파일에 액세스할 수 없습니다.

- **권장 사항**: Data Lake Analytics 작업을 위해 사용자가 제공 하는 서비스 주체 또는 인증서가 루트 폴더에서 Data Lake Analytics 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인 합니다.


### <a name="error-code--2707"></a>오류 코드: 2707

- **메시지**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **원인**: 연결 된 서비스의 Data Lake Analytics 계정이 잘못 되었습니다.

- **권장 사항**: 올바른 계정이 제공 되는지 확인 합니다.


### <a name="error-code--2703"></a>오류 코드: 2703

- **메시지**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **원인**: Data Lake Analytics에서 오류가 발생 했습니다.

- **권장 사항**: 예제와 같은 오류는 작업이 Data Lake Analytics 전송 되었고 스크립트가 실패 한 것을 의미 합니다. Data Lake Analytics를 조사 합니다. 포털에서 Data Lake Analytics 계정으로 이동 하 고 Data Factory 활동 실행 ID (파이프라인 실행 ID 아님)를 사용 하 여 작업을 찾습니다. 작업은 오류에 대한 자세한 정보를 제공 하 고 문제를 해결 하는 데 도움이 됩니다. 해결 방법이 명확 하지 않은 경우 Data Lake Analytics 지원 팀에 문의 하 여 계정 이름 및 작업 ID를 포함 하는 작업 URL을 제공 합니다.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>오류 코드: 3602

- **메시지**: `Invalid HttpMethod: '%method;'.`

- **원인**: 활동 페이로드에 지정 된 Http 메서드는 Azure Function 활동에서 지원 되지 않습니다.

- **권장 사항**: 지원 되는 Http 메서드는 PUT, POST, GET, DELETE, OPTIONS, HEAD 및 TRACE입니다.


### <a name="error-code--3603"></a>오류 코드: 3603

- **메시지**: `Response Content is not a valid JObject.`

- **원인**: 호출 된 Azure 함수에서 응답에 JSON 페이로드를 반환 하지 않았습니다. ADF Azure 함수 활동은 JSON 응답 콘텐츠만 지원 합니다.

- **권장 사항**: 유효한 JSON 페이로드를 반환 하도록 Azure 함수를 업데이트 합니다 C# . 예를 들어 함수에서 (Actionresult) 새 okobjectresult ("{\"Id\":\"123\"}")를 반환할 수 있습니다.


### <a name="error-code--3606"></a>오류 코드: 3606

- **메시지**: Azure 함수 작업에서 함수 키가 누락 되었습니다.

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AZUREFUNCTION 활동 JSON 정의에 이름이 ' functionkey ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3607"></a>오류 코드: 3607

- **메시지**: `Azure function activity missing function name.`

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AZUREFUNCTION 활동 JSON 정의에 이름이 ' functionName ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3608"></a>오류 코드: 3608

- **메시지**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **원인**: 활동 정의의 Azure 함수 정보가 잘못 되었을 수 있습니다.

- **권장 사항**: azure 함수 세부 정보를 수정 하 고 다시 시도 하세요.


### <a name="error-code--3609"></a>오류 코드: 3609

- **메시지**: `Azure function activity missing functionAppUrl.`

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AZUREFUNCTION 활동 JSON 정의에 이름이 ' functionAppUrl ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3610"></a>오류 코드: 3610

- **메시지**: `There was an error while calling endpoint.`

- **원인**: 함수 URL이 잘못 되었을 수 있습니다.

- **권장 사항**: 작업 JSON에서 ' functionAppUrl '의 값이 올바른지 확인 하 고 다시 시도 하세요.


### <a name="error-code--3611"></a>오류 코드: 3611

- **메시지**: `Azure function activity missing Method in JSON.`

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AZUREFUNCTION 활동 JSON 정의에 이름이 ' method ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3612"></a>오류 코드: 3612

- **메시지**: `Azure function activity missing LinkedService definition in JSON.`

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AZUREFUNCTION 활동 JSON 정의에 연결 된 서비스 정보가 있는지 확인 하세요.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>오류 코드: 4101

- **메시지**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **원인**: '% propertyName; ' 속성의 형식이 잘못 되었거나 정의가 누락 되었습니다.

- **권장 사항**: '% activityName; ' 활동에 올바른 데이터로 정의 된 '% propertyName; ' 속성이 있는지 확인 하세요.


### <a name="error-code--4110"></a>오류 코드: 4110

- **메시지**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **원인**: AzureMLExecutePipeline 작업 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AZUREMLEXECUTEPIPELINE 작업 JSON 정의에 연결 된 서비스 정보가 있는지 확인 하세요.


### <a name="error-code--4111"></a>오류 코드: 4111

- **메시지**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **원인**: 잘못 된 작업 정의입니다.

- **권장 사항**: 입력 AZUREMLEXECUTEPIPELINE 작업 JSON 정의에 올바른 연결 된 서비스 정보가 있는지 확인 하세요.


### <a name="error-code--4112"></a>오류 코드: 4112

- **메시지**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **원인**: '% propertyName; ' 속성의 형식이 잘못 되었거나 정의가 누락 되었습니다.

- **권장 사항**: 연결 된 서비스에 올바른 데이터로 정의 된 '% propertyName; ' 속성이 있는지 확인 하세요.


### <a name="error-code--4121"></a>오류 코드: 4121

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure Machine Learning 액세스 하는 데 사용 된 자격 증명이 만료 되었습니다.

- **권장 사항**: 자격 증명이 올바른지 확인 한 후 다시 시도 하세요.


### <a name="error-code--4122"></a>오류 코드: 4122

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: 연결 된 Azure Machine Learning 서비스에서 제공 된 자격 증명이 잘못 되었거나 작업에 대한 권한이 없습니다.

- **권장 사항**: 연결 된 서비스의 자격 증명이 유효 하 고 Azure Machine Learning에 액세스할 수 있는 권한이 있는지 확인 하세요.


### <a name="error-code--4123"></a>오류 코드: 4123

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: pipelineParameters와 같은 활동의 속성은 Azure ML 파이프라인에 대해 유효 하지 않습니다.

- **권장 사항**: 활동 속성의 값을 확인 하 여 연결 된 서비스에 지정 된 게시 된 Azure ML 파이프라인의 예상 페이로드에 일치 하는지 확인 하세요.


### <a name="error-code--4124"></a>오류 코드: 4124

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: 게시 된 Azure ML 파이프라인 엔드포인트이 없습니다.

- **권장 사항**: Azure Machine Learning에 연결 된 서비스에 지정 된 게시 된 Azure Machine Learning 파이프라인 엔드포인트이 있는지 확인 하세요.


### <a name="error-code--4125"></a>오류 코드: 4125

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure Machine Learning에서 서버 오류가 발생 했습니다.

- **권장 사항**: 나중에 다시 시도 하세요. 문제가 계속 되 면 Azure Machine Learning 팀에 도움을 요청 하세요.


### <a name="error-code--4126"></a>오류 코드: 4126

- **메시지**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **원인**: Azure ML 파이프라인 실행이 실패 했습니다.

- **권장 사항**: 자세한 오류 로그를 확인 하 고 ML 파이프라인을 수정 하려면 Azure Machine Learning를 확인 하세요.



## <a name="common"></a>일반

### <a name="error-code--2103"></a>오류 코드: 2103

- **메시지**: `Please provide value for the required property '%propertyName;'.`

- **원인**: 속성의 값이 제공 되지 않지만 시나리오에 필요 합니다.

- **권장 사항**: 메시지의 값을 제공 하 고 다시 시도 하세요.


### <a name="error-code--2104"></a>오류 코드: 2104

- **메시지**: `The type of the property '%propertyName;' is incorrect.`

- **원인**: 제공 된 속성의 형식이 예상과 동일 하지 않습니다.

- **권장 사항**: 속성 유형을 수정 하 고 다시 시도 하세요.


### <a name="error-code--2105"></a>오류 코드: 2105

- **메시지**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **원인**: 속성 값이 잘못 되었거나 필요한 형식이 아닙니다.

- **권장 사항**: 속성에 대한 설명서를 조회 하 여 제공 된 값이 필요한 형식 및 형식 인지 확인 하세요.


### <a name="error-code--2106"></a>오류 코드: 2106

- **메시지**: `The storage connection string is invalid. %errorMessage;`

- **원인**: 저장소에 대한 연결 문자열이 잘못 되었거나 형식이 잘못 되었습니다.

- **권장 사항**: Azure Portal로 이동 하 여 저장소를 찾고 연결 문자열을 복사 하 여 연결 된 서비스에 붙여넣은 후 다시 시도 하세요.


### <a name="error-code--2108"></a>오류 코드: 2108

- **메시지**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 초과와 같은 기본 문제로 인해 요청이 실패 했습니다.

- **권장 사항**: Fiddler/postman을 사용 하 여 요청의 유효성을 검사 합니다.


### <a name="error-code--2110"></a>오류 코드: 2110

- **메시지**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **원인**: 활동에 지정 된 연결 된 서비스가 잘못 되었습니다.

- **권장 사항**: 연결 된 서비스 유형이 작업에 대해 지원 되는 유형 중 하나 인지 확인 하세요. 예를 들어 HDI 활동의 경우 연결 된 서비스 유형은 HDInsight 또는 HDInsightOnDemand 일 수 있습니다.


### <a name="error-code--2111"></a>오류 코드: 2111

- **메시지**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **원인**: 제공 된 속성의 형식이 예상과 동일 하지 않습니다.

- **권장 사항**: 속성 유형을 수정 하 고 다시 시도 하세요.


### <a name="error-code--2112"></a>오류 코드: 2112

- **메시지**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **원인**: 클라우드 유형이 지원 되지 않거나 endpointsuffix의 저장소에 대해 확인할 수 없습니다.

- **권장 사항**: 다른 클라우드의 저장소를 사용 하 여 다시 시도 하세요.


### <a name="error-code--2128"></a>오류 코드: 2128

- **메시지**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 초과

- **권장 사항**: 적중 하려는 엔드포인트이 요청에 응답 하는지 확인 합니다. Fiddler/Postman과 같은 도구를 사용할 수 있습니다.



## <a name="custom"></a>맞춤형 서비스

다음 표는 Azure Batch에 적용 됩니다.
      
### <a name="error-code--2500"></a>오류 코드: 2500

- **메시지**: `Hit unexpected exception and execution failed.`

- **원인**: `Can't launch command, or the program returned an error code.`

- **권장 사항**: 실행 파일이 있는지 확인 합니다. 프로그램이 시작 되 면 *stdout* 및 *stderr* 이 저장소 계정에 업로드 되었는지 확인 합니다. 디버깅을 위해 코드에서 copious 로그를 내보내는 것이 좋습니다.


### <a name="error-code--2501"></a>오류 코드: 2501

- **메시지**: `Cannot access user batch account; please check batch account settings.`

- **원인**: 일괄 처리 액세스 키 또는 풀 이름이 잘못 되었습니다.

- **권장 사항**: 연결 된 서비스의 풀 이름 및 Batch 액세스 키를 확인 합니다.


### <a name="error-code--2502"></a>오류 코드: 2502

- **메시지**: `Cannot access user storage account; please check storage account settings.`

- **원인**: 저장소 계정 이름 또는 액세스 키가 잘못 되었습니다.

- **권장 사항**: 연결 된 서비스에서 저장소 계정 이름 및 액세스 키를 확인 합니다.


### <a name="error-code--2504"></a>오류 코드: 2504

- **메시지**: `Operation returned an invalid status code 'BadRequest'.`

- **원인**: 사용자 지정 활동의 folderPath에 파일이 너무 많습니다. ResourceFiles의 총 크기는 32768 자를 초과할 수 없습니다.

- **권장 사항**: 불필요 한 파일을 제거 합니다. 또는 압축을 풀고 압축 풀기 명령을 추가 하 여 압축을 풉니다. 예를 들어 `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`를 사용 합니다.


### <a name="error-code--2505"></a>오류 코드: 2505

- **메시지**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **원인**: 사용자 지정 작업은 액세스 키를 사용 하는 저장소 계정만 지원 합니다.

- **권장 사항**: 오류 설명을 참조 하십시오.


### <a name="error-code--2507"></a>오류 코드: 2507

- **메시지**: `The folder path does not exist or is empty: ...`

- **원인**: 저장소 계정에 지정 된 경로의 파일이 없습니다.

- **권장 사항**: 폴더 경로는 실행 하려는 실행 파일을 포함 해야 합니다.


### <a name="error-code--2508"></a>오류 코드: 2508

- **메시지**: `There are duplicate files in the resource folder.`

- **원인**: 이름이 같은 여러 파일이 folderPath의 다른 하위 폴더에 있습니다.

- **권장 사항**: 사용자 지정 활동은 folderPath에서 폴더 구조를 평면화 합니다.  폴더 구조를 유지 해야 하는 경우 파일을 압축 하 고 압축 풀기 명령을 사용 하 여 Azure Batch에서 압축을 풉니다. 예를 들어 `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`를 사용 합니다.


### <a name="error-code--2509"></a>오류 코드: 2509

- **메시지**: `Batch   url ... is invalid; it must be in Uri format.`

- **원인**: 일괄 처리 url은 `https://mybatchaccount.eastus.batch.azure.com`와 비슷해야 합니다.

- **권장 사항**: 오류 설명을 참조 하십시오.


### <a name="error-code--2510"></a>오류 코드: 2510

- **메시지**: `An   error occurred while sending the request.`

- **원인**: 일괄 처리 URL이 잘못 되었습니다.

- **권장 사항**: 일괄 처리 URL을 확인 합니다.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>오류 코드: 200

- **메시지**: `Unexpected error happened: '%error;'.`

- **원인**: 내부 서비스 문제가 있습니다.

- **권장 사항**: 추가 지원이 필요 하면 ADF 지원에 문의 하세요.


### <a name="error-code--201"></a>오류 코드: 201

- **메시지**: `JobType %jobType; is not found.`

- **원인**: ADF에서 지원 하지 않는 새 작업 유형이 있습니다.

- **권장 사항**: 추가 지원이 필요 하면 ADF 지원 팀에 문의 하세요.


### <a name="error-code--202"></a>오류 코드: 202

- **메시지**: `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 오류 메시지에 문제가 발생 한 세부 정보가 표시 됩니다.

- **권장 사항**: 문제를 해결 하는 데 도움이 되는 오류 메시지입니다. 충분 한 정보가 없는 경우 ADF 지원에 문의 하 여 추가 도움말을 확인 하세요.


### <a name="error-code--203"></a>오류 코드: 203

- **메시지**: `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 오류 메시지에 문제가 발생 한 세부 정보가 표시 됩니다.

- **권장 사항**: 문제를 해결 하는 데 도움이 되는 오류 메시지입니다. 충분 한 정보가 없는 경우 ADF 지원에 문의 하 여 추가 도움말을 확인 하세요.


### <a name="error-code--204"></a>오류 코드: 204

- **메시지**: `The resumption token is missing for runId '%runId;'.`

- **원인**: 내부 서비스 문제가 있습니다.

- **권장 사항**: 추가 지원이 필요 하면 ADF 지원에 문의 하세요.


### <a name="error-code--205"></a>오류 코드: 205

- **메시지**: `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **원인**: hdi on 주문형 클러스터를 만드는 동안 오류가 발생 했습니다.

- **권장 사항**: 추가 지원이 필요 하면 ADF 지원에 문의 하세요.


### <a name="error-code--206"></a>오류 코드: 206

- **메시지**: `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **원인**:이를 일으킨 서비스에 내부 문제가 있습니다.

- **권장 사항**: 일시적인 문제일 수 있습니다. 작업을 다시 시도 하세요. 문제가 계속 되 면 ADF 지원에 문의 하 여 추가 지원을 요청 하세요.


### <a name="error-code--207"></a>오류 코드: 207

- **메시지**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **원인**: 기본 저장소 계정에서 영역을 확인 하는 동안 내부 오류가 발생 했습니다.

- **권장 사항**: 다른 저장소를 사용해 보세요. 이 솔루션을 사용할 수 없는 경우에는 ADF 지원 팀에 도움을 요청 하세요.


### <a name="error-code--208"></a>오류 코드: 208

- **메시지**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **원인**: 서비스 주체를 읽거나 MSI 인증을 인스턴스화하는 동안 내부 오류가 발생 했습니다.

- **권장 사항**: 제공 된 구독에서 HDInsight 클러스터를 만들 수 있는 권한이 있는 서비스 주체를 제공 하 고 다시 시도 하세요. 이 솔루션을 사용할 수 없는 경우에는 ADF 지원 팀에 도움을 요청 하세요.


### <a name="error-code--2300"></a>오류 코드: 2300

- **메시지**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **원인**: 오류 메시지에 ' 원격 이름을 확인할 수 없습니다. '와 유사한 메시지가 포함 된 경우 제공 된 클러스터 URI가 잘못 된 것일 수 있습니다.


- **권장 사항**: 클러스터가 삭제 되지 않았고 제공 된 URI가 올바른지 확인 합니다. 브라우저에서 URI를 열 때 Ambari UI가 표시 되어야 합니다. 클러스터가 가상 네트워크에 있는 경우 URI는 개인 URI 여야 합니다. 이를 열려면 동일한 가상 네트워크의 일부인 VM을 사용 합니다. 자세한 내용은 [this](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)를 참조하세요.
                  

<br>

- **원인**: 오류 메시지에 ' a 태스크가 취소 되었습니다. '와 유사한 메시지가 포함 된 경우 작업 전송 시간이 초과 되었음을 의미 합니다.

- **권장 사항**: 문제는 일반적인 HDInsight 연결 또는 네트워크 연결 중 하나일 수 있습니다. 먼저 모든 브라우저에서 HDInsight Ambari UI를 사용할 수 있는지 확인 합니다. 자격 증명이 여전히 유효한 지 확인 합니다. 자체 호스팅 IR (통합 런타임)을 사용 하는 경우 자체 호스팅 IR이 설치 된 VM 또는 컴퓨터에서이 작업을 수행 해야 합니다. 그런 다음 Data Factory에서 작업을 다시 제출 하십시오. 그래도 오류가 계속 발생 하면 Data Factory 팀에 지원을 요청 하세요.

<br>

- **원인**: 오류 메시지에 ' 사용자 관리자가 Ambari에서 잠겼습니다. ' 또는 ' 권한이 없음: Ambari 사용자 이름 또는 암호가 잘못 되었습니다. '와 유사한 메시지가 포함 된 경우 HDInsight의 자격 증명이 잘못 되었거나 만료 된 것을 의미 합니다.

- **권장 사항**: 자격 증명을 수정 하 고 연결 된 서비스를 다시 배포 합니다. 먼저 브라우저에서 클러스터 URI를 열고 로그인을 시도 하 여 HDInsight에서 자격 증명이 작동 하는지 확인 합니다. 자격 증명이 작동 하지 않는 경우 Azure Portal에서 자격 증명을 다시 설정할 수 있습니다.

<br>

- **원인**: 오류 메시지에 ' 502-게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못 된 응답을 받았습니다. '와 유사한 메시지가 포함 된 경우 HDInsight 서비스에서이 오류가 반환 됩니다.


- **권장 사항**: Azure HDInsight 문제 해결 설명서 (예: https://hdinsight.github.io/ambari/ambari-ui-502-error.html , https://hdinsight.github.io/spark/spark-thriftserver-errors.html , https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502 를 참조 하세요.
                  

<br>

- **원인**: 오류 메시지에 ' 전송 작업 요청을 서비스 하지 못했습니다. ' 또는 ' 큐 루트를 너무 많이 templeton 서비스를 사용 중입니다. joblauncher 관리자에 이미 500 응용 프로그램이 있으므로 응용 프로그램 제출을 허용할 수 없습니다 .이는 동시에 HDInsight에 너무 많은 작업이 전송 됨을 의미 합니다.

- **권장 사항**: HDInsight에 제출 되는 동시 작업 수를 제한 하는 것이 좋습니다. 동일한 작업을 통해 작업을 전송 하는 경우 Data Factory 작업 동시성을 참조 하세요. 동시 파이프라인 실행이 시간이 지남에 따라 분산 되도록 트리거를 변경 합니다. Templeton를 조정 하려면 HDInsight 설명서를 참조 하십시오. parallellism에서 제안 하는 것 처럼.


### <a name="error-code--2301"></a>오류 코드: 2301

- **메시지**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **원인**: HDInsight 클러스터 또는 서비스에 문제가 있습니다.


- **권장 사항**:이 오류는 ADF가 실행 중인 작업의 상태를 가져오려고 할 때 HDInsight 클러스터에서 응답을 받지 못하는 경우에 발생 합니다. 클러스터 자체에서 문제가 발생 하거나 HDInsight 서비스의 작동이 중단 될 수 있습니다. https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide 에서 HDInsight 문제 해결 설명서를 참조 하거나 추가 지원을 요청 하세요.
                


### <a name="error-code--2302"></a>오류 코드: 2302

- **메시지**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **원인**: 작업이 hdi 클러스터에 제출 되어 실패 했습니다.

- **권장 사항**: 작업 실행 출력의 Yarn logs 링크를 따르고 hdi 출력에서 오류를 찾습니다. 필요한 경우 HDInsight 팀에 지원을 요청 하세요.


### <a name="error-code--2303"></a>오류 코드: 2303

- **메시지**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **원인**: 작업이 hdi 클러스터에 제출 되어 실패 했습니다.

- **권장 사항**: 작업 실행 출력의 Yarn logs 링크를 따르고 hdi 출력에서 오류를 찾습니다. 다시 시도 하거나 필요한 경우 HDInsight 팀에 지원을 요청 하세요.


### <a name="error-code--2304"></a>오류 코드: 2304

- **메시지**: `MSI authentication is not supported on storages for HDI activities.`

- **원인**: hdi 연결 된 서비스 또는 hdi 활동에 사용 된 저장소 연결 된 서비스가 MSI 인증으로 구성 되어 있으며이는 지원 되지 않습니다.

- **권장 사항**: hdi 연결 된 서비스 또는 hdi 활동에서 사용 되는 저장소 계정에 대한 전체 연결 문자열을 제공 하세요.


### <a name="error-code--2305"></a>오류 코드: 2305

- **메시지**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **원인**: hdi 클러스터에 대한 연결 정보가 잘못 되었거나 제공 된 사용자에 게 필요한 작업을 수행할 수 있는 권한이 없거나, HDInsight 서비스에서 ADF의 요청에 응답 하는 동안 문제가 발생 했습니다.

- **권장 사항**: 사용자 정보가 올바른지 확인 하세요. 또한 HDI 클러스터의 Ambari UI가 자체 호스팅 IR의 경우 IR이 설치 된 VM의 브라우저에서 열 수 있는지 또는 Azure IR 경우 모든 컴퓨터에서 열 수 있는지 확인 합니다.


### <a name="error-code--2306"></a>오류 코드: 2306

- **메시지**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **원인**: 스크립트 작업에 제공 된 json이 잘못 되었습니다.


- **권장 사항**: 오류 메시지는 문제를 식별 하는 데 도움이 됩니다. Json 구성을 수정 하 고 다시 시도 하세요. 자세한 내용은 https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service 를 확인 하세요.
                


### <a name="error-code--2310"></a>오류 코드: 2310

- **메시지**: `Failed to submit Spark job. Error: '%message;'`

- **원인**: ADF가 Livy API (Livy/batch)를 사용 하 여 Spark 클러스터에서 일괄 처리를 만들려고 했지만 오류가 수신 되었습니다.

- **권장 사항**: 오류 메시지를 따라 문제를 해결 하십시오. 정보를 확인 하기에 충분 하지 않은 경우 HDI 팀에 문의 하 고 ADF 모니터링 페이지의 작업 실행 출력에서 찾을 수 있는 일괄 처리 ID 및 작업 ID를 제공 합니다.


### <a name="error-code--2312"></a>오류 코드: 2312

- **메시지**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **원인**: HDInsight Spark 클러스터에서 작업이 실패 했습니다.

- **권장 사항**: HDInsight Spark 클러스터에서 실행 문제를 해결 하려면 ADF 모니터링에서 작업 출력 실행 페이지의 링크를 따르세요. 추가 지원이 필요 하면 HDInsight 지원 팀에 문의 하세요.


### <a name="error-code--2313"></a>오류 코드: 2313

- **메시지**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **원인**: HDInsight Spark 클러스터에서 일괄 처리가 삭제 되었습니다.

- **권장 사항**: HDInsight Spark 클러스터에서 일괄 처리 문제를 해결 합니다. 추가 지원이 필요 하면 HDInsight 지원에 문의 하세요. 


### <a name="error-code--2328"></a>오류 코드: 2328

- **메시지**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **원인**: `The error message should show the details of what went wrong.`

- **권장 사항**: 문제를 해결 하는 데 도움이 되는 오류 메시지입니다.


### <a name="error-code--2329"></a>오류 코드: 2329

- **메시지**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 오류 메시지에 문제가 발생 한 세부 정보가 표시 됩니다.

- **권장 사항**: 문제를 해결 하는 데 도움이 되는 오류 메시지입니다.


### <a name="error-code--2331"></a>오류 코드: 2331

- **메시지**: `The file path should not be null or empty.`

- **원인**: 제공 된 파일 경로가 비어 있습니다.

- **권장 사항**: 존재 하는 파일에 대한 경로를 제공 하세요.


### <a name="error-code--2340"></a>오류 코드: 2340

- **메시지**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **원인**: HDInsightOnDemand 연결 된 서비스는 SelfHosted IR을 통한 실행을 지원 하지 않습니다.

- **권장 사항**: Azure IR을 선택 하 고 다시 시도 하세요.


### <a name="error-code--2341"></a>오류 코드: 2341

- **메시지**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **원인**: 제공 된 URL의 형식이 잘못 되었습니다.

- **권장 사항**: 클러스터 URL을 수정한 후 다시 시도 하세요.


### <a name="error-code--2342"></a>오류 코드: 2342

- **메시지**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **원인**: 제공 된 자격 증명이 클러스터에 대해 잘못 되었거나 네트워크 구성 또는 연결 문제나 IR에서 클러스터에 연결 하는 데 문제가 있습니다.

- **권장 사항**:  
      1. 브라우저에서 HDInsight 클러스터의 Ambari UI를 열어 자격 증명이 올바른지 확인 합니다.
      2. 클러스터가 VNet에 있고 자체 호스팅 IR을 사용 하는 경우 HDI URL은 Vnet의 개인 URL 이어야 합니다. 즉, 클러스터 이름 뒤에 '-i n t '가 있어야 합니다. 예를 들어 "https://mycluster.azurehdinsight.net/ "은 "https://mycluster-int.azurehdinsight.net/ "로 변경 해야 합니다.
      2. 클러스터가 VNet에 있고, 자체 호스팅 IR을 사용 하 고 있으며, 연결이 여전히 실패 한 경우 IR이 설치 된 VM이 HDI에 연결 하는 데 문제가 있는 것입니다. IR이 설치 된 VM에 연결 하 고 브라우저에서 Ambari UI를 엽니다. 클러스터에 대한 개인 URL을 사용 합니다. 이 연결은 브라우저에서 작동 해야 합니다. 그렇지 않은 경우 추가 지원이 필요 하면 HDInsight 지원 팀에 문의 하세요.
      3. 자체 호스팅 IR을 사용 하지 않는 경우 HDI 클러스터에 공개적으로 액세스할 수 있어야 합니다. 브라우저에서 Ambari UI를 열고 열리고 있는지 확인 합니다. 클러스터 또는 서비스에 문제가 있는 경우 HDInsight 지원 팀에 도움을 요청 하세요.
      따라서 일반적으로 ADF 연결 된 서비스에 사용 되는 HDI 클러스터 URL에는 테스트 연결이 통과할 수 있도록 ADF IR (자체 호스팅 또는 Azure)에서 액세스할 수 있어야 하 고, 실행이 작동 합니다. 이는 VM 또는 모든 공용 컴퓨터에서 브라우저에서 해당 URL을 열어 쉽게 확인할 수 있습니다.
    


### <a name="error-code--2343"></a>오류 코드: 2343

- **메시지**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **원인**: 사용자 이름 또는 암호가 비어 있습니다.

- **권장 사항**: hdi에 연결 하는 데 올바른 자격 증명을 제공 하 고 다시 시도 하세요.


### <a name="error-code--2345"></a>오류 코드: 2345

- **메시지**: `Failed to read the content of the hive script. Error: '%message;'`

- **원인**: 스크립트 파일이 없거나 ADF가 스크립트 위치에 연결할 수 없습니다.

- **권장 사항**: 스크립트가 있는지 확인 하 고 연결 된 연결 된 서비스에 연결에 대한 적절 한 자격 증명이 있는지 확인 하십시오.


### <a name="error-code--2346"></a>오류 코드: 2346

- **메시지**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **원인**: ADF가 hdi 클러스터에 ODBC 연결을 설정 하려고 했으나 오류가 발생 하 여 실패 했습니다.

- **권장 사항**: 오류 메시지 및 오류 코드는 ODBC 연결 오류 문제를 해결 하는 데 도움이 됩니다. 문제를 해결 하기에 충분 하지 않은 경우 Azure HDInsight 팀에 지원을 문의 하세요.


### <a name="error-code--2347"></a>오류 코드: 2347

- **메시지**: `Hive execution through ODBC failed with error message '%message;'.`

- **원인**: ADF가 ODBC 연결을 통해 hdi 클러스터에 실행 하기 위해 hive 스크립트를 제출 했으며 hdi에서 스크립트가 실패 했습니다.

- **권장 사항**: hdi 클러스터에서 hive 스크립트를 실행 하지 못했으며 오류 메시지 및 오류 코드에서 문제를 해결 하는 데 도움이 됩니다. 문제를 해결 하기에 충분 하지 않은 경우 Azure HDInsight 팀에 지원을 문의 하세요.


### <a name="error-code--2348"></a>오류 코드: 2348

- **메시지**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **원인**: 저장소 연결 된 서비스 속성이 올바르게 설정 되지 않았습니다.

- **권장 사항**: hdi 활동에 대한 기본 저장소 연결 된 서비스에서 전체 연결 문자열만 지원 됩니다. MSI 인증 또는 응용 프로그램을 사용 하 고 있지 않은지 확인 하세요.


### <a name="error-code--2350"></a>오류 코드: 2350

- **메시지**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **원인**: 파일이 있는 저장소에 연결 하기 위해 제공 된 자격 증명이 잘못 되었거나 파일이 존재 하지 않습니다.

- **권장 사항**:이 오류는 ADF가 hdi 활동에 대한 준비 단계를 수행할 때 발생 합니다. HDI에 작업을 제출 하기 전에 주 저장소에 파일을 복사 하려고 합니다. 파일이 제공 된 위치에 있는지, 저장소 연결이 올바른지 확인 합니다. ADF HDI 활동은 HDI 활동과 관련 된 저장소 계정의 MSI 인증을 지원 하지 않으므로 연결 된 서비스에 전체 키가 있거나 Azure Key Vault를 사용 하 고 있는지 확인 합니다.


### <a name="error-code--2351"></a>오류 코드: 2351

- **메시지**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **원인**: 파일이 지정 된 경로에 없습니다.

- **권장 사항**: 파일이 실제로 존재 하 고이 파일을 가리키는 연결 정보를 사용 하는 연결 된 서비스에 올바른 자격 증명이 있는지 확인 하세요.


### <a name="error-code--2352"></a>오류 코드: 2352

- **메시지**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **원인**: 파일 저장소 연결 된 서비스 속성이 올바르게 설정 되지 않았습니다.

- **권장 사항**: 파일 저장소 연결 된 서비스의 속성이 제대로 구성 되어 있는지 확인 하세요.


### <a name="error-code--2353"></a>오류 코드: 2353

- **메시지**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **원인**: 스크립트 저장소 연결 된 서비스 속성이 올바르게 설정 되지 않았습니다.

- **권장 사항**: 스크립트 저장소 연결 된 서비스의 속성이 제대로 구성 되어 있는지 확인 하세요.


### <a name="error-code--2354"></a>오류 코드: 2354

- **메시지**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **원인**: 저장소 연결 된 서비스 유형이 활동에서 지원 되지 않습니다.

- **권장 사항**: 선택한 연결 된 서비스에 대해 지원 되는 작업 유형 중 하나가 있는지 확인 하세요. HDI 활동은 AzureBlobStorage 및 AzureBlobFSStorage 연결 된 서비스를 지원 합니다.


### <a name="error-code--2355"></a>오류 코드: 2355

- **메시지**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **원인**: commandenvironment에 대해 제공 된가 잘못 되었습니다.

- **권장 사항**:  
      제공 된 값이 \"commandEnvironment\": [\"variableName = variableValue\"]와 비슷하며 각 변수가 목록에 한 번만 표시 되는지 확인 하세요.
    


### <a name="error-code--2356"></a>오류 코드: 2356

- **메시지**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **원인**: 변수가 commandenvironment에 두 번 제공 되었습니다.

- **권장 사항**:  
      제공 된 값이 \"commandEnvironment\": [\"variableName = variableValue\"]와 비슷하며 각 변수가 목록에 한 번만 표시 되는지 확인 하세요.
    


### <a name="error-code--2357"></a>오류 코드: 2357

- **메시지**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **원인**: 제공 된 자격 증명이 잘못 되었습니다.

- **권장 사항**: ADLS Gen 1 연결 된 서비스에서 연결 정보를 확인 하 고 연결 테스트에 성공 했는지 확인 하세요.


### <a name="error-code--2358"></a>오류 코드: 2358

- **메시지**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **원인**: 필수 속성 ' TimeToLive '에 제공 된 값의 형식이 잘못 되었습니다. 

- **권장 사항**: 제안 된 범위에 있는 값을 업데이트 하 고 다시 시도 하세요.


### <a name="error-code--2359"></a>오류 코드: 2359

- **메시지**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **원인**: ' roles ' 속성에 제공 된 값이 잘못 되었습니다.

- **권장**사항: 제안 중 하나에 해당 하는 값을 업데이트 하 고 다시 시도 하세요.


### <a name="error-code--2360"></a>오류 코드: 2360

- **메시지**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **원인**: HCatalogLinkedService에 대해 제공 된 연결 문자열이 잘못 되었습니다.

- **권장 사항**: 올바른 Azure SQL 연결 문자열로 값을 업데이트 하 고 다시 시도 하세요.


### <a name="error-code--2361"></a>오류 코드: 2361

- **메시지**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **원인**: 클러스터를 만들지 못했으며 ADF가 HDInsight 서비스에서 오류를 가져오지 못했습니다.

- **권장 사항**: Azure Portal을 열고 제공 된 이름의 hdi 리소스를 찾아서 프로 비전 상태를 확인 합니다. 추가 지원이 필요 하면 HDInsight 지원 팀에 문의 하세요.


### <a name="error-code--2362"></a>오류 코드: 2362

- **메시지**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **원인**: 제공 된 추가 저장소가 Azure Blob storage가 아닙니다.

- **권장 사항**: Azure Blob storage 계정을 주문형 HDInsight 연결 된 서비스에 대한 추가 저장소로 제공 합니다.



## <a name="web-activity"></a>웹 작업

### <a name="error-code--2128"></a>오류 코드: 2128

- **메시지**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 초과

- **권장 사항**: 적중 하려는 엔드포인트이 요청에 응답 하는지 확인 합니다. Fiddler/Postman과 같은 도구를 사용할 수 있습니다.


### <a name="error-code--2108"></a>오류 코드: 2108

- **메시지**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 초과와 같은 기본 문제로 인해 요청이 실패 했습니다.

- **권장 사항**: Fiddler/postman을 사용 하 여 요청의 유효성을 검사 합니다.
<br>


#### <a name="more-details"></a>자세한 내용
Fiddler를 사용 하 여 모니터링 되는 웹 응용 프로그램의 HTTP 세션을 만들려면 다음을 수행 합니다.

1. [Fiddler](https://www.telerik.com/download/fiddler)를 다운로드, 설치 및 엽니다.

1. 웹 응용 프로그램에서 HTTPS를 사용 하는 경우 **도구** > **Fiddler 옵션** > **https**로 이동 합니다. Https **연결 캡처** 및 **https 트래픽 해독**을 선택 합니다.

   ![Fiddler 옵션](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 응용 프로그램에서 SSL 인증서를 사용 하는 경우 Fiddler 인증서를 장치에 추가 합니다. **도구** > **Fiddler 옵션** > **HTTPS** > **작업** > **루트 인증서를 데스크톱으로 내보내기**로 이동 합니다.

1. **파일** > **캡처 트래픽**으로 이동 하 여 캡처를 해제 합니다. 또는 **F12**키를 누릅니다.

1. 모든 캐시 된 항목을 제거 하 고 다시 다운로드 해야 하도록 브라우저의 캐시를 지웁니다.

1. 요청을 만듭니다.

   1. **작성기** 탭을 선택 합니다.

   1. HTTP 메서드 및 URL을 설정 합니다.
   
   1. 필요한 경우 헤더 및 요청 본문을 추가 합니다.

   1. **실행**을 선택합니다.

1. 트래픽 캡처를 다시 켜고 페이지에서 문제가 되는 트랜잭션을 완료 합니다.

1. **파일** > **모든 세션** > **저장** 으로 이동 합니다.

자세한 내용은 [Fiddler 시작](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

문제 해결에 대한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)


            
