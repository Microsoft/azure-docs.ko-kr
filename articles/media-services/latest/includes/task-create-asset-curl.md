---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: cURL
ms.openlocfilehash: ca7f0dab2302386a9cb7d21ebb3224d24afe5e19
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608897"
---
<!--Create a media services asset cURL-->

다음 Azure 말아 명령은 새 Media Services 자산을 만듭니다. `subscriptionID`, 및 값을 `resourceGroup` `amsAccountName` 현재 작업 중인 값으로 바꿉니다. 여기에서 설정 하 여 자산 이름을 지정 `assetName` 합니다.

```cURL
curl -X PUT 'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{"properties": {"description": "",}}'
```
