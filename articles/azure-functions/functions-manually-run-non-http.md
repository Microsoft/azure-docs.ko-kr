---
title: HTTP 이외 트리거 Azure Functions를 수동으로 실행
description: HTTP 요청을 사용하여 HTTP 이외 트리거 Azure Functions 실행
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 37f79d717b7ea0e26717e7b51f9e66b908b96521
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640969"
---
# <a name="manually-run-a-non-http-triggered-function"></a>HTTP 이외 트리거 함수를 수동으로 실행

이 문서에서는 특별한 형식의 HTTP 요청을 통해 HTTP 이외 트리거 함수를 수동으로 실행하는 방법에 대해 설명합니다.

일부 컨텍스트에서 간접적으로 트리거되는 "주문형" Azure Function을 실행해야 합니다.  간접 트리거의 예제에는 [일정에 따른 함수](./functions-create-scheduled-function.md)나 [다른 리소스의 작업](./functions-create-storage-blob-triggered-function.md) 결과로 실행되는 함수가 포함됩니다. 

다음 예제에서는 [Postman](https://www.getpostman.com/)을 사용할 수 있지만 [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) 또는 HTTP 요청을 전송하는 도구와 같은 기타 기능을 사용할 수도 있습니다.

## <a name="define-the-request-location"></a>요청 위치 정의

HTTP가 아닌 트리거 함수를 실행하려면 Azure에 해당 함수를 실행하기 위한 요청을 보낼 방법이 필요합니다. 이 요청에 사용된 URL은 특정 형식을 사용합니다.

![요청 위치를 정의합니다. 함수 이름 + 폴더 경로 + 호스트 이름](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **호스트 이름:** 함수 앱의 공용 위치는 함수 앱의 이름 및 *azurewebsites.net*이나 사용자 지정 도메인으로 구성됩니다.
- **폴더 경로:** HTTP 요청을 통해 HTTP 이외 트리거 함수에 액세스하려면 *admin/functions* 폴더를 통해 요청을 보내야 합니다.
- **함수 이름:** 실행하려는 함수의 이름입니다.

Azure에 대한 요청에서 함수의 마스터 키와 함께 Postman의 요청 위치를 사용하여 함수를 실행합니다.

> [!NOTE]
> 로컬에서 실행하는 경우 함수의 마스터 키가 필요하지 않습니다. `x-functions-key` 헤더를 생략하고 직접 [함수를 호출](#call-the-function)할 수 있습니다.

## <a name="get-the-functions-master-key"></a>함수의 마스터 키 가져오기

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동 하 여 **앱 키**를 선택 하 고 키를 선택 `_master` 합니다. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="복사할 마스터 키를 찾습니다." border="true":::

1. **키 편집** 섹션에서 키 값을 클립보드에 복사한 다음 **확인**을 선택 합니다.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="마스터 키를 클립보드에 복사 합니다." border="true":::

1. *_Master* 키를 복사한 후 **코드 + 테스트**를 선택 하 고 **로그**를 선택 합니다. Postman에서 함수를 수동으로 실행하는 경우 여기에 기록된 함수의 메시지가 표시됩니다.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="로그를 확인 하 여 마스터 키 테스트 결과를 확인 합니다." border="true":::

> [!CAUTION]  
> 함수 앱에서는 마스터 키를 통해 높은 권한이 부여되므로, 이 키를 제3자와 공유하거나 애플리케이션에 배포해서는 안 됩니다. 키는 HTTPS 끝점 으로만 전송 되어야 합니다.

## <a name="call-the-function"></a>함수 호출

Postman을 열고 다음 단계를 수행합니다.

1. **URL 텍스트 상자에서 요청 위치**를 입력합니다.
1. HTTP 메서드가 **POST**.로 설정되었는지 확인합니다.
1. **Headers** 탭을 선택합니다.
1. 첫 번째 키로 **x-함수 키** 를 입력 하 고 클립보드의 마스터 키를 값으로 붙여넣습니다.
1. **Content-type** 을 두 번째 키로 입력 하 고 **application/json** 을 값으로 입력 합니다.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Postman headers 설정입니다." border="true":::

1. **본문** 탭을 선택합니다.
1. 요청 본문으로 **{"input": "test"}** 를 입력 합니다.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Postman body 설정입니다." border="true":::

1. **보내기**를 선택합니다.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Postman을 사용 하 여 요청을 보냅니다." border="true":::

    그런 다음, Postman은 **202 수락됨**이라는 상태를 보고합니다.

1. 다음으로, Azure Portal에서 해당 함수로 돌아갑니다. 로그를 검토 하면 함수에 대 한 수동 호출에서 들어오는 메시지가 표시 됩니다.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="로그를 확인 하 여 마스터 키 테스트 결과를 확인 합니다." border="true":::

## <a name="next-steps"></a>다음 단계

- [Azure Functions에서 코드를 테스트하기 위한 전략](./functions-test-a-function.md)
- [Azure Function Event Grid 트리거 로컬 디버깅](./functions-debug-event-grid-trigger-local.md)
