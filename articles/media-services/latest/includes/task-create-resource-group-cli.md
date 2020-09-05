---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602353"
---
<!-- Create a resource group -->

다음 명령을 사용 하 여 리소스 그룹을 만듭니다. Media Services 계정에 대 한 메타 데이터 레코드 및 미디어를 저장 하는 데 사용할 지역을 선택 합니다. 이 지역은 미디어를 처리하고 스트림하는 데 사용됩니다.

```azurecli
az group create --name amsResourceGroup --location westus2
```
