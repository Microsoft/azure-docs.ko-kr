---
title: 빠른 시작 - Azure Portal을 사용하여 Private Link 서비스 만들기
titlesuffix: Azure Private Link
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Private Link 서비스를 만드는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 0d873401d377a03581a319769604f3d976f365be
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927241"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Private Link 서비스 만들기

Azure Private Link 서비스는 Private Link에서 관리하는 자체 서비스를 말합니다. Azure 표준 Load Balancer 배후에서 작동하는 서비스 또는 리소스에 대한 Private Link 액세스를 제공할 수 있습니다. 서비스 소비자가 자신의 가상 네트워크에서 서비스에 비공개로 액세스할 수 있습니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Private Link 서비스를 만드는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-an-internal-load-balancer"></a>내부 부하 분산 장치 만들기

먼저 가상 네트워크를 만듭니다. 그런 다음, Private Link 서비스에 사용할 내부 부하 분산 장치를 만듭니다.

## <a name="virtual-network-and-parameters"></a>가상 네트워크 및 매개 변수

이 섹션에서는 가상 네트워크를 만듭니다. 또한 Private Link 서비스에 액세스하는 부하 분산 장치를 호스트하는 서브넷을 만듭니다.

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꾸어야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 미국 동부 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>표준 부하 분산 장치 만들기

포털을 사용하여 표준 내부 부하 분산 장치를 만듭니다. 지정한 이름과 IP 주소는 자동으로 부하 분산 장치의 프런트 엔드로 구성됩니다.

1. 포털의 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Load Balancer**를 선택합니다.

1. **부하 분산 장치 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | **구독**               | 구독을 선택합니다.    |
    | **리소스 그룹**         | 상자에서 **myResourceGroupLB**를 선택합니다.|
    | **이름**                   | **myLoadBalancer**를 입력합니다.                                   |
    | **지역**         | **미국 동부 2**를 선택합니다.                                        |
    | **유형**          | **내부**를 선택합니다.                                        |
    | **SKU**           | **표준**을 선택합니다.                          |
    | **가상 네트워크**           | **myVNet**을 선택합니다.                          |
    | **IP 주소 할당**              | **고정**을 선택합니다.   |
    | **개인 IP 주소**|가상 네트워크 및 서브넷의 주소 공간에 있는 주소를 입력합니다. 예를 들면 10.3.0.7입니다.  |

1. 나머지 설정에 기본값을 적용한 다음, **검토 + 만들기**를 선택합니다.

1. **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

### <a name="create-standard-load-balancer-resources"></a>표준 부하 분산 장치 리소스 만들기

이 섹션에서는 백 엔드 주소 풀 및 상태 프로브에 대한 부하 분산 장치 설정을 구성합니다. 또한 부하 분산 장치 규칙을 지정합니다.

#### <a name="create-a-back-end-pool"></a>백 엔드 풀 만들기

백 엔드 풀 주소에는 부하 분산 장치에 연결된 가상 NIC의 IP 주소가 포함됩니다. 이 풀을 사용하면 트래픽을 리소스로 분산시킬 수 있습니다. 트래픽의 부하를 분산시키는 리소스를 포함하도록 백 엔드 주소 풀 **myBackendPool**을 만듭니다.

1. 맨 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.
1. **모든 리소스**를 선택한 다음, 리소스 목록에서 **myLoadBalancer**를 선택합니다.
1. **설정**에서 **백 엔드 풀**을 선택한 다음, **추가**를 선택합니다.
1. **백 엔드 풀 추가** 페이지에서 백 엔드 풀 이름에 **myBackendPool**을 입력한 후 **추가**를 선택합니다.

#### <a name="create-a-health-probe"></a>상태 프로브 만들기

부하 분산 장치에서 리소스 상태를 모니터링할 수 있도록 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 대한 리소스 응답을 기반으로 부하 분산 장치 순환에서 리소스를 동적으로 추가하거나 제거합니다.

리소스 상태를 모니터링하는 상태 프로브를 만들려면:

1. 맨 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **myLoadBalancer**를 선택합니다.

1. **설정**에서 **상태 프로브**를 선택한 다음, **추가**를 선택합니다.

1. **상태 프로브 추가** 페이지에서 다음 값을 입력하거나 선택합니다.

   - **Name**: **myHealthProbe**를 입력합니다.
   - **프로토콜**: **TCP**를 선택합니다.
   - **포트**: **80**을 입력합니다.
   - **간격**: **15**를 입력합니다. 이 값은 프로브 시도 사이의 시간(초)입니다.
   - **비정상 임계값**: **2**를 입력합니다. 이 값은 가상 머신이 비정상 상태로 간주되는 데 필요한 연속 프로브 오류 횟수입니다.

1. **확인**을 선택합니다.

#### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 트래픽이 리소스에 분산되는 방식을 정의합니다. 이 규칙은 다음을 정의합니다.

- 들어오는 트래픽에 대한 프런트 엔드 IP 구성
- 트래픽을 수신할 백 엔드 IP 풀
- 필요한 원본 및 대상 포트

**myLoadBalancerRule**이라는 부하 분산 장치 규칙은 **LoadBalancerFrontEnd** 프런트 엔드의 포트 80에서 수신 대기합니다. 이 규칙은 포트 80에서 네트워크 트래픽을 백 엔드 주소 풀 **myBackendPool**로 보냅니다.

부하 분산 장치 규칙을 만들려면 다음을 수행합니다.

1. 맨 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **myLoadBalancer**를 선택합니다.

1. **설정** 아래에서 **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.

1. **부하 분산 규칙 추가** 페이지에서 다음 값을 입력하거나 선택합니다(아직 없는 경우).

   - **Name**: **myLoadBalancerRule**을 입력합니다.
   - **프런트 엔드 IP 주소:** **LoadBalancerFrontEnd**를 입력합니다.
   - **프로토콜**: **TCP**를 선택합니다.
   - **포트**: **80**을 입력합니다.
   - **백 엔드 포트**: **80**을 입력합니다.
   - **백 엔드 풀**: **myBackendPool**을 선택합니다.
   - **상태 프로브**: **myHealthProbe**를 선택합니다. 

1. **확인**을 선택합니다.

## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기

이 섹션에서는 표준 부하 분산 장치 뒤에 Private Link 서비스를 만듭니다.

1. Azure Portal 페이지의 왼쪽 위 부분에서 **리소스 만들기** > **네트워킹** > **Private Link 센터(미리 보기)** 를 선택합니다. 포털의 검색 상자를 사용하여 Private Link를 검색할 수도 있습니다.

1. **Private Link 센터 - 개요** > **다른 사용자가 연결할 수 있도록 자신의 서비스 노출**에서 **시작**을 선택합니다.

1. **Private Link 서비스 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정           | 값                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | 프로젝트 세부 정보:  |                                                                              |
    | **구독**      | 구독을 선택합니다.                                                     |
    | **리소스 그룹**    | **myResourceGroupLB**를 선택합니다.                                                    |
    | 인스턴스 세부 정보: |                                                                              |
    | **이름**              | **myPrivateLinkService**를 입력합니다. |
    | **지역**            | **미국 동부 2**를 선택합니다.                                                        |

1. 완료되면 **다음: 아웃바운드 설정**을 선택합니다.

1. **Private Link 서비스 만들기 - 아웃바운드 설정**에서 다음 정보를 입력하거나 선택합니다.

    | 설정                           | 값                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | **myLoadBalancer**를 선택합니다.                                                           |
    | **Load Balancer 프런트 엔드 IP 주소** | **myLoadBalancer**의 프런트 엔드 IP 주소를 선택합니다.                                |
    | **원본 NAT 가상 네트워크**        | **myVNet**을 선택합니다.                                                                   |
    | **원본 NAT 서브넷**                 | **myBackendSubnet**을 선택합니다.                                                          |
    | **TCP 프록시 V2 사용**               | 애플리케이션에 TCP 프록시 v2 헤더가 필요한지 여부에 따라 **예** 또는 **아니요**를 선택합니다. |
    | **개인 IP 주소 설정**       | 각 NAT IP에 대한 할당 방법 및 IP 주소를 구성합니다.                  |

1. 완료되면 **다음: 액세스 보안**을 선택합니다.

1. **Private Link 서비스 만들기 - 액세스 보안**에서 **표시 유형**을 선택한 다음, **역할 기반 액세스 제어 전용**을 선택합니다.
  
1. **다음: 태그** > **검토 + 만들기**를 선택하거나 페이지 맨 위에서 **검토 + 만들기** 탭을 선택합니다.

1. 정보를 검토하고 **만들기**를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

Private Link 서비스 사용을 마쳤으면 리소스 그룹을 삭제하여 이 빠른 시작에 사용된 리소스를 정리합니다.

1. 포털 맨 위에 있는 검색 상자에 **myResourceGroupLB**를 입력하고 검색 결과에서 **myResourceGroupLB**를 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다.
1. **리소스 그룹 이름 입력**에서 **myResourceGroup**을 입력합니다.
1. **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 내부 Azure 부하 분산 장치와 Private Link 서비스를 만들었습니다. 또한 [Azure Portal을 사용하여 프라이빗 엔드포인트를 만드는 방법](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)을 알아볼 수도 있습니다.
