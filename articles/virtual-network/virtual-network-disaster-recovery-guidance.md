---
title: Virtual Network 비즈니스 연속성 | Microsoft Docs
description: Azure Virtual Network에 영향을 주는 Azure 서비스 중단 발생 시 수행할 작업을 알아봅니다.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: f8068524a33c6d29056af456a26b8bd87fc3e3d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742712"
---
# <a name="virtual-network--business-continuity"></a>Virtual Network – 비즈니스 연속성

## <a name="overview"></a>개요
Virtual Network(VNet)는 클라우드의 사용자 네트워크를 논리적으로 나타내는 표현입니다. 이를 통해 사용자 고유의 개인 IP 주소 공간을 정의하고 네트워크를 서브넷으로 분할할 수 있습니다. VNet은 Azure Virtual Machines 및 Cloud Services(웹/작업자 역할)와 같은 계산 리소스를 호스트하기 위한 트러스트 경계 역할도 합니다. VNet에서는 VNet 안에 호스트된 리소스 간에 직접 개인 IP 통신을 허용합니다. 가상 네트워크를 VPN Gateway 또는 ExpressRoute를 통해 온-프레미스 네트워크에 연결할 수 있습니다.

VNet은 지역 범위 내에서 만들어집니다. 두 개의 서로 다른 지역에서 동일한 주소 공간을 사용하여 VNet을 만들 수 있습니다(예: 미국 동부 및 미국 서부에 있지만 함께 연결할 수 없음). 

## <a name="business-continuity"></a>비즈니스 연속성

여러 가지 방법으로 애플리케이션이 중단될 수 있습니다. 지역은 자연 재해 또는 여러 디바이스 또는 서비스의 장애로 인한 부분 재해 때문에 완전히 차단될 수 있습니다. VNet 서비스에 미치는 영향은 이러한 각 상황에서 차이가 있습니다.

**Q: 전체 지역에 중단이 발생 하는 경우 어떻게 하나요? 예를 들어, 자연 재해로 인해 지역이 완전히 차단될 경우 지역에 호스트된 가상 네트워크는 어떻게 되나요?**

A: 서비스 중단 시간 동안 가상 네트워크 및 영향을 받은 지역에 리소스 계속 액세스할 수 없습니다.

![간단한 Virtual Network 다이어그램](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q: 어떤 다른 지역에 동일한 가상 네트워크를 만들려면 다시 수행할 수 있나요?**

A: 가상 네트워크는 비교적 경량의 리소스입니다. Azure API를 호출하여 다른 지역에 동일한 주소 공간을 갖는 VNet을 만들 수 있습니다. 영향을 받은 지역에 존재하던 동일한 환경을 다시 만들려면 Cloud Services 웹 및 작업자 역할 및 사용하던 가상 머신을 다시 배포하는 API 호출을 수행해야 합니다. 하이브리드 배포와 같은 온-프레미스 연결이 있는 경우 새 VPN Gateway를 배포하고 온-프레미스 네트워크에 연결해야 합니다.

가상 네트워크를 만들려면 [가상 네트워크 만들기](manage-virtual-network.md#create-a-virtual-network)를 참조하세요.

**Q: 지정 된 지역의 VNet 복제본 수 다시 만든 미리 다른 지역에 있나요?**

A: 예, 동일한 개인 IP 주소 공간 및 리소스를 사용 하 여 사전에 두 개의 다른 지역의 두 Vnet을 만들 수 있습니다. VNet에 인터넷 연결 서비스를 호스트하는 경우 활성 지역에 트래픽을 지리적으로 라우팅하도록 Traffic Manager를 설정할 수 있습니다. 그러나 라우팅 문제를 발생할 수 있으므로 동일한 주소 공간을 가진 두 Vnet을 온-프레미스 네트워크에 연결할 수 없습니다. 한 지역에서 재해 및 VNet 손실이 발생할 경우 주소 공간이 온-프레미스 네트워크의 주소 공간과 일치하는 사용 가능한 지역의 다른 VNet을 연결할 수 있습니다.

가상 네트워크를 만들려면 [가상 네트워크 만들기](manage-virtual-network.md#create-a-virtual-network)를 참조하세요.

