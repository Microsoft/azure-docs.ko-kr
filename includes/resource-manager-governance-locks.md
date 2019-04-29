---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742140"
---
리소스 잠금은 조직의 사용자가 실수로 중요한 리소스를 삭제하거나 수정하는 것을 방지합니다. 역할 기반 액세스 제어와 달리 리소스 잠금은 모든 사용자와 역할 전반에 제한을 적용합니다. 

잠금 수준을 **CanNotDelete** 또는 **ReadOnly**로 설정할 수 있습니다. 포털에서 잠금 수준이 각각 **Delete** 및 **로 표시됩니다**.

* **CanNotDelete**는 권한이 부여된 사용자가 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다. 
* **ReadOnly**는 권한이 부여된 사용자가 리소스를 읽을 수 있지만 해당 리소스를 삭제하거나 업데이트할 수 없음을 의미합니다. 이 잠금을 적용하는 것은 권한 있는 모든 사용자에게 **판독기** 역할에서 부여한 권한을 제한하는 것과 유사합니다. 

> [!TIP]
> **ReadOnly** 잠금을 적용할 때는 주의하세요. 읽기 작업처럼 보이는 일부 작업은 실제로 추가적인 조치를 요구합니다. 예를 들어, 저장소 계정에 **ReadOnly** 잠금을 설정하면 모든 사용자가 키를 나열하지 않도록 방지합니다. 반환되는 키를 쓰기 작업에 사용할 수 있기 때문에 목록 키 작업은 POST 요청을 통해 처리됩니다. App Service 리소스에 **ReadOnly** 잠금을 설정하면 해당 상호 작용이 쓰기 액세스를 필요로 하기 때문에 Visual Studio 서버 탐색기가 리소스에 대한 파일을 표시하지 않도록 방지합니다.

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

리소스 관리자 잠금은 `https://management.azure.com`에 전송된 작업으로 구성되는 관리 수준에서 발생된 작업에만 적용됩니다. 잠금은 리소스가 자체 기능을 수행하는 방법을 제한하지 않습니다. 리소스 변경은 제한되지만 리소스 작업은 제한되지 않습니다. 예를 들어, SQL Database에 대해 ReadOnly 잠금이 지정되면 데이터베이스를 삭제하거나 수정하지 못합니다. 데이터베이스에서 데이터를 생성, 업데이트 또는 삭제하지 못하게 됩니다. 이러한 작업이 `https://management.azure.com`로 전송되지 않기 때문에 데이터 전송이 허용됩니다.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>조직에서 잠금을 만들거나 삭제할 수 있는 사람
관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다.
