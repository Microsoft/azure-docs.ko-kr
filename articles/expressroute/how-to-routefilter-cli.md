---
title: 'Microsoft 피어링에 대한 경로 필터 구성 - ExpressRoute: Azure CLI | Microsoft Docs'
description: 이 문서에서는 Azure CLI를 사용하여 Microsoft 피어링에 대한 경로 필터를 구성하는 방법을 설명합니다.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: cfd9f4c52d3ddddd944186a833cba48e6ca76182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837864"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Microsoft 피어링에 대한 경로 필터 구성: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

경로 필터는 Microsoft 피어링을 통해 지원되는 서비스의 하위 집합을 사용하는 방법입니다. 이 문서의 단계는 ExpressRoute 회로에 대한 경로 필터를 구성하고 관리하는 데 도움이 됩니다.

Dynamics 365 서비스 및 Exchange Online, SharePoint Online 및 비즈니스용 Skype와 같은 Office 365 서비스는 Microsoft 피어링을 통해 액세스할 수 있습니다. Microsoft 피어링이 ExpressRoute 회로에 구성되면 설정된 BGP 세션을 통해 이러한 서비스와 관련된 모든 접두사가 보급됩니다. BGP 커뮤니티 값은 접두사를 통해 제공되는 서비스를 식별하는 모든 접두사에 연결됩니다. BGP 커뮤니티 값과 매핑되는 서비스의 목록은 [BGP 커뮤니티](expressroute-routing.md#bgp)를 참조하세요.

모든 서비스에 연결해야 하는 경우 많은 수의 접두사가 BGP를 통해 보급됩니다. 그러면 네트워크 내의 라우터에서 유지 관리되는 경로 테이블의 크기가 상당히 증가합니다. Microsoft 피어링을 통해 제공되는 서비스의 하위 집합만 사용하려는 경우 두 가지 방법으로 경로 테이블의 크기를 줄일 수 있습니다. 다음을 수행할 수 있습니다.

* BGP 커뮤니티에 라우팅 필터를 적용하여 필요 없는 접두사를 필터링합니다. 표준 네트워킹 방법은 많은 네트워크 내에서 일반적으로 사용됩니다.

* 경로 필터를 정의하고 ExpressRoute 회로에 적용합니다. 경로 필터는 Microsoft 피어링을 통해 사용하려는 서비스 목록을 선택할 수 있는 새 리소스입니다. ExpressRoute 라우터는 경로 필터에서 식별된 서비스에 속하는 접두사 목록만을 보냅니다.

### <a name="about"></a>경로 필터 정보

Microsoft 피어링이 ExpressRoute 회로에 구성되면 Microsoft 에지 라우터는 에지 라우터(사용자 또는 연결 공급자 소유)를 사용하여 한 쌍의 BGP 세션을 설정합니다. 경로는 네트워크에 보급되지 않습니다. 네트워크에 경로 보급을 사용하려면 경로 필터를 연결해야 합니다.

경로 필터를 사용하면 ExpressRoute 회로의 Microsoft 피어링을 통해 사용하려는 서비스를 식별할 수 있습니다. 특히 모든 BGP 커뮤니티 값의 허용 목록입니다 경로 필터 리소스가 정의되고 ExpressRoute 회로에 연결되면 BGP 커뮤니티 값에 매핑되는 모든 접두사는 네트워크에 보급됩니다.

경로 필터를 Office 365 서비스에 연결할 수 있으려면 ExpressRoute를 통해 Office 365 서비스를 사용할 수 있는 권한이 부여되어야 합니다. ExpressRoute를 통해 Office 365 서비스를 사용할 수 있는 권한이 없는 경우 경로 필터를 연결하는 작업에 실패합니다. 권한 부여 프로세스에 대한 자세한 내용은 [Office 365용 Azure ExpressRoute](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)를 참조하세요. Dynamics 365 서비스에 대한 연결에는 사전 권한 부여가 필요하지 않습니다.

> [!IMPORTANT]
> 경로 필터를 정의하지 않은 경우에도 2017년 8월 1일 이전에 구성된 ExpressRoute 회로의 Microsoft 피어링에는 Microsoft 피어링을 통해 보급된 모든 서비스 접두사가 포함됩니다. 2017년 8월 1일 이후에 구성되는 ExpressRoute 회로의 Microsoft 피어링에는 경로 필터를 회로에 연결할 때까지 접두사가 보급되지 않습니다.
> 
> 

### <a name="workflow"></a>워크플로

Microsoft 피어링을 통해 서비스에 성공적으로 연결할 수 있으려면 다음 구성 단계를 완료해야 합니다.

* Microsoft 피어링을 프로비전하는 활성 ExpressRoute 회로가 있어야 합니다. 다음 지침을 사용하여 이러한 작업을 수행할 수 있습니다.
  * [ExpressRoute 회로를 만들고](howto-circuit-cli.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. ExpressRoute 회로는 프로비전되고 활성화된 상태여야 합니다.
  * 직접 BGP 세션을 관리하는 경우 [Microsoft 피어링을 만듭니다](howto-routing-cli.md). 또는 연결 공급자가 회로에 Microsoft 피어링을 프로비전하도록 합니다.

* 경로 필터를 만들고 구성해야 합니다.
  * Microsoft 피어링을 통해 사용하려는 서비스를 식별합니다.
  * 서비스와 연결된 BGP 커뮤니티 값의 목록을 식별합니다
  * BGP 커뮤니티 값과 일치하는 접두사 목록을 허용하는 규칙을 만듭니다.

* 경로 필터를 ExpressRoute 회로에 연결해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

시작하기 전에 최신 버전의 CLI 명령(2.0 이상)을 설치합니다. CLI 설치 명령에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 및 [워크플로](expressroute-workflows.md)를 검토합니다.

* 활성화된 ExpressRoute 회로가 있어야 합니다. 지침을 수행하여 [ExpressRoute 회로를 만들고](howto-circuit-cli.md) 진행하기 전에 연결 공급자를 통해 회로를 사용하도록 설정합니다. ExpressRoute 회로는 프로비전되고 활성화된 상태여야 합니다.

* 활성 Microsoft 피어링이 있어야 합니다. [피어링 구성 수정 및 만들기](howto-routing-cli.md)의 지침에 따릅니다.

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure 계정에 로그인하고 구독을 선택합니다.

구성을 시작하려면, Azure 계정에 로그인합니다. "사용해 보세요"를 사용하는 경우 자동으로 로그인되며 로그인 단계를 건너뛸 수 있습니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

```azurecli
az login
```

계정에 대한 구독을 확인합니다.

```azurecli-interactive
az account list
```

ExpressRoute 회로를 만들려는 구독을 선택합니다.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>1단계: 접두사 및 BGP 커뮤니티 값의 목록 가져오기

### <a name="1-get-a-list-of-bgp-community-values"></a>1. BGP 커뮤니티 값의 목록 가져오기

다음 cmdlet을 사용하여 Microsoft 피어링을 통해 액세스할 수 있는 서비스와 관련된 BGP 커뮤니티 값의 목록 및 관련된 접두사 목록을 가져옵니다.

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. 사용하려는 값의 목록 확인

경로 필터에 사용하려는 BGP 커뮤니티 값 목록을 만듭니다. 예를 들어, Dynamics 365 서비스의 BGP 커뮤니티 값은 12076:5040입니다.

## <a name="filter"></a>2단계: 경로 필터 및 필터 규칙 만들기

경로 필터에는 하나의 규칙만이 있을 수 있고 규칙은 '허용' 형식이어야 합니다. 이 규칙에는 관련된 BGP 커뮤니티 값의 목록이 있을 수 있습니다

### <a name="1-create-a-route-filter"></a>1. 경로 필터 만들기

먼저, 경로 필터를 만듭니다. 이 명령은 `az network route-filter create` 경로 필터 리소스만 만듭니다. 리소스를 만든 후에 규칙을 만들고 경로 필터 개체에 연결해야 합니다. 다음 명령을 실행하여 경로 필터 리소스를 만듭니다.

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. 필터 규칙 만들기

다음 명령을 실행하여 새 규칙을 만듭니다.
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>3단계: 경로 필터를 ExpressRoute 회로에 연결합니다.

다음 명령을 실행하여 경로 필터를 ExpressRoute 회로에 연결합니다.

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>일반 작업

### <a name="getproperties"></a>경로 필터의 속성을 가져오려면

경로 필터의 속성을 가져오려면 다음 명령을 사용합니다.

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>경로 필터의 속성을 업데이트하려면

경로 필터가 이미 회로에 연결된 경우 BGP 커뮤니티 목록에 대한 업데이트로 인해 설정된 BGP 세션을 통해 적절한 접두사 보급 변경 내용을 자동으로 전파합니다. 다음 명령을 사용하여 경로 필터의 BGP 커뮤니티 목록을 업데이트할 수 있습니다.

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>ExpressRoute 회로에서 경로 필터를 분리하려면

경로 필터를 ExpressRoute 회로에서 분리하면 접두사가 BGP 세션을 통해 보급되지 않습니다. 다음 명령을 사용하여 ExpressRoute 회로에서 경로 필터를 분리할 수 있습니다.

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>경로 필터를 삭제하려면

회로에 연결되지 않은 경우에만 필터를 삭제할 수 있습니다. 경로 필터를 삭제하기 전에 회로에 연결되지 않았는지 확인합니다. 다음 명령을 사용하여 경로 필터를 삭제할 수 있습니다.

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

ExpressRoute에 대한 자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md)를 참조하세요.
