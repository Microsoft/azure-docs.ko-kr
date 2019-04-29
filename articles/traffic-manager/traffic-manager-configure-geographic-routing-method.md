---
title: Azure Traffic Manager를 사용한 지리적 트래픽 라우팅 방법 구성
description: 이 문서에서는 Azure Traffic Manager를 사용하여 지리적 트래픽 라우팅 방법을 구성하는 방법을 설명합니다.
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 47cc56aac7d3e0147ef8577aac19776c6cacf7a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884158"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Traffic Manager를 사용한 지리적 트래픽 라우팅 방법 구성

지리적 트래픽 라우팅 방법을 사용하면 요청이 발생하는 지리적 위치를 기반으로 특정 엔드포인트에 트래픽을 보낼 수 있습니다. 이 자습서는 이 라우팅 방법을 사용하여 Traffic Manager 프로필을 만들고, 특정 지역에서 트래픽을 받도록 엔드포인트를 구성하는 방법을 보여 줍니다.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
2. **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기**를 클릭합니다.
4. **Traffic Manager 프로필 만들기**에서 다음을 수행합니다.
    1. 사용자의 프로필에 사용할 이름을 제공합니다. 이 이름은 trafficmanager.net 영역 내에서 고유해야 합니다. DNS 이름을 사용 하면 Traffic Manager 프로필에 액세스 하려면 `<profilename>.trafficmanager.net`합니다.
    2. **지리적** 라우팅 방법을 선택합니다.
    3. 이 프로필에서 만들려는 구독을 선택합니다.
    4. 기존 리소스 그룹을 사용하거나 이 프로필에서 대체할 새 리소스 그룹을 만듭니다. 새 리소스 그룹을 만드는 경우 **리소스 그룹 위치** 드롭다운을 사용하여 리소스 그룹의 위치를 지정합니다. 이 설정은 리소스 그룹의 위치를 나타내며 전역적으로 배포되는 Traffic Manager 프로필에는 영향을 미치지 않습니다.
    5. **만들기**를 클릭하면 사용자의 Traffic Manager 프로필이 생성되고 전역적으로 배포됩니다.

![Traffic Manager 프로필 만들기](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>엔드포인트 추가

1. 포털의 검색 창에서 직접 만든 Traffic Manager 프로필 이름을 검색하고 결과가 표시되면 클릭합니다.
2. Traffic Manager에서 **설정** -> **끝점**으로 이동합니다.
3. **추가**를 클릭하여 **엔드포인트 추가**를 표시합니다.
3. **추가**를 클릭하고 표시되는 **엔드포인트 추가**에서 다음과 같이 정보를 입력합니다.
4. 추가하려는 엔드포인트의 형식에 따라 **형식**을 선택합니다. 프로덕션에 사용되는 지리적 라우팅 프로필의 경우 둘 이상의 엔드포인트가 있는 자식 프로필을 포함하는 중첩 엔드포인트 형식을 사용하는 것이 좋습니다. 자세한 내용은 [지리적 트래픽 라우팅 방법에 대한 FAQ](traffic-manager-FAQs.md)를 참조하세요.
5. 이 엔드포인트를 인식하는 기준으로 사용할 **이름**을 제공합니다.
6. 이 페이지의 특정 필드는 사용자가 추가하는 엔드포인트의 형식에 따라 달라집니다.
    1. Azure 엔드포인트를 추가하는 경우 트래픽을 보낼 리소스에 따라 **대상 리소스 형식** 및 **대상**을 선택합니다.
    2. **외부** 엔드포인트를 추가하는 경우 사용자의 엔드포인트에 사용할 **FQDN(정규화된 도메인 이름)** 을 제공합니다.
    3. **중첩 엔드포인트**를 추가하는 경우 사용할 자식 프로필에 해당하는 **대상 리소스**를 선택하고 **최소 자식 엔드포인트 수**를 지정합니다.
7. 지역 매핑 섹션에서 드롭다운을 사용하여 이 엔드포인트로 전송할 트래픽이 발생되는 지역을 추가합니다. 하나 이상의 영역을 추가해야 하며 여러 지역을 매핑할 수 있습니다.
8. 이 프로필에서 추가하려는 모든 엔드포인트에 대해 이 단계를 반복합니다.

![Traffic Manager 엔드포인트 추가](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager 프로필 사용
1.  포털의 검색 창에서 이전 섹션에서 만들었던 **Traffic Manager 프로필** 이름을 검색하고 표시되는 결과에서 해당 Traffic Manager 프로필을 클릭합니다.
2. **개요**를 클릭합니다.
3. **Traffic Manager 프로필**에 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이는 라우팅 형식에서 결정된 대로 올바른 엔드포인트로 라우팅되도록 모든 클라이언트가 사용할 수 있습니다(예를 들어 웹 브라우저를 사용하여 이동).  지리적 라우팅의 경우 Traffic Manager는 들어오는 요청의 원본 IP를 찾아 그 발생 지역을 결정합니다. 해당 지역이 엔드포인트에 매핑된 경우 트래픽은 그곳으로 라우팅됩니다. 이 지역이 엔드포인트에 매핑되지 않은 경우 Traffic Manager는 NODATA 쿼리 응답을 반환합니다.

## <a name="next-steps"></a>다음 단계

- [지리적 트래픽 라우팅 방법](traffic-manager-routing-methods.md#geographic)에 대해 자세히 알아봅니다.
- [Traffic Manager 설정 테스트](traffic-manager-testing-settings.md)에 대해 알아보세요.
