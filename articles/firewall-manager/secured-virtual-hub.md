---
title: 보안 가상 허브 란 무엇 인가요?
description: 보안 가상 허브에 대한 자세한 정보
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518438"
---
# <a name="what-is-a-secured-virtual-hub"></a>보안 가상 허브 란 무엇 인가요?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

가상 허브는 다른 리소스에서 연결할 수 있도록 하는 Microsoft에서 관리 하는 가상 네트워크입니다. Azure Portal의 가상 WAN에서 가상 허브를 만들면 가상 허브 VNet 및 게이트웨이 (선택 사항)가 구성 요소로 만들어집니다.

보안 *가상 허브* 는 Azure 방화벽 관리자에서 구성 하는 연결 된 보안 및 라우팅 정책을 포함 하는 [Azure 가상 WAN 허브](../virtual-wan/virtual-wan-about.md#resources) 입니다. 보안 virtual hubs를 사용 하 여 트래픽 거 버 넌 스 및 보호를 위한 기본 보안 서비스를 통해 허브 및 스포크 및 전이적 아키텍처를 쉽게 만들 수 있습니다. 

온-프레미스 연결이 없는 관리 되는 중앙 VNet으로 보안 된 가상 허브를 사용할 수 있습니다. 이전에 Azure 방화벽 배포에 필요한 중앙 VNet을 대체 합니다. 보안 가상 허브는 자동화 된 라우팅을 제공 하므로 방화벽을 통해 트래픽을 라우팅하도록 자체 UDRs (사용자 정의 경로)를 구성할 필요가 없습니다.

또한 전체 가상 WAN 아키텍처의 일부로 보안 가상 허브를 사용할 수 있습니다. 이 아키텍처는 Azure에 대한 보안, 최적화 및 자동화 된 분기 연결을 제공 합니다. 서비스를 선택 하 여 Azure 방화벽 및 기타 타사 SECaaS (security as a service) 공급자를 비롯 한 네트워크 트래픽을 보호 하 고 관리할 수 있습니다.

## <a name="create-a-secured-virtual-hub"></a>보안 가상 허브 만들기

Azure Portal에서 방화벽 관리자를 사용 하 여 새 보안 가상 허브를 만들거나 이전에 Azure 가상 WAN을 사용 하 여 만든 기존 가상 허브를 변환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

보안 가상 허브를 만들어 허브 및 스포크 네트워크를 보호 하 고 관리 하는 데 사용 하려면 [자습서: Azure Portal을 사용 하 여 Azure 방화벽 관리자로 클라우드 네트워크 보호](secure-cloud-network.md)를 참조 하세요.