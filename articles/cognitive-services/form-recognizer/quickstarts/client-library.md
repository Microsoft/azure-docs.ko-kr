---
title: '빠른 시작: Form Recognizer 클라이언트 라이브러리 또는 REST API'
titleSuffix: Azure Cognitive Services
description: Form Recognizer 클라이언트 라이브러리 또는 REST API를 사용하여 사용자 지정 문서에서 키/값 쌍 및 테이블 데이터를 추출하는 양식 처리 앱을 만듭니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: 양식 처리, 자동화된 데이터 처리
ms.openlocfilehash: b4631892f1c35c665c4468a6e0b3ad481a19e8df
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516402"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>빠른 시작: Form Recognizer 클라이언트 라이브러리 또는 REST API 사용

선택한 개발 언어로 Form Recognizer를 시작합니다. Azure Form Recognizer는 기계 학습 기술을 사용하여 자동화된 데이터 처리 소프트웨어를 빌드할 수 있는 인식 서비스입니다. 양식 문서에서 텍스트, 키/값 쌍, 선택 표시 및 테이블 데이터 등을 식별하고 추출합니다. &mdash;서비스는 원본 파일의 관계를 포함하는 구조화된 데이터를 출력합니다. REST API 또는 SDK를 통해 Form Recognizer를 사용할 수 있습니다. 다음 단계에 따라 SDK 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 보세요.

Form Recognizer를 사용하여 다음을 수행합니다.

* [레이아웃 분석](#analyze-layout)
* [영수증 분석](#analyze-receipts)
* [명함 분석](#analyze-business-cards)
* [송장 분석](#analyze-invoices)
* [ID 문서 분석](#analyze-identity-documents)
* [사용자 지정 모델 학습](#train-a-custom-model)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 관리](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
