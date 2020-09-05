---
title: CloudSimple의 Azure VMware 솔루션-CloudSimple service 만들기
description: Azure Portal에서 CloudSimple 서비스를 만드는 방법에 대해 알아봅니다. 시작 하기 전에 필요한 구성을 검토 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2a196e717dd6e02570e4bdf830a24ce342b47ece
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140550"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>CloudSimple service에서 Azure VMware 솔루션 만들기

CloudSimple로 Azure VMware 솔루션을 시작 하려면 Azure Portal에서 CloudSimple service로 Azure VMware 솔루션을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

게이트웨이 서브넷에 대 한/28 CIDR 블록을 할당 합니다. 게이트웨이 서브넷은 CloudSimple 서비스 마다 필요 하며 생성 된 지역에 대해 고유 합니다. 게이트웨이 서브넷은 edge 네트워크 서비스에 사용 되며/28 CIDR 블록이 필요 합니다. 게이트웨이 서브넷 주소 공간은 고유 해야 합니다. CloudSimple 환경과 통신 하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신 하는 네트워크에는 온-프레미스 네트워크 및 Azure virtual network가 포함 됩니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스**를 선택합니다.
2. **Cloudsimple 서비스**를 검색 합니다.
    ![CloudSimple Service 검색](media/create-cloudsimple-service-search.png)
3. **Cloudsimple 서비스**를 선택 합니다.
4. **추가** 를 클릭 하 여 새 서비스를 만듭니다.
    ![CloudSimple Service 추가](media/create-cloudsimple-service-add.png)
5. CloudSimple 서비스를 만들려는 구독을 선택 합니다.
6. 서비스에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 **새로 만들기**를 클릭 합니다.
7. 서비스를 식별 하는 이름을 입력 합니다.
8. 서비스 게이트웨이의 CIDR을 입력 합니다. 온-프레미스 서브넷, Azure 서브넷 또는 계획 된 CloudSimple 서브넷과 겹치지 않는/28 서브넷을 지정 합니다. 서비스를 만든 후에는 CIDR을 변경할 수 없습니다.

    ![CloudSimple 서비스 만들기](media/create-cloudsimple-service.png)
9. **확인**을 클릭합니다.

서비스가 만들어지고 서비스 목록에 추가 됩니다.

## <a name="next-steps"></a>다음 단계

* [노드를 프로 비전](create-nodes.md) 하는 방법 알아보기
* [사설 클라우드를 만드는](create-private-cloud.md) 방법 알아보기
* [사설 클라우드 환경을 구성](quickstart-create-private-cloud.md) 하는 방법 알아보기
