---
title: 기술력과의 사용자 지정 AML 기술
titleSuffix: Azure Cognitive Search
description: Azure Machine Learning 모델을 사용 하 여 Azure Cognitive Search 기술력과의 기능을 확장 합니다.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6a3916a41635a1c76bddbb092294f6d362fc6050
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924714"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure Cognitive Search 보강 파이프라인의 AML 기술

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 .NET SDK는 지원되지 않습니다.

**Aml** 스킬을 사용 하면 사용자 지정 [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) (aml) 모델을 사용 하 여 AI 보강을 확장할 수 있습니다. AML 모델을 [학습 하 고 배포한](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)후 **aml** 기술은이를 AI 보강 통합 합니다.

기본 제공 기술과 마찬가지로 **AML** 스킬에는 입력 및 출력이 있습니다. 입력은 배포 된 AML 서비스에 json 개체로 전송 되며, json 페이로드는 성공 상태 코드와 함께 응답으로 JSON 페이로드를 출력 합니다. 응답은 **AML** 기술로 지정 된 출력을 포함할 것으로 예상 됩니다. 다른 응답은 오류로 간주되며 강화는 수행되지 않습니다.

> [!NOTE]
> 이 인덱서는 AML 서비스에서 반환 된 특정 표준 HTTP 상태 코드에 대해 두 번 다시 시도 합니다. 이러한 HTTP 상태 코드는 다음과 같습니다.
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>필수 구성 요소

* [AML 작업 영역](../machine-learning/concept-workspace.md)
* [배포 된 모델](../machine-learning/how-to-deploy-azure-kubernetes-service.md) 을 사용 하 여이 작업 영역의 [AZURE Kubernetes 서비스 AML 계산 대상](../machine-learning/concept-compute-target.md)
  * [계산 대상에서 SSL을 사용 하도록 설정 해야](../machine-learning/how-to-secure-web-service.md#deploy-on-aks-and-field-programmable-gate-array-fpga)합니다. Azure Cognitive Search는 **https** 끝점에 대 한 액세스만 허용 합니다.
  * 자체 서명 된 인증서를 사용할 수 없습니다.

## <a name="odatatype"></a>@odata.type  
Microsoft. 사용자 지정. AmlSkill

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다. 사용 하도록 선택 하는 매개 변수는 [AML 서비스에 필요한 인증 (있는 경우](#WhatSkillParametersToUse) )에 따라 달라 집니다.

| 매개 변수 이름 | Description |
|--------------------|-------------|
| `uri` | ( [인증 또는 키 인증 안 함](#WhatSkillParametersToUse)) _JSON_ 페이로드가 전송 될 [AML 서비스의 점수 매기기 URI](../machine-learning/how-to-consume-web-service.md) 입니다. **Https** URI 스키마만 사용할 수 있습니다. |
| `key` | ( [키 인증](#WhatSkillParametersToUse)에 필요) [AML 서비스의 키](../machine-learning/how-to-consume-web-service.md#authentication-with-keys)입니다. |
| `resourceId` | [토큰 인증](#WhatSkillParametersToUse)에 필요 합니다. AML 서비스의 Azure Resource Manager 리소스 ID입니다. Subscription/{guid}/resourceGroups/{MachineLearningServices/workspaces/{workspace-name}/services/{service_name} 형식 이어야 합니다. |
| `region` | ( [토큰 인증](#WhatSkillParametersToUse)의 경우 선택 사항). AML 서비스가 배포 된 [지역](https://azure.microsoft.com/global-infrastructure/regions/) 입니다. |
| `timeout` | (선택 사항) 지정할 경우 API 호출을 수행하는 http 클라이언트에 대한 시간 제한을 나타냅니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 예를 들어, 60초인 경우 `PT60S`입니다. 설정하지 않으면 기본값 30초가 선택됩니다. 제한 시간은 최대 230 초, 최소 1 초로 설정할 수 있습니다. |
| `degreeOfParallelism` | 필드 지정 된 경우 인덱서가 제공 된 끝점과 병렬로 수행 될 호출 수를 나타냅니다. 끝점이 요청 부하를 너무 많이 초과 하 여 실패 하는 경우이 값을 줄일 수 있으며, 끝점에서 더 많은 요청을 수락 하 고 인덱서 성능을 증가 시킬 수 있는 경우이 값을 낮출 수 있습니다.  이 값을 설정 하지 않으면 기본값인 5가 사용 됩니다. DegreeOfParallelism은 최대 10 개까지 설정할 수 있으며 최소값은 1입니다.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>사용할 기술 매개 변수

사용 해야 하는 AML 기술 매개 변수는 AML 서비스에서 사용 하는 인증 (있는 경우)에 따라 달라 집니다. AML 서비스는 세 가지 인증 옵션을 제공 합니다.

* [키 기반 인증](../machine-learning/concept-enterprise-security.md#authentication-for-web-service-deployment) AML 기술의 점수 매기기 요청을 인증 하기 위한 정적 키가 제공 됩니다.
  * _Uri_ 및 _키_ 매개 변수 사용
* [토큰 기반 인증](../machine-learning/concept-enterprise-security.md#authentication). AML 서비스는 [토큰 기반 인증을 사용 하 여 배포](../machine-learning/how-to-deploy-azure-kubernetes-service.md#authentication-with-tokens)됩니다. Azure Cognitive Search 서비스의 [관리 id](../active-directory/managed-identities-azure-resources/overview.md) 에는 AML 서비스의 작업 영역에서 [읽기 권한자 역할이](../machine-learning/how-to-assign-roles.md) 부여 됩니다. 그런 다음 AML 기술은 Azure Cognitive Search 서비스의 관리 id를 사용 하 여 정적 키가 필요 없는 AML 서비스에 대해 인증 합니다.
  * _ResourceId_ 매개 변수를 사용 합니다.
  * Azure Cognitive Search 서비스가 AML 작업 영역에서 다른 지역에 있는 경우 _지역_ 매개 변수를 사용 하 여 aml 서비스가 배포 된 지역을 설정 합니다.
* 인증 없음. AML 서비스를 사용 하는 데 인증이 필요 하지 않습니다.
  * _Uri_ 매개 변수 사용

## <a name="skill-inputs"></a>기술 입력

이 기술에 대해 "미리 정의된" 입력은 없습니다. 이 스킬의 실행 시점에 이미 사용할 수 있는 하나 이상의 필드를 선택 하 고 AML 서비스로 전송 되는 _JSON_ 페이로드는 다른 필드를 가질 수 있습니다.

## <a name="skill-outputs"></a>기술 출력

이 기술에 대해 "미리 정의된" 출력은 없습니다. AML 서비스가 반환 하는 응답에 따라 _JSON_ 응답에서 선택할 수 있도록 출력 필드를 추가 합니다.

## <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>샘플 입력 JSON 구조

이 _JSON_ 구조는 AML 서비스로 전송 되는 페이로드를 나타냅니다. 구조의 최상위 필드는 `inputs` 기술 정의의 섹션에 지정 된 "이름"에 해당 합니다. 해당 필드의 값은 해당 필드의 `source`에서 가져옵니다(문서의 필드 또는 다른 기술에서 가져올 수 있음).

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>샘플 출력 JSON 구조

출력은 AML 서비스에서 반환 된 응답에 해당 합니다. AML 서비스는 _JSON_ 페이로드를 반환 해야 하며 (응답 헤더를 확인 하 여 확인 `Content-Type` ), 필드가의 "이름"과 일치 하 `output` 고 값이 보강로 간주 되는 강화 개체 여야 합니다.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>인라인 셰이핑 샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>인라인 셰이핑 입력 JSON 구조

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>인라인 모양 지정 샘플 출력 JSON 구조

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>오류 사례
AML를 사용할 수 없거나 성공 하지 않은 상태 코드를 전송 하는 것 외에도 다음은 잘못 된 사례로 간주 됩니다.

* AML 서비스가 성공 상태 코드를 반환 하지만 응답에서 성공 상태 코드를 반환 하는 경우 응답이 `application/json` 잘못 된 것으로 간주 되 고 강화 수행 되지 않습니다.
* AML 서비스가 잘못 된 json을 반환 하는 경우

AML 서비스를 사용할 수 없는 경우 또는 HTTP 오류를 반환 하는 경우에는 HTTP 오류에 대 한 모든 사용 가능한 정보에 대 한 친숙 한 오류가 인덱서 실행 기록에 추가 됩니다.

## <a name="see-also"></a>참조

+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [AML 서비스 문제 해결](../machine-learning/how-to-troubleshoot-deployment.md)