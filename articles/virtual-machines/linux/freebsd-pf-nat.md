---
title: Azure에서 FreeBSD 패킷 필터를 사용하여 방화벽 만들기 | Microsoft Docs
description: Azure에서 FreeBSD의 PF를 사용하여 NAT 방화벽을 배포하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 8cfa1696a18925e9e9e8b96299f1255875e85aa8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542992"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Azure에서 FreeBSD 패킷 필터를 사용하여 보안 방화벽을 만드는 방법
이 문서에서는 일반 웹 서버 시나리오에 대해 Azure Resource Manager 템플릿을 통해 FreeBSD 패킷 필터를 사용하여 NAT 방화벽을 배포하는 방법을 소개합니다.

## <a name="what-is-pf"></a>PF란?
PF(패킷 필터 또는 pf)는 BSD에서 사용이 허가된 상태 저장 패킷 필터로, 방화벽을 위한 소프트웨어의 핵심 부분입니다. PF는 빠르게 발전해 왔으며 현재 사용 가능한 다른 방화벽에 비해 여러 가지 이점을 제공합니다. NAT(네트워크 주소 변환)는 처음부터 PF에 포함되었으며, 이후에 ALTQ를 통합하고 PF의 구성을 통해 구성할 수 있게 함으로써 패킷 스케줄러 및 활성 큐 관리 기능이 PF에 통합되었습니다. 장애 조치(failover) 및 중복성을 위한 pfsync 및 CARP, 세션 인증을 위한 authpf, 까다로운 FTP 프로토콜을 방화벽으로 쉽게 차단하기 위한 ftp-proxy 등의 기능도 PF를 확장했습니다. 즉, PF는 강력하고 풍부한 기능을 갖춘 방화벽입니다. 

## <a name="get-started"></a>시작하기
웹 서버에 대해 클라우드에서 보안 방화벽을 설정하려는 경우 지금 시작해 보겠습니다. 네트워킹 토폴로지를 설정하기 위해 이 Azure Resource Manager 템플릿에 사용된 스크립트를 적용할 수도 있습니다.
Azure Resource Manager 템플릿은 Nginx 웹 서버가 설치 및 구성된 2대의 FreeBSD 가상 머신과 PF를 사용하여 NAT/리디렉션을 수행하는 FreeBSD 가상 머신을 설정합니다. 두 웹 서버 송신 트래픽에 대해 NAT를 수행하는 것 외에도, NAT/리디렉션 가상 머신은 HTTP 요청을 가로챈 후 로빈 방식으로 두 웹 서버로 리디렉션합니다. VNet은 라우팅할 수 없는 개인 IP 주소 공간 10.0.0.2/24를 사용하며 사용자는 템플릿의 매개 변수를 수정할 수 있습니다. 또한 Azure Resource Manager 템플릿은 대상 IP 주소를 기준으로 Azure 기본 경로를 재정의하는 데 사용되는 개별 경로 컬렉션에 해당하는 전체 VNet에 대해 경로 테이블을 정의합니다. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Azure CLI를 통해 배포
최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 계정에 로그인해야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제는 `West US` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westus
```

다음으로 [az group deployment create](/cli/azure/group/deployment)를 사용하여 [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) 템플릿을 배포합니다. 같은 경로 아래에 [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json)을 다운로드하고 `adminPassword`, `networkPrefix` 및 `domainNamePrefix`와 같은 자체 리소스 값을 정의합니다. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

약 5분 후에 `"provisioningState": "Succeeded"` 정보를 얻게 됩니다. 그런 후에 프런트 엔드 VM(NAT)에 대해 ssh를 수행하거나 프런트 엔드 VM(NAT)의 공용 IP 주소 또는 FQDN을 사용하여 브라우저에서 Nginx 웹 서버에 액세스할 수 잇습니다. 다음 예제에서는 `myResourceGroup` 리소스 그룹의 프런트 엔드 VM(NAT)에 할당된 FQDN 및 공용 IP 주소를 나열합니다. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>다음 단계
Azure에서 자체 NAT을 설정하려고 하나요? 무료 오픈 소스면서 강력한 기능을 원하시나요? 그렇다면 PF가 적격입니다. [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) 템플릿을 사용하면 일반적인 웹 서버 시나리오에 대해 Azure에서 FreeBSD PF를 사용하여 라운드 로빈 부하 분산 방식으로 NAT 방화벽을 설정하는 데 5분이면 충분합니다. 

Azure의 FreeBSD 제품에 대해 알아보려면 [Azure의 FreeBSD 소개](freebsd-intro-on-azure.md)를 참조하세요.

PF에 대한 자세한 내용은 [FreeBSD 핸드북](https://www.freebsd.org/doc/handbook/firewalls-pf.html) 또는 [PF-사용자 가이드](https://www.freebsd.org/doc/handbook/firewalls-pf.html)를 참조하세요.
