---
title: Azure Logic Apps에서 REST 엔드포인트에 연결
description: Azure Logic Apps를 사용 하 여 자동화 된 작업, 프로세스 및 워크플로의 REST 엔드포인트 모니터링
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787372"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 REST 엔드포인트 호출

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 HTTP + Swagger 커넥터를 사용 하 여 논리 앱을 빌드하여 [Swagger 파일](https://swagger.io) 을 통해 REST 엔드포인트을 정기적으로 호출 하는 워크플로를 자동화할 수 있습니다. HTTP + Swagger 트리거 및 작업은 [http 트리거와](connectors-native-http.md) 작업과 동일 하 게 작동 하지만 Swagger 파일에 설명 된 API 구조와 출력을 노출 하 여 논리 앱 디자이너에서 더 나은 환경을 제공 합니다. 폴링 트리거를 구현 하려면 사용자 지정 Api 만들기에 설명 된 폴링 패턴에 따라 [논리 앱에서 다른 api, 서비스 및 시스템을 호출](../logic-apps/logic-apps-create-api-app.md#polling-triggers)합니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 대상 REST 엔드포인트을 설명 하는 Swagger (OpenAPI 아님) 파일의 URL입니다.

  일반적으로 REST 엔드포인트은 커넥터가 작동 하려면이 조건을 충족 해야 합니다.

  * Swagger 파일은 공개적으로 액세스할 수 있는 HTTPS URL에 호스팅해야 합니다.

  * Swagger 파일에는 [CORS (크로스-원본 자원 공유)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 를 사용 하도록 설정 해야 합니다.

  호스트 되지 않거나 보안 및 원본 간 요구 사항을 충족 하지 않는 Swagger 파일을 참조 하려면 [Azure storage 계정의 blob 컨테이너에 swagger 파일을 업로드](#host-swagger)하 고 해당 저장소 계정에서 CORS를 사용 하도록 설정 하 여 파일을 참조할 수 있습니다.

  이 항목의 예제에서는 [Cognitive Services 계정 및 액세스 키](../cognitive-services/cognitive-services-apis-create-account.md)가 필요한 [Cognitive Services Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)를 사용 합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토하세요.

* 대상 엔드포인트을 호출 하려는 논리 앱입니다. HTTP + Swagger 트리거를 시작 하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP + Swagger 동작을 사용 하려면 원하는 트리거를 사용 하 여 논리 앱을 시작 합니다. 이 예제에서는 첫 번째 단계로 HTTP + Swagger 트리거를 사용 합니다.

## <a name="add-an-http--swagger-trigger"></a>HTTP + Swagger 트리거 추가

이 기본 제공 트리거는 REST API을 설명 하 고 해당 파일의 콘텐츠를 포함 하는 응답을 반환 하는 Swagger 파일의 URL에 HTTP 요청을 보냅니다.

1. [Azure portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너의 검색 상자에 "swagger"를 필터로 입력 합니다. **트리거** 목록에서 **HTTP + Swagger** 트리거를 선택 합니다.

   ![HTTP + Swagger 트리거를 선택 합니다.](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. **SWAGGER 엔드포인트 url** 상자에 swagger 파일의 url을 입력 하 고 **다음**을 선택 합니다.

   이 예에서는 [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에 대한 미국 서 부 지역에 있는 Swagger URL을 사용 합니다.

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Swagger 엔드포인트에 대한 URL 입력](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Swagger 파일에 설명 된 작업이 디자이너에 표시 되 면 사용 하려는 작업을 선택 합니다.

   ![Swagger 파일의 작업](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 선택한 작업에 따라 달라 지는 트리거 매개 변수에 대한 값을 지정 합니다 .이 값은 엔드포인트 호출에 포함 시킬 수 있습니다. 트리거가 엔드포인트을 호출 하는 빈도에 대한 되풀이를 설정 합니다.

   이 예에서는 단계가 보다 설명적인 이름을 갖도록 "HTTP + Swagger 트리거: 얼굴 감지"로 트리거의 이름을 바꿉니다.

   ![작업 세부 정보](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 사용할 수 있는 다른 매개 변수를 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택 합니다.

   HTTP + Swagger에 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃 바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조 하세요.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 완료 되 면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="add-an-http--swagger-action"></a>HTTP + Swagger 동작 추가

이 기본 제공 작업은 REST API를 설명 하는 Swagger 파일의 URL에 대한 HTTP 요청을 수행 하 고 해당 파일의 콘텐츠를 포함 하는 응답을 반환 합니다.

1. [Azure portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

1. HTTP + Swagger 작업을 추가 하려는 단계 아래에서 **새 단계**를 선택 합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시 되는 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

1. 디자이너의 검색 상자에 "swagger"를 필터로 입력 합니다. **작업** 목록에서 **HTTP + Swagger** 동작을 선택 합니다.

    ![HTTP + Swagger 동작 선택](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. **SWAGGER 엔드포인트 url** 상자에 swagger 파일의 url을 입력 하 고 **다음**을 선택 합니다.

   이 예에서는 [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)에 대한 미국 서 부 지역에 있는 Swagger URL을 사용 합니다.

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Swagger 엔드포인트에 대한 URL 입력](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Swagger 파일에 설명 된 작업이 디자이너에 표시 되 면 사용 하려는 작업을 선택 합니다.

   ![Swagger 파일의 작업](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 선택한 작업에 따라 달라 지는 작업 매개 변수에 대한 값을 지정 합니다 .이 값은 엔드포인트 호출에 포함 시킬 수 있습니다.

   이 예제에는 매개 변수가 없지만 동작의 이름을 "HTTP + Swagger 동작: 얼굴 식별"으로 지정 하 여 단계에 보다 설명적인 이름을 지정 합니다.

   ![작업 세부 정보](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 사용할 수 있는 다른 매개 변수를 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택 합니다.

   HTTP + Swagger에 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃 바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조 하세요.

1. 완료 되 면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Azure Storage에서 Swagger 호스트

해당 파일을 Azure storage 계정의 blob 컨테이너에 업로드 하 고 해당 저장소 계정에서 CORS를 사용 하도록 설정 하 여 호스트 되지 않거나 보안 및 원본 간 요구 사항을 충족 하지 않는 Swagger 파일을 참조할 수 있습니다. Azure Storage에서 Swagger 파일을 만들고 설정 하 고 저장 하려면 다음 단계를 수행 합니다.

1. [Azure Storage 계정 만들기](../storage/common/storage-create-storage-account.md)

1. 이제 blob에 CORS를 사용 하도록 설정 합니다. 저장소 계정의 메뉴에서 **CORS**를 선택 합니다. **Blob service** 탭에서 이러한 값을 지정 하 고 **저장**을 선택 합니다.

   | 자산 | Value |
   |----------|-------|
   | **허용 된 원본** | `*` |
   | **허용 된 메서드** | `GET`, `HEAD`, `PUT` |
   | **허용 된 헤더** | `*` |
   | **노출 된 헤더** | `*` |
   | **최대 기간** (초) | `200` |
   |||

   이 예제에서는 [Azure Portal](https://portal.azure.com)를 사용 하지만 [Azure Storage 탐색기](https://storageexplorer.com/)와 같은 도구를 사용 하거나이 샘플 [PowerShell 스크립트](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)를 사용 하 여이 설정을 자동으로 구성할 수 있습니다.

1. [Blob 컨테이너를 만듭니다](../storage/blobs/storage-quickstart-blobs-portal.md). 컨테이너의 **개요** 창에서 **액세스 수준 변경**을 선택 합니다. **공용 액세스 수준** 목록에서 **blob (blob에 대한 익명 읽기 액세스만 해당)** 을 선택 하 고 **확인**을 선택 합니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Storage 탐색기](https://storageexplorer.com/)를 통해 [Swagger 파일을 blob 컨테이너에 업로드](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)합니다.

1. Blob 컨테이너에서 파일을 참조 하려면 다음 형식을 따르는 HTTPS 링크를 사용 합니다 .이는 대/소문자를 구분 합니다.

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>커넥터 참조

HTTP + Swagger 트리거 또는 작업의 출력에 대한 자세한 내용은 다음과 같습니다. HTTP + Swagger 호출은 다음 정보를 반환 합니다.

| 속성 이름 | Type | 설명 |
|---------------|------|-------------|
| 헤더 | object | 요청의 헤더입니다. |
| body | object | JSON 개체 | 요청의 본문 내용이 포함 된 개체입니다. |
| 상태 코드 | int | 요청의 상태 코드 |
|||

| 상태 코드 | 설명 |
|-------------|-------------|
| 200 | 확인 |
| 202 | 수락됨 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
