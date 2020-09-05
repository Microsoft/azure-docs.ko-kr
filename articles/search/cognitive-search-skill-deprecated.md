---
title: 더 이상 사용 되지 않는 인식 기술
titleSuffix: Azure Cognitive Search
description: 이 페이지에는 더 이상 사용 되지 않는 것으로 간주 되며 Azure Cognitive Search 기술력과의 가까운 장래에 지원 되지 않는 인식 기술 목록이 포함 됩니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936098"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Azure Cognitive Search에서 사용 되지 않는 인식 기술

이 문서에서는 사용되지 않는 것으로 간주되는 기술을 설명합니다. 내용에 대해서는 다음 가이드를 사용합니다.

* 기술 이름: 사용되지 않는 기술 이름이 @odata.type 특성에 매핑됩니다.
* 마지막으로 사용 가능한 api 버전: 해당 하는 사용 되지 않는 스킬을 포함 하는 기술력과를 사용 하는 Azure Cognitive Search 공용 API의 마지막 버전을 만들거나 업데이트할 수 있습니다.
* 지원 종료: 종료 마지막 날 이후로 해당 기술은 지원되지 않는 것으로 간주됩니다. 이전에 만든 기술 세트는 계속 작동해야 하지만 사용자는 사용되지 않는 기술에서 마이그레이션하는 것이 좋습니다.
* 권장 사항: 지원되는 기술을 사용하기 위한 마이그레이션 경로 전달. 사용자는 지원을 계속 받을 수 있도록 권장 사항을 따르는 것이 좋습니다.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>사용 가능한 마지막 api 버전

\2017-11-11-Preview

### <a name="end-of-support"></a>지원 종료

2019년 2월 15일

### <a name="recommendations"></a>권장 사항 

대신 [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)을 사용합니다. NamedEntityRecognitionSkill의 대부분의 기능을 더 높은 품질로 제공합니다. 또한 복잡한 출력 필드에 다양한 정보가 있습니다.

[엔터티 인식 기술](cognitive-search-skill-entity-recognition.md)로 마이그레이션하려면 기술 정의에 다음 변경 사항 중 하나 이상을 수행해야 합니다. [기술 세트 API 업데이트](/rest/api/searchservice/update-skillset)를 사용하여 기술 정의를 업데이트할 수 있습니다.

> [!NOTE]
> 현재 개념으로서 신뢰도 점수는 지원되지 않습니다. `minimumPrecision` 매개 변수는 향후 사용 및 이전 버전과의 호환성을 위해 `EntityRecognitionSkill`에 있습니다.

1. *(필수)*`@odata.type`을 `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"`에서 `"#Microsoft.Skills.Text.EntityRecognitionSkill"`로 변경

2. *(선택 사항)*`entities` 출력을 사용하는 경우 대신 `EntityRecognitionSkill`에서 `namedEntities` 복잡한 컬렉션 출력을 사용합니다. 기술 정의에서 `targetName`을 사용하여 `entities`라는 주석에 매핑할 수 있습니다.

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

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술](cognitive-search-skill-entity-recognition.md)