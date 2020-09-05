---
author: baanders
description: 기본 Azure Digital Twins 인스턴스 및 앱 등록을 정리하기 위한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891501"
---
이 자습서에서 만든 리소스가 더 이상 필요하지 않은 경우 다음 절차에 따라 삭제합니다.

[Azure Cloud Shell](https://shell.azure.com)을 사용하면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령으로 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 그러면 리소스 그룹과 Azure Digital Twins 인스턴스가 제거됩니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

Azure Cloud Shell을 열고 다음 명령을 실행하여 리소스 그룹과 그 안에 포함된 모든 항목을 삭제합니다.

```azurecli-interactive
az group delete --name <your-resource-group>
```

그리고 다음 명령을 사용하여 클라이언트 앱에 대해 만든 Azure Active Directory 앱 등록을 삭제합니다.

```azurecli-interactive
az ad app delete --id <your-application-ID>
```