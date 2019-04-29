---
title: Azure Data Factory에서 웹 후크 작업 | Microsoft Docs
description: 웹 후크 작업 파이프라인의 실행을 사용자 지정 하는 특정 조건 사용 하 여 연결 된 데이터 집합의 유효성을 검사 될 때까지 계속 되지 않습니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 6ec43b06ce266b9ceaddb5dd21cbf52f509d6596
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764308"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory에서 웹 후크 작업
사용자 지정 코드를 통해 파이프라인 실행을 제어 하는 웹 후크 작업을 사용할 수 있습니다. 고객 수 webhook 활동을 사용 하는 끝점을 호출 하 고 콜백 URL을 전달 합니다. 파이프라인 실행은 다음 작업을 계속 하기 전에 호출할 콜백을 기다립니다.

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



자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
이름 | 웹 후크 작업의 이름 | String | 예 |
형식 | 로 설정 되어야 합니다 **웹 후크**합니다. | String | 예 |
메서드 | 대상 엔드포인트에 대한 Rest API 메서드입니다. | 문자열입니다. 지원되는 형식: ' POST' | 예 |
URL | 대상 엔드포인트 및 경로입니다. | 문자열(또는 resultType 문자열이 있는 식). | 예 |
headers | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어와 유형을 설정 하려면: "헤더": {"수용 언어": "en-우리", "Content-type": "application/json"을 (를). | 문자열(또는 resultType 문자열이 있는 식) | 예, Content-Type 헤더가 필요합니다. "headers":{ "Content-Type":"application/json"} |
본문 | 엔드포인트에 전송된 페이로드를 나타냅니다. | 본문 다시 호출에 다시 전달 된 URI는 유효한 JSON 이어야 합니다. [요청 페이로드 스키마](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0) 섹션에서 요청 페이로드의 스키마를 참조하세요. | 예 |
인증 | 엔드포인트를 호출하는 데 사용되는 인증 방법입니다. 지원 되는 형식은 "Basic" 또는 "ClientCertificate"입니다. 자세한 내용은 [인증](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0) 섹션을 참조하세요. 인증이 필요 없는 경우 이 속성을 제외합니다. | 문자열(또는 resultType 문자열이 있는 식) | 아닙니다. |
시간 제한 | 작업은 대기 시간를 &#39;callBackUri&#39; 를 호출 합니다. 기간 활동 호출할 'callBackUri' 대기 합니다. 기본값은 10mins ("00: 10:00"). 즉, d.hh:mm:ss Timespan 형식은 | String | 아닙니다. |

## <a name="additional-notes"></a>추가적인 참고 사항

Azure Data Factory는 추가 속성을 "callBackUri" url 끝점의 본문에서 전달 됩니다 하 고이 uri를 지정 된 시간 제한 값 보다 먼저 호출 수를 예상 합니다. Uri가 호출 되지 '초과' 상태로 작업이 실패 합니다.

웹 후크 작업 자체에 사용자 지정 끝점에 대 한 호출에 실패 한 경우 실패 합니다. 오류 메시지 콜백 본문에 추가 하 고 후속 활동에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
