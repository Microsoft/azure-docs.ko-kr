---
title: Azure DNS에서 DNS 레코드 집합 및 레코드 관리
description: Azure DNS는 도메인을 호스트하는 경우 DNS 레코드 집합 및 레코드를 관리하는 기능을 제공합니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 891adfacde6e46b1d8fe8e2f6b5fb39c90ce27a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296381"
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Azure 포털을 사용하여 DNS 레코드 및 레코드 집합 관리

이 문서는 Azure 포털을 사용하여 DNS 영역에 대한 레코드 집합 및 레코드를 관리하는 방법을 보여줍니다.

DNS 레코드 집합과 개별 DNS 레코드 사이의 차이를 이해하는 것이 중요합니다. 레코드 집합은 영역 내에서 동일한 이름과 형식을 가진 DNS 레코드 컬렉션입니다. 자세한 내용은 [Azure 포털을 사용하여 DNS 레코드 집합 및 레코드 만들기](dns-getstarted-create-recordset-portal.md)를 참조하세요.

## <a name="create-a-new-record-set-and-record"></a>새 레코드 집합 및 레코드 만들기

Azure 포털에서 레코드 집합을 만들려면 [Azure 포털을 사용하여 DNS 레코드 만들기](dns-getstarted-create-recordset-portal.md)를 참조하세요.

## <a name="view-a-record-set"></a>레코드 집합 보기

1. Azure 포털에서 **DNS 영역** 블레이드로 이동합니다.
2. 레코드 집합을 검색한 후 선택합니다. 레코드 집합 속성이 열립니다.

    ![레코드 집합 검색](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>레코드 집합에 새 레코드 추가

레코드 집합에 최대 20개의 레코드를 추가할 수 있습니다. 레코드 집합에는 두 개의 동일한 레코드가 포함될 수 없습니다. 빈 레코드 집합(0개 레코드 포함)을 만들 수 있지만 Azure DNS 이름 서버에는 표시되지 않습니다. CNAME 형식의 레코드 집합은 최대 하나의 레코드를 포함할 수 있습니다.

1. DNS 영역에 대한 **레코드 설정 속성** 블레이드에서 레코드를 추가하려는 레코드 집합을 클릭합니다.

    ![레코드 집합 선택](./media/dns-operations-recordsets-portal/selectset500.png)

2. 필드에 입력하여 레코드 집합 속성을 지정합니다.

    ![레코드 추가](./media/dns-operations-recordsets-portal/addrecord500.png)

3. 블레이드의 맨 위에서 **저장** 을 클릭하여 설정을 저장합니다. 그런 다음 블레이드를 닫습니다.
4. 가장자리에 레코드가 저장되는 것이 표시됩니다.

    ![레코드 집합 저장](./media/dns-operations-recordsets-portal/saving150.png)

레코드가 저장된 후 **DNS 영역** 블레이드의 값은 새 레코드를 반영합니다.

## <a name="update-a-record"></a>레코드 업데이트

기존 레코드 집합에서 레코드를 업데이트할 때 업데이트할 수 있는 필드는 사용하는 레코드의 유형에 따라 다릅니다.

1. 레코드 집합에 대한 **레코드 설정 속성** 블레이드에서 레코드를 검색합니다.
2. 레코드를 수정합니다. 레코드를 수정할 때 레코드에 대해 사용 가능한 설정을 변경할 수 있습니다. 다음 예제에서는 **IP 주소** 필드가 선택되고 IP 주소는 수정되는 중입니다.

    ![레코드 수정](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. 블레이드의 맨 위에서 **저장** 을 클릭하여 설정을 저장합니다. 오른쪽 위 모서리에서 레코드가 저장되었다는 알림을 볼 수 있습니다.

    ![저장된 레코드 집합](./media/dns-operations-recordsets-portal/saved150.png)

레코드가 저장된 후 **DNS 영역** 블레이드의 레코드 집합에 대한 값은 업데이트된 레코드를 반영합니다.

## <a name="remove-a-record-from-a-record-set"></a>레코드 집합에서 레코드 제거

Azure 포털을 사용하여 레코드 집합에서 레코드를 제거할 수 있습니다. 레코드 집합에서 마지막 레코드를 제거해도 레코드 집합은 삭제되지 않습니다.

1. 레코드 집합에 대한 **레코드 설정 속성** 블레이드에서 레코드를 검색합니다.
2. 제거하려는 레코드를 클릭합니다. 그런 후 **제거**를 선택합니다.

    ![레코드 제거](./media/dns-operations-recordsets-portal/removerecord500.png)

3. 블레이드의 맨 위에서 **저장** 을 클릭하여 설정을 저장합니다.
4. 레코드가 제거된 후 **DNS 영역** 블레이드의 레코드 값에 제거 결과가 반영됩니다.

## <a name="delete"></a>레코드 집합 삭제

1. 레코드 집합에 대한 **레코드 집합 속성** 블레이드에서 **삭제**를 클릭합니다.

    ![레코드 집합 삭제](./media/dns-operations-recordsets-portal/deleterecordset500.PNG)

2. 레코드 집합을 삭제할지 묻는 메시지가 표시됩니다.
3. 이름이 삭제하려는 레코드 집합과 일치하는지 확인한 다음 **예**를 클릭합니다.
4. **DNS 영역** 블레이드에서 레코드 집합을 더 이상 볼 수 없는지 확인합니다.

## <a name="work-with-ns-and-soa-records"></a>NS 및 SOA 레코드 작업

자동으로 생성되는 NS 및 SOA 레코드는 다른 레코드 유형과 다르게 관리됩니다.

### <a name="modify-soa-records"></a>SOA 레코드 수정

영역 루트(이름 = "\@")에 설정된 자동으로 생성된 SOA 레코드 집합에서 레코드를 추가 또는 제거할 수는 없습니다. 그러나 SOA 레코드 내의 매개 변수("Host" 제외) 및 레코드 집합 TTL을 수정할 수 있습니다.

### <a name="modify-ns-records-at-the-zone-apex"></a>영역 루트의 NS 레코드 수정

각 DNS 영역에 영역 루트의 NS 레코드 집합이 자동으로 만들어집니다. 여기에는 영역에 할당된 Azure DNS 이름 서버의 이름이 포함됩니다.

이 NS 레코드 집합에 추가 이름 서버를 추가하여 DNS 공급자가 2개 이상 있는 공동 호스팅 도메인을 지원할 수 있습니다. 또한 이 레코드 집합의 TTL 및 메타데이터를 수정할 수 있습니다.또한 이 레코드 집합의 TTL 및 메타데이터를 수정할 수 있습니다. 그러나 미리 채워진 Azure DNS 이름 서버를 제거 또는 수정할 수 없습니다.

이는 영역 루트에 있는 NS 레코드 집합에만 적용됩니다. 영역의 다른 NS 레코드 집합은 제약 없이 수정할 수 있습니다(자식 영역을 위임하는 데 사용되므로).

### <a name="delete-soa-or-ns-record-sets"></a>SOA 또는 NS 레코드 집합 삭제

영역을 만들 때 자동으로 만들어진 영역 루트(이름 = "\@")의 SOA 및 NS 레코드 집합은 삭제할 수 없습니다. 영역을 삭제하면 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

* Azure DNS에 대한 자세한 내용은 [Azure DNS 개요](dns-overview.md)를 참조하세요.
* DNS 자동화에 대한 자세한 내용은 [.NET SDK를 사용하여 DNS 영역 및 레코드 집합 만들기](dns-sdk.md)를 참조하세요.
* 역방향 DNS 레코드에 대한 자세한 내용은 [Azure의 역방향 DNS 및 지원 개요](dns-reverse-dns-overview.md)를 참조하세요.
* Azure DNS 별칭 레코드에 대한 자세한 내용은 [Azure DNS 별칭 레코드 개요](dns-alias.md)를 참조하세요.
