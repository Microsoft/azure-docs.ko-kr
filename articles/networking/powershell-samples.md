---
title: Azure PowerShell 샘플 | Microsoft Docs
description: Azure PowerShell 샘플
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.openlocfilehash: 5bd0b4e33b5bef4d293e0475880692c72bf1504c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564673"
---
# <a name="azure-powershell-samples-for-networking"></a>네트워킹에 대한 Azure PowerShell 샘플

다음 표에는 Azure PowerShell을 사용하여 빌드된 스크립트에 대한 링크가 포함되어 있습니다.

| | |
|-|-|
|**Azure 리소스 간 연결**||
| [다중 계층 애플리케이션을 위한 가상 네트워크 만들기](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 트래픽은 HTTP로 제한되며, 백 엔드 서브넷에 대한 트래픽은 SQL, 포트 1433으로 제한됩니다. |
| [2개 가상 네트워크 피어링](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 동일한 지역에 2개 가상 네트워크를 만들고 연결합니다. |
| [네트워크 가상 어플라이언스를 통한 트래픽 라우팅](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | 프런트 엔드 및 백 엔드 서브넷과 두 서브넷 간에 트래픽을 라우팅할 수 있는 VM이 있는 가상 네트워크를 만듭니다. |
| [인바운드 및 아웃바운드 VM 네트워크 트래픽 필터링](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 인바운드 네트워크 트래픽은 HTTP 및 HTTPS로 제한됩니다. 백 엔드 서브넷에서 인터넷으로의 아웃바운드 트래픽은 허용되지 않습니다. |
|**부하 분산 및 트래픽 방향**||
| [고가용성을 위해 VM에 트래픽 부하 분산](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | 고가용성의 부하가 분산된 구성으로 여러 가상 머신을 만듭니다. |
| [VM에서 여러 웹 사이트에 부하 분산](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Azure 가용성 집합에 연결되고 Azure 부하 분산 장치를 통해 액세스할 수 있는 2개의 VM을 여러 IP 구성을 사용해서 만듭니다. |
| [높은 애플리케이션 가용성을 위해 여러 지역 간에 트래픽 전송](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  두 개의 App Service 계획, 두 개의 웹앱, Traffic Manager 프로필 및 두 개의 Traffic Manager 엔드포인트를 만듭니다. |
| | |
