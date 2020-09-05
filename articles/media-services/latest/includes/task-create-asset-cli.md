---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605975"
---
<!--Create a media services asset CLI-->

다음 Azure CLI 명령은 새 Media Services 자산을 만듭니다. `assetName` `amsAccountName` 및 `resourceGroup` 값을 현재 작업 중인 값으로 바꿉니다.

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
