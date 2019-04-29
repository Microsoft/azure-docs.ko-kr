---
title: 사설 도메인에 Azure DNS 사용
description: Microsoft Azure의 사설 DNS 호스팅 서비스에 대한 개요입니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/1/2019
ms.author: victorh
ms.openlocfilehash: 7f5f377f34a43dfb01ea516e023bb98f118d0dd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596547"
---
# <a name="use-azure-dns-for-private-domains"></a>사설 도메인에 Azure DNS 사용

DNS(Domain Name System)는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. DNS 도메인에 대한 호스팅 서비스인 Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. 인터넷 연결 DNS 도메인 지원 외에도 Azure DNS는 이제 사설 DNS 도메인을 미리 보기 기능으로 지원합니다.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Azure DNS는 사용자 지정 DNS 솔루션을 추가하지 않고도 가상 네트워크의 도메인 이름을 관리하고 확인할 수 있는 안정적이고 신뢰할 수 있는 DNS 서비스를 제공합니다. 사설 DNS 영역을 사용하면 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 사용할 수 있습니다. 사용자 지정 도메인 이름을 사용하면 조직의 요구 사항에 가장 적합하도록 가상 네트워크 아키텍처를 조정할 수 있습니다. 가상 네트워크 내에서 그리고 가상 네트워크 간에 VM(가상 머신)에 대한 이름을 확인할 수 있게 해줍니다. 또한 분할-수평 보기를 사용해 영역을 구성할 수도 있습니다. 그러면 개인 DNS 영역과 공용 DNS 영역이 이름을 공유할 수 있습니다.

가상 네트워크에 사설 DNS 영역에 게시하려면 영역 내에서 레코드를 확인하도록 허용된 가상 네트워크 목록을 지정합니다. 이것을 *확인 가상 네트워크*라고 합니다. 또한 VM이 생성되고, IP가 변경되고, 삭제될 때마다 Azure DNS가 호스트 이름 레코드를 유지 관리하게 되는 가상 네트워크를 지정할 수 있습니다. 이것을 *등록 가상 네트워크*라고 합니다.

등록 가상 네트워크를 지정하는 경우, 사설 영역에 등록된 해당 가상 네트워크의 VM에 대한 DNS 레코드는 Azure Powershell 및 Azure CLI API에서 보거나 검색할 수 없지만 VM 레코드는 실제로 등록되어 성공적으로 확인됩니다.

![DNS 개요](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 모범 사례로, 사설 DNS 영역에 대 한.local 도메인을 사용 하지 마십시오. 일부 운영 체제는이 지원 합니다.

## <a name="benefits"></a>이점

Azure DNS는 다음과 같은 이점을 제공합니다.

* **사용자 지정 DNS 솔루션이 필요 없음**. 이전에는 많은 고객이 가상 네트워크에서 DNS 영역을 관리하기 위해 사용자 지정 DNS 솔루션을 만들었습니다. 이제 기본 Azure 인프라를 사용하여 DNS 영역 관리를 수행할 수 있으므로 사용자 지정 DNS 솔루션을 만들고 관리해야 하는 부담이 사라졌습니다.

* **모든 공용 DNS 레코드 형식 사용**. Azure DNS는 A, AAAA, CNAME, MX, PTR, SOA, SRV 및 TXT 레코드를 지원합니다.

* **자동 호스트 레코드 관리**. Azure는 사용자 지정 DNS 레코드를 호스팅할 뿐 아니라 VM의 호스트 이름 레코드를 지정된 가상 네트워크에 자동으로 보존합니다. 이 시나리오에서는 사용자 지정 DNS 솔루션을 만들거나 애플리케이션을 수정할 필요 없이 사용하는 도메인 이름을 최적화할 수 있습니다.

* **가상 네트워크 간의 호스트 이름 확인**. Azure에서 제공하는 호스트 이름과는 달리, 사설 DNS 영역은 가상 네트워크 간에 공유할 수 있습니다. 이 기능은 가상 네트워크 피어링 같은 교차 네트워크 및 서비스 검색 시나리오를 단순화합니다.

* **친숙한 도구 및 사용자 환경**. 이 새로운 기능은 학습 곡선을 단축할 수 있도록 잘 설정된 Azure DNS 도구(PowerShell, Azure Resource Manager 템플릿, REST API)를 사용합니다.

* **수평 분할 DNS 지원**. Azure DNS를 사용하면 이름이 같고 가상 네트워크 내 그리고 공용 인터넷의 다른 답변을 확인하는 영역을 만들 수 있습니다. 분할 수평 DNS에 대한 일반적인 시나리오는 가상 네트워크 내에서 사용할 수 있는 전용 서비스 버전을 제공하는 것입니다.

* **모든 Azure 지역에서 사용 가능**. Azure DNS Private Zones 기능은 Azure 공용 클라우드의 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="capabilities"></a>기능

Azure DNS는 다음과 같은 기능을 제공합니다.

* **등록 가상 네트워크로 사설 영역에 연결된 단일 가상 네트워크에서 가상 머신 자동 등록**. 가상 머신이 개인 IP를 가리키는 A 레코드로 사설 영역에 등록(추가)됩니다. 등록 가상 네트워크의 가상 머신이 삭제되면 Azure가 연결된 사설 영역에서 해당 DNS 레코드도 자동으로 제거합니다. 

  또한 영역에 대한 DNS 확인이 가상 네트워크 내의 모든 가상 머신에서 작동한다는 점에서 등록 가상 네트워크는 기본적으로 확인 가상 네트워크 역할을 합니다.

  > [!NOTE]
  > 등록 가상 네트워크를 지정하는 경우, 사설 영역에 등록된 해당 가상 네트워크의 VM에 대한 DNS 레코드는 Azure Powershell 및 Azure CLI API에서 보거나 검색할 수 없습니다. VM 레코드는 실제로 등록되어 성공적으로 확인됩니다.

* **확인 가상 네트워크로 사설 영역에 연결된 가상 네트워크 전체에서 지원되는 정방향 DNS 확인**. 가상 네트워크 간 DNS 확인의 경우 가상 네트워크가 서로 연결되는 명시적 종속성은 없습니다. 그러나 고객이 다른 시나리오(예: HTTP 트래픽)를 위해 가상 네트워크를 피어링할 수도 있습니다.

* **DNS 역방향 조회는 가상 네트워크 범위 내에서 지원됩니다**. 사설 영역에 할당된 가상 네트워크 내의 개인 IP 주소에 대한 역방향 DNS 조회에서는 호스트/레코드 이름과 영역 이름이 접미사로 포함된 FQDN이 반환됩니다.

## <a name="limitations"></a>제한 사항

Azure DNS의 제한 사항은 다음과 같습니다.

* 사설 영역당 하나의 등록 가상 네트워크만 허용됩니다.
* 사설 영역당 최대 10개의 확인 가상 네트워크가 허용됩니다. 이 한도는 이 기능이 일반 공급될 때 제거됩니다.
* 특정 가상 네트워크가 1개의 사설 영역에만 등록 가상 네트워크로 연결될 수 있습니다.
* 특정 가상 네트워크가 최대 10개의 사설 영역에 확인 가상 네트워크로 연결될 수 있습니다. 이 한도는 이 기능이 일반 공급될 때 제거됩니다.
* 등록 가상 네트워크를 지정하는 경우, 사설 영역에 등록된 해당 가상 네트워크의 VM에 대한 DNS 레코드는 Azure Powershell 및 Azure CLI API에서 보거나 검색할 수 없습니다. VM 레코드는 실제로 등록되어 성공적으로 확인됩니다.
* 역방향 DNS는 등록 가상 네트워크의 개인 IP 공간에 대해서만 작동합니다.
* 사설 영역에 등록되지 않은 사설 IP(예: 확인 가상 네트워크로 사설 영역에 연결된 가상 네트워크에 있는 가상 머신의 사설 IP)에 대한 역방향 DNS는 DNS 접미사로 *internal.cloudapp.net*을 반환합니다. 그러나 이 접미사를 확인할 수 없습니다.
* 가상 네트워크는 등록 또는 확인 가상 네트워크로 사설 영역에 처음 연결할 때 완전히 비어 있어야 합니다. 그러나 가상 네트워크가 나중에 등록 또는 확인 가상 네트워크로 다른 사설 영역에 연결할 때는 비어 있지 않을 수 있습니다.
* 현재는 Azure와 온-프레미스 네트워크 간 확인이 가능하도록 하려는 등의 경우 조건부 전달이 지원되지 않습니다. 고객이 다른 메커니즘을 통해 이 시나리오를 실현할 수 있는 방법에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

특정 종류의 작업에 필요한 특정 DNS 등록 및 확인 동작을 포함하여 Azure DNS의 사설 영역에 대한 일반적인 질문과 대답은 [FAQ](./dns-faq.md#private-dns)를 참조하세요.  

## <a name="pricing"></a>가격

공개 미리 보기 기간에는 사설 DNS 영역 기능을 무료로 사용할 수 있습니다. 일반 공급 기간에 이 기능은 기존 Azure DNS 제품과 비슷한 사용량 기반 가격 책정 모델을 제공합니다. 

## <a name="next-steps"></a>다음 단계

[Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 사설 영역을 만드는 방법을 알아봅니다.

Azure DNS에서 사설 영역으로 실현할 수 있는 몇 가지 일반 [사설 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 사설 영역에 대한 일반적인 질문과 대답은 [FAQ](./dns-faq.md#private-dns)를 참조하세요. 

[DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.
