---
title: Azure CLI 스크립트 샘플 - Azure CLI를 통해 여러 웹 사이트 부하 분산 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 동일한 가상 머신으로 여러 웹 사이트 부하 분산
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: e3dc9476d188382db31b03b37b2a23affc61aed3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564887"
---
# <a name="load-balance-multiple-websites"></a>여러 웹 사이트 부하 분산

이 스크립트 샘플에서는 가용성 집합의 구성원인 두 개의 VM(가상 머신)과 가상 네트워크를 만듭니다. 부하 분산 장치는 두 VM에 두 개의 별도 IP 주소에 대한 트래픽을 보냅니다. 스크립트를 실행한 후 웹 서버 소프트웨어를 VM에 배포하고 여러 웹 사이트를 각각 고유한 IP 주소로 호스트할 수 있습니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 네트워크, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Azure Virtual Network 및 서브넷을 만듭니다. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | 고정 IP 주소 및 연결된 DNS 이름을 사용하여 공용 IP 주소를 만듭니다. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Azure Load Balancer를 만듭니다. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | 부하 분산 장치 프로브를 만듭니다. 부하 분산 장치 프로브는 부하 분산 장치 집합의 각 VM을 모니터링하는 데 사용됩니다. 모든 VM이 액세스할 수 없게 되면 트래픽은 VM에 라우팅되지 않습니다. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | 부하 분산 장치 규칙을 만듭니다. 이 샘플에서는 포트 80에 대한 규칙을 만듭니다. HTTP 트래픽이 부하 분산 장치에 도착하면 부하 분산 장치 집합에서 VM 중 하나인 포트 80에 라우팅됩니다. |
| [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) | 부하 분산 장치의 프런트 엔드 IP 주소를 만듭니다. |
| [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool) | 백 엔드 주소 풀을 만듭니다. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | 가상 네트워크 카드를 만들고 가상 네트워크 및 서브넷에 연결합니다. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | 가용성 집합을 만듭니다. 가용성 집합을 사용하면 오류가 발생하는 경우 물리적 리소스 간에 가상 머신을 분산하여 애플리케이션 가동 시간을 확인합니다. 전체 집합은 영향을 받지 않습니다. |
| [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config) | IP 구성을 만듭니다. 구독에서 Microsoft.Network/AllowMultipleIpConfigurationsPerNic 기능을 사용하도록 설정해야 합니다. --make-primary 플래그를 사용하여 하나의 구성만 NIC당 기본 IP 구성으로 지정될 수 있습니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 네트워킹 CLI 스크립트 샘플은 [Azure 네트워킹 개요 설명서](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)에서 찾을 수 있습니다.
