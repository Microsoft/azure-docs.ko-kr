---
title: 인바운드/아웃 바운드 IP 주소
description: Azure App Service에서 인바운드 및 아웃 바운드 IP 주소를 사용 하는 방법, 변경 될 때 그리고 앱에 대한 주소를 찾는 방법을 알아봅니다.
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671597"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service의 인바운드 및 아웃바운드 IP 주소

[Azure App Service](overview.md)는 [App Service Environment](environment/intro.md)를 제외한 다중 테넌트 서비스입니다. App Service Environment에 없는 앱([격리 계층](https://azure.microsoft.com/pricing/details/app-service/)에 있지 않음)은 다른 앱과 네트워크 인프라를 공유합니다. 결과적으로, 앱의 인바운드 및 아웃바운드 IP 주소는 다를 수 있으며 특정 상황에서 변경할 수 있습니다. 

[App Service Environment](environment/intro.md)는 전용 네트워크 인프라를 사용하므로 App Service Environment에서 실행되는 앱은 인바운드 및 아웃바운드 연결 모두에 대해 고정 전용 IP 주소를 가져옵니다.

## <a name="when-inbound-ip-changes"></a>인바운드 IP가 변경되는 경우

스케일 아웃 인스턴스의 수에 관계 없이 각 앱에는 단일 인바운드 IP 주소가 있습니다. 다음 작업 중 하나를 수행할 때 인바운드 IP 주소가 변경될 수 있습니다.

- 앱을 삭제하고 다른 리소스 그룹에서 다시 만듭니다.
- 리소스 그룹 _및_ 지역 조합에서 마지막 앱을 삭제하고 다시 만듭니다.
- 인증서 갱신 중과 같은 기존 SSL 바인딩을 삭제 합니다 ( [인증서 갱신](configure-ssl-certificate.md#renew-certificate)참조).

## <a name="find-the-inbound-ip"></a>인바운드 IP 찾기

로컬 터미널에서 다음 명령을 실행 하기만 하면 됩니다.

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>고정 인바운드 IP 가져오기

경우에 따라 앱에 대해 고정 전용 IP 주소를 사용하고자 할 수 있습니다. 고정 인바운드 IP 주소를 얻으려면 [IP 기반 SSL 바인딩](configure-ssl-bindings.md#secure-a-custom-domain)을 구성해야 합니다. 실제로 앱을 보호하는 SSL 기능이 필요하지 않은 경우 이 바인딩에 대한 자체 서명된 인증서를 업로드할 수도 있습니다. IP 기반 SSL 바인딩에서는 인증서가 자체 IP 주소에 바인딩되므로 App Service는 고정 IP 주소를 프로비전하여 이를 구현합니다. 

## <a name="when-outbound-ips-change"></a>아웃바운드 IP가 변경되는 경우

스케일 아웃 인스턴스의 수에 관계 없이 각 앱에는 지정된 시간에 정해진 아웃바운드 IP 주소 갯수가 있습니다. App Service 앱에서 가령 백 엔드 데이터베이스로의 모든 아웃바운드 연결은 원래 IP 주소로 아웃바운드 IP 주소 중 하나를 사용합니다. 주어진 앱 인스턴스가 아웃바운드 연결을 생성하는 데 어떤 IP 주소를 사용할지 미리 알 수 없으므로 백 엔드 서비스는 앱에 대한 모든 아웃바운드 IP 주소에 대해 해당 방화벽을 열어야 합니다.

더 낮은 계층(**Basic**, **Standard** 및 **Premium**)과 **Premium V2** 계층 사이에서 앱의 규모를 조정하는 경우 앱에 대한 아웃바운드 IP 주소 집합이 변경됩니다.

가격 책정 계층에 관계 없이 `possibleOutboundIpAddresses` 속성이 나 Azure Portal의 **속성** 블레이드에서 **추가 아웃 바운드 ip 주소** 필드를 검색 하 여 앱에서 사용할 수 있는 모든 아웃 바운드 ip 주소 집합을 찾을 수 있습니다. [아웃바운드 IP 찾기](#find-outbound-ips)를 참조하세요.

## <a name="find-outbound-ips"></a>아웃바운드 IP 찾기

Azure Portal의 앱에서 현재 사용하는 아웃바운드 IP 주소를 찾으려면 앱의 왼쪽 탐색 창에서 **속성**을 클릭합니다. 이러한 필드는 **아웃 바운드 IP 주소** 필드에 나열 됩니다.

[Cloud Shell](../cloud-shell/quickstart.md)에서 다음 명령을 실행하면 동일한 정보를 찾을 수 있습니다.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

가격 책정 계층에 관계 없이 앱에 대해 가능한 _모든_ 아웃 바운드 IP 주소를 찾으려면 앱의 왼쪽 탐색에서 **속성** 을 클릭 합니다. **추가 아웃 바운드 IP 주소** 필드에 나열 됩니다.

[Cloud Shell](../cloud-shell/quickstart.md)에서 다음 명령을 실행하면 동일한 정보를 찾을 수 있습니다.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>다음 단계

원본 IP 주소에서 인바운드 트래픽을 제한하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [고정 IP 제한](app-service-ip-restrictions.md)
