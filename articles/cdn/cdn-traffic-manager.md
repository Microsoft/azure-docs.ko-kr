---
title: Traffic Manager를 사용 하 여 여러 Azure CDN 끝점 간 장애 조치 (Failover)
description: Azure Traffic Manager를 사용 하 여 여러 Azure Content Delivery Network 끝점에서 장애 조치 (failover)를 설정 하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/18/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: b55e418393d6d446ae0d3557f2d1f4cf98d89293
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192499"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Azure Traffic Manager를 사용하여 여러 Azure CDN 엔드포인트 간 장애 조치(failover) 설정

Azure CDN(Content Delivery Network)을 구성하는 경우 요구에 대해 최적의 공급자 및 가격 책정 계층을 선택할 수 있습니다. Azure CDN은 전역으로 분산된 인프라를 사용하여 기본적으로 로컬 및 지역적 중복 및 전역 부하 분산을 만들어 서비스 가용성 및 성능을 개선합니다. 콘텐츠를 제공하는 데 위치를 사용할 수 없는 경우 자동으로 요청이 다른 위치로 라우팅되며 최적의 POP(요청 위치 및 서버 부하 같은 요인에 따른)가 각 클라이언트 요청을 처리하는 데 사용됩니다. 
 
여러 CDN 프로필이 있는 경우 Azure Traffic Manager를 사용하여 가용성 및 성능을 더욱 개선할 수 있습니다. 장애 조치(failover), 지역 부하 분산 및 기타 시나리오에 대해 여러 CDN 엔드포인트 간에 부하를 분산하려면 Azure CDN을 통해 Azure Traffic Manager를 사용할 수 있습니다. 일반적인 장애 조치(failover) 시나리오에서 모든 클라이언트 요청은 먼저 기본 CDN 프로필로 이동됩니다. 만약 프로필을 사용할 수 없는 경우 요청은 기본 CDN 프로필이 다시 온라인 상태가 될 때까지 보조 CDN 프로필에 전달됩니다. 이러한 방식으로 Azure Traffic Manager를 사용하여 웹 애플리케이션을 항상 사용할 수 있는지 확인합니다. 

이 문서에서는 **Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필을 사용하여 장애 조치(failover)를 설정하는 방법에 대한 지침 및 예제를 제공합니다.

## <a name="set-up-azure-cdn"></a>Azure CDN 설정 
다른 공급자를 사용하여 두 개 이상의 Azure CDN 프로필 및 엔드포인트를 만듭니다.

1. [새 CDN 프로필 만들기](cdn-create-new-endpoint.md#create-a-new-cdn-profile)의 단계를 수행하여 **Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필을 만듭니다.
 
   ![여러 CDN 프로필](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. 각 새 프로필에서 [새 CDN 엔드포인트 만들기](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)의 단계를 수행하여 하나 이상의 엔드포인트를 만듭니다.

## <a name="set-up-azure-traffic-manager"></a>Azure Traffic Manager 설정
Azure Traffic Manager 프로필을 만들고 CDN 엔드포인트 간 부하 분산을 설정합니다. 

1. [Traffic Manager 프로필 만들기](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)의 단계를 수행하여 Azure Traffic Manager 프로필을 만듭니다. 

    **라우팅 메서드**의 경우 **우선 순위**를 선택합니다.

2. [Traffic Manager 엔드포인트 추가](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)의 단계를 수행하여 Traffic Manager 프로필에서 CDN 엔드포인트 추가

    **형식**의 경우 **외부 엔드포인트**를 선택합니다. **우선 순위**의 경우 숫자를 입력합니다.

    예를 들어 *1*의 우선 순위로 *cdndemo101akamai.azureedge.net*을 만들고 *2*의 우선 순위로 *cdndemo101verizon.azureedge.net*을 만듭니다.

   ![CDN Traffic Manager 엔트포인트](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Azure CDN 및 Azure Traffic Manager에서 사용자 지정 도메인 설정
CDN 및 Traffic Manager 프로필을 설정한 후 이러한 단계를 수행하여 CDN 엔드포인트에 DNS 매핑을 추가하고 사용자 지정 도메인을 등록합니다. 예를 들어 사용자 지정 도메인 이름은 *cdndemo101.dustydogpetcare.online*입니다.

1. GoDaddy 같은 사용자 지정 도메인의 도메인 공급자에 대한 웹 사이트로 이동하고 두 DNS CNAME 항목을 만듭니다. 

    a. 첫 번째 CNAME 항목의 경우 cdnverify 하위 도메인이 있는 사용자 지정 도메인을 CDN 엔드포인트에 매핑합니다. 이 항목은 2단계에서 Traffic Manager에 추가한 CDN 엔드포인트에 사용자 지정 도메인을 등록하는 데 필요한 단계입니다.

      예를 들면 다음과 같습니다. 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. 두 번째 CNAME 항목의 경우 cdnverify 하위 도메인이 없는 사용자 지정 도메인을 CDN 엔드포인트에 매핑합니다. 이 항목은 Traffic Manager에 사용자 지정 도메인을 매핑합니다. 

      예를 들어: 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > 도메인이 현재 라이브라서 중단할 수 없는 경우 이 단계를 마지막에 수행합니다. 사용자 지정 도메인 DNS를 Traffic Manager로 업데이트하기 전에 Traffic Manager 도메인 및 CDN 엔드포인트가 라이브인지 확인합니다.
    >


2.  Azure CDN 프로필에서 첫 번째 CDN 엔드포인트(Akamai에)를 선택합니다. **사용자 지정 도메인 추가** 및 입력 *cdndemo101. dustydogpetcare*를 선택 합니다. 사용자 지정 도메인의 유효성을 검사하는 확인 표시가 녹색인지 확인합니다. 

    이 등록 프로세스를 완료하려면 Azure CDN은 *cdnverify* 하위 도메인을 사용하여 DNS 매핑의 유효성을 검사합니다. 자세한 내용은 [CNAME DNS 레코드 만들기](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)를 참조하세요. 이 단계에서는 해당 요청에 응답할 수 있도록 Azure CDN이 사용자 지정 도메인을 인식할 수 있게 설정합니다.
    
    > [!NOTE]
    > **Akamai 프로필의 Azure CDN** 에서 TLS를 사용 하도록 설정 하려면 사용자 지정 도메인을 끝점에 직접 cname 해야 합니다. TLS를 사용 하도록 설정 하기 위한 cdnverify는 아직 지원 되지 않습니다. 
    >

3.  사용자 지정 도메인의 도메인 공급자에 대한 웹 사이트로 돌아가서 첫 번째 DNS 매핑을 업데이트하여 사용자 지정 도메인이 두 번째 CDN 엔드포인트에 매핑되게 합니다.
                             
    예를 들어: 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Azure CDN 프로필에서 두 번째 CDN 엔드포인트(Verizon)를 선택하고 2단계를 반복합니다. **사용자 지정 도메인 추가**를 선택 하 고 *cdndemo101. dustydogpetcare*를 입력 합니다.
 
이러한 단계를 완료한 후 장애 조치(failover) 기능이 포함된 다중 CDN 서비스는 Azure Traffic Manager를 사용하여 설정됩니다. 사용자 지정 도메인에서 테스트 URL에 액세스할 수 있습니다. 기능을 테스트하려면 기본 CDN 엔드포인트를 사용하지 않도록 설정하고 요청이 두 번째 CDN 엔드포인트에 올바르게 이동되는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
다른 CDN 엔드포인트 간에 부하를 분산하려면 지리 같은 다른 라우팅 방법을 설정할 수도 있습니다. 자세한 내용은 [Traffic Manager를 사용한 지리적 트래픽 라우팅 방법 구성](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)을 참조하세요.



