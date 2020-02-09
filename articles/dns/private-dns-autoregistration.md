---
title: Azure DNS 개인 영역의 이라고 기능
description: Azure DNS 개인 영역의 이라고 기능 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961231"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS 개인 영역의 이라고 기능

Azure DNS 개인 영역 자동 등록 기능을 사용 하면 가상 네트워크에 배포 된 가상 컴퓨터에 대한 DNS 레코드 관리의 어려움을 해소 합니다. 개인 DNS 영역을 사용 하 여 [가상 네트워크를 연결](./private-dns-virtual-network-links.md) 하 고 모든 가상 컴퓨터에 대해 자동 등록을 사용 하는 경우 가상 네트워크에 배포 된 가상 컴퓨터에 대한 dns 레코드가 자동으로 개인 dns 영역에 생성 됩니다. 전방 조회 레코드 (A 레코드) 외에도 가상 컴퓨터에 대한 역방향 조회 레코드 (PTR 레코드)도 자동으로 생성 됩니다.
가상 네트워크에 가상 컴퓨터를 더 추가 하는 경우 해당 가상 컴퓨터에 대한 DNS 레코드도 자동으로 연결 된 개인 DNS 영역에 생성 됩니다.

가상 컴퓨터를 삭제 하면 가상 컴퓨터에 대한 DNS 레코드가 개인 DNS 영역에서 자동으로 삭제 됩니다.

가상 네트워크 링크를 만드는 동안 "자동 등록 사용" 옵션을 선택 하 여 이라고를 사용 하도록 설정할 수 있습니다.

![자동 등록 사용](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Restrictions

* 이라고는 가상 컴퓨터에 대해서만 작동 합니다. 내부 부하 분산 장치와 같은 다른 모든 리소스의 경우 가상 네트워크에 연결 된 개인 DNS 영역에서 DNS 레코드를 수동으로 만들 수 있습니다.
* DNS 레코드는 주 가상 컴퓨터 NIC에 대해서만 자동으로 생성 됩니다. 가상 컴퓨터에 NIC가 둘 이상 있는 경우 다른 네트워크 인터페이스에 대한 DNS 레코드를 수동으로 만들 수 있습니다.
* 이라고 for IPv6 (AAAA 레코드)는 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.md)를 참조하세요.
