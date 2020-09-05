---
title: 서비스 끝점과 Application Gateway 통합-Azure App Service | Microsoft Docs
description: Application Gateway 서비스 끝점으로 보안이 설정 된 Azure App Service와 통합 되는 방법을 설명 합니다.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: fdb502685259a5e12bf0182c36aa6c73ec7c1dd5
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958936"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>서비스 끝점과 Application Gateway 통합
Azure 애플리케이션 게이트웨이와의 통합에 대해 약간 다른 구성이 필요한 App Service에는 세 가지 변형이 있습니다. 이러한 변형에는 일반 App Service, ILB (내부 Load Balancer) App Service Environment ASE () 및 외부 ASE 라고도 합니다. 이 문서에서는 App Service (다중 테 넌 트)를 사용 하 여 구성 하 고 ILB 및 외부 ASE에 대 한 고려 사항을 설명 하는 방법을 안내 합니다.

## <a name="integration-with-app-service-multi-tenant"></a>App Service와 통합 (다중 테 넌 트)
App Service (다중 테 넌 트)에는 공용 인터넷 연결 끝점이 있습니다. [서비스 끝점](../../virtual-network/virtual-network-service-endpoints-overview.md) 을 사용 하 여 Azure Virtual Network 내의 특정 서브넷 에서만 트래픽을 허용 하 고 다른 모든 항목을 차단할 수 있습니다. 다음 시나리오에서는이 기능을 사용 하 여 App Service 인스턴스가 특정 Application Gateway 인스턴스의 트래픽만 받을 수 있도록 합니다.

![App Service와 Application Gateway 통합](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

App Service 및 Application Gateway를 만드는 것 외에도이 구성에는 두 가지 부분이 있습니다. 첫 번째 부분은 Application Gateway 배포 된 Virtual Network의 서브넷에서 서비스 끝점을 사용 하도록 설정 하는 것입니다. 서비스 끝점은 서브넷을 나가는 모든 네트워크 트래픽이 특정 서브넷 ID로 태그를 지정 하 여 App Service 합니다. 두 번째 부분은 특정 서브넷 ID로 태그가 지정 된 트래픽만 허용 되도록 특정 웹 앱의 액세스 제한을 설정 하는 것입니다. 기본 설정에 따라 다른 도구를 사용 하 여 구성할 수 있습니다.

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal를 사용 하 여 설치를 프로 비전 하 고 구성 하는 네 가지 단계를 수행 합니다. 기존 리소스가 있는 경우 첫 번째 단계를 건너뛸 수 있습니다.
1. App Service 설명서의 빠른 시작 중 하나를 사용 하 여 App Service를 만듭니다. 예를 들어 [.NET Core 빠른](../quickstart-dotnetcore.md) 시작
2. [포털 빠른](../../application-gateway/quick-create-portal.md)시작을 사용 하 여 Application Gateway를 만들지만 백 엔드 대상 추가 섹션을 건너뜁니다.
3. [Application Gateway에서 App Service을 백 엔드로](../../application-gateway/configure-web-app-portal.md)구성 하지만 액세스 제한 섹션을 건너뜁니다.
4. 마지막으로 [서비스 끝점을 사용 하 여 액세스 제한을](../../app-service/app-service-ip-restrictions.md#service-endpoints)만듭니다.

이제 Application Gateway를 통해 App Service에 액세스할 수 있지만 App Service에 직접 액세스 하려고 하면 웹 사이트가 중지 되었음을 나타내는 403 HTTP 오류가 표시 됩니다.

![App Service와 Application Gateway 통합](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
[리소스 관리자 배포 템플릿에서][template-app-gateway-app-service-complete] 는 전체 시나리오를 프로 비전 합니다. 시나리오는 Application Gateway에서 트래픽을 수신 하기 위한 서비스 끝점 및 액세스 제한으로 잠긴 App Service 인스턴스로 구성 됩니다. 템플릿에는 간단 하 게 하기 위해 리소스 이름에 많은 스마트 기본값과 고유한 postfixes가 추가 되어 있습니다. 이를 재정의 하려면 리포지토리를 복제 하거나 템플릿을 다운로드 하 고 편집 해야 합니다. 

템플릿을 적용 하려면 템플릿 설명에 있는 Azure에 배포 단추를 사용 하거나 적절 한 PowerShell/CLI를 사용할 수 있습니다.

## <a name="using-azure-command-line-interface"></a>Azure 명령줄 인터페이스 사용
[Azure CLI 샘플](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) 은 Application Gateway에서의 트래픽만 수신 하도록 서비스 끝점 및 액세스 제한과 함께 잠긴 App Service를 프로 비전 합니다. 기존 App Service 기존 Application Gateway의 트래픽만 격리 해야 하는 경우 다음 명령을 사용할 수 있습니다.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

기본 구성에서는 명령을 사용 하 여 서브넷에 있는 서비스 끝점 구성과 App Service의 액세스 제한 설정을 모두 확인할 수 있습니다.

## <a name="considerations-for-ilb-ase"></a>ILB ASE에 대 한 고려 사항
ILB ASE는 인터넷에 노출 되지 않으므로 인스턴스와 Application Gateway 간의 트래픽은 이미 Virtual Network에 격리 되어 있습니다. 다음 [방법 가이드](../environment/integrate-with-application-gateway.md) 에서는 ILB ASE를 구성 하 고 Azure Portal를 사용 하 여 Application Gateway와 통합 합니다. 

Application Gateway 서브넷의 트래픽만 ASE에 도달 하도록 하려면 ASE의 모든 웹 앱에 영향을 주는 NSG (네트워크 보안 그룹)를 구성할 수 있습니다. NSG의 경우 서브넷 IP 범위 및 선택적으로 포트 (80/443)를 지정할 수 있습니다. ASE가 제대로 작동 하는 [데 필요한 NSG 규칙](../environment/network-info.md#network-security-groups) 을 재정의 하지 않아야 합니다.

개별 웹 앱에 대 한 트래픽을 격리 하려면 ASE에서 서비스 끝점이 작동 하지 않으므로 ip 기반 액세스 제한을 사용 해야 합니다. IP 주소는 Application Gateway 인스턴스의 개인 IP 여야 합니다.

## <a name="considerations-for-external-ase"></a>외부 ASE에 대 한 고려 사항
외부 ASE에는 다중 테 넌 트 App Service와 같은 공용 연결 부하 분산 장치가 있습니다. 서비스 끝점은 ASE에 대해 작동 하지 않으므로 Application Gateway 인스턴스의 공용 IP를 사용 하 여 ip 기반 액세스 제한을 사용 해야 합니다. Azure Portal를 사용 하 여 외부 ASE를 만들려면이 [빠른](../environment/create-external-ase.md) 시작을 수행할 수 있습니다.

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "전체 시나리오에 대 한 Azure Resource Manager 템플릿"

## <a name="considerations-for-kuduscm-site"></a>Kudu/scm 사이트에 대 한 고려 사항
Kudu 라고도 하는 scm 사이트는 모든 웹 앱에 대해 존재 하는 관리 사이트입니다. Scm 사이트를 역방향으로 프록시 하는 것은 불가능 하며, 개별 IP 주소 또는 특정 서브넷으로 잠금을 해제 하려고 할 수도 있습니다.

기본 사이트와 동일한 액세스 제한을 사용 하려는 경우 다음 명령을 사용 하 여 설정을 상속할 수 있습니다.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Scm 사이트에 대 한 개별 액세스 제한을 설정 하려면 아래와 같이--scm-사이트 플래그를 사용 하 여 액세스 제한을 추가할 수 있습니다.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>다음 단계
App Service Environment에 대 한 자세한 내용은 [App Service Environment 설명서](/azure/app-service/environment)를 참조 하세요.

웹 앱을 더욱 안전 하 게 보호 하기 위해 Application Gateway의 웹 응용 프로그램 방화벽에 대 한 정보는 [Azure 웹 응용 프로그램 방화벽 설명서](../../web-application-firewall/ag/ag-overview.md)에서 찾을 수 있습니다.