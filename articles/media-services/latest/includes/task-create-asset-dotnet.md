---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653891"
---
<!--Create a media services asset REST-->

다음 Azure .NET 명령은 새 Media Services 자산을 만듭니다. `subscriptionID`, 및 값을 `resourceGroup` `amsAccountName` 현재 작업 중인 값으로 바꿉니다. 여기에서 설정 하 여 자산 이름을 지정 `assetName` 합니다.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
