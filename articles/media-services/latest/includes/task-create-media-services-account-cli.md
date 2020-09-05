---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602548"
---
<!--Create a media services account -->

다음 Azure CLI 명령은 새 Media Services 계정을 만듭니다. 다음 값을 바꿀 수 있습니다. `amsaccount` `storageaccountforams` (는 저장소 계정에 대해 지정한 값과 일치 해야 함) 및 `amsResourceGroup` (리소스 그룹에 대해 지정한 값과 일치 해야 함)  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```