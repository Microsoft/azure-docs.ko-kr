---
title: 사용되지 않는 인식 기술 - Azure Search
description: 이 페이지에는 사용되지 않는 것으로 간주되어 향후 지원되지 않는 인식 검색 기술 목록이 포함됩니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e4849487c2dd9330dbf9e6b6cf0ed4d1b6b96020
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637521"
---
# <a name="deprecated-cognitive-search-skills"></a>사용되지 않는 인식 검색 기술

이 문서에서는 사용되지 않는 것으로 간주되는 기술을 설명합니다. 내용에 대해서는 다음 가이드를 사용합니다.

* 기술 이름: 사용되지 않는 기술 이름이 @odata.type 특성에 매핑됩니다.
* 사용 가능한 마지막 api 버전: 사용되지 않는 해당 기술이 포함된 기술 세트를 만들고 업데이트할 수 있는 Azure Search 공용 API의 마지막 버전입니다.
* 지원 종료: 이 날짜 이후에는 해당 기술이 지원되지 않는 것으로 간주되는 마지막 날입니다. 이전에 만든 기술 세트는 계속 작동해야 하지만 사용자는 사용되지 않는 기술에서 마이그레이션하는 것이 좋습니다.
* 권장 사항: 지원되는 기술을 사용하도록 마이그레이션 경로를 전달합니다. 사용자는 지원을 계속 받을 수 있도록 권장 사항을 따르는 것이 좋습니다.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>사용 가능한 마지막 api 버전

2017-11-11-preview

### <a name="end-of-support"></a>지원 종료

2019년 2월 15일

### <a name="recommendations"></a>권장 사항 

대신 [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)을 사용합니다. NamedEntityRecognitionSkill의 대부분의 기능을 더 높은 품질로 제공합니다. 또한 복잡한 출력 필드에 다양한 정보가 있습니다.

[엔터티 인식 기술](cognitive-search-skill-entity-recognition.md)로 마이그레이션하려면 기술 정의에 다음 변경 사항 중 하나 이상을 수행해야 합니다. [기술 세트 API 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-skillset)를 사용하여 기술 정의를 업데이트할 수 있습니다.

_참고_: 현재 개념으로서 신뢰도 점수는 지원되지 않습니다. 신뢰도 점수는 나중에 지원될 예정입니다. `minimumPrecision` 매개 변수는 향후 사용 및 이전 버전과의 호환성을 위해 `EntityRecognitionSkill`에 있습니다.

1. *(필수)* `@odata.type`을 `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"`에서 `"#Microsoft.Skills.Text.EntityRecognitionSkill"`로 변경

2. *(선택 사항)* `entities` 출력을 사용하는 경우 대신 `EntityRecognitionSkill`에서 `namedEntities` 복잡한 컬렉션 출력을 사용합니다. 기술 정의에서 `targetName`을 사용하여 `entities`라는 주석에 매핑할 수 있습니다.

3. *(선택 사항)* 명시적으로 `categories`를 지정하지 않는 경우 `EntityRecognitionSkill`은 `NamedEntityRecognitionSkill`에서 지원된 카테고리 외에 다른 형식의 카테고리를 반환할 수 있습니다. 이 동작이 바람직하지 않은 경우 명시적으로 `categories` 매개 변수를 `["Person", "Location", "Organization"]`으로 설정해야 합니다.

    _샘플 마이그레이션 정의_

    * 간단한 마이그레이션

        _(이전) NamedEntityRecognition 기술 정의_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(이후) EntityRecognition 기술 정의_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * 약간 복잡한 마이그레이션

        _(이전) NamedEntityRecognition 기술 정의_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(이후) EntityRecognition 기술 정의_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술](cognitive-search-skill-entity-recognition.md)
