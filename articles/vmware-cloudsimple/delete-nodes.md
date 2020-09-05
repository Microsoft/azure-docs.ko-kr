---
title: CloudSimple에서 VMware 솔루션에 대 한 노드 삭제
description: CloudSimple 배포를 사용 하 여 VMWare에서 노드를 삭제 하는 방법에 대해 알아봅니다. CloudSimple 노드는 계량 됩니다. Azure Portal에서 사용 되지 않는 노드를 삭제 합니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142267"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>CloudSimple로 Azure VMware 솔루션에서 노드 삭제

CloudSimple 노드는 생성 되 면 계량 됩니다.  노드 계량을 중지 하려면 노드를 삭제 해야 합니다.  Azure Portal에서 사용 되지 않는 노드를 삭제 합니다.

## <a name="before-you-begin"></a>시작하기 전에

노드는 다음 조건 에서만 삭제할 수 있습니다.

* 노드를 사용 하 여 만든 사설 클라우드는 삭제 됩니다.  사설 클라우드를 삭제 하려면 [CloudSimple 사설 클라우드에서 Azure VMware 솔루션 삭제](delete-private-cloud.md)를 참조 하세요.
* 사설 클라우드를 축소 하 여 사설 클라우드에서 노드가 제거 되었습니다.  사설 클라우드를 축소 하려면 [CloudSimple 사설 클라우드에서 Azure VMware 솔루션 축소](shrink-private-cloud.md)를 참조 하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="delete-cloudsimple-node"></a>CloudSimple 노드 삭제

1. **모든 서비스**를 선택합니다.

2. **Cloudsimple 노드**를 검색 합니다.

   ![CloudSimple 노드 검색](media/create-cloudsimple-node-search.png)

3. **Cloudsimple 노드**를 선택 합니다.

4. 삭제할 사설 클라우드에 속하지 않는 노드를 선택 합니다.  **사설 클라우드 이름** 열 노드가 속한 사설 클라우드 이름을 표시 합니다.  노드가 사설 클라우드에서 사용 되지 않는 경우에는 값이 비어 있게 됩니다. 

    ![CloudSimple 노드 선택](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 사설 클라우드의 일부가 아닌 노드만 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [사설 클라우드에](cloudsimple-private-cloud.md) 대 한 자세한 정보
