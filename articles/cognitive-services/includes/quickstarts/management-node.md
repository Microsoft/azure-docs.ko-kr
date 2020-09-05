---
title: '빠른 시작: Node.js용 Azure 관리 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Node.js용 Azure 관리 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fcfefb2b6cd5c23e9b77d7d27413d1ff6d775bfe
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321622"
---
[참조 설명서](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [패키지(NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>사전 요구 사항

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

계속하기 전에 _index.js_라는 파일을 만듭니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

다음 NPM 패키지를 설치합니다.

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

### <a name="import-libraries"></a>라이브러리 가져오기

_index.js_ 스크립트를 열고 다음 라이브러리를 가져옵니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>클라이언트 인증

다음 필드를 스크립트의 루트에 추가하고 사용자가 만든 서비스 주체와 Azure 계정 정보를 사용하여 해당 값을 입력합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

그런 다음, 다음 `quickstart` 함수를 추가하여 프로그램의 주요 작업을 처리합니다. 첫 번째 코드 블록은 위에서 입력한 자격 증명 변수를 사용하여 **CognitiveServicesManagementClient** 개체를 생성합니다. 이 개체는 모든 Azure 관리 작업에 필요합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>호출 관리 함수

`quickstart` 함수 끝에 다음 코드를 추가하여 사용 가능한 리소스를 나열하고, 샘플 리소스를 만들고, 소유한 리소스를 나열한 다음, 샘플 리소스를 삭제합니다. 다음 단계에서 이러한 함수를 정의합니다.

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-service-and-pricing-tier"></a>서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)(또는 SKU)과 함께 사용할 서비스의 "종류"를 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다. 다음 함수는 사용 가능한 Cognitive Service "종류"를 나열합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

새 Cognitive Services 리소스를 만들고 구독하려면 **Create** 함수를 사용합니다. 이 함수는 전달하는 리소스 그룹에 청구 가능한 새 리소스를 추가합니다. 새 리소스를 만들 때 가격 책정 계층(또는 SKU) 및 Azure 위치와 함께 사용할 서비스의 "종류"를 알아야 합니다. 다음 함수는 이러한 인수를 모두 사용하여 리소스를 만듭니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>리소스 보기

모든 리소스 그룹에서 Azure 계정 아래의 모든 리소스를 보려면 다음 함수를 사용합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>리소스 삭제

다음 함수는 지정된 리소스 그룹에서 지정된 리소스를 삭제합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>애플리케이션 실행

오류를 처리하는 주 `quickstart` 함수를 호출하려면 다음 코드를 스크립트의 아래쪽에 추가합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

그런 다음, 콘솔 창에서 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="see-also"></a>참고 항목

* [Azure Management SDK 참조 설명서](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Azure Cognitive Services란?](../../Welcome.md)
* [Azure Cognitive Services에 대한 요청 인증](../../authentication.md)
* [Azure Portal를 사용하여 새 리소스 만들기](../../cognitive-services-apis-create-account.md)