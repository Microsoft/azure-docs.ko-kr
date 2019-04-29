---
title: 여러 Azure 지역에 Azure API Management 서비스 배포 | Microsoft Docs
description: 여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658200"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법

Azure API Management는 원하는 Azure 지역의 수에 상관 없이 단일 Azure API Management 서비스를 배포하기 위해 API 게시자를 사용할 수 있는 여러 지역 배포를 지원합니다. 이를 통해 지역적으로 배포된 API 소비자가 느끼는 요청 대기 시간을 줄일 수 있으며 한 지역이 오프라인인 경우 가능한 서비스를 개선할 수도 있습니다.

새 Azure API Management 서비스는 처음에 단일 Azure 지역인 주 지역에 [단위][unit]를 하나만 포함합니다. 추가 지역은 Azure Portal을 통해 쉽게 추가할 수 있습니다. API Management 게이트웨이 서버는 각 지역에 배포 되 고 호출 트래픽 대기 시간 측면에서 가장 가까운 게이트웨이에 라우팅됩니다. 지역이 오프라인 상태가 되면 다음으로 가장 가까운 게이트웨이에 트래픽이 자동으로 리디렉션됩니다.

> [!NOTE]
> Azure API Management는 여러 지역에서 API 게이트웨이 구성 요소만 복제합니다. 서비스 관리 구성 요소는 주 영역에서만 호스팅됩니다. 주 지역에서 중단이 발생하는 경우 Azure API Management 서비스 인스턴스에 구성 변경 사항(설정 또는 정책 업데이트 포함)을 적용할 수 없습니다.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"></a>새 지역에 API Management 서비스 인스턴스 배포

> [!NOTE]
> 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [API Management 서비스 인스턴스 만들기][Create an API Management service instance]를 참조하세요.

Azure Portal에서 API Management 서비스 인스턴스에 대한 **규모 및 가격 책정** 탭으로 이동합니다. 

![크기 조정 탭][api-management-scale-service]

새 지역에 배포하려면 도구 모음에서 **+ 지역 추가**를 클릭합니다.

![지역 추가][api-management-add-region]

드롭다운 목록에서 위치를 선택하고 슬라이더를 사용하여 단위 수를 설정합니다.

![단위 지정][api-management-select-location-units]

**추가**를 클릭하여 위치 테이블에 선택 항목을 배치합니다. 

모든 위치를 구성할 때까지 이 프로세스를 반복한 후 도구 모음에서 **저장**을 클릭하여 배포 프로세스를 시작합니다.

## <a name="remove-region"></a>위치에 API Management 서비스 인스턴스 삭제

Azure Portal에서 API Management 서비스 인스턴스에 대한 **규모 및 가격 책정** 탭으로 이동합니다. 

![크기 조정 탭][api-management-scale-service]

제거할 위치에 대해 테이블의 오른쪽 끝에 있는 **...** 단추를 사용하여 상황에 맞는 메뉴를 엽니다. **삭제** 옵션을 선택합니다.

삭제를 확인하고 **저장**을 클릭하여 변경 내용을 적용합니다.

## <a name="route-backend"> </a>지역 백 엔드 서비스로 API 호출 라우팅

Azure API Management는 백 엔드 서비스 URL을 하나만 제공합니다. 다양한 지역에 Azure API Management 인스턴스가 있더라도 API 게이트웨이는 하나의 지역에만 배포된 동일한 백 엔드 서비스로 요청을 전달합니다. 이런 경우 성능 향상은 Azure API Management 내에서 요청에 특정한 지역에 캐시된 응답에서만 발생하고 전 세계의 백 엔드에 계속 연결하는 경우 대기 시간이 길어질 수 있습니다.

시스템의 지리적 분포를 최대한 활용하려면 Azure API Management 인스턴스와 동일한 지역에 백 엔드 서비스를 배포해야 합니다. 그런 다음, 정책과 `@(context.Deployment.Region)` 속성을 사용하여 백 엔드의 로컬 인스턴스로 트래픽을 라우팅하면 됩니다.

1. Azure API Management 인스턴스로 이동하여 왼쪽 메뉴에서 **API**를 클릭합니다.
2. 원하는 API를 선택합니다.
3. **인바운드 처리**의 화살표 드롭다운에서 **코드 편집기**를 클릭합니다.

    ![API 코드 편집기](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. 조건부 `choose` 정책과 결합된 `set-backend`를 사용하여 파일의 `<inbound> </inbound>` 섹션에 적절한 라우팅 정책을 생성합니다.

    예를 들어 아래 XML 파일은 미국 서부와 동아시아 지역에 해당합니다.

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> 사용 하 여 백 엔드 서비스 프런트 수도 있습니다 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), Traffic Manager에 API 호출을 직접 및 라우팅을 자동으로 해결 되도록 합니다.

## <a name="custom-routing"> </a>지역 API Management 게이트웨이에 사용자 지정 라우팅을 사용 하 여

API Management는 지역에 요청을 라우트하 *게이트웨이* 기준 [최저 대기 시간](../traffic-manager/traffic-manager-routing-methods.md#performance)합니다. API Management에서이 설정을 재정의할 수는 없지만 사용자 지정 라우팅 규칙을 사용 하 여 사용자 고유의 Traffic Manager를 사용할 수 있습니다.

1. 직접 만들어보십시오 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)합니다.
1. 사용자 지정 도메인을 사용 하는 경우 [Traffic Manager와 함께 사용할](../traffic-manager/traffic-manager-point-internet-domain.md) API Management 서비스를 대신 합니다.
1. [Traffic Manager에서 끝점을 지역 API Management를 구성](../traffic-manager/traffic-manager-manage-endpoints.md)합니다. 지역별 끝점의 URL 패턴을 따릅니다 `https://<service-name>-<region>-01.regional.azure-api.net`예를 들어 `https://contoso-westus2-01.regional.azure-api.net`합니다.
1. [API Management 지역 상태 끝점을 Traffic Manager에서 구성](../traffic-manager/traffic-manager-monitoring.md)합니다. 지역 상태 끝점의 URL 패턴을 따릅니다 `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`예를 들어 `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`합니다.
1. 지정할 [라우팅 방법을](../traffic-manager/traffic-manager-routing-methods.md) Traffic Manager의 합니다.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
