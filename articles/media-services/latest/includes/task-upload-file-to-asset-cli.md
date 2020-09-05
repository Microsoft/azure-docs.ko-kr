---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657141"
---
<!-- ### Upload files with the CLI -->

다음 명령은 단일 파일을 업로드합니다.  

다음 값을 업데이트합니다.

1. `/path/to/file`을 파일의 로컬 경로로 변경합니다.  
1. 자산 ID(이름 아님)를 사용하여 `MyContainer`를 이전에 만든 자산으로 변경합니다.
1. `MyBlob`을 업로드된 파일에 사용할 이름으로 변경합니다.
1. `MyStorageAccountName`을 사용하는 스토리지 계정의 이름으로 변경합니다.
1. `MyStorageAccountKey`을 스토리지 계정의 액세스 키로 변경합니다.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
