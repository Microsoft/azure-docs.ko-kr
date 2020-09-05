---
title: Azure CLI를 사용 하 여 Cognitive Services 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Azure 명령줄 인터페이스를 사용 하 여 리소스를 만들고 구독 하 여 Azure Cognitive Services를 시작 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 36e21a131181831c2a87c0c6d2c24c9aa6e0acf7
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245012"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Azure 명령줄 인터페이스 (CLI)를 사용 하 여 Cognitive Services 리소스 만들기

[AZURE CLI (명령줄 인터페이스)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용 하 여 azure Cognitive Services를 시작 하려면이 빠른 시작을 사용 하세요. Cognitive Services은 Azure 구독에서 만든 Azure [리소스로](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) 표시 됩니다. 리소스를 만든 후에는 응용 프로그램을 인증 하기 위해 생성 된 키 및 끝점을 사용 합니다.


이 빠른 시작에서는 azure Cognitive Services에 등록 하 고 [AZURE CLI (명령줄 인터페이스)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용 하 여 단일 서비스 또는 다중 서비스 구독이 있는 계정을 만드는 방법에 대해 알아봅니다. 이러한 서비스는 하나 이상의 Azure Cognitive Services API에 연결할 수 있는 Azure [리소스로](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)표시 됩니다.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>필수 구성 요소

* 유효한 Azure 구독-무료로 [하나를 만듭니다](https://azure.microsoft.com/free/cognitive-services) .
* [AZURE CLI (명령줄 인터페이스)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLI 설치 및 로그인

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치 합니다. CLI의 로컬 설치에 로그인 하려면 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) 명령을 실행 합니다.

```azurecli-interactive
az login
```

또한 녹색 **사용해 보기** 단추를 사용 하 여 브라우저에서 이러한 명령을 실행할 수 있습니다.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>새 Azure Cognitive Services 리소스 그룹 만들기

Cognitive Services 리소스를 만들기 전에 리소스를 포함 하는 Azure 리소스 그룹이 있어야 합니다. 새 리소스를 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용 하는 옵션이 있습니다. 이 문서에서는 새 리소스 그룹을 만드는 방법을 보여 줍니다.

### <a name="choose-your-resource-group-location"></a>리소스 그룹 위치 선택

리소스를 만들려면 구독에 사용할 수 있는 Azure 위치 중 하나가 필요 합니다. [Az account list-위치도](/cli/azure/account#az-account-list-locations) command를 사용 하 여 사용 가능한 위치 목록을 검색할 수 있습니다. 대부분의 Cognitive Services는 여러 위치에서 액세스할 수 있습니다. 가장 가까운 것을 선택 하거나 서비스에 사용할 수 있는 위치를 확인 합니다.

> [!IMPORTANT]
> * Azure Cognitive Services를 호출할 때 필요 하므로 Azure 위치를 염두에 두어야 합니다.
> * 일부 Cognitive Services의 가용성은 지역에 따라 다를 수 있습니다. 자세한 내용은 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)을 참조 하세요.  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure 위치를 만든 후에 [az group create](/cli/azure/group#az-group-create) 명령을 사용 하 여 Azure CLI에 새 리소스 그룹을 만듭니다.

아래 예제에서는 Azure 위치를 `westus2` 구독에 사용할 수 있는 azure 위치 중 하나로 바꿉니다.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>인식 서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/) (또는 sku)과 함께 사용 하려는 서비스의 "종류"를 알고 있어야 합니다. 리소스를 만들 때이 및 기타 정보를 매개 변수로 사용 합니다.

### <a name="multi-service"></a>다중 서비스

| 서비스                    | 종류                      |
|----------------------------|---------------------------|
| 여러 서비스. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/) 페이지를 참조 하세요.            | `CognitiveServices`     |


> [!NOTE]
> 아래 Cognitive Services 대부분은 서비스를 시도 하는 데 사용할 수 있는 무료 계층이 있습니다. 무료 계층을 사용 하려면 `F0` 리소스의 sku로를 사용 합니다.

### <a name="vision"></a>Vision

| 서비스                    | 종류                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision-예측 | `CustomVision.Prediction` |
| Custom Vision 교육   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>검색

| 서비스            | 종류                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing 사용자 지정 검색 | `Bing.CustomSearch`   |
| Bing Entity Search | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing 맞춤법 검사   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>음성

| 서비스            | 종류                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| 음성 인식 | `SpeakerRecognition` |

### <a name="language"></a>언어

| 서비스            | 종류                |
|--------------------|---------------------|
| 양식 이해 | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 텍스트 분석     | `TextAnalytics`     |
| 텍스트 번역   | `TextTranslation`   |

### <a name="decision"></a>의사 결정

| 서비스           | 종류               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

[Az cognitiveservices account account list-종류](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) 명령을 사용 하 여 사용 가능한 인식 서비스 "종류"의 목록을 찾을 수 있습니다.

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>리소스 그룹에 새 리소스 추가

새 Cognitive Services 리소스를 만들고 구독 하려면 [az cognitiveservices account account create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create) 명령을 사용 합니다. 이 명령은 이전에 만든 리소스 그룹에 청구 가능한 리소스를 새로 추가 합니다. 새 리소스를 만들 때 가격 책정 계층 (또는 sku) 및 Azure 위치와 함께 사용 하려는 서비스의 "종류"를 알고 있어야 합니다.

다음 명령을 사용 하 여 명명 된 변칙 감지기에 대 한 F0 (무료) 리소스를 만들 수 있습니다 `anomaly-detector-resource` .

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>리소스의 키를 가져옵니다.

CLI (명령줄 인터페이스)의 로컬 설치에 로그인 하려면 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 명령을 사용 합니다.

```azurecli-interactive
az login
```

[Az cognitiveservices account account keys list](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) 명령을 사용 하 여 인식 서비스 리소스의 키를 가져옵니다.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>가격 책정 계층 및 요금 청구

가격 책정 계층 및 청구 되는 금액은 인증 정보를 사용 하 여 전송 하는 트랜잭션 수를 기반으로 합니다. 각 가격 책정 계층은 다음을 지정 합니다.
* 초당 허용 되는 최대 트랜잭션 수 (TPS)입니다.
* 가격 책정 계층 내에서 사용 하도록 설정 된 서비스 기능입니다.
* 미리 정의 된 트랜잭션 양에 대 한 비용입니다. 이 용량을 초과 하면 서비스에 대 한 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 에 지정 된 대로 추가 요금이 발생 합니다.

## <a name="get-current-quota-usage-for-your-resource"></a>리소스의 현재 할당량 사용량을 가져옵니다.

[Az cognitiveservices account account list-usage](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-usage) 명령을 사용 하 여 인지 서비스 리소스에 대 한 사용 현황을 가져옵니다.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 리소스를 정리 하 고 제거 하려면 해당 리소스 그룹을 삭제 하거나 리소스 그룹을 삭제 하면 됩니다. 리소스 그룹을 삭제하면 그룹에 포함된 모든 리소스가 함께 삭제됩니다.

리소스 그룹 및 연결 된 리소스를 제거 하려면 az group delete 명령을 사용 합니다.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>참고 항목

* [Azure Cognitive Services에 대한 요청 인증](authentication.md)
* [Azure Cognitive Services란?](Welcome.md)
* [자연어 지원](language-support.md)
* [Docker 컨테이너 지원](cognitive-services-container-support.md)
