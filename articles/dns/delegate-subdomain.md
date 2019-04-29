---
title: Azure DNS 하위 도메인 위임
description: Azure DNS 하위 도메인을 위임하는 방법을 알아봅니다.
services: dns
author: WenJason
ms.service: dns
ms.topic: article
origin.date: 2/7/2019
ms.date: 02/25/2019
ms.author: v-jay
ms.openlocfilehash: 31543db8e177701ddfe6beaaa3091d6465b0e9cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790813"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Azure DNS 하위 도메인 위임

Azure Portal을 사용하여 DNS 하위 도메인을 위임할 수 있습니다. 예를 들어 contoso.com 도메인을 소유하고 있으면 *engineering*이라는 하위 도메인을 contoso.com 영역과 별도로 관리할 수 있는 별도의 다른 영역에 위임할 수 있습니다.

원하는 경우 [Azure PowerShell](delegate-subdomain-ps.md)을 사용하여 하위 도메인을 위임할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure DNS 하위 도메인을 위임하려면 먼저 공용 도메인을 Azure DNS에 위임해야 합니다. 위임에 대한 이름 서버를 구성하는 방법에 대한 지침은 [Azure DNS에 도메인 위임](./dns-delegate-domain-azure-dns.md)을 참조하세요. 도메인이 Azure DNS 영역에 위임되면 하위 도메인을 위임할 수 있습니다.

> [!NOTE]
> Contoso.com은 이 문서 전체에서 예로 사용됩니다. Contoso.com을 고유한 도메인 이름으로 바꾸세요.

## <a name="create-a-zone-for-your-subdomain"></a>하위 도메인에 대한 영역 만들기

먼저 **engineering** 하위 도메인에 대한 영역을 만듭니다.

1. Azure Portal에서 **리소스 만들기**를 선택합니다.
2. 검색 상자에서 **DNS**를 입력하고, **DNS 영역**을 선택합니다.
3. **만들기**를 선택합니다.
4. **DNS 영역 만들기** 창의 **이름** 텍스트 상자에서 **engineering.contoso.com**을 입력합니다.
5. 영역에 대한 리소스 그룹을 선택합니다. 비슷한 리소스를 함께 유지하기 위해 부모 영역과 동일한 리소스 그룹을 사용할 수 있습니다.
6. **만들기**를 클릭합니다.
7. 배포가 성공하면 새 영역으로 이동합니다.

## <a name="note-the-name-servers"></a>이름 서버 기록

engineering 하위 도메인에서 4개의 이름 서버를 적어 둡니다.

**engineering** 영역 창에서 영역에 대한 4개의 이름 서버를 적어 둡니다. 이러한 이름 서버는 나중에 사용합니다.

## <a name="create-a-test-record"></a>테스트 레코드 만들기

테스트에 사용할 **A** 레코드를 만듭니다. 예를 들어 **www** A 레코드를 만들고, **10.10.10.10** IP 주소로 구성합니다.

## <a name="create-an-ns-record"></a>NS 레코드 만들기

다음으로, **engineering** 영역에 대한 NS(이름 서버) 레코드를 만듭니다.

1. 부모 도메인에 대한 영역으로 이동합니다.
2. **+ 레코드 집합**을 선택합니다.
3. **레코드 집합 추가** 창의 **이름** 텍스트 상자에 **engineering**을 입력합니다.
4. **형식**에 대해 **NS**를 선택합니다.
5. **engineering** 영역에서 이전에 기록한 네 개의 이름 서버를 **이름 서버** 아래에 입력합니다.
6. **확인**을 클릭합니다.

## <a name="test-the-delegation"></a>위임 테스트

nslookup을 사용하여 위임을 테스트합니다.

1. PowerShell 창을 엽니다.
2. 명령 프롬프트에서 `nslookup www.engineering.contoso.com.`을 입력합니다.
3. **10.10.10.10** 주소를 보여 주는 신뢰할 수 없는 응답을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure에서 호스트되는 서비스에 대해 역방향 DNS 구성](dns-reverse-dns-for-azure-services.md) 방법에 대해 알아봅니다.