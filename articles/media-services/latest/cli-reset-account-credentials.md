---
title: Azure CLI 스크립트 예제 - 계정 자격 증명 다시 설정 | Microsoft Docs
description: Azure CLI 스크립트를 사용하여 계정 자격 증명을 다시 설정하고 app.config 설정을 다시 가져옵니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: da997ec71655231876749d9f3aa65ba06fd3a1f8
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295838"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI 예제: 계정 자격 증명을 다시 설정합니다.

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서의 Azure CLI 스크립트는 계정 자격 증명을 다시 설정하고 app.config 설정을 다시 가져오는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services 계정 만들기](./create-account-howto.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>예제 스크립트

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>다음 단계

* [az ams](/cli/azure/ams)
* [자격 증명 다시 설정](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
