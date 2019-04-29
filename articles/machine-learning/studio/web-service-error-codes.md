---
title: REST API 오류 코드 - Azure Machine Learning Studio | Microsoft Docs
description: 이러한 오류 코드는 Azure Machine Learning 웹 서비스의 작업에서 반환될 수 있습니다.
keywords: ''
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 8e91d0cd68997dee9bb00cceeaa6b697f6644ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736567"
---
# <a name="azure-machine-learning-studio-rest-api-error-codes"></a>Azure Machine Learning Studio REST API 오류 코드
 
다음 오류 코드는 Azure Machine Learning Studio 웹 서비스의 작업에서 반환될 수 있습니다.
 
## <a name="badargument-http-status-code-400"></a>BadArgument(HTTP 상태 코드 400)
 
잘못된 인수가 제공되었습니다.
 
이 오류 클래스는 어딘가에 제공된 인수가 잘못되었다는 의미입니다. 웹 서비스에 전달된 내용에 대한 Azure Storage의 위치 또는 자격 증명일 수 있습니다. 구체적으로 어떤 인수가 잘못되었는지 진단하려면 "세부 정보" 섹션의 오류 "코드" 필드를 살펴보세요.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| BadParameterValue | 제공된 매개 변수 값이 해당 매개 변수에 대한 매개 변수 규칙을 충족하지 않습니다. |
| BadSubscriptionId | 점수를 매기는 데 사용되는 구독 ID가 리소스에 있는 ID가 아닙니다. |
| BadVersionCall | API 호출 중에 잘못된 버전 매개 변수가 전달되었습니다. {0}. 올바른 버전을 전달하려면 API 도움말 페이지를 확인하고 다시 시도하세요. |
| BatchJobInputsNotSpecified | 다음 필수 입력이 요청에 지정되지 않았습니다. {0}. 입력 데이터를 모두 지정하고 다시 시도하세요. |
| BatchJobInputsTooManySpecified | 서비스에 정의된 것보다 많은 입력이 요청에 지정되었습니다. 허용된 입력 목록: {0}. 입력 데이터를 모두 올바르게 지정하고 다시 시도하세요. |
| BlobNameTooLong | 진단 출력을 위해 제공된 Azure Blob Storage 경로가 너무 깁니다. {0}. 경고를 줄이고 다시 시도하세요. |
| BlobNotFound | 제공된 Azure Blob - {0}에 액세스할 수 없습니다.  Azure 오류 메시지: {1}. |
| ContainerIsEmpty | Azure Storage 컨테이너 이름이 제공되지 않았습니다. 유효한 컨테이너 이름을 제공하고 다시 시도하세요. |
| ContainerSegmentInvalid | 컨테이너 이름이 잘못되었습니다. 유효한 컨테이너 이름을 제공하고 다시 시도하세요. |
| ContainerValidationFailed | Blob 컨테이너 유효성 검사가 {0} 오류로 인해 실패했습니다. |
| DataTypeNotSupported | 지원되지 않는 데이터 형식이 제공되었습니다. 유효한 데이터 형식을 제공하고 다시 시도하세요. |
| DuplicateInputInBatchCall | 일괄 처리 요청이 잘못되었습니다. 단일 입력과 여러 입력을 동시에 지정할 수 없습니다. 요청에서 이러한 항목 중 하나를 제거하고 다시 시도하세요. |
| ExpiryTimeInThePast | 제공된 만료 시간이 과거입니다. {0}. 미래의 만료 시간을 UTC로 제공하고 다시 시도하세요. 만료되지 않도록 하려면 만료 시간을 NULL로 설정하세요. |
| IncompleteSettings | 진단 설정이 완전하지 않습니다. |
| InputBlobRelativeLocationInvalid | Azure Storage Blob 이름이 제공되지 않았습니다. 유효한 Blob 이름을 제공하고 다시 시도하세요. |
| InvalidBlob | Blob {0}에 대한 Blob 사양이 잘못되었습니다. 연결 문자열/상대 경로 또는 SAS 토큰 사양이 올바른지 확인하고 다시 시도하세요. |
| InvalidBlobConnectionString | 입력/출력 Blob 중 하나에 지정된 연결 문자열이 잘못되었습니다. {0}. 잘못된 내용을 수정하고 다시 시도하세요. |
| InvalidBlobExtension | Blob 참조 {0}에 잘못되거나 누락된 파일 확장명이 있습니다. 이 출력 형식에 지원되는 파일 확장명은 “{1}”입니다. |
| InvalidInputNames | 잘못된 서비스 입력 이름이 요청에 지정되었습니다. {0}. 입력 데이터를 올바른 서비스 입력에 매핑하고 다시 시도하세요. |
| InvalidOutputOverrideName | 잘못된 출력 재정의 이름: {0}. 서비스에 이름이 같은 출력 노드가 없습니다. 재정의를 위해 올바른 출력 노드 이름을 전달하세요(대/소문자 구분 적용됨). |
| InvalidQueryParameter | 잘못된 쿼리 매개 변수 ‘{0}’. {1} |
| MissingInputBlobInformation | Azure Storage Blob 정보가 누락되었습니다. 유효한 연결 문자열 및 상대 경로 또는 URI를 제공하고 다시 시도하세요. |
| MissingJobId | 작업 ID가 제공되지 않았습니다. 작업 ID는 작업이 처음 제출될 때 반환됩니다. 작업 ID가 올바른지 확인하고 다시 시도하세요. |
| MissingKeys | 키가 제공되지 않았거나 기본 또는 보조 키 중 하나가 제공되지 않았습니다. |
| MissingModelPackage | 모델 패키지 ID 또는 모델 패키지가 제공되지 않았습니다. 유효한 모델 패키지 ID 또는 모델 패키지를 제공하고 다시 시도하세요. |
| MissingOutputOverrideSpecification | 요청에 출력 재정의 {0}에 대한 Blob 사양이 누락되었습니다. 요청에 유효한 Blob 위치를 지정하거나 위치 재정의가 필요하지 않으면 출력 사양을 제거하세요. |
| MissingRequestInput | 웹 서비스에 입력이 필요한데 입력이 제공되지 않았습니다. 모델에서 게시된 입력 포트를 기반으로 유효한 입력을 반드시 제공하고 다시 시도하세요. |
| MissingRequiredGlobalParameters | 필수 웹 서비스 매개 변수 중 일부가 제공되지 않았습니다. 모듈에 필요한 매개 변수가 올바른지 확인하고 다시 시도하세요. |
| MissingRequiredOutputOverrides | 암호화된 서비스 엔드포인트를 호출하는 경우 모든 서비스의 출력에 대해 출력 재정의를 반드시 전달해야 합니다. 이러한 출력에 대해 현재 누락된 재정의: {0} |
| MissingWebServiceGroupId | 웹 서비스 그룹 ID가 제공되지 않았습니다. 유효한 웹 서비스 그룹 ID를 제공하고 다시 시도하세요. |
| MissingWebServiceId | 웹 서비스 ID가 제공되지 않았습니다. 유효한 웹 서비스 ID를 제공하고 다시 시도하세요. |
| MissingWebServicePackage | 웹 서비스 패키지가 제공되지 않았습니다. 유효한 웹 서비스 패키지를 제공하고 다시 시도하세요. |
| MissingWorkspaceId | 작업 영역 ID가 제공되지 않았습니다. 유효한 작업 영역 ID를 제공하고 다시 시도하세요. |
| ModelConfigurationInvalid | 모델 패키지에 모델 구성이 잘못되었습니다. 모델 구성에 출력 엔드포인트 정의, std 오류 엔드포인트, std 출력 엔드포인트가 포함되었는지 확인하고 다시 시도하세요. |
| ModelPackageIdInvalid | 모델 패키지 ID가 잘못되었습니다. 모델 패키지 ID가 올바른지 확인하고 다시 시도하세요. |
| RequestBodyInvalid | 요청 본문이 제공되지 않았거나 요청 본문을 역직렬화하는 동안 오류가 발생했습니다. |
| RequestIsEmpty | 요청이 제공되지 않았습니다. 유효한 요청을 제공하고 다시 시도하세요. |
| UnexpectedParameter | 예기치 않은 매개 변수가 제공되었습니다. 모든 매개 변수 이름의 철자가 올바른지, 예상된 매개 변수가 전달되는지 확인하고 다시 시도하세요. |
| UnknownError | 알 수 없는 오류입니다. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | {0} 웹 서비스에 대한 동시 요청 요구 사항을 변경할 수 없습니다. |
| WebServiceIdInvalid | 잘못된 웹 서비스 ID가 제공되었습니다. 웹 서비스 ID는 유효한 GUID여야 합니다. |
| WebServiceTooManyConcurrentRequestRequirement | 동시 요청 요구 사항을 {0}개 넘게 설정할 수 없습니다. |
| WebServiceTypeInvalid | 잘못된 웹 서비스 유형이 제공되었습니다. 웹 서비스 유형이 올바른지 확인하고 다시 시도하세요. 유효한 웹 서비스 유형: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument(HTTP 상태 코드 400)
 
잘못된 사용자 인수가 제공되었습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| InputMismatchError | 입력 데이터가 입력 포트 스키마와 일치하지 않습니다. |
| InputParseError | 입력 벡터의 구문 분석에 실패했습니다.  입력 벡터에 열 수와 데이터 형식이 올바른지 확인하세요.  추가 정보: {0}. |
| MissingRequiredGlobalParameters | 웹 서비스에 필요한 매개 변수가 누락되었습니다. 웹 서비스에 필요한 모든 필수 매개 변수가 올바른지 확인하고 다시 시도하세요. |
| UnexpectedParameter | 웹 서비스에 필요한 필수 매개 변수만 전달되는지 확인하고 다시 시도하세요. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation(HTTP 상태 코드 400)
 
현재 컨텍스트에서 요청이 잘못되었습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| CannotStartJob | 작업이 {0} 상태이기 때문에 시작할 수 없습니다. |
| IncompatibleModel | 모델이 요청 버전과 호환되지 않습니다. 요청 버전은 단일 DataTable 출력 모델만 지원합니다. |
| MultipleInputsNotAllowed | 모델이 여러 개의 입력을 허용하지 않습니다. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError(HTTP 상태 코드 400)
 
모듈 실행에 내부 라이브러리 오류가 발생했습니다.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError(HTTP 상태 코드 400)
 
모듈 실행에 오류가 발생했습니다.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError(HTTP 상태 코드 400)
 
웹 서비스 패키지가 잘못되었습니다. 제공된 웹 서비스 패키지가 올바른지 확인하고 다시 시도하세요.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| FormatError | 웹 서비스 패키지 형식이 잘못되었습니다. 세부 정보: {0} |
| RuntimesError | 웹 서비스 패키지 그래프가 잘못되었습니다. 세부 정보: {0} |
| ValidationError | 웹 서비스 패키지 그래프가 잘못되었습니다. 세부 정보: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Unauthorized(HTTP 상태 코드 401)
 
요청이 리소스에 액세스할 권한이 없습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| AdminRequestUnauthorized | 권한 없음 |
| ManagementRequestUnauthorized | 권한 없음 |
| ScoreRequestUnauthorized | 잘못된 자격 증명이 제공되었습니다. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP 상태 코드 404)
 
리소스를 찾을 수 없습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| ModelPackageNotFound | 모델 패키지를 찾을 수 없습니다. 모델 패키지 ID가 올바른지 확인하고 다시 시도하세요. |
| WebServiceIdNotFoundInWorkspace | 이 작업 영역에서 웹 서비스를 찾을 수 없습니다. webServiceId와 workspaceId가 일치하지 않습니다. 제공된 웹 서비스가 작업 영역의 일부인지 확인하고 다시 시도하세요. |
| WebServiceNotFound | 웹 서비스를 찾을 수 없습니다. 웹 서비스 ID가 올바른지 확인하고 다시 시도하세요. |
| WorkspaceNotFound | 작업 영역을 찾을 수 없습니다. 작업 영역 ID가 올바른지 확인하고 다시 시도하세요. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout(HTTP 상태 코드 408)
 
허용된 시간 내에 작업을 완료할 수 없습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| RequestCanceled | 클라이언트에 의해 요청이 취소되었습니다. |
| ScoreRequestTimeout | 실행 요청 시간이 초과되었습니다. |
 
## <a name="conflict-http-status-code-409"></a>Conflict(HTTP 상태 코드 409)
 
리소스가 이미 있습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| ModelOutputMetadataMismatch | 출력 매개 변수 이름이 잘못되었습니다. 메타데이터 편집기 모듈을 사용하여 열 이름 변경을 시도하고 다시 시도하세요. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation(HTTP 상태 코드 413)
 
모델에 할당된 메모리 할당량이 초과되었습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| OutOfMemoryLimit | 모델에 책정된 메모리보다 더 많은 메모리가 사용되었습니다. 모델에 허용된 최대 메모리는 {0}MB입니다. 모델에서 문제를 확인하세요. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError(HTTP 상태 코드 500)
 
실행에 내부 오류가 발생했습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | 시스템 오류로 인해 컨테이너 프로세스가 중지되었습니다. |
| ContainerProcessTerminatedWithUnknownError | 알 수 없는 오류로 인해 컨테이너 프로세스가 중지되었습니다. |
| ContainerValidationFailed | Blob 컨테이너 유효성 검사가 {0} 오류로 인해 실패했습니다. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | 인수가 제공되지 않았습니다. 유효한 인수가 전달되는지 확인하고 다시 시도하세요. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port id={0}에 지원되지 않는 데이터 형식 {1}이(가) 있습니다. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger를 생성하지 못했습니다. 세부 정보: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | 알 수 없는 작업 상태 코드 {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, 세부 정보: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory(HTTP 상태 코드 500)
 
실행에 내부 오류가 발생했습니다. 시스템 메모리가 부족합니다. 나중에 다시 시도하세요.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError(HTTP 상태 코드 500)
 
모델 패키지가 잘못되었습니다. 제공된 모델 패키지가 올바른지 확인하고 다시 시도하세요.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError(HTTP 상태 코드 500)
 
웹 서비스 패키지가 잘못되었습니다. 제공된 웹 패키지가 올바른지 확인하고 다시 시도하세요.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| ModuleError | 웹 서비스 패키지 그래프가 잘못되었습니다. 세부 정보: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers(HTTP 상태 코드 503)
 
컨테이너를 초기화하는 중이라서 요청을 실행할 수 없습니다.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable(HTTP 상태 코드 503)
 
서비스를 일시적으로 사용할 수 없습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| NoMoreResources | 요청에 사용할 수 있는 리소스가 없습니다. |
| RequestThrottled | 요청이 {0} 엔드포인트에 대해 제한되었습니다. 엔드포인트에 대한 최대 동시성은 {1}입니다. |
| TooManyConcurrentRequests | 동시 요청을 너무 많이 보냈습니다. |
| TooManyHostsBeingInitialized | 동시에 초기화되는 호스트가 너무 많습니다. 제한/다시 시도하는 것이 좋습니다. |
| TooManyHostsBeingInitializedPerModel | 동시에 초기화되는 호스트가 너무 많습니다. 제한/다시 시도하는 것이 좋습니다. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout(HTTP 상태 코드 504)
 
허용된 시간 내에 작업을 완료할 수 없습니다.
 
| 오류 코드 | 사용자 메시지 |
| ---------- |--------------|
| BackendInitializationTimeout | 허용된 시간 내에 웹 서비스 초기화를 완료할 수 없습니다. |
| BackendScoreTimeout | 허용된 시간 내에 웹 서비스 요청 실행을 완료할 수 없습니다. |
 
