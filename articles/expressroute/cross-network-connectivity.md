---
title: Azure 네트워크 간 연결
description: 이 페이지에서는 Azure 네트워킹 기능을 기반으로 하는 네트워크 간 연결 및 솔루션에 대한 응용 프로그램 시나리오를 설명 합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644259"
---
# <a name="cross-network-connectivity"></a>네트워크 간 연결

Fabrikam Inc.는 미국 동부에 있는 대규모의 실제 Azure 배포 기업입니다. Fabrikam은 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다. 마찬가지로 Contoso l t d .는 미국 서 부에 존재 하 고 Azure를 배포 합니다. Contoso는 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다.  

Fabrikam Inc. Contoso l t d .를 가져옵니다. 병합기를 따라 Fabrikam은 네트워크를 상호 연결 하려고 합니다. 다음 그림은 이 시나리오를 보여 줍니다.

 [![1]][1]

위 그림의 중간에 있는 파선 화살표는 원하는 네트워크 상호 연결을 표시 합니다. 특히 세 가지 종류의 연결을 사용할 수 있습니다. 1) Fabrikam 및 Contoso Vnet 교차 연결, 2) 교차 지역 온-프레미스와 Vnet 교차 연결 (즉, Fabrikam 온-프레미스 네트워크를 Contoso VNet에 연결 하 고 Contoso에 연결) 온-프레미스 네트워크에서 Fabrikam VNet으로), 3) Fabrikam 및 Contoso 온-프레미스 네트워크 교차 연결 

다음 표에서는 병합 전 Contoso l t d .의 Express 경로에 대한 개인 피어 링의 경로 테이블을 보여 줍니다.

[![2]][2]

다음 표에서는 합병 전의 Contoso 구독에 있는 VM의 유효 경로를 보여 줍니다. 테이블 마다 VNet의 VM은 VNet 주소 공간 및 Contoso 온-프레미스 네트워크를 기본 네트워크와 별도로 인식 합니다. 

[![4]][4]

다음 표에서는 병합 전 Fabrikam Inc.의 Express 경로에 대한 개인 피어 링의 경로 테이블을 보여 줍니다.

[![3]][3]

다음 표에서는 병합 전의 Fabrikam 구독에 있는 VM의 유효 경로를 보여 줍니다. 테이블 마다 VNet의 VM은 VNet 주소 공간 및 Fabrikam 온-프레미스 네트워크를 기본 파일과는 별도로 인식 합니다.

[![5]][5 개]

이 문서에서는 다음 Azure 네트워크 기능을 사용 하 여 원하는 교차 연결을 구현 하는 방법을 단계별로 살펴보겠습니다.

* [가상 네트워크 피어링][Virtual network peering] 
* [가상 네트워크 Express 경로 연결][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>교차 연결 Vnet

VNet 피어 링 (가상 네트워크 피어 링)은 두 개의 가상 네트워크를 연결할 때 가장 최적의 네트워크 성능을 제공 합니다. VNet 피어 링은 동일한 Azure 지역 (일반적으로 VNet 피어 링 이라고 함)과 서로 다른 두 Azure 지역 (일반적으로 글로벌 VNet 피어 링 이라고 함) 내에서 두 개의 Vnet 피어 링을 지원 합니다. 

Contoso와 Fabrikam Azure 구독의 Vnet 간에 글로벌 VNet 피어 링을 구성 해 보겠습니다. 두 가상 네트워크 간에 가상 네트워크 피어 링을 만드는 방법에 대한 자세한 내용은 [가상 네트워크 피어 링 만들기][Configure VNet peering] 문서를 참조 하세요.

다음 그림은 글로벌 VNet 피어 링을 구성한 후의 네트워크 아키텍처를 보여 줍니다.

[![6]][6]

다음 표에서는 Contoso 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 마지막 항목에 주의 합니다. 이 항목은 가상 네트워크 교차 연결의 결과입니다.

[![7]][7]

다음 표에서는 Fabrikam 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 마지막 항목에 주의 합니다. 이 항목은 가상 네트워크 교차 연결의 결과입니다.

[![8]][8]

VNet 피어 링은 두 개의 가상 네트워크를 직접 연결 합니다 (위의 두 테이블에 *Vnetglobalpeering 링* 항목에 대한 다음 홉이 없음 참조).

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Vnet을 온-프레미스 네트워크에 상호 연결

Express 경로 회로를 여러 가상 네트워크에 연결할 수 있습니다. Express 경로 회로에 연결할 수 있는 최대 가상 네트워크 수에 대한 [구독 및 서비스 제한][Subscription limits] 을 참조 하세요. 

Fabrikam Express 경로 회로를 Contoso 구독 VNet에 연결 하 고, 비슷한 Contoso Express 경로 회로를 Fabrikam 구독 VNet에 연결 하 여 가상 네트워크와 온-프레미스 네트워크 간에 교차 연결을 사용할 수 있도록 합니다. 다른 구독의 Express 경로 회로에 가상 네트워크를 연결 하려면 권한 부여를 만들고 사용 해야 합니다.  [가상 네트워크를 express 경로 회로에 연결][Connect-ER-VNet]문서를 참조 하세요.

다음 그림은 가상 네트워크에 대한 Express 경로 교차 연결을 구성한 후의 네트워크 아키텍처를 보여 줍니다.

[![9]][9]

다음 표는 Express 경로를 통해 가상 네트워크를 온-프레미스 네트워크에 교차 연결한 후 Contoso l t d .의 Express 경로에 대한 개인 피어 링의 경로 테이블을 보여 줍니다. 경로 테이블에 두 가상 네트워크에 속하는 경로가 있는지 확인 합니다.

[![10]][10]

다음 표에서는 Express 경로를 통해 가상 네트워크를 온-프레미스 네트워크에 교차 연결한 후 Fabrikam Inc.의 Express 경로에 대한 개인 피어 링의 경로 테이블을 보여 줍니다. 경로 테이블에 두 가상 네트워크에 속하는 경로가 있는지 확인 합니다.

[![pt]][11]

다음 표에서는 Contoso 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 *가상 네트워크 게이트웨이* 항목에 주의 합니다. VM은 온-프레미스 네트워크에 대한 경로를 확인 합니다.

[![10]][12]

다음 표에서는 Fabrikam 구독 VM에 알려진 경로를 보여 줍니다. 테이블의 *가상 네트워크 게이트웨이* 항목에 주의 합니다. VM은 온-프레미스 네트워크에 대한 경로를 확인 합니다.

[![/10]][13]

>[!NOTE]
>Fabrikam 및/또는 Contoso 구독에는 해당 허브 VNet에 대한 스포크 Vnet 있습니다 (이 문서의 아키텍처 다이어그램에는 허브 및 스포크 디자인이 설명 되지 않음). 허브 VNet 게이트웨이와 Express 경로 간 교차 연결을 사용 하는 경우에도 동 및 서 부 허브와 스포크 간의 통신이 가능 합니다.
>

## <a name="cross-connecting-on-premises-networks"></a>온-프레미스 네트워크 교차 연결

Express 경로 Global Reach는 서로 다른 Express 경로 회로에 연결 된 온-프레미스 네트워크 간의 연결을 제공 합니다. Contoso와 Fabrikam Express 경로 회로 간에 Global Reach를 구성 해 보겠습니다. Express 경로 회로는 서로 다른 구독에 있으므로 권한 부여를 만들고 사용 해야 합니다. 단계별 지침은 [express 경로 구성 Global Reach][Configure Global Reach] 문서를 참조 하세요.

다음 그림에서는 Global Reach 구성한 후의 네트워크 아키텍처를 보여 줍니다.

[![14]][14]

다음 표에서는 Global Reach 구성한 후 Contoso l t i t i o l i d .의 개인 피어 링에 대한 경로 테이블을 보여 줍니다. 경로 테이블에는 온-프레미스 네트워크에 속하는 경로가 있는지 확인 합니다. 

[![15]][15]

다음 표에서는 Global Reach 구성한 후 Fabrikam Inc.의 Express 경로에 대한 개인 피어 링의 경로 테이블을 보여 줍니다. 경로 테이블에는 온-프레미스 네트워크에 속하는 경로가 있는지 확인 합니다.

[![x]][16]

## <a name="next-steps"></a>다음 단계

VNet 및 VNet 피어 링에 대한 추가 질문은 [virtual NETWORK FAQ][VNet-FAQ]를 참조 하세요. Express 경로 및 가상 네트워크 연결에 대한 추가 질문은 [express 경로 FAQ][ER-FAQ] 를 참조 하세요.

Global Reach은 국가/지역에 따라 국가/지역에서 롤아웃 됩니다. 원하는 국가/지역에서 Global Reach를 사용할 수 있는지 확인 하려면 [express 경로 Global Reach][Global Reach]를 참조 하세요.

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "적용 시나리오"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png. "합병 전에 Contoso express 경로 테이블"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png. "병합 전 Fabrikam express 경로 테이블"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM 경로 병합 전"
 "병합 전 FABRIKAM VM 경로" [5 개]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png
[6]: ./media/cross-network-connectivity/vnet-peering.png "VNet 피어 링 후의 아키텍처"
[]: ./media/cross-network-connectivity/contosovm-routes-peering.png "VNet 피어 링 후 Contoso VM 경로"
[]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "VNet 피어 링 후 Fabrikam VM 경로" 8 개
[]: ./media/cross-network-connectivity/exr-x-connect.png "연결할 expressroutes 교차 연결 후의 아키텍처" 9
 "ExR 및 vnet를 교차 연결한 후의 Contoso express 경로 테이블" [10 개]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "r 및 vnet를 교차 연결한 후 Fabrikam express 경로 테이블"
[]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "Exr 및 vnet를 교차 연결한 후의 12 개 Contoso VM 경로"
[]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "Exr 및 vnet를 교차 연결한 후 Fabrikam VM 경로" 13 개
[]: ./media/cross-network-connectivity/globalreach.png "Global Reach 구성한 후의 아키텍처" 14
Global Reach 후 [15 개의]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso express 경로 테이블"
 "Global Reach 후에 Fabrikam express 경로 테이블" [16 개]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq