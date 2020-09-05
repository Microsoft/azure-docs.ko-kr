---
title: Azure 방화벽을 사용 하 여 개인 끝점으로 향하는 트래픽을 검사 합니다.
titleSuffix: Azure Private Link
description: Azure 방화벽을 사용 하 여 개인 끝점으로 향하는 트래픽을 검사할 수 있는 방법을 알아봅니다.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 734d52dadbb849925303febb0d3d1195bbddb0df
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236688"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Azure 방화벽을 사용 하 여 개인 끝점으로 향하는 트래픽을 검사 합니다.

Azure 개인 끝점은 Azure 개인 링크에 대 한 기본 빌딩 블록입니다. 개인 끝점을 사용 하면 가상 네트워크에 배포 된 Azure 리소스가 개인 링크 리소스와 개인적으로 통신할 수 있습니다.

개인 끝점은 가상 네트워크에 배포 된 개인 링크 서비스에 대 한 리소스 액세스를 허용 합니다. 가상 네트워크 피어 링 및 온-프레미스 네트워크 연결을 통해 개인 끝점에 액세스 하면 연결이 확장 됩니다.

클라이언트에서 개인 끝점을 통해 노출 되는 서비스로 트래픽을 검사 하거나 차단 해야 할 수 있습니다. [Azure 방화벽](../firewall/overview.md) 또는 타사 네트워크 가상 어플라이언스를 사용 하 여이 검사를 완료 합니다.

다음과 같은 제한 사항이 적용됩니다.

* NSGs (네트워크 보안 그룹)는 개인 끝점에 적용 되지 않습니다.
* UDR (사용자 정의 경로)은 전용 끝점에 적용 되지 않습니다.
* 단일 경로 테이블을 서브넷에 연결할 수 있습니다.
* 경로 테이블은 최대 400 경로를 지원 합니다.

Azure 방화벽은 다음 중 하나를 사용 하 여 트래픽을 필터링 합니다.

* TCP 및 UDP 프로토콜에 대 한 [네트워크 규칙의 FQDN](../firewall/fqdn-filtering-network-rules.md)
* HTTP, HTTPS 및 MSSQL의 [응용 프로그램 규칙에서 FQDN](../firewall/features.md#application-fqdn-filtering-rules) . 

개인 끝점을 통해 노출 되는 대부분의 서비스는 HTTPS를 사용 합니다. Azure SQL을 사용 하는 경우 네트워크 규칙에 대 한 응용 프로그램 규칙을 사용 하는 것이 좋습니다.

> [!NOTE]
> SQL FQDN 필터링은 [프록시 모드](../azure-sql/database/connectivity-architecture.md#connection-policy)에서만 지원됩니다(포트 1433). **프록시** 모드를 사용할 경우 *리디렉션*에 비해 대기 시간이 길어질 수 있습니다. Azure 내에서 연결 하는 클라이언트에 대 한 기본값 인 리디렉션 모드를 계속 사용 하려는 경우 방화벽 네트워크 규칙에서 FQDN을 사용 하 여 액세스를 필터링 할 수 있습니다.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>시나리오 1: 허브 및 스포크 아키텍처-전용 가상 네트워크 전용 끝점

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="전용 끝점에 대 한 전용 Virtual Network" border="true":::

이 시나리오는 개인 끝점을 사용 하 여 여러 Azure 서비스에 개인적으로 연결 하는 가장 확장성이 뛰어난 아키텍처입니다. 개인 끝점이 배포 된 네트워크 주소 공간을 가리키는 경로가 만들어집니다. 이 구성은 관리 오버 헤드를 줄이고 400 경로 제한을 초과 하는 것을 방지 합니다.

클라이언트 가상 네트워크에서 허브 가상 네트워크의 Azure 방화벽으로 연결 하면 가상 네트워크가 피어 링 때 요금이 발생 합니다.

피어 링 가상 네트워크와의 연결과 관련 된 요금에 대 한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/private-link/) 페이지의 FAQ 섹션을 참조 하세요.

>[!NOTE]
> 이 시나리오는 응용 프로그램 규칙 대신 타사 NVA 또는 Azure 방화벽 네트워크 규칙을 사용 하 여 구현할 수 있습니다.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>시나리오 2: 허브 및 스포크 아키텍처-개인 끝점 및 가상 컴퓨터에 대 한 공유 가상 네트워크

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="동일한 Virtual Network의 전용 끝점 및 Virtual Machines" border="true":::

이 시나리오는 다음과 같은 경우에 구현 됩니다.

* 전용 끝점에 대 한 전용 가상 네트워크를 사용할 수 없습니다.

* 개인 끝점을 사용 하 여 소수의 서비스만 가상 네트워크에 노출 되는 경우

가상 머신은 각 개인 끝점을 가리키는/32 시스템 경로를 가집니다. 개인 끝점 당 하나의 경로는 Azure 방화벽을 통해 트래픽을 라우팅하도록 구성 됩니다. 

서비스가 가상 네트워크에 노출 될 때 경로 테이블을 유지 관리 하는 관리 오버 헤드가 증가 합니다. 또한 경로 제한에 도달할 가능성이 높아집니다.

전반적인 아키텍처에 따라 400 경로 제한까지 실행할 수 있습니다. 가능 하면 항상 시나리오 1을 사용 하는 것이 좋습니다.

클라이언트 가상 네트워크에서 허브 가상 네트워크의 Azure 방화벽으로 연결 하면 가상 네트워크가 피어 링 때 요금이 발생 합니다.

피어 링 가상 네트워크와의 연결과 관련 된 요금에 대 한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/private-link/) 페이지의 FAQ 섹션을 참조 하세요.

>[!NOTE]
> 이 시나리오는 응용 프로그램 규칙 대신 타사 NVA 또는 Azure 방화벽 네트워크 규칙을 사용 하 여 구현할 수 있습니다.

## <a name="scenario-3-single-virtual-network"></a>시나리오 3: 단일 가상 네트워크

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="단일 가상 네트워크" border="true":::

구현에 대 한 몇 가지 제한 사항이 있습니다. 허브 및 스포크 아키텍처로의 마이그레이션은 가능 하지 않습니다. 시나리오 2에서와 동일한 고려 사항이 적용 됩니다. 이 시나리오에서는 가상 네트워크 피어 링 요금이 적용 되지 않습니다.

>[!NOTE]
> 타사 NVA 또는 Azure 방화벽을 사용 하 여이 시나리오를 구현 하려면 개인 끝점으로 향하는 트래픽을 SNAT 하기 위해 응용 프로그램 규칙이 아닌 네트워크 규칙이 필요 합니다. 그렇지 않으면 가상 컴퓨터와 개인 끝점 간의 통신에 실패 합니다.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>시나리오 4: 전용 끝점에 대 한 온-프레미스 트래픽

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="전용 끝점에 대 한 온-프레미스 트래픽" border="true":::

다음 중 하나를 사용 하 여 온-프레미스 네트워크와의 연결을 구성한 경우이 아키텍처를 구현할 수 있습니다. 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [사이트 간 VPN](..\vpn-gateway\vpn-gateway-howto-site-to-site-resource-manager-portal.md) 

보안 요구 사항에 따라 보안 어플라이언스를 통해 라우팅할 개인 끝점을 통해 노출 되는 서비스에 대 한 클라이언트 트래픽이 필요한 경우이 시나리오를 배포 합니다.

위의 시나리오 2와 동일한 고려 사항이 적용 됩니다. 이 시나리오에서는 가상 네트워크 피어 링 요금이 발생 하지 않습니다. 온-프레미스 워크 로드가 개인 끝점에 액세스할 수 있도록 DNS 서버를 구성 하는 방법에 대 한 자세한 내용은 [dns 전달자를 사용 하는 온-프레미스 워크 로드](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)를 참조 하세요.

>[!NOTE]
> 타사 NVA 또는 Azure 방화벽을 사용 하 여이 시나리오를 구현 하려면 개인 끝점으로 향하는 트래픽을 SNAT 하기 위해 응용 프로그램 규칙이 아닌 네트워크 규칙이 필요 합니다. 그렇지 않으면 가상 컴퓨터와 개인 끝점 간의 통신에 실패 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독
* Log Analytics 작업 영역.  

구독에 작업 영역이 없는 경우 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 를 참조 하세요.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-vm"></a>VM 만들기

이 섹션에서는 개인 링크 리소스에 액세스 하는 데 사용 되는 VM을 호스트 하는 가상 네트워크 및 서브넷을 만듭니다. Azure SQL database는 예제 서비스로 사용 됩니다.

## <a name="virtual-networks-and-parameters"></a>가상 네트워크 및 매개 변수

다음에 대 한 세 개의 가상 네트워크 및 해당 서브넷을 만듭니다.

* VM과 개인 끝점 간의 통신을 제한 하는 데 사용 되는 Azure 방화벽을 포함 합니다.
* 개인 링크 리소스에 액세스 하는 데 사용 되는 VM을 호스팅합니다.
* 개인 끝점을 호스팅합니다.

단계에서 다음 매개 변수를 아래 정보로 바꿉니다.

### <a name="azure-firewall-network"></a>Azure 방화벽 네트워크
| 매개 변수                   | 값                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | 미국 중남부      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>가상 머신 네트워크
| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | 미국 중남부      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>개인 끝점 네트워크
| 매개 변수                   | 값                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | 미국 중남부      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. 1 ~ 9 단계를 반복 하 여 가상 머신 및 개인 끝점 리소스 호스팅을 위한 가상 네트워크를 만듭니다.

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기**  >  **계산**  >  **가상 머신**를 선택 합니다.

2. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 이 리소스 그룹을 만들었습니다.  |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **Myvm**을 입력 합니다. |
    | 지역 | **(US) 남부 중부 US**를 선택 합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | **Ubuntu Server 18.04 LTS-Gen1**을 선택 합니다. |
    | Size | **Standard_B2s**를 선택 합니다. |
    | **관리자 계정** |  |
    | 인증 유형 | **암호**를 선택합니다. |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | **없음**을 선택합니다. |
    |||

3. 완료되면 **다음: 디스크**를 선택합니다.

4. **가상 머신 만들기 - 디스크**에서 기본값을 그대로 두고 **다음: 네트워킹**을 선택합니다.

5. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | **Myvmvnet**을 선택 합니다.  |
    | 서브넷 | **VMSubnet (10.1.0.0/24)** 을 선택 합니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip**를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | **SSH**를 선택 합니다.|
    ||

6. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

7. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

## <a name="deploy-the-firewall"></a>방화벽 배포

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.

2. 검색 상자에 **방화벽**을 입력하고 **Enter** 키를 누릅니다.

3. **방화벽**을 선택하고 **만들기**를 선택합니다.

4. **방화벽 만들기** 페이지에서 다음 표를 사용하여 방화벽을 구성합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다.  |
    | **인스턴스 세부 정보** |  |
    | 속성 | **Myazurefirewall**을 입력 합니다. |
    | 지역 | **남부 중부 US**를 선택 합니다. |
    | 가용성 영역 | 기본값인 **없음**을 그대로 둡니다. |
    | 가상 네트워크 선택    |    **기존 사용**을 선택 합니다.    |
    | 가상 네트워크    |    **MyAzFwVNet**를 선택 합니다.    |
    | 공용 IP 주소    |    **새로 추가** 를 선택 하 고 이름에서 **myfirewall-ip**를 입력 합니다.    |
    | 강제 터널링    | 기본값은 **사용 안 함**상태로 둡니다.    |
    |||
5. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

6. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

## <a name="enable-firewall-logs"></a>방화벽 로그 사용

이 섹션에서는 방화벽에서 로그를 사용 하도록 설정 합니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택 합니다.

2. 리소스 목록에서 방화벽 **Myazurefirewall** 을 선택 합니다.

3. 방화벽 설정의 **모니터링** 아래에서 **진단 설정** 을 선택 합니다.

4. 진단 설정에서 **+ 진단 설정 추가** 를 선택 합니다.

5. **진단 설정**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 진단 설정 이름 | **MyDiagSetting**를 입력 합니다. |
    | 범주 세부 정보 | |
    | log | **AzureFirewallApplicationRule** 및 **AzureFirewallNetworkRule**를 선택 합니다. |
    | 대상 세부 정보 | **Log Analytics에 보내기**를 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | Log Analytics 작업 영역 | Log Analytics 작업 영역을 선택합니다. |

6. **저장**을 선택합니다.

## <a name="create-azure-sql-database"></a>Azure SQL 데이터베이스 만들기

이 섹션에서는 개인 SQL Database를 만듭니다.

1. Azure Portal 화면의 왼쪽 위에서 SQL Database **리소스 데이터베이스 만들기**를 선택  >  **Databases**  >  **SQL Database**합니다.

2. **SQL Database 만들기-기본 사항**에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. 이전 섹션에서 이 리소스 그룹을 만들었습니다.|
    | **데이터베이스 세부 정보** |  |
    | 데이터베이스 이름  | **mydatabase**를 입력합니다.  |
    | 서버 | **새로 만들기** 를 선택 하 고 아래 정보를 입력 합니다.    |
    | 서버 이름 | **Mydbserver**를 입력 합니다. 이 이름을 사용 하는 경우 고유한 이름을 입력 합니다.   |
    | 서버 관리자 로그인 | 선택한 이름을 입력 합니다. |
    | 암호    |    선택한 암호를 입력합니다.    |
    | 암호 확인 | 암호를 다시 입력합니다.    |
    | 위치    | **(US) 남부 중부 US**를 선택 합니다.    |
    | SQL 탄력적 풀을 사용 하려고 합니다.    | 기본값인 **아니요**를 그대로 둡니다. |
    | 컴퓨팅 + 스토리지 | 기본 범용 **Gen5 2 개 vcores, 32 GB 저장소**를 그대로 둡니다. |
    |||

3. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

4. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

## <a name="create-private-endpoint"></a>개인 끝점 만들기

이 섹션에서는 이전 섹션에서 Azure SQL database에 대 한 개인 끝점을 만듭니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택 합니다.

2. 서비스 목록에서 Azure SQL server **mydbserver** 를 선택 합니다.  다른 서버 이름을 사용한 경우 해당 이름을 선택 합니다.

3. 서버 설정에서 **보안**아래의 **개인 끝점 연결** 을 선택 합니다.

4. **+ 프라이빗 엔드포인트**를 선택합니다.

5. **개인 끝점 만들기**에서 **기본 사항** 탭에 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다. |
    | **인스턴스 세부 정보** | |
    | 속성 | **SQLPrivateEndpoint**를 입력 합니다. |
    | 지역 | **(US) 남부 중부 US를 선택 합니다.** |

6. **리소스** 탭을 선택 하거나 페이지 맨 아래에 있는 **다음: 리소스** 를 선택 합니다.

7. **리소스** 탭에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 연결 방법 | **내 디렉터리의 Azure 리소스에 연결**을 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.Sql/servers**를 선택합니다. |
    | 리소스 | **Mydbserver** 또는 이전 단계에서 만든 서버의 이름을 선택 합니다.
    | 대상 하위 리소스 | **SqlServer**를 선택 합니다. |

8. **구성** 탭을 선택 하거나 페이지 맨 아래에 있는 **다음: 구성** 을 선택 합니다.

9. **구성** 탭에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워킹** | |
    | 가상 네트워크 | **MyPEVnet**를 선택 합니다. |
    | 서브넷 | **PrivateEndpointSubnet**를 선택 합니다. |
    | **사설 DNS 통합** | |
    | 프라이빗 DNS 영역과 통합 | **예**를 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | 사설 DNS 영역 | 기본 **privatelink.database.windows.net**을 그대로 둡니다. |

10. **검토 + 만들기** 탭을 선택 하거나 페이지 맨 아래에 있는 **검토 + 만들기** 를 선택 합니다.

11. **만들기**를 선택합니다.

12. 끝점을 만든 후에는 **보안**에서 **방화벽 및 가상 네트워크** 를 선택 합니다.

13. **방화벽 및 가상 네트워크**에서 **Azure 서비스 및 리소스에서이 서버에 액세스할 수 있도록 허용**옆에서 **예** 를 선택 합니다.

14. **저장**을 선택합니다.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>가상 네트워크 피어 링을 사용 하 여 가상 네트워크 연결

이 섹션에서는 피어 링을 사용 하 여 가상 네트워크 **Myvmvnet** 및 **myPEVNet** 을 **myAzFwVNet** 에 연결 합니다. **Myvmvnet** 과 **myPEVNet**간에 직접 연결 되지 않습니다.

1. 포털의 검색 창에서 **myAzFwVNet**을 입력 합니다.

2. **설정** 메뉴에서 **피어 링** 을 선택 하 고 **+ 추가**를 선택 합니다.

3. **피어 링 추가** 에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | MyAzFwVNet에서 원격 가상 네트워크로의 피어 링 이름 | **MyAzFwVNet-myVMVNet을**입력 합니다. |
    | **피어 세부 정보** |  |
    | 가상 네트워크 배포 모델  | 기본 **리소스 관리자**를 그대로 둡니다.  |
    | 내 리소스 ID를 알고 있습니다. | 확인되지 않은 상태로 둡니다.    |
    | Subscription | 구독을 선택합니다.    |
    | 가상 네트워크 | **Myvmvnet**을 선택 합니다. |
    | 원격 가상 네트워크에서 myAzFwVNet로의 피어 링 이름    |    **Myvmvnet-myAzFwVNet을**입력 합니다.    |
    | **Configuration** | |
    | **가상 네트워크 액세스 설정 구성** | |
    | MyAzFwVNet에서 원격 가상 네트워크에 대 한 가상 네트워크 액세스 허용 | 기본값인 **사용**을 그대로 둡니다.    |
    | 원격 가상 네트워크에서 myAzFwVNet에 대 한 가상 네트워크 액세스 허용    | 기본값인 **사용**을 그대로 둡니다.    |
    | **전달 된 트래픽 설정 구성** | |
    | 원격 가상 네트워크에서 myAzFwVNet로 전달 된 트래픽 허용    | **사용**을 선택합니다. |
    | MyAzFwVNet에서 원격 가상 네트워크로 전달 된 트래픽 허용 | **사용**을 선택합니다. |
    | **게이트웨이 전송 설정 구성** | |
    | 게이트웨이 전송 허용 | 선택 하지 않은 상태로 유지 |
    |||

4. **확인**을 선택합니다.

5. **+추가**를 선택합니다.

6. **피어 링 추가** 에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | MyAzFwVNet에서 원격 가상 네트워크로의 피어 링 이름 | **MyAzFwVNet-myPEVNet를**입력 합니다. |
    | **피어 세부 정보** |  |
    | 가상 네트워크 배포 모델  | 기본 **리소스 관리자**를 그대로 둡니다.  |
    | 내 리소스 ID를 알고 있습니다. | 확인되지 않은 상태로 둡니다.    |
    | Subscription | 구독을 선택합니다.    |
    | 가상 네트워크 | **MyPEVNet**를 선택 합니다. |
    | 원격 가상 네트워크에서 myAzFwVNet로의 피어 링 이름    |    **MyPEVNet-myAzFwVNet를**입력 합니다.    |
    | **Configuration** | |
    | **가상 네트워크 액세스 설정 구성** | |
    | MyAzFwVNet에서 원격 가상 네트워크에 대 한 가상 네트워크 액세스 허용 | 기본값인 **사용**을 그대로 둡니다.    |
    | 원격 가상 네트워크에서 myAzFwVNet에 대 한 가상 네트워크 액세스 허용    | 기본값인 **사용**을 그대로 둡니다.    |
    | **전달 된 트래픽 설정 구성** | |
    | 원격 가상 네트워크에서 myAzFwVNet로 전달 된 트래픽 허용    | **사용**을 선택합니다. |
    | MyAzFwVNet에서 원격 가상 네트워크로 전달 된 트래픽 허용 | **사용**을 선택합니다. |
    | **게이트웨이 전송 설정 구성** | |
    | 게이트웨이 전송 허용 | 선택 하지 않은 상태로 유지 |

7. **확인**을 선택합니다.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>개인 DNS 영역에 가상 네트워크 연결

이 섹션에서는 가상 네트워크 **Myvmvnet** 및 **myAzFwVNet** 을 **privatelink.database.windows.net** 개인 DNS 영역에 연결 합니다. 이 영역은 개인 끝점을 만들 때 생성 되었습니다. 

이 링크는 VM 및 방화벽이 데이터베이스의 FQDN을 개인 끝점 주소로 확인 하는 데 필요 합니다. 개인 끝점을 만들 때 가상 네트워크 **myPEVNet** 가 자동으로 연결 되었습니다.

>[!NOTE]
>VM 및 방화벽 가상 네트워크를 개인 DNS 영역에 연결 하지 않는 경우 VM과 방화벽은 모두 SQL Server FQDN을 확인할 수 있습니다. 공용 IP 주소로 확인 됩니다.

1. 포털의 검색 창에서 **privatelink**을 입력 합니다.

2. 검색 결과에서 **privatelink.database.windows.net** 를 선택 합니다.

3. **설정**아래에서 **가상 네트워크 링크** 를 선택 합니다.

4. **+ 추가**를 선택합니다.

5. **가상 네트워크 추가 링크** 에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 링크 이름 | **링크-myVMVNet을**입력 합니다. |
    | **Virtual network 세부 정보** |  |
    | 가상 네트워크의 리소스 ID를 알고 있습니다.  | 확인되지 않은 상태로 둡니다.  |
    | Subscription | 구독을 선택합니다.    |
    | 가상 네트워크 | **Myvmvnet**을 선택 합니다. |
    | **CONFIGURATION** | |
    | 자동 등록 사용 | 확인되지 않은 상태로 둡니다.    |


6. **확인**을 선택합니다.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Azure 방화벽에서 SQL FQDN을 사용 하 여 응용 프로그램 규칙 구성

이 섹션에서는 **Myvm** 과 SQL Server **mydbserver.database.windows.net**개인 끝점 간의 통신을 허용 하도록 응용 프로그램 규칙을 구성 합니다. 

이 규칙은 이전 단계에서 만든 방화벽을 통해 통신할 수 있도록 합니다.

1. 포털의 검색 창에서 **Myazurefirewall**을 입력 합니다.

2. 검색 결과에서 **Myazurefirewall** 을 선택 합니다.

3. **Myazurefirewall** 개요의 **설정** 에서 **규칙** 을 선택 합니다.

4. **애플리케이션 규칙 컬렉션** 탭을 선택합니다.

5. **+ 응용 프로그램 규칙 컬렉션 추가**를 선택 합니다.

6. **응용 프로그램 규칙 컬렉션 추가** 에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | **SQLPrivateEndpoint**를 입력 합니다. |
    | 우선 순위 | **100**을 입력합니다. |
    | 작업 | **허용**을 입력 합니다. |
    | **규칙.** |  |
    | **FQDN 태그** | |
    | 이름  | 비워 둡니다.  |
    | 소스 형식 | 기본 **IP 주소**를 그대로 둡니다.    |
    | 원본 | 비워 둡니다. |
    | FQDN 태그 | 기본값 0을 **선택**된 상태로 둡니다. |
    | **대상 Fqdn** | |
    | 이름 | **SQLPrivateEndpoint**를 입력 합니다.    |
    | 소스 형식 | 기본 **IP 주소**를 그대로 둡니다. |
    | 원본 | **10.1.0.0/16**을 입력 합니다. |
    | 프로토콜: 포트 | **Mssql: 1433**을 입력 합니다. |
    | 대상 Fqdn | **Mydbserver.database.windows.net**를 입력 합니다. |
    |||

7. **추가**를 선택합니다.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Azure 방화벽을 통해 가상 머신과 개인 끝점 간의 트래픽 라우팅

가상 네트워크 **Myvmvnet** 및 **myPEVNet**간에 가상 네트워크 피어 링을 직접 만들지 않았습니다. 가상 컴퓨터 **Myvm** 에는 만든 개인 끝점에 대 한 경로가 없습니다. 

이 섹션에서는 사용자 지정 경로를 사용 하 여 경로 테이블을 만듭니다. 

경로는 Azure 방화벽을 통해 **Myvm** 서브넷의 트래픽을 가상 네트워크 **myPEVNet**의 주소 공간으로 보냅니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기**를 선택합니다.

2. 검색 상자에 **경로 테이블** 을 입력 하 고 **enter**키를 누릅니다.

3. **경로 테이블** 을 선택 하 고 **만들기**를 선택 합니다.

4. **경로 테이블 만들기** 페이지에서 다음 테이블을 사용 하 여 경로 테이블을 구성 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup**을 선택합니다.  |
    | **인스턴스 세부 정보** |  |
    | 지역 | **남부 중부 US**를 선택 합니다. |
    | 이름 | **VMsubnet 대 AzureFirewall을**입력 합니다. |
    | 게이트웨이 경로 전파 | **아니오**를 선택합니다. |

5. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

6. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

7. 배포가 완료 되 면 **리소스로 이동을**선택 합니다.

8. **설정**에서 **경로** 를 선택 합니다.

9. **+추가**를 선택합니다.

10. **경로 추가** 페이지에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 경로 이름 | **Myvmsubnet-privateendpoint을**입력 합니다. |
    | 주소 접두사 | **10.2.0.0/16**을 입력 합니다.  |
    | 다음 홉 유형 | **가상 어플라이언스**를 선택합니다. |
    | 다음 홉 주소 | **10.0.0.4**를 입력 합니다. |

11. **확인**을 선택합니다.

12. **설정**에서 **서브넷** 을 선택 합니다.

13. **+ 연결**을 선택합니다.

14. **서브넷 연결** 페이지에서 다음 정보를 입력 하거나 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | **Myvmvnet**을 선택 합니다. |
    | 서브넷 | **VMSubnet**을 선택 합니다.  |

15. **확인**을 선택합니다.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>클라이언트 컴퓨터에서 가상 컴퓨터에 연결

다음과 같이 인터넷에서 **myVm** VM에 연결합니다.

1. 포털의 검색 창에서 **Myvm-ip**를 입력 합니다.

2. 검색 결과에서 **Myvm-ip** 를 선택 합니다.

3. **IP 주소**아래에서 값을 복사 하거나 적어 씁니다.

4. Windows 10을 사용 하는 경우 PowerShell을 사용 하 여 다음 명령을 실행 합니다. 다른 Windows 클라이언트 버전의 경우 [Putty](https://www.putty.org/)와 같은 SSH 클라이언트를 사용 합니다.

* **Username** 은 VM을 만들 때 입력 한 관리자 사용자 이름으로 바꿉니다.

* **IPaddress** 를 이전 단계의 IP 주소로 바꿉니다.

    ```bash
    ssh username@IPaddress
    ```

5. **Myvm** 을 만들 때 정의한 암호를 입력 합니다.

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>가상 머신에서 개인적으로 SQL Server 액세스

이 섹션에서는 개인 끝점을 사용 하 여 SQL Database에 비공개로 연결 합니다.

1. `nslookup mydbserver.database.windows.net`을 입력합니다.
    
    다음과 비슷한 메시지가 표시 됩니다.

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. [SQL Server 명령줄 도구](https://docs.microsoft.com/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15#tools)를 설치 합니다.

3. 다음 명령을 실행 하 여 SQL Server에 연결 합니다. 이전 단계에서 SQL Server를 만들 때 정의한 서버 관리자와 암호를 사용 합니다.

* **\<ServerAdmin>** 을 SQL server를 만드는 동안 입력 한 관리자 사용자 이름으로 대체 합니다.

* **\<YourPassword>** 을 SQL server를 만들 때 입력 한 관리자 암호로 바꿉니다.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. 로그인에 성공 하면 SQL 명령 프롬프트가 표시 됩니다. **종료** 를 입력 하 여 **sqlcmd** 도구를 종료 합니다.

5. **Exit**를 입력 하 여 **myvm** 에 대 한 연결을 닫습니다.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Azure 방화벽 로그에서 트래픽 유효성 검사

1. Azure Portal에서 **모든 리소스** 를 선택 하 고 Log Analytics 작업 영역을 선택 합니다.

2. Log Analytics 작업 영역 페이지에서 **일반** 아래에 있는 **로그** 를 선택 합니다.

3. 파란색 **시작** 단추를 선택 합니다.

4. **예제 쿼리** 창의 **모든 쿼리에서** **방화벽** 을 선택 합니다.

5. **응용 프로그램 규칙 로그 데이터**에서 **실행** 단추를 선택 합니다.

6. 로그 쿼리 출력에서 **mydbserver.database.windows.net** 이 **FQDN** 아래에 나열 되 고 **SQLPrivateEndpoint** 가 **RuleCollection**아래에 나열 되는지 확인 합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스를 사용 하 여 작업을 완료 하면 리소스 그룹 및 포함 된 모든 리소스를 삭제 합니다.

1. 포털 맨 위에 있는 **검색** 상자에 **myResourceGroup**을 입력하고 검색 결과에서 **myResourceGroup**을 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. **리소스 그룹 이름 입력**에 대해 **myResourceGroup**을 입력하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 방화벽을 사용 하 여 가상 머신과 개인 끝점 간의 트래픽을 제한 하는 데 사용할 수 있는 다양 한 시나리오를 살펴보았습니다. 

VM에 연결 하 고 개인 링크를 사용 하 여 Azure 방화벽을 통해 데이터베이스에 안전 하 게 전달 했습니다.

개인 끝점에 대 한 자세한 내용은 [Azure 개인 끝점 이란?](private-endpoint-overview.md)을 참조 하세요.
