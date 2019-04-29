---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743282"
---
## <a name="scenario"></a>시나리오
이 문서에서는 특정 시나리오에서 VM에 다중 NIC를 사용하는 배포를 살펴봅니다. 이 시나리오에는 Azure에 호스트되는 2계층 IaaS 워크로드가 있습니다. 각 계층은 가상 네트워크(VNet)의 자체 서브넷에 배포됩니다. 프런트 엔드 계층은 고가용성을 위해 부하 분산 장치 집합으로 그룹화되어 있는 몇 개의 웹 서버로 구성됩니다. 백 엔드 계층은 몇 개의 데이터베이스 서버로 구성됩니다. 데이터베이스 서버는 각각 2개의 NIC(데이터베이스 액세스를 위한 NIC와 관리를 위한 NIC)와 배포됩니다. 시나리오에는 배포에서 각 서브넷 및 NIC에 허용되는 트래픽을 제어하기 위한 NSG(네트워크 보안 그룹)도 포함됩니다. 다음 그림은 이 시나리오의 기본 아키텍처를 보여줍니다.

![MultiNIC 시나리오](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)