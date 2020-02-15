---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2d440db3e55638fcf2e3d3f3fe76f82ac4d25948
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192836"
---
## <a name="limitations"></a>제한 사항

- 가상 머신 확장 집합은 현재 전용 호스트에서 지원 되지 않습니다.
- 초기 릴리스는 다음 VM 시리즈를 지원 합니다. DSv3, ESv3 및 Fsv2. 

## <a name="create-a-host-group"></a>호스트 그룹 만들기

**호스트 그룹** 은 전용 호스트의 컬렉션을 나타내는 새 리소스입니다. 지역 및 가용성 영역에서 호스트 그룹을 만들고 여기에 호스트를 추가 합니다. 고가용성을 계획할 때 추가 옵션을 사용할 수 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다를 사용할 수 있습니다. 
- 여러 가용성 영역에 걸쳐 있습니다. 이 경우에는 사용 하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 실제 랙에 매핑되는 여러 장애 도메인에 걸쳐 있습니다. 
 
두 경우 모두 호스트 그룹의 장애 도메인 수를 제공 해야 합니다. 그룹의 장애 도메인을 확장 하지 않으려면 장애 도메인 수 1을 사용 합니다. 

가용성 영역 및 장애 도메인을 모두 사용 하도록 결정할 수도 있습니다. 

이 예제에서는 1 개의 가용성 영역 및 2 개의 장애 도메인을 사용 하 여 호스트 그룹을 만듭니다. 


1. Azure [portal](https://portal.azure.com)을 엽니다.
1. 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
1. **호스트 그룹** 을 검색 하 고 결과에서 **호스트 그룹** 을 선택 합니다.

    ![호스트 그룹 검색 결과입니다.](./media/virtual-machines-common-dedicated-hosts-portal/host-group.png)
1. **호스트 그룹** 페이지에서 **만들기**를 선택 합니다.
1. 사용할 구독을 선택한 다음 **새로 만들기** 를 선택 하 여 새 리소스 그룹을 만듭니다.
1. **이름** 으로 *myDedicatedHostsRG* 를 입력 한 다음 **확인을**선택 합니다.
1. **호스트 그룹 이름**에 *myHostGroup*을 입력 합니다.
1. **위치**에 대해 **미국 동부**를 선택합니다.
1. **가용성 영역**에 대해 **1**을 선택 합니다.
1. **오류 도메인 수**에 대해 **2**를 선택 합니다.
1. **검토 + 만들기** 를 선택한 다음 유효성 검사를 기다립니다.

    ![호스트 그룹 설정](./media/virtual-machines-common-dedicated-hosts-portal/host-group-settings.png)
1. **유효성 검사 통과** 메시지가 표시 되 면 **만들기** 를 선택 하 여 호스트 그룹을 만듭니다.

호스트 그룹을 만드는 데 몇 분 밖에 걸리지 않습니다.

## <a name="create-a-dedicated-host"></a>전용 호스트 만들기

이제 호스트 그룹에서 전용 호스트를 만듭니다. 호스트의 이름 외에 호스트의 SKU를 제공 해야 합니다. 호스트 SKU는 지원 되는 VM 시리즈 뿐만 아니라 전용 호스트의 하드웨어 생성도 캡처합니다.

호스트 Sku 및 가격 책정에 대 한 자세한 내용은 [Azure 전용 호스트 가격](https://aka.ms/ADHPricing)을 참조 하세요.

호스트 그룹에 대 한 장애 도메인 수를 설정 하는 경우 호스트에 대 한 장애 도메인을 지정 하 라는 메시지가 표시 됩니다.  

1. 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
1. **전용 호스트** 를 검색 한 다음 결과에서 **전용 호스트** 를 선택 합니다.

    ![호스트 그룹 검색 결과입니다.](./media/virtual-machines-common-dedicated-hosts-portal/host.png)
1. **전용 호스트** 페이지에서 **만들기**를 선택 합니다.
1. 사용할 구독을 선택 합니다.
1. **리소스 그룹**으로 *myDedicatedHostsRG* 을 선택 합니다.
1. **인스턴스 세부 정보**에서 **이름** 에 *myhost* 를 입력 하 고 위치에 대해 *미국 동부* 를 선택 합니다.
1. **하드웨어 프로필**에서 **크기 제품군**에 대해 *Standard Es3 family-Type 1* 을 선택 하 고 **호스트 그룹** 에 대해 *myhostgrup* 을 선택한 다음 **장애 도메인**에 대해 *1* 을 선택 합니다. 나머지 필드에 대해서는 기본값을 그대로 둡니다.
1. 완료 되 면 **검토 + 만들기** 를 선택 하 고 유효성 검사를 기다립니다.

    ![호스트 설정](./media/virtual-machines-common-dedicated-hosts-portal/host-settings.png)
1. **유효성 검사 통과** 메시지가 표시 되 면 **만들기** 를 선택 하 여 호스트를 만듭니다.


