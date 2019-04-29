---
title: IPv6을 사용하여 인터넷 연결 부하 분산 장치 배포 - Azure 템플릿
titlesuffix: Azure Load Balancer
description: Azure Load Balancer 및 부하 분산된 VM에 대한 IPv6 지원을 배포하는 방법.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: ipv6, Azure Load Balancer, 이중 스택, 공용 IP, 기본 ipv6, 모바일, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4a8c7309a07238ef3410e42c3d631ad525f023cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216855"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>템플릿을 사용하여 IPv6로 인터넷 연결 부하 분산 장치 솔루션을 배포합니다.

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [템플릿](load-balancer-ipv6-internet-template.md)



Azure 부하 분산 장치는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치는 부하 분산 장치 집합에 있는 클라우드 서비스 또는 가상 머신의 정상 서비스 인스턴스 간에 들어오는 트래픽을 배포하여 고가용성을 제공합니다. Azure Load Balancer는 여러 포트, 여러 IP 주소 또는 둘 다에서 이러한 서비스를 제공할 수도 있습니다.

## <a name="example-deployment-scenario"></a>예제 배포 시나리오

다음 다이어그램은 이 문서에서 설명된 예제 템플릿을 사용하여 배포된 부하 분산 솔루션을 보여 줍니다.

![부하 분산 장치 시나리오](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

이 시나리오에서는 다음과 같은 Azure 리소스를 만듭니다.

* 할당된 IPv4 및 IPv6 주소를 사용하는 각 VM에 대한 가상 네트워크 인터페이스
* IPv4 및 IPv6 공용 IP 주소를 가진 인터넷 연결 부하 분산 장치
* 공용 VIP를 개인 엔드포인트로 매핑하기 위한 두 개의 부하 분산 규칙
* 두 개의 VM이 들어 있는 가용성 집합
* 2개의 가상 머신(VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Azure Portal을 사용하여 템플릿 배포

이 문서에서는 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) 갤러리에 게시된 템플릿을 참조합니다. 갤러리에서 템플릿을 다운로드하거나 갤러리에서 직접 Azure에 배포를 시작할 수 있습니다. 이 문서에서는 템플릿을 로컬 컴퓨터에 다운로드했다고 가정합니다.

1. Azure Portal을 열고 Azure 구독 내에서 VM 및 네트워킹 리소스를 만들 권한이 있는 계정으로 로그인합니다. 또한 기존 리소스를 사용하고 있는 것이 아니라면 리소스 그룹 및 저장소 계정을 만들기 위해서는 계정에 권한이 필요합니다.
2. 메뉴에서 "+새"를 클릭한 다음 검색 상자에 "템플릿"을 입력합니다. 검색 결과에서 "템플릿 배포"를 선택합니다.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. 모두 블레이드에서 "템플릿 배포"를 클릭합니다.

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. "만들기"를 클릭합니다.

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. "템플릿 편집"을 클릭합니다. (시작 및 종료{ }를 포함하기 위해) 기존 내용을 삭제하고 템플릿 파일의 전체 내용을 복사/붙여넣기한 다음 "저장"을 클릭합니다.

    > [!NOTE]
    > Microsoft Internet Explorer를 사용한다면 붙여넣기할 때 Windows 클립보드에 대한 액세스를 허용하도록 요청하는 대화 상자가 나타납니다. "액세스 허용"을 클릭합니다.

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. "매개 변수 편집"을 클릭합니다. 매개 변수 블레이드의 템플릿 매개 변수 섹션에는 지침당 값을 지정하고 "저장"을 클릭하여 매개 변수 블레이드를 닫습니다. 사용자 지정 배포 블레이드에서 구독이나 기존 리소스 그룹을 선택하거나 또는 새로 만듭니다. 리소스 그룹을 만드는 경우 리소스 그룹에 대한 위치를 선택합니다. 다음으로, **약관**을 클릭한 다음 그 약관에 대한 **구매**를 클릭합니다. Azure가 리소스를 배포하기 시작합니다. 모든 리소스를 배포하는 데 몇 분 정도 걸립니다.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    이러한 매개 변수에 대한 자세한 내용은 이 문서의 뒷부분에 나오는 [템플릿 매개 변수 및 변수](#template-parameters-and-variables) 섹션을 참조하세요.

7. 템플릿으로 만든 리소스를 보려면 찾아보기를 클릭하고 "리소스 그룹"이 보일 때까지 목록을 아래로 스크롤한 후 그것을 클릭합니다.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. 리소스 그룹 블레이드에서 6단계 때 지정한 리소스 그룹의 이름을 클릭합니다. 배포된 모든 리소스 목록이 표시됩니다. 모든 것이 순조롭다면 "마지막 배포" 아래에 "Succeeded"이라고 표시되어야 합니다. 그렇지 않다면 사용 중인 계정에 필요한 리소스를 만들 권한이 있는지 확인합니다.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > 6단계를 마친 후 즉시 리소스 그룹을 찾아보기할 경우 "마지막 배포"는 리소스가 배포되는 동안 "배포" 상태를 표시합니다.

9. 리소스 목록에서 "myIPv6PublicIP"을 클릭합니다. IP 주소 아래에 IPv6 주소가 있으며 DNS 이름이 6단계에서 dnsNameforIPv6LbIP 매개 변수에 대해 지정한 값인지 표시합니다. 이 리소스는 인터넷 클라이언트에 액세스할 수 있는 공용 IPv6 주소 및 호스트 이름입니다.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>연결 유효성 검사

템플릿이 성공적으로 배포했다면 다음 작업을 완료하여 연결 유효성 검사를 할 수 있습니다.

1. Azure Portal에 로그인하여 템플릿 배포를 통해 만들어진 각 VM에 연결합니다. Windows Server VM을 배포한 경우 명령 프롬프트에서 ipconfig /all을 실행합니다. VM에 IPv4 및 IPv6 주소가 모두 표시됩니다. Linux VM을 배포한 경우, Linux 배포를 위해 제공된 지침을 사용하여 동적 IPv6 주소를 수신하도록 Linux OS를 구성해야 합니다.
2. IPv6 인터넷에 연결된 클라이언트에서 부하 분산 장치의 공용 IPv6 주소에 연결을 시작합니다. 부하 분산 장치가 두 VM 간에 적절히 분산하는지 확인하려면 각 VM에 Microsoft IIS(인터넷 정보 서비스)와 같은 웹 서버를 설치할 수 있습니다. 각 서버의 기본 웹 페이지는 고유하게 식별하기 위해 텍스트 "Server0" 또는 "Server1"을 포함할 수 있습니다. 그런 다음 IPv6 인터넷에 연결된 클라이언트에서 인터넷 브라우저를 열고 각 VM에 대한 종단간 IPv6 연결을 확인하기 위해 부하 분산 장치의 dnsNameforIPv6LbIP 매개 변수에 대해 지정한 호스트 이름으로 이동합니다. 단 하나의 서버 웹 페이지 만이 표시된다면 브라우저 캐시를 지워야 할 수도 있습니다. 여러 개인 찾아보기 세션을 엽니다. 각 서버에서의 응답이 표시됩니다.
3. IPv4 인터넷에 연결된 클라이언트에서 부하 분산 장치의 공용 IPv4 주소에 연결을 시작합니다. 부하 분산 장치가 VM 두 개를 부하 분산하고 있는지 확인하려면 2 단계에서 설명된 대로 IIS를 사용하여 테스트할 수 있습니다.
4. 각 VM에서 IPv6 또는 IPv4에 연결된 인터넷 디바이스에 아웃 바운드 연결을 시작합니다. 두 경우 모두 대상 디바이스에 의해 표시된 원본 IP는 부하 분산 디바이스의 공용 IPv4 또는 IPv6 주소입니다.

> [!NOTE]
> IPv4 및 IPv6 모두에 대한 ICMP은 Azure 네트워크에서 차단됩니다. 따라서 ping과 같은 ICMP 도구는 언제나 작동하지 않습니다. 연결을 테스트하려면 TCPing 또는 PowerShell Test-NetConnection cmdlet와 같은 대체 TCP를 사용합니다. 다이어그램에 표시된 IP 주소는 나타날 수도 있는 값의 예제임에 유의하세요. IPv6 주소는 동적으로 할당되므로 받게 될 주소는 다르며 지역에 따라 달라질 수 있습니다. 또한 부하 분산 장치에 있는 공용 IPv6 주소가 백 엔드 풀에 있는 개인 IPv6 주소와는 다른 접두사로 시작하는 것은 일반적입니다.

## <a name="template-parameters-and-variables"></a>템플릿 매개 변수 및 변수

Azure Resource Manager 템플릿은 요구에 맞게 사용자 지정할 수 있는 여러 변수 및 매개 변수를 포함합니다. 변수는 사용자에게 변경하도록 하지 않을 고정된 값에 대해 사용됩니다. 매개 변수는 사용자에게 템플릿을 배포할 때 제공하도록 할 값에 대해 사용합니다. 이 문서에 설명된 시나리오에 대한 예제 템플릿이 구성됩니다. 환경 요건을 사용자에 맞게 설정할 수 있습니다.

이 문서에서 사용된 예제 템플릿에는 다음 변수 및 매개 변수가 들어 있습니다.

| 매개 변수 / 변수 | 메모 |
| --- | --- |
| adminUsername |가상 머신에 로그인하는 데 사용되는 관리자 계정의 이름을 지정합니다. |
| adminPassword |가상 머신에 로그인하는 데 사용되는 관리자 계정의 비밀번호를 지정합니다. |
| dnsNameforIPv4LbIP |부하 분산 장치의 공개 이름으로 할당하려는 DNS 호스트 이름을 지정합니다. 이 이름은 부하 분산 장치의 공용 IPv4 주소로 바뀝니다. 이름은 소문자이어야 하며 다음 정규식과 일치해야 합니다. ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ |
| dnsNameforIPv6LbIP |부하 분산 장치의 공개 이름으로 할당하려는 DNS 호스트 이름을 지정합니다. 이 이름은 부하 분산 장치의 공용 IPv6 주소로 바뀝니다. 이름은 소문자이어야 하며 다음 정규식과 일치해야 합니다. ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ 이 이름은 IPv4 주소와 같은 이름일 수 있습니다. 클라이언트가 이 이름에 대한 DNS 쿼리를 보낼 때 Azure는 이름이 공유된 경우 A 및 AAAA 기록 모두를 반환합니다. |
| vmNamePrefix |VM 이름 접두사를 지정합니다. VM이 만들어질 때 템플릿은 이름에 번호(0, 1, 등)를 추가합니다. |
| nicNamePrefix |네트워크 인터페이스 이름 접두사를 지정합니다. 네트워크 인터페이스가 만들어질 때 템플릿은 이름에 번호(0, 1, 등)를 추가합니다. |
| storageAccountName |기존 저장소 계정의 이름을 입력하거나 템플릿으로 만든 새 이름을 지정합니다. |
| availabilitySetName |그런 다음 VM에 사용하도록 설정할 가용성 집합의 이름을 입력합니다. |
| addressPrefix |Virtual Network의 주소 범위를 정의하는 데 사용되는 주소 접두사 |
| subnetName |VNet에 대해 생성되는 서브넷의 이름 |
| subnetPrefix |서브넷의 주소 범위를 정의하는 데 사용되는 주소 접두사 |
| vnetName |VM에서 사용하는 VNet에 대한 이름을 지정합니다. |
| ipv4PrivateIPAddressType |개인 IP 주소(정적 또는 동적)에 대해 사용되는 할당 방법. |
| ipv6PrivateIPAddressType |개인 IP 주소(동적)에 대해 사용되는 할당 방법. IPv6 만이 동적 할당을 지원합니다. |
| numberOfInstances |템플릿을 통해 배포한 부하 분산 인스턴스의 수 |
| ipv4PublicIPAddressName |부하 분산 장치의 공용 IPv4 주소와 통신하는 데 사용할 DNS 이름을 지정합니다. |
| ipv4PublicIPAddressType |공용 IP 주소(정적 또는 동적)에 대해 사용되는 할당 방법. |
| Ipv6PublicIPAddressName |부하 분산 장치의 공용 IPv6 주소와 통신하는 데 사용할 DNS 이름을 지정합니다. |
| ipv6PublicIPAddressType |공용 IP 주소(동적)에 대해 사용되는 할당 방법입니다. IPv6 만이 동적 할당을 지원합니다. |
| lbName |부하 분산 장치의 이름을 지정합니다. 이 이름은 포털에 표시되거나 CLI 또는 PowerShell 명령을 사용하여 참조할 때 사용됩니다. |

템플릿의 나머지 변수는 Azure가 리소스를 만들 때 할당되는 파생 값을 포함합니다. 이러한 변수를 변경하지 마십시오.

## <a name="next-steps"></a>다음 단계

템플릿에서 부하 분산 장치의 JSON 구문 및 속성은 [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)를 참조하세요.
