---
title: Azure Container Instances 실행-Text Analytics
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 컨테이너를 Azure Container Instance에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: f7dbb79e2d9009b00b21d4c12663d8e6c855a4c0
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243754"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Azure Container Instances에 Text Analytics 컨테이너 배포

Cognitive Services [Text Analytics][install-and-run-containers] 컨테이너를 Azure [Container Instances][container-instances]에 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 Text Analytics 리소스 생성, 연결 된 감정 분석 이미지 만들기, 브라우저에서 두 작업의이 오케스트레이션을 실행 하는 기능을 예시 합니다. 컨테이너를 사용 하면 개발자가 인프라를 관리 하지 않고 응용 프로그램 개발에 집중 하는 것으로 전환할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독을 사용합니다. Azure 구독이 아직 없는 경우, 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만드세요.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[언어 감지](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[상태 Text Analytics](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>다음 단계 

* 추가적인 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md) 사용
* [Text Analytics 연결 된 서비스](../vs-text-connected-service.md) 사용

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances