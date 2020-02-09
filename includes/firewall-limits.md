---
title: 포함 파일
description: 포함 파일
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 92c2e79910e40721a0ef62d44825bd1f3e19fc79
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548221"
---
| 리소스 | 기본 제한 |
| --- | --- |
| 데이터 처리량 |30gbps<sup>1</sup> |
|규칙|1만. 모든 규칙 유형을 결합 합니다.|
|최대 DNAT 규칙|299|
|AzureFirewallSubnet 최소 크기 |/26|
|네트워크 및 애플리케이션 규칙의 포트 범위|0-64,000 이 제한을 완화하는 작업이 진행 중입니다.|
|공용 IP 주소|100 최대 (현재 SNAT 포트는 처음 5 개의 공용 IP 주소에 대해서만 추가 됩니다.)|
|경로 테이블|기본적으로 AzureFirewallSubnet에는 NextHopType 값이 **인터넷**으로 설정 된 0.0.0.0/0 경로가 있습니다.<br><br>Azure Firewall에는 직접 인터넷 연결이 있어야 합니다. AzureFirewallSubnet가 BGP를 통해 온-프레미스 네트워크에 대한 기본 경로를 학습 하는 경우 직접 인터넷 연결을 유지 하려면 **internet** 로 설정 된 **NextHopType** 값을 사용 하 여 0.0.0.0/0 udr으로 재정의 해야 합니다. 기본적으로 Azure Firewall은 온-프레미스 네트워크에 대한 강제 터널링을 지원하지 않습니다.<br><br>그러나 구성에 온-프레미스 네트워크에 대한 강제 터널링이 필요한 경우 Microsoft는 사례별로 지원할 예정입니다. 사용자의 사례를 검토할 수 있도록 지원 부서에 연락해주시기 바랍니다. 수락되면 사용자의 구독을 허용하고 필요한 방화벽 인터넷 연결이 유지되도록 합니다.|

<sup>1</sup> 이러한 제한을 늘려야 하는 경우 Azure 지원에 문의 하세요.
