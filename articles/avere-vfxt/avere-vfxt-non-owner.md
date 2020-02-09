---
title: Avere vFXT 소유자 권한이 없는 사용자 해결 방법 - Azure
description: 구독 소유자 권한이 없는 사용자가 Avere vFXT for Azure를 배포하는 문제를 해결하는 방법입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 1b411fe465a67f8ea5421ac0dc93348b4e92e8ec
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153278"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>소유자 권한이 없는 사용자에게 Avere vFXT 배포 권한 부여

다음 지침은 구독 소유자 권한이 없는 사용자가 Avere vFXT for Azure 시스템을 만들 수 있는 해결 방법입니다.

(Avere vFXT 시스템을 배포하는 데 권장되는 방법은 [Avere vFXT 만들기 준비](avere-vfxt-prereqs.md)에서 설명한 대로 소유자 권한이 있는 사용자가 만들기 단계를 수행하도록 하는 것입니다.)  

해결 방법에는 사용자에게 클러스터를 설치할 수 있는 충분한 권한을 부여하는 추가 액세스 역할을 만드는 작업이 포함됩니다. 역할은 구독 소유자가 만들어야 하며, 이 소유자가 적절한 사용자에게 해당 역할을 할당해야 합니다.

또한 구독 소유자는 Avere vFXT 마켓플레이스 이미지에 대한 [사용 약관에도 동의](avere-vfxt-prereqs.md)해야 합니다.

> [!IMPORTANT]
> 이러한 모든 단계는 클러스터에 사용할 구독에 대한 소유자 권한이 있는 사용자가 수행해야 합니다.

1. 다음 줄을 복사하여 파일(예: `averecreatecluster.json`)에 저장합니다. `AssignableScopes` 문의 구독 ID를 사용합니다.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. 다음 명령을 실행하여 역할을 만듭니다.

   `az role definition create --role-definition <PATH_TO_FILE>`

    예:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. 클러스터를 만들 사용자에게 이 역할을 할당합니다.

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

이 프로세스를 완료 하면 역할은 사용자에 게 구독에 대한 다음 사용 권한을 할당 합니다.

* 네트워크 인프라 만들기 및 구성
* 클러스터 컨트롤러 만들기
* 클러스터 컨트롤러에서 클러스터 만들기 스크립트를 실행하여 클러스터 만들기
