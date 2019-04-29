---
title: Azure CLI 스크립트 샘플 - 다중 계층 애플리케이션용 네트워크 만들기 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 다중 계층 애플리케이션을 위한 가상 네트워크를 만듭니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 685896cdbd74788f138b8b9dc09efbcd68a5b565
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60624449"
---
# <a name="create-a-network-for-multi-tier-applications"></a>다중 계층 애플리케이션을 위한 네트워크 만들기

이 스크립트 샘플은 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 트래픽은 HTTP 및 SSH로 제한되며, 백 엔드 서브넷에 대한 트래픽은 MySQL(3306 포트)로 제한됩니다. 스크립트를 실행한 후에는 웹 서버와 MySQL 소프트웨어를 배포할 수 있는 두 개의 가상 머신이 각 서브넷에 하나씩 있게 됩니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>샘플 스크립트


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 네트워크 및 네트워크 보안 그룹을 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az network vnet create](/cli/azure/network/vnet) | Azure 가상 네트워크 및 프런트 엔드 서브넷을 만듭니다. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | 백 엔드 서브넷을 만듭니다. |
| [az network public-ip create](/cli/azure/network/public-ip) | 인터넷에서 VM에 액세스하기 위한 공용 IP 주소를 만듭니다. |
| [az network nic create](/cli/azure/network/nic) | 가상 네트워크 인터페이스를 만들고 가상 네트워크의 프런트 엔드 및 백 엔드 서브넷에 연결합니다. |
| [az network nsg create](/cli/azure/network/nsg) | 프런트 엔드 및 백 엔드 서브넷과 연결되는 NSG(네트워크 보안 그룹)을 만듭니다. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |특정 포트를 특정 서브넷에 허용하거나 차단하는 NSG 규칙을 만듭니다. |
| [az vm create](/cli/azure/vm) | 가상 머신을 만들고 각 VM에 NIC를 연결합니다. 또한 이 명령은 사용할 가상 컴퓨터 이미지와 관리 자격 증명을 지정합니다. |
| [az group delete](/cli/azure/group) | 리소스 그룹 및 이 그룹에 속한 모든 리소스를 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 네트워킹 CLI 스크립트 샘플은 [Azure 네트워킹 개요 설명서](../cli-samples.md)에서 찾을 수 있습니다.
