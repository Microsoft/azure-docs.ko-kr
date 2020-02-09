---
title: Azure DNS 사설 영역에 대한 가상 네트워크 링크 하위 리소스 정의
description: 가상 네트워크 링크 하위 리소스 a Azure DNS 개인 영역에 대한 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646764"
---
# <a name="what-is-a-virtual-network-link"></a>Virtual network 링크 란?

Azure에서 개인 DNS 영역을 만들면 가상 네트워크에서 즉시 액세스할 수 없습니다. 해당 네트워크에서 호스트 되는 VM이 개인 DNS 영역에 액세스할 수 있으려면 먼저 가상 네트워크에 연결 해야 합니다.
개인 DNS 영역을 가상 네트워크와 연결 하려면 개인 DNS 영역 아래에 가상 네트워크 링크를 만들어야 합니다. 모든 개인 DNS 영역에는 가상 네트워크 링크 자식 리소스 모음이 있습니다. 이러한 각 리소스는 가상 네트워크에 대한 연결을 나타냅니다.

가상 네트워크를 등록 가상 네트워크 또는 확인 가상 네트워크로 개인 DNS 영역에 연결할 수 있습니다.

## <a name="registration-virtual-network"></a>등록 가상 네트워크

개인 DNS 영역과 가상 네트워크 간에 [링크를 만들](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) 때 가상 컴퓨터에 대한 DNS 레코드 [이라고](./private-dns-autoregistration.md) 를 설정 하는 옵션이 있습니다. 이 옵션을 선택 하는 경우 가상 네트워크는 개인 DNS 영역에 대한 등록 가상 네트워크가 됩니다. DNS 레코드는 네트워크에 배포 하는 가상 컴퓨터에 대해 자동으로 생성 됩니다. 가상 네트워크에 이미 배포 된 가상 컴퓨터에 대한 DNS 레코드가 생성 됩니다. 가상 네트워크 관점에서 개인 DNS 영역은 해당 가상 네트워크의 등록 영역이 됩니다.
하나의 개인 DNS 영역에는 여러 개의 등록 가상 네트워크가 있을 수 있지만, 모든 가상 네트워크에는 정확히 하나의 등록 영역을 연결할 수 있습니다.

## <a name="resolution-virtual-network"></a>확인 가상 네트워크

개인 DNS 영역에서 가상 네트워크 링크를 만들고 DNS 레코드 이라고를 사용 하지 않도록 선택 하면 가상 네트워크가 해결 전용 가상 네트워크로 취급 됩니다. 이러한 네트워크에 배포 된 가상 컴퓨터에 대한 DNS 레코드는 연결 된 개인 DNS 영역에서 자동으로 만들어지지 않습니다. 그러나 이러한 네트워크에 배포 된 가상 컴퓨터는 개인 DNS 영역에서 DNS 레코드를 성공적으로 쿼리할 수 있습니다. 이러한 레코드는 수동으로 만들거나 개인 DNS 영역을 사용 하 여 등록 네트워크로 연결 된 다른 가상 네트워크에서 채워질 수 있습니다.
하나의 개인 DNS 영역에 여러 확인 가상 네트워크를 사용할 수 있으며 가상 네트워크에는 여러 해결 영역이 연결 될 수 있습니다.

## <a name="limits"></a>제한

등록 및 해상도 네트워크의 수를 이해 하려면 개인 DNS 영역에 연결할 수 있습니다 [Azure DNS 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) 을 참조 하세요.

## <a name="other-considerations"></a>기타 고려 사항

* 클래식 배포 모델을 사용 하 여 배포 된 가상 네트워크는 지원 되지 않습니다.

* 개인 DNS 영역과 가상 네트워크 사이에는 하나의 링크만 만들 수 있습니다.

* 개인 DNS 영역 아래의 각 가상 네트워크 링크는 개인 DNS 영역의 컨텍스트 내에서 고유한 이름을 가져야 합니다. 다른 개인 DNS 영역에 동일한 이름의 링크가 있을 수 있습니다.

* 가상 네트워크 링크를 만든 후 가상 네트워크 링크 리소스의 "링크 상태" 필드를 확인 합니다. 가상 네트워크의 크기에 따라 링크가 작동 하 고 링크 상태가 *완료*로 변경 되기까지 몇 분 정도 걸릴 수 있습니다.

* 가상 네트워크를 삭제 하면 다른 개인 DNS 영역에 연결 된 모든 가상 네트워크 연결 및 자동 등록 된 DNS 레코드가 자동으로 삭제 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) 를 사용 하 여 사설 DNS 영역에 가상 네트워크를 연결 하는 방법을 알아봅니다.

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.md)를 참조하세요.
