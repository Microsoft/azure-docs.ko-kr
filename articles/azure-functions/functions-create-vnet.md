---
title: Azure 가상 네트워크와 Azure Functions 통합
description: Azure 가상 네트워크에 함수를 연결 하는 방법을 보여 주는 단계별 자습서입니다.
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433224"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>자습서: Azure virtual network와 함수 통합

이 자습서에서는 Azure Functions를 사용 하 여 Azure 가상 네트워크의 리소스에 연결 하는 방법을 보여 줍니다. 가상 네트워크에서 WordPress를 실행 하는 VM과 인터넷 모두에 액세스할 수 있는 기능을 만듭니다.

> [!div class="checklist"]
> * 프리미엄 계획에서 함수 앱 만들기
> * 가상 네트워크의 VM에 WordPress 사이트 배포
> * 가상 네트워크에 함수 앱 연결
> * WordPress 리소스에 액세스 하는 함수 프록시 만들기
> * 가상 네트워크 내에서 WordPress 파일 요청

## <a name="topology"></a>토폴로지

다음 다이어그램은 사용자가 만드는 솔루션의 아키텍처를 보여 줍니다.

 ![가상 네트워크 통합을 위한 UI](./media/functions-create-vnet/topology.png)

프리미엄 계획에서 실행 되는 함수는 VNet 통합 기능을 포함 하는 Azure App Service의 웹 앱과 동일한 호스팅 기능을 제공 합니다. 문제 해결 및 고급 구성을 포함 하 여 VNet 통합에 대한 자세한 내용은 [Azure 가상 네트워크와 앱 통합](../app-service/web-sites-integrate-with-vnet.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 IP 주소 지정 및 서브넷 지정을 이해 하는 것이 중요 합니다. [주소 지정 및 서브넷 지정의 기본 사항을 설명 하는이 문서](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)부터 시작할 수 있습니다. 온라인에서 많은 문서와 비디오를 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-function-app-in-a-premium-plan"></a>프리미엄 계획에서 함수 앱 만들기

먼저 [프리미엄 계획]에서 함수 앱을 만듭니다. 이 계획은 가상 네트워크 통합을 지 원하는 동시에 서버 리스 규모를 제공 합니다.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

오른쪽 위 모서리에 있는 고정 아이콘을 선택 하 여 함수 앱을 대시보드에 고정할 수 있습니다. 고정을 사용 하면 VM을 만든 후이 함수 앱으로 쉽게 돌아갈 수 있습니다.

## <a name="create-a-vm-inside-a-virtual-network"></a>가상 네트워크 내에 VM 만들기

다음으로 가상 네트워크 내에서 WordPress를 실행 하는 미리 구성 된 VM을 만듭니다 ([WORDPRESS LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). WordPress VM은 저렴 하 고 편리 하므로 사용 됩니다. 이 시나리오는 REST Api, App Service 환경 및 기타 Azure 서비스와 같은 가상 네트워크의 모든 리소스에 대해 작동 합니다. 

1. 포털의 왼쪽 탐색 창에서 **+ 리소스 만들기** 를 선택 하 고 검색 필드에 `WordPress LEMP7 Max Performance`를 입력 한 다음 enter 키를 누릅니다.

1. 검색 결과에서 **Wordpress LEMP 최대 성능** 을 선택 합니다. **소프트웨어 요금제** 로 **Wordpress Lemp Max Performance** 의 소프트웨어 요금제를 선택 하 고 **만들기**를 선택 합니다.

1. **기본 사항** 탭에서 이미지 아래 표에 지정 된 대로 VM 설정을 사용 합니다.

    ![VM을 만들기 위한 기본 사항 탭](./media/functions-create-vnet/create-vm-1.png)

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 생성 되는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | `myResourceGroup`또는 함수 앱을 사용 하 여 만든 리소스 그룹을 선택 합니다. 함수 앱, WordPress VM 및 호스팅 계획에 동일한 리소스 그룹을 사용 하면이 자습서를 완료 하면 리소스를 더 쉽게 정리할 수 있습니다. |
    | **가상 머신 이름** | VNET-Wordpress | VM 이름은 리소스 그룹에서 고유 해야 합니다. |
    | **[Region](https://azure.microsoft.com/regions/)** | (유럽) 서유럽 | 가까운 지역 또는 VM에 액세스 하는 함수 근처의 지역을 선택 합니다. |
    | **크기** | B1s | **크기 변경** 을 선택한 다음 1 개의 vcpu 및 1gb의 메모리를 포함 하는 B1s 표준 이미지를 선택 합니다. |
    | **인증 유형** | 암호 | 암호 인증을 사용 하려면 **사용자 이름**, 보안 **암호**및 **암호 확인**도 지정 해야 합니다. 이 자습서에서는 문제를 해결 해야 하는 경우가 아니면 VM에 로그인 할 필요가 없습니다. |

1. **네트워킹** 탭을 선택 하 고 가상 네트워크 구성에서 **새로 만들기**를 선택 합니다.

1. **가상 네트워크 만들기**에서 이미지 아래 표의 설정을 사용 합니다.

    ![VM 만들기의 네트워킹 탭](./media/functions-create-vnet/create-vm-2.png)

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **이름** | myResourceGroup-vnet | 가상 네트워크에 대해 생성 된 기본 이름을 사용할 수 있습니다. |
    | **주소 범위** | 10.10.0.0/16 | 가상 네트워크의 단일 주소 범위를 사용 합니다. |
    | **서브넷 이름** | 자습서-Net | 서브넷의 이름입니다. |
    | **주소 범위** (서브넷) | 10.10.1.0/24   | 서브넷 크기는 서브넷에 추가할 수 있는 인터페이스 수를 정의 합니다. 이 서브넷은 WordPress 사이트에서 사용 됩니다.  `/24` 서브넷은 254 호스트 주소를 제공 합니다. |

1. **확인** 을 선택 하 여 가상 네트워크를 만듭니다.

1. **네트워킹** 탭으로 돌아가서 **공용 IP**에 대해 **없음** 을 선택 합니다.

1. **관리** 탭을 선택한 다음, **진단 저장소 계정**에서 함수 앱을 사용 하 여 만든 저장소 계정을 선택 합니다.

1. **검토 + 만들기**를 선택합니다. 유효성 검사가 완료 된 후 **만들기**를 선택 합니다. VM 만들기 프로세스는 몇 분 정도 걸립니다. 만든 VM은 가상 네트워크에만 액세스할 수 있습니다.

1. VM을 만든 후 **리소스로 이동** 을 선택 하 여 새 VM에 대한 페이지를 표시 한 다음 **설정**아래에서 **네트워킹** 을 선택 합니다.

1. **공용 IP**가 없는지 확인 합니다. 함수 앱에서 VM에 연결 하는 데 사용 하는 **개인 IP**를 기록해 둡니다.

    ![VM의 네트워킹 설정](./media/functions-create-vnet/vm-networking.png)

이제 가상 네트워크 내에 완전히 배포 된 WordPress 사이트가 있습니다. 이 사이트는 공용 인터넷에서 액세스할 수 없습니다.

## <a name="connect-your-function-app-to-the-virtual-network"></a>가상 네트워크에 함수 앱 연결

가상 네트워크의 VM에서 실행 되는 WordPress 사이트를 사용 하 여 이제 함수 앱을 해당 가상 네트워크에 연결할 수 있습니다.

1. 새 함수 앱에서 **플랫폼 기능** > **네트워킹**을 선택 합니다.

    ![함수 앱에서 네트워킹 선택](./media/functions-create-vnet/networking-0.png)

1. **VNet 통합**에서 **구성 하려면 여기를 클릭**하세요 .를 선택 합니다.

    ![네트워크 기능 구성 상태](./media/functions-create-vnet/Networking-1.png)

1. 가상 네트워크 통합 페이지에서 **VNet 추가 (미리 보기)** 를 선택 합니다.

    ![VNet 통합 미리 보기 추가](./media/functions-create-vnet/networking-2.png)

1. **네트워크 기능 상태**에서 이미지 아래 표의 설정을 사용 합니다.

    ![함수 앱 가상 네트워크 정의](./media/functions-create-vnet/networking-3.png)

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | 이 가상 네트워크는 이전에 만든 가상 네트워크입니다. |
    | **서브넷** | 새 서브넷 만들기 | 사용할 함수 앱에 대한 가상 네트워크의 서브넷을 만듭니다. 빈 서브넷을 사용 하도록 VNet 통합을 구성 해야 합니다. 함수에서 VM과 다른 서브넷을 사용 하는 것은 중요 하지 않습니다. 가상 네트워크는 두 서브넷 간에 트래픽을 자동으로 라우팅합니다. |
    | **서브넷 이름** | 함수-Net | 새 서브넷의 이름입니다. |
    | **Virtual network 주소 블록** | 10.10.0.0/16 | WordPress 사이트에서 사용 하는 것과 동일한 주소 블록을 선택 합니다. 하나의 주소 블록만 정의 해야 합니다. |
    | **주소 범위** | 10.10.2.0/24   | 서브넷 크기는 프리미엄 계획 함수 앱이 확장할 수 있는 총 인스턴스 수를 제한 합니다. 이 예제에서는 254 사용 가능한 호스트 주소를 사용 하는 `/24` 서브넷을 사용 합니다. 이 서브넷은 과도 하 게 프로 비전 되었지만 계산 하기 쉽습니다. |

1. **확인** 을 선택 하 여 서브넷을 추가 합니다. VNet 통합 및 네트워크 기능 상태 페이지를 닫아 함수 앱 페이지로 돌아갑니다.

이제 함수 앱은 WordPress 사이트를 실행 하는 가상 네트워크에 액세스할 수 있습니다. 다음으로 [Azure Functions 프록시](functions-proxies.md) 를 사용 하 여 WordPress 사이트에서 파일을 반환 합니다.

## <a name="create-a-proxy-to-access-vm-resources"></a>VM 리소스에 액세스 하는 프록시 만들기

VNet 통합을 사용 하도록 설정 하면 함수 앱에서 프록시를 만들어 가상 네트워크에서 실행 중인 VM에 요청을 전달할 수 있습니다.

1. 함수 앱에서 **프록시** >  **+** 을 선택한 다음 이미지 아래의 표에 있는 프록시 설정을 사용 합니다.

    ![프록시 설정 정의](./media/functions-create-vnet/create-proxy.png)

    | 설정  | 제안 값  | Description      |
    | -------- | ---------------- | ---------------- |
    | **이름** | PlAnT | 이름은 임의의 값일 수 있습니다. 프록시를 식별 하는 데 사용 됩니다. |
    | **경로 템플릿** | /플랜트 | VM 리소스에 매핑되는 경로입니다. |
    | **백 엔드 URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | `<YOUR_VM_IP>`를 앞에서 만든 WordPress VM의 IP 주소로 바꿉니다. 이 매핑은 사이트에서 단일 파일을 반환 합니다. |

1. **만들기** 를 선택 하 여 함수 앱에 프록시를 추가 합니다.

## <a name="try-it-out"></a>사용해 보기

1. 브라우저에서 **백 엔드 url**로 사용한 url에 액세스를 시도 합니다. 예상 대로 요청 시간이 초과 됩니다. WordPress 사이트는 인터넷이 아닌 가상 네트워크에만 연결 되므로 시간 초과가 발생 합니다.

1. 새 프록시에서 **프록시 URL** 값을 복사 하 여 브라우저의 주소 표시줄에 붙여 넣습니다. 반환 된 이미지는 가상 네트워크 내에서 실행 되는 WordPress 사이트에서 가져온 것입니다.

    ![WordPress 사이트에서 반환 된 공장 이미지 파일](./media/functions-create-vnet/plant.png)

함수 앱은 인터넷 및 가상 네트워크에 연결 되어 있습니다. 프록시는 공용 인터넷을 통해 요청을 받은 다음 해당 요청을 연결 된 가상 네트워크로 전달 하는 간단한 HTTP 프록시로 작동 합니다. 그러면 프록시가 인터넷을 통해 공개적으로 응답을 다시 릴레이 합니다.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서 WordPress 사이트는 함수 앱에서 프록시를 사용 하 여 호출 되는 API 역할을 합니다. 이 시나리오는 쉽게 설정 하 고 시각화할 수 있으므로 좋은 자습서를 만듭니다. 가상 네트워크 내에 배포 된 다른 API를 사용할 수 있습니다. 가상 네트워크 내에 배포 된 Api를 호출 하는 코드를 사용 하 여 함수를 만들었을 수도 있습니다. 보다 현실적인 시나리오는 데이터 클라이언트 Api를 사용 하 여 가상 네트워크에 배포 된 SQL Server 인스턴스를 호출 하는 함수입니다.

프리미엄 계획에서 실행 되는 함수는 PremiumV2 계획의 web apps와 동일한 기본 App Service 인프라를 공유 합니다. [Azure App Service의 웹 앱](../app-service/overview.md) 에 대한 모든 설명서는 프리미엄 계획 기능에 적용 됩니다.

> [!div class="nextstepaction"]
> [기능의 네트워킹 옵션에 대한 자세한 정보](./functions-networking-options.md)

[프리미엄 계획]: functions-scale.md#premium-plan
