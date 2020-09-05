---
title: '빠른 시작: Python용 Azure 관리 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Python용 Azure 관리 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 7cc76ab7c9ce2191a54d5bd61282267467603694
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321595"
---
[참조 설명서](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [패키지(PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>사전 요구 사항

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

원하는 편집기 또는 IDE에서 새 Python 애플리케이션을 만들고 콘솔 창에서 프로젝트로 이동합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-mgmt-cognitiveservices
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

### <a name="import-libraries"></a>라이브러리 가져오기

Python 스크립트를 열고 다음 라이브러리를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>클라이언트 인증

다음 필드를 스크립트의 루트에 추가하고 사용자가 만든 서비스 주체와 Azure 계정 정보를 사용하여 해당 값을 입력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

그런 다음, **CognitiveServicesManagementClient** 개체를 구성하는 다음 코드를 추가합니다. 이 개체는 모든 Azure 관리 작업에 필요합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-service-and-pricing-tier"></a>서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)(또는 SKU)과 함께 사용할 서비스의 "종류"를 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다. 다음 함수는 사용 가능한 Cognitive Service "종류"를 나열합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

새 Cognitive Services 리소스를 만들고 구독하려면 **Create** 함수를 사용합니다. 이 함수는 전달하는 리소스 그룹에 청구 가능한 새 리소스를 추가합니다. 새 리소스를 만들 때 가격 책정 계층(또는 SKU) 및 Azure 위치와 함께 사용할 서비스의 "종류"를 알아야 합니다. 다음 함수는 이러한 인수를 모두 사용하여 리소스를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

## <a name="view-your-resources"></a>리소스 보기

모든 리소스 그룹에서 Azure 계정 아래의 모든 리소스를 보려면 다음 함수를 사용합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>리소스 삭제

다음 함수는 지정된 리소스 그룹에서 지정된 리소스를 삭제합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>호출 관리 함수

위의 함수를 호출하려면 다음 코드를 스크립트의 아래쪽에 추가합니다. 이 코드는 사용 가능한 리소스를 나열하고, 샘플 리소스를 만들고, 소유한 리소스를 나열한 다음, 샘플 리소스를 삭제합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>애플리케이션 실행

`python` 명령을 사용하여 명령줄에서 애플리케이션을 실행합니다.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>참고 항목

* [Azure Management SDK 참조 설명서](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Azure Cognitive Services란?](../../Welcome.md)
* [Azure Cognitive Services에 대한 요청 인증](../../authentication.md)
* [Azure Portal를 사용하여 새 리소스 만들기](../../cognitive-services-apis-create-account.md)