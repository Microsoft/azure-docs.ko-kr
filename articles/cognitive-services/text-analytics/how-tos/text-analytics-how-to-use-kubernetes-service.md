---
title: Azure Kubernetes 서비스 실행-Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics 컨테이너 이미지를 Azure Kubernetes Service에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b7a5953edd9aec96a7f75e747c39e8f07f7210bb
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243771"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 Text Analytics 컨테이너 배포

Azure Kubernetes 서비스 (AKS)에 Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) 컨테이너 이미지를 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 Text Analytics 리소스를 만드는 방법, 연결 된 감정 분석 이미지를 만드는 방법 및 브라우저에서 두 작업의 오케스트레이션을 실행 하는 방법을 보여 줍니다. 컨테이너를 사용 하면 인프라 관리에서 응용 프로그램 개발에 집중 하는 대신 주의를 끌 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다. Azure Cloud Shell 사용 하지 마세요. 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만듭니다.
* 텍스트 편집기 (예: [Visual Studio Code](https://code.visualstudio.com/download)).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 설치 되었습니다.
* [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 가 설치 되어 있습니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
    * F0 또는 표준 가격 책정 계층을 사용 하는 **Azure Text Analytics** 리소스
    * S0 가격 책정 계층을 사용 하는 **Azure Cognitive Services** 리소스

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[언어 감지](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>다음 단계

* 더 많은 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md) 사용
* [Text Analytics 연결 된 서비스](../vs-text-connected-service.md) 사용
