---
title: Azure DNS에서 DNS 영역 관리 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 DNS 영역을 관리할 수 있습니다. 이 문서에서는 Azure DNS에서 DNS 영역을 업데이트, 삭제 및 만드는 방법을 설명합니다.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2017
ms.author: victorh
ms.openlocfilehash: d0a20de8738e8c7b2719a9de85d5fd16aa5778cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926341"
---
# <a name="how-to-manage-dns-zones-in-the-azure-portal"></a>Azure Portal에서 DNS 영역을 관리하는 방법

> [!div class="op_single_selector"]
> * [포털](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure 클래식 CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

이 문서는 Azure Portal을 사용하여 DNS 영역을 관리하는 방법을 보여줍니다. 플랫폼 간 [Azure CLI](dns-operations-dnszones-cli.md) 또는 Azure [PowerShell](dns-operations-dnszones.md)을 사용하여 DNS 영역을 관리할 수도 있습니다.

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

1. Azure Portal에 로그인
2. 허브 메뉴에서 **리소스 만들기 > 네트워킹 > DNS 영역**으로 이동하여 **DNS 영역 만들기** 블레이드를 엽니다.

    ![DNS 영역](./media/dns-operations-dnszones-portal/openzone650.png)

4. **DNS 영역 만들기** 블레이드에서 다음 값을 입력한 다음 **만들기**를 클릭합니다.


   | **설정** | **값** | **세부 정보** |
   |---|---|---|
   |**Name**|contoso.com|DNS 영역의 이름|
   |**구독**|[구독 이름]|DNS 영역을 만들 구독을 선택합니다.|
   |**리소스 그룹**|**새로 만들기:** contosoDNSRG|리소스 그룹을 만듭니다. 리소스 그룹 이름은 선택한 구독 내에서 고유해야 합니다. 리소스 그룹에 대해 자세히 알아보려면 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 개요 문서를 참조하세요.|
   |**위치**:|미국 서부||

> [!NOTE]
> 리소스 그룹은 리소스 그룹의 위치를 나타내며 DNS 영역에 영향을 미치지 않습니다. DNS 영역 위치는 항상 "전역"이며 표시되지 않습니다.

## <a name="list-dns-zones"></a>DNS 영역 나열

Azure Portal에서 **더 많은 서비스** > **네트워킹** > **DNS 영역**으로 이동합니다. 각 DNS 영역은 자체 리소스이며, 레코드 집합의 수 및 이름 서버와 같은 정보를 이 보기에서 볼 수 있습니다. 열 **이름 서버**는 기본 보기에 없습니다. 이 열을 추가하려면 **열**을 클릭하고 **이름 서버**를 선택한 후 **완료**를 클릭합니다.

![DNS 영역 나열](./media/dns-operations-dnszones-portal/listzones.png)

## <a name="delete-a-dns-zone"></a>DNS 영역 삭제

포털에서 DNS 영역으로 이동합니다. **DNS 영역** 블레이드에서 **영역 삭제**를 클릭합니다. DNS 영역을 삭제할지 확인하라는 메시지가 표시됩니다. DNS 영역을 삭제하면 해당 영역에 포함된 모든 레코드도 삭제됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal을 사용하여 Azure DNS 시작](dns-getstarted-portal.md)을 방문하여 DNS 영역 및 레코드 사용 방법을 알아봅니다.