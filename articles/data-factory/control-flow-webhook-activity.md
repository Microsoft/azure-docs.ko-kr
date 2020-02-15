---
title: Azure Data Factory의 Webhook 활동
description: 웹 후크 작업은 사용자가 지정 된 특정 조건에 따라 연결 된 데이터 집합의 유효성을 검사할 때까지 파이프라인의 실행을 계속 하지 않습니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 70c67a99274eaedc5592c7b90b1ef80a3a17acf8
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109993"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory의 Webhook 활동
웹 후크 작업을 사용 하 여 사용자 지정 코드를 통해 파이프라인의 실행을 제어할 수 있습니다. 고객은 webhook 활동을 사용 하 여 끝점을 호출 하 고 콜백 URL을 전달할 수 있습니다. 파이프라인 실행은 다음 작업을 진행 하기 전에 콜백이 호출 될 때까지 기다립니다.

## <a name="syntax"></a>구문

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```


## <a name="type-properties"></a>형식 속성



속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | 웹 후크 활동의 이름입니다. | String | yes |
type | **WebHook**로 설정 되어야 합니다. | String | yes |
method | 대상 엔드포인트에 대한 Rest API 메서드입니다. | 문자열입니다. 지원 되는 형식: ' POST ' | yes |
url | 대상 엔드포인트 및 경로입니다. | 문자열(또는 resultType 문자열이 있는 식). | yes |
headers | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어 및 형식을 설정 하려면: "headers": {"Accept-Language": "en-us", "Content-type": "application/json"}. | 문자열(또는 resultType 문자열이 있는 식) | 예, Content-Type 헤더가 필요합니다. "headers":{ "Content-Type":"application/json"} |
본문 | 엔드포인트에 전송된 페이로드를 나타냅니다. | 유효한 JSON 또는 JSON의 resultType을 사용 하는 식입니다. [요청 페이로드 스키마](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) 섹션에서 요청 페이로드의 스키마를 참조하세요. | yes |
인증 | 엔드포인트를 호출하는 데 사용되는 인증 방법입니다. 지원 되는 형식은 "Basic" 또는 "ClientCertificate"입니다. 자세한 내용은 [인증](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) 섹션을 참조하세요. 인증이 필요 없는 경우 이 속성을 제외합니다. | 문자열(또는 resultType 문자열이 있는 식) | 예 |
시간 제한 | 활동에서 &#39;callbackuri&#39; 가 호출 될 때까지 대기 하는 시간입니다. 작업에서 ' callBackUri '가 호출 될 때까지 대기 하는 시간입니다. 기본값은 10 분 ("00:10:00")입니다. Format은 Timespan입니다 (예: d. d. hh: mm: ss). | String | 예 |
콜백에 대 한 보고서 상태 | 활동을 실패로 표시 하는 웹 후크 활동의 실패 상태를 보고할 수 있습니다. | 부울 | 예 |

## <a name="authentication"></a>인증

다음은 webhook 작업에서 지원 되는 인증 유형입니다.

### <a name="none"></a>None

인증이 필요 없는 경우 "authentication" 속성을 포함하지 않습니다.

### <a name="basic"></a>Basic

기본 인증에 사용할 사용자 이름 및 암호를 지정합니다.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>클라이언트 인증서

PFX 파일의 base64로 인코딩된 콘텐츠 및 암호를 지정합니다.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>관리 ID

데이터 팩터리에 대한 관리 ID를 사용하여 요청되는 액세스 토큰에 대한 리소스 URI를 지정합니다. Azure Resource 관리 API를 호출하려면 `https://management.azure.com/`을 사용합니다. 관리 ID의 작동 방식에 대한 자세한 내용은 [Azure 리소스의 관리 ID 개요 페이지](/azure/active-directory/managed-identities-azure-resources/overview)를 참조하세요.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 데이터 팩터리가 git 리포지토리로 구성 된 경우 기본 또는 클라이언트 인증서 인증을 사용 하도록 Azure Key Vault에 자격 증명을 저장 해야 합니다. Azure Data Factory는 git에 암호를 저장 하지 않습니다.

## <a name="additional-notes"></a>추가적인 참고 사항

Azure Data Factory는 본문의 추가 속성인 "callBackUri"를 url 끝점에 전달 하 고,이 uri는 지정 된 시간 제한 값 보다 먼저 호출 될 것으로 간주 합니다. Uri가 호출 되지 않으면 작업이 실패 하 고 ' TimedOut ' 상태가 나타납니다.

사용자 지정 끝점에 대 한 호출이 실패 하면 웹 후크 작업 자체가 실패 합니다. 모든 오류 메시지는 콜백 본문에 추가 되 고 후속 작업에 사용 될 수 있습니다.

콜백 URI로 다시 전달 되는 본문은 유효한 JSON 이어야 합니다. `application/json`콘텐츠 형식 헤더를 설정 해야 합니다.

"콜백에 상태 보고" 옵션을 사용 하는 경우 콜백을 수행할 때 본문에 다음 코드 조각을 추가 해야 합니다.

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>다음 단계

Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
