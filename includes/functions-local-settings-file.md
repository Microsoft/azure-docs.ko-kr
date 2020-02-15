---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205721"
---
## <a name="local-settings-file"></a>로컬 설정 파일

로컬 개발 도구에서 사용 하는 앱 설정, 연결 문자열 및 설정을 저장 하는 로컬 설정입니다. 로컬에서 프로젝트를 실행 하는 경우에만 로컬 설정 json 파일의 설정이 사용 됩니다. 로컬 설정 파일의 구조는 다음과 같습니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

이러한 설정은 프로젝트를 로컬로 실행할 때 지원 됩니다.

| 설정      | Description                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 이 설정이 `true`로 설정 된 경우 모든 값은 로컬 컴퓨터 키로 암호화 됩니다. `func settings` 명령과 함께 사용됩니다. 기본값은 `false`입니다. |
| **`Values`** | 프로젝트를 로컬로 실행할 때 사용 되는 응용 프로그램 설정 및 연결 문자열의 배열입니다. 이러한 키-값 (문자열 문자열) 쌍은 [`AzureWebJobsStorage`]와 같이 Azure의 함수 앱에 있는 응용 프로그램 설정에 해당 합니다. 많은 트리거와 바인딩에는 [Blob storage 트리거의](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)`Connection`와 같은 연결 문자열 앱 설정을 참조 하는 속성이 있습니다. 이러한 속성의 경우 `Values` 배열에 정의 된 응용 프로그램 설정이 필요 합니다. <br/>[`AzureWebJobsStorage`] 는 HTTP 이외의 트리거에 필요한 앱 설정입니다. <br/>버전 2.x 이상의 함수 런타임에는 핵심 도구에서 프로젝트에 대해 생성 되는 [`FUNCTIONS_WORKER_RUNTIME`] 설정이 필요 합니다. <br/> [Azure storage 에뮬레이터](../articles/storage/common/storage-use-emulator.md) 를 로컬로 설치 하 고 [`AzureWebJobsStorage`] 를 `UseDevelopmentStorage=true`로 설정 하면 핵심 도구에서 에뮬레이터를 사용 합니다. 에뮬레이터는 개발 중에 유용 하지만 배포 하기 전에 실제 저장소 연결로 테스트 해야 합니다.<br/> 값은 문자열 이어야 하며 JSON 개체 또는 배열이 아닙니다. 설정 이름은 콜론 (`:`) 또는 이중 밑줄 (`__`)을 포함할 수 없습니다. 이러한 문자는 런타임에 예약 됩니다.  |
| **`Host`** | 이 섹션의 설정은 프로젝트를 로컬로 실행할 때 호스트 프로세스 기능을 사용자 지정 합니다. 이러한 설정은 Azure에서 프로젝트를 실행 하는 경우에도 적용 되는 호스트 json 설정과는 별개입니다. |
| **`LocalHttpPort`** | 로컬 Functions 호스트(`func host start` 및 `func run`)를 실행할 때 사용되는 기본 포트를 설정합니다. `--port` 명령줄 옵션이이 설정 보다 우선적으로 적용 됩니다. |
| **`CORS`** | [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)에 허용된 원본을 정의합니다. 원본은 공백 없이 쉼표로 구분된 목록으로 제공됩니다. 와일드카드 값(\*)이 지원되므로 모든 원본에서 요청할 수 있습니다. |
| **`CORSCredentials`** |  `true`로 설정 하면 `withCredentials` 요청을 허용 합니다. |
| **`ConnectionStrings`** | 컬렉션입니다. 함수 바인딩에서 사용 하는 연결 문자열에는이 컬렉션을 사용 하지 마세요. 이 컬렉션은 일반적으로 [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)와 같이 구성 파일의 `ConnectionStrings` 섹션에서 연결 문자열을 가져오는 프레임 워크 에서만 사용 됩니다. 이 개체의 연결 문자열은 공급자 유형이 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)인 환경에 추가됩니다. 이 컬렉션의 항목은 다른 앱 설정을 사용 하 여 Azure에 게시 되지 않습니다. 함수 앱 설정의 `Connection strings` 컬렉션에 이러한 값을 명시적으로 추가 해야 합니다. 함수 코드에 [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 을 만드는 경우 포털의 **응용 프로그램 설정** 에서 연결 문자열 값을 다른 연결과 함께 저장 해야 합니다. |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
