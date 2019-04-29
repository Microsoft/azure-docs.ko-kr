---
title: 영역별 프런트 엔드가 있는 Load Balancer 만들기 - Azure Portal
titlesuffix: Azure Load Balancer
description: Azure Portal을 사용하여 영역별 프런트 엔드가 있는 표준 Load Balancer를 만드는 방법 알아보기
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: c81ff5ea330c4c0ba26a92a3b5399cfa961e4b2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60753523"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Azure Portal을 사용하여 영역별 프런트 엔드가 있는 표준 Load Balancer 만들기

이 문서에서는 영역별 프런트 엔드 IP 구성이 있는 공용 [표준 Load Balancer](https://aka.ms/azureloadbalancerstandard)를 만드는 단계를 안내합니다. Standard Load Balancer에서 가용성 영역이 작동하는 방법에 대한 내용은 [Standard Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md)을 참조하세요. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!NOTE]
> Azure 리소스, 지역 및 VM 크기 제품군을 선택하는 데 가용성 영역에 대한 지원을 사용할 수 있습니다. 시작하는 방법에 대한 자세한 내용 및 가용성 영역을 사용해 볼 수 있는 Azure 리소스, 지역 및 VM 크기 제품군은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요. 지원을 위해 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones)에 연결하거나 [Azure 지원 티켓을 열](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 수 있습니다.  

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal ( https://portal.azure.com ) 에 로그인합니다.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>영역별 프런트 엔드 IP 주소가 있는 부하 분산 장치 만들기

1. 브라우저에서 Azure Portal [https://portal.azure.com](https://portal.azure.com)로 이동하고 Azure 계정으로 로그인합니다.
2. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **부하 분산 장치**를 선택합니다.
3. **부하 분산 장치 만들기** 페이지에서 **이름**에 **myLoadBalancer**를 입력합니다.
4. **형식**에서 **Public**을 선택합니다.
5. SKU에서 **표준**을 선택합니다.
6. **공용 IP 주소**를 클릭하고, **새로 만들기**를 클릭하고, **공용 IP 주소 만들기** 페이지에서 이름에 **myPublicIPZonal**을 입력하고, SKU에는 **표준**을, 가용성 영역에는 **1**을 선택합니다.
    
>[!NOTE] 
> 이 단계에서 만든 공용 IP는 기본적으로 표준 SKU입니다.

1. **리소스 그룹**에서 **새로 만들기**를 클릭하고 리소스 그룹의 이름으로 **myResourceGroupZLB**를 입력합니다.
1. **위치**로 **유럽 서부**를 선택하고 **확인**을 클릭합니다. 그러면 부하 분산 장치에서 배포를 시작하며 배포를 완료하는 데 몇 분 정도가 걸립니다.

    ![Azure Portal을 사용하여 영역 중복 표준 Load Balancer 만들기](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>다음 단계
- [Standard Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md)에 대해 자세히 알아봅니다.



