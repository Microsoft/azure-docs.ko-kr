---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 0c3bc3f2995131c7777bfc48269a17fceda33192
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742270"
---
> [!div class="op_single_selector"]
> * [Azure Portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

Azure VM(Virtual Machine)에는 하나 이상의 네트워크 인터페이스(NIC)가 연결되어 있습니다. 모든 NIC에는 하나 이상의 정적 또는 동적 공용 및 개인 IP 주소가 할당되어 있을 수 있습니다. VM에 여러 IP 주소를 할당하면 다음을 수행할 수 있습니다.

* 단일 서버에서 IP 주소와 SSL 인증서를 사용하여 여러 웹 사이트 또는 서비스를 호스트할 수 있습니다.
* 방화벽 또는 부하 분산 장치와 같은 네트워크 가상 어플라이언스로 사용됩니다.
* NIC의 개인 IP 주소를 Azure Load Balancer 백 엔드 풀에 추가할 수 있습니다. 이전에 기본 NIC의 기본 IP 주소만 백 엔드 풀에 추가할 수 있었습니다. 여러 IP 구성의 부하를 분산하는 방법에 대해 자세히 알아보려면 [여러 IP 구성의 부하 분산](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

VM에 연결된 모든 NIC에는 하나 이상의 IP 구성이 연결되어 있습니다. 각 구성에는 하나의 정적 또는 동적 개인 IP 주소가 할당됩니다. 각 구성에는 하나의 공용 IP 주소 리소스가 연결되어 있을 수도 있습니다. 공용 IP 주소 리소스에는 동적 또는 정적 공용 IP 주소가 할당되어 있습니다. Azure의 IP 주소에 대한 자세한 내용을 알아보려면 [Azure의 IP 주소](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)를 확인하세요. 

NIC에 할당할 수 있는 개인 IP 주소의 수를 제한합니다. Azure 구독에서 사용할 수 있는 공용 IP 주소의 수도 제한되어 있습니다. 자세한 내용은 [Azure 제한](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 문서를 참조하세요.
