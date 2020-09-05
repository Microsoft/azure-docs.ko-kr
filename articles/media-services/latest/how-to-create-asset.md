---
title: Azure CLI를 사용하여 Azure Media Services 자산에 콘텐츠 업로드
description: 이 토픽의 Azure CLI 스크립트는 콘텐츠를 업로드 할 Media Services Asset을 만드는 방법을 보여줍니다.
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
ms.openlocfilehash: aa5c9178bc84983b7e577cd63cf5b8d9fb9a8436
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254664"
---
# <a name="create-an-asset"></a>자산 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 Media Services 자산을 만드는 방법을 보여줍니다.  자산을 사용하여 인코딩 및 스트리밍을 위한 미디어 콘텐츠를 보관할 것입니다.  Media Services 자산에 대한 자세한 내용은 [Azure Media Services v3의 자산](assets-concept.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[Media Services 계정 만들기](./create-account-howto.md)의 단계에 따라 자산을 만드는 데 필요한 Media Services 계정 및 리소스 그룹을 만듭니다.

## <a name="methods"></a>메서드

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST (영문)](#tab/rest/)

### <a name="using-rest"></a>REST 사용

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>cURL 사용

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>Postman 사용

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>다음 단계

[Media Services 개요](media-services-overview.md)
