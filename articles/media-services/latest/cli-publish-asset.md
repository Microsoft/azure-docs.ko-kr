---
title: Azure CLI 스크립트 예제 - 자산 게시 | Microsoft Docs
description: 이 문서에서는 Azure CLI 스크립트를 사용하여 자산을 게시하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3b3c358a84dd74595c476f029a1c8f28bc3c901f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295872"
---
# <a name="cli-example-publish-an-asset"></a>CLI 예: 자산 게시

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서의 Azure CLI 스크립트는 스트리밍 로케이터를 만들고 스트리밍 URL을 다시 가져오는 방법을 보여줍니다. 

## <a name="prerequisites"></a>사전 요구 사항 

[Media Services 계정 만들기](./create-account-howto.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/publish-asset/Publish-Asset.sh "Publish an asset")]

## <a name="next-steps"></a>다음 단계

[Media Services 개요](media-services-overview.md)
