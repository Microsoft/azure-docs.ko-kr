---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556362"
---
<!-- List and set subscriptions -->

1. [az account list](/cli/azure/account#az-account-list) 명령을 사용하여 구독 리스트를 가져옵니다.

    ```
    az account list --output table
    ```

2. 전환하려고 하는 구독 ID 또는 이름으로 `az account set`을 사용합니다.

    ```
    az account set --subscription "My Demos"
    ```
