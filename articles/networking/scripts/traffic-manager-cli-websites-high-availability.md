---
title: Azure CLI 스크립트 샘플 - 애플리케이션 고가용성을 위한 트래픽 라우팅 | Microsoft Docs
description: Azure CLI 스크립트 샘플 - 애플리케이션 고가용성을 위한 트래픽 라우팅
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 00964a49940b208144167e9fe1ff4248f46e35fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763202"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>애플리케이션 고가용성을 위해 트래픽 라우팅 - Azure CLI

이 스크립트는 리소스 그룹, 2개 App Service 계획, 2개 웹앱, Traffic Manager 프로필 및 2개 Traffic Manager 엔드포인트를 만듭니다. Traffic Manager는 주 지역인 한 지역의 애플리케이션 및 주 지역의 애플리케이션을 사용할 수 없을 때 보조 지역으로 트래픽을 전달합니다. 스크립트를 실행하기 전에 MyWebApp, MyWebAppL1 및 MyWebAppL2 값을 Azure에서 고유한 값으로 변경해야 합니다. 스크립트를 실행한 후에는 mywebapp.trafficmanager.net URL을 사용하여 주 지역의 응용 프로그램에 액세스할 수 있습니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, App Service 앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 웹앱, Traffic Manager 프로필 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az appservice plan create](/cli/azure/appservice/plan) | App Service 계획을 만듭니다. Azure 웹앱에 대한 서버 팜과 비슷합니다. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | App Service 계획 내에서 Azure 웹앱을 만듭니다. |
| [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile) | Azure Traffic Manager 프로필을 만듭니다. |
| [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint) | Azure Traffic Manager 프로필에 엔드포인트를 추가합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 App Service CLI 스크립트 샘플은 [Azure 네트워킹 설명서](../cli-samples.md)에서 찾을 수 있습니다.