---
title: Azure Firewall Manager 배포 개요
description: Azure Firewall Manager에 필요한 상위 수준 배포 단계에 대해 알아봅니다.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079101"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Azure Firewall Manager 배포 개요

Azure Firewall Manager를 배포하는 방법은 여러 가지가 있지만 다음과 같은 일반적인 프로세스가 권장됩니다.

## <a name="general-deployment-process"></a>일반 배포 프로세스

### <a name="hub-virtual-networks"></a>Hub 가상 네트워크

1.  방화벽 정책 만들기

    - 새 정책 만들기
<br>*or*<br>
    - 기본 정책 도출 및 로컬 정책 사용자 지정
<br>*or*<br>
    - 기존 Azure Firewall에서 규칙을 가져옵니다. 여러 방화벽에 적용해야 하는 정책에서 NAT 규칙을 제거해야 합니다.
1. 허브 및 스포크 아키텍처 만들기
   - Azure Firewall Manager를 사용하여 Hub Virtual Network를 만들고 가상 네트워크 피어링을 사용하여 피어 스포크 가상 네트워크를 만듭니다.
<br>*or*<br>
    - 가상 네트워크를 만들고 가상 네트워크 피어링을 사용하여 가상 네트워크 연결 및 피어 스포크 가상 네트워크를 추가합니다.

3. 보안 공급자를 선택하고 방화벽 정책을 연결합니다. 현재 Azure Firewall만 지원되는 공급자입니다.

   - Hub Virtual Network를 만드는 동안이 작업을 수행합니다.
<br>*or*<br>
    - 기존 가상 네트워크를 Hub Virtual Network로 변환합니다. 여러 가상 네트워크를 변환할 수도 있습니다.

4. 트래픽을 Hub Virtual Network 방화벽으로 라우팅하도록 사용자 정의 경로를 구성합니다.


### <a name="secured-virtual-hubs"></a>보안 가상 허브

1. 허브 및 스포크 아키텍처 만들기

   - Azure Firewall Manager를 사용하여 보안 가상 허브를 만들고 가상 네트워크 연결을 추가합니다.<br>*or*<br>
   - 가상 WAN 허브를 만들고 가상 네트워크 연결을 추가합니다.
2. 보안 공급자 선택

   - 보안 가상 허브를 만드는 동안 완료됩니다.<br>*or*<br>
   - 기존 가상 WAN 허브를 보안 가상 허브로 변환합니다.
3. 방화벽 정책을 만들고 허브와 연결

   - Azure Firewall을 사용하는 경우에만 적용 가능합니다.
   - 타사 SECaaS(SECurity as a Service) 정책은 파트너 관리 환경을 통해 구성됩니다.
4. 보안 허브로 트래픽을 라우팅하도록 경로 설정 구성

   - 보안 가상 허브 경로 설정 페이지를 사용하여 스포크 가상 네트워크에서 UDR(사용자 정의 경로) 없이 필터링 및 로깅을 위해 보안 허브로 트래픽을 쉽게 라우팅할 수 있습니다.

> [!NOTE]
> - 지역별 가상 WAN당 허브가 두 개 이상 있을 수 없습니다. 그러나 지역에서 가상 WAN을 더 추가할 수 있습니다.
> - vWAN에서 허브의 IP 공간이 겹쳐서도 안 됩니다.
> - 허브 VNet 연결은 허브와 같은 지역에 있어야 합니다.
>
> 알려진 문제에 대한 자세한 내용은 [Azure Firewall Manager란?](overview.md#known-issues)을 참조하세요.

## <a name="convert-virtual-networks"></a>가상 네트워크 변환

기존 가상 네트워크를 허브 가상 네트워크로 변환하는 경우 다음 정보가 적용됩니다.

- 가상 네트워크에 기존 Azure Firewall이 있는 경우 기존 방화벽과 연결할 방화벽 정책을 선택합니다. 방화벽 정책이 방화벽 규칙을 대체하는 동안 방화벽 프로비전 상태가 업데이트됩니다. 프로비전 상태에서 방화벽은 트래픽을 계속 처리하고 가동 중지 시간이 발생하지 않습니다. 방화벽 관리자 또는 Azure PowerShell을 사용하여 기존 규칙을 방화벽 정책으로 가져올 수 있습니다.
- 가상 네트워크에 연결된 Azure Firewall이 없는 경우 방화벽이 배포되고 방화벽 정책이 새 방화벽과 연결됩니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 통해 Azure Firewall Manager를 사용하여 클라우드 네트워크 보호](secure-cloud-network.md)