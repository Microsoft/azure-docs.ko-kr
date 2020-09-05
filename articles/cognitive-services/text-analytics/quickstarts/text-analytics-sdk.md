---
title: '빠른 시작: Text Analytics 클라이언트 라이브러리 v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Azure Cognitive Services에서 애플리케이션을 Text Analytics API에 연결합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 2d81175c73928d25c06acb396dac587c7cc73637
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931185"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>빠른 시작: Text Analytics 클라이언트 라이브러리 사용

Text Analytics 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

Text Analytics 클라이언트 라이브러리를 사용하여 수행하는 작업은 다음과 같습니다.

* 정서 분석
* 언어 검색
* 엔터티 인식
* 핵심 문구 추출

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.0`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.0`입니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.0`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요.
> * [브라우저에서](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md) 이 버전의 Text Analytics 클라이언트 라이브러리를 실행할 수도 있습니다.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * Text Analytics API의 안정적인 최신 버전은 `3.0`입니다.
>    * 사용 중인 버전에 대한 지침만 준수해야 합니다.
> * 간단한 설명을 위해 이 문서의 코드에서는 동기 메서드와 보안되지 않은 자격 증명 스토리지를 사용합니다. 프로덕션 시나리오의 경우 성능 및 확장성을 위해 일괄 처리된 비동기 메서드를 사용하는 것이 좋습니다. 아래의 참조 설명서를 참조하세요. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>추가 언어 지원

이 탭을 클릭한 경우 선호하는 프로그래밍 언어의 빠른 시작이 표시되지 않을 수 있습니다. 걱정하지 마세요. 추가 빠른 시작을 사용할 수 있습니다. 표를 사용하여 프로그래밍 언어에 적합한 샘플을 찾습니다.

| 언어 | 사용 가능한 버전 | 
|----------|------------------------|
| Ruby     | [버전 2.1](ruby-sdk.md) | 
| Go       | [버전 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [솔루션 살펴보기](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
* [언어 감지](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [언어 인식](../how-tos/text-analytics-how-to-language-detection.md)
