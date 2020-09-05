---
title: CloudSimple로 VMware 솔루션에 대 한 노드 프로 비전-Azure
description: Azure Portal에서 CloudSimple 배포를 사용 하 여 VMWare에 노드를 추가 하는 방법에 대해 알아봅니다. 사설 클라우드 환경에 대 한 종 량 제 용량을 설정할 수 있습니다.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140737"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>CloudSimple로 Azure VMware 솔루션에 대 한 노드 프로 비전

Azure Portal에서 노드를 프로 비전 합니다. 그런 다음 CloudSimple 사설 클라우드 환경에 대 한 종 량 제 용량을 설정할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드에 노드 추가

1. **모든 서비스**를 선택합니다.
2. **Cloudsimple 노드**를 검색 합니다.

   ![CloudSimple 노드 검색](media/create-cloudsimple-node-search.png)

3. **Cloudsimple 노드**를 선택 합니다.
4. **추가** 를 클릭 하 여 노드를 만듭니다.

    ![CloudSimple 노드 추가](media/create-cloudsimple-node-add.png)

5. CloudSimple 노드를 프로 비전 할 구독을 선택 합니다.
6. 노드에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 **새로 만들기**를 클릭 합니다.
7. 노드를 식별 하는 접두사를 입력 합니다.
8. 노드 리소스의 위치를 선택 합니다.
9. 노드 리소스를 호스트할 전용 위치를 선택 합니다.
10. [노드 형식을](cloudsimple-node.md)선택 합니다.
11. 프로 비전 할 노드 수를 선택 합니다.
12. **검토 + 만들기**를 선택합니다.
13. 설정을 검토 합니다. 설정을 수정 하려면 **이전**을 클릭 합니다.
14. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [사설 클라우드 만들기](create-private-cloud.md)
