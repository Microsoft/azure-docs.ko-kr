---
title: 여러 IP 구성의 부하 분산 - Azure Portal
titlesuffix: Azure Load Balancer
description: 기본 및 보조 IP 구성에서 부하 분산.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0cf5aa45e1e8a28dfcdadac0ea32658e5993d06c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591708"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Azure Portal을 사용하여 여러 IP 구성의 부하 분산

> [!div class="op_single_selector"]
> * [포털](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)


이 문서에서는 보조 NIC(네트워크 인터페이스 컨트롤러)에 여러 IP 주소가 있는 Azure Load Balancer를 사용하는 방법을 설명합니다. 아래 다이어그램은 시나리오를 보여 줍니다.

![부하 분산 장치 시나리오](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

시나리오에 다음 구성이 사용됩니다.

- Windows를 실행하는 두 개의 VM(가상 머신)
- 각 VM에는 주 NIC 및 보조 NIC가 있습니다.
- 각 보조 NIC에는 두 가지 IP 구성이 있습니다.
- 각 VM은 contoso.com 및 fabrikam.com이라는 두 웹 사이트를 호스트합니다.
- 각 웹 사이트는 보조 NIC의 IP 구성에 바인딩됩니다.
- Azure Load Balancer는 각 웹 사이트에 하나씩 두 개의 프런트 엔드 IP 주소를 노출하는 데 사용됩니다. 프런트 엔드 주소는 트래픽을 각 웹 사이트의 각 IP 구성에 분산하는 데 사용됩니다.
- 프런트 엔드 IP 주소와 백 엔드 풀 IP 주소 모두에 동일한 포트 번호가 사용됩니다.

## <a name="prerequisites"></a>필수 조건

시나리오 예제에서는 다음과 같이 구성된 **contosofabrikam**이라는 리소스 그룹이 있다고 가정합니다.

- 리소스 그룹에는 **myVNet**이라는 가상 네트워크가 포함됩니다.
- **myVNet** 네트워크에는 **VM1** 및 **VM2**라는 두 개의 VM이 포함됩니다.
- VM1 및 VM2는 **myAvailset**이라는 동일한 가용성 집합에 있습니다. 
- VM1 및 VM2에는 각각 **VM1NIC1** 및 **VM2NIC1**이라는 주 NIC가 있습니다. 
- VM1 및 VM2에는 각각 **VM1NIC2** 및 **VM2NIC2**라는 보조 NIC가 있습니다.

여러 NIC로 VM 만들기에 대한 자세한 내용은 [PowerShell을 사용하여 여러 NIC로 VM 만들기](../virtual-machines/windows/multiple-nics.md)를 참조하세요.

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>여러 IP 구성에서 부하 분산 수행

이 문서에 설명된 시나리오를 수행하려면 다음 단계를 완료합니다.

### <a name="step-1-configure-the-secondary-nics"></a>1단계: 보조 NIC 구성

가상 네트워크의 각 VM에 대해 보조 NIC에 대한 IP 구성을 추가합니다.  

1. Azure Portal https://portal.azure.com로 이동합니다. Azure 계정으로 로그인합니다.

2. 화면 왼쪽 위에 있는 **리소스 그룹** 아이콘을 선택합니다. 그런 다음 VM이 있는 리소스 그룹(예: **contosofabrikam**)을 선택합니다. **리소스 그룹** 창에 VM의 모든 리소스와 NIC가 표시됩니다.

3. 각 VM의 보조 NIC에 IP 구성을 추가합니다.

    1. 구성할 보조 NIC를 선택합니다.
    
    2. **IP 구성**을 선택합니다. 다음 창에서 위쪽에 있는 **추가**를 선택합니다.

    3. **IP 구성 추가**에서 두 번째 IP 구성을 NIC에 추가합니다. 

        1. 보조 IP 구성의 이름을 입력합니다. (예를 들어, VM1 및 VM2에 대해 IP 구성 이름을 각각 **VM1NIC2-ipconfig2** 및 **VM2NIC2-ipconfig2**로 지정합니다.)

        2. **개인 IP 주소**, **할당** 설정에 대해 **정적**을 선택합니다.

        3. **확인**을 선택합니다.

보조 NIC에 대한 두 번째 IP 구성을 완료하면 지정된 NIC에 대한 **IP 구성** 설정에 그 내용이 표시됩니다.

### <a name="step-2-create-the-load-balancer"></a>2단계: 부하 분산 장치 만들기

구성에 대한 부하 분산 장치를 만듭니다.

1. Azure Portal https://portal.azure.com로 이동합니다. Azure 계정으로 로그인합니다.

2. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Load Balancer**를 선택합니다. 다음 **만들기**를 선택합니다.

3. **부하 분산 장치 만들기**에 부하 분산 장치의 이름을 입력합니다. 이 시나리오에는 **mylb**라는 이름이 사용됩니다.

4. **공용 IP 주소** 아래에 **PublicIP1**이라는 새 공용 IP를 만듭니다.

5. **리소스 그룹** 아래에서 VM의 기존 리소스 그룹(예: **contosofabrikam**)을 선택합니다. 부하 분산 장치를 배포할 위치를 선택한 다음 **확인**을 선택합니다.

부하 분산 장치가 배포되기 시작합니다. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 완료된 후에는 부하 분산 장치가 리소스 그룹에 리소스로 표시됩니다.

### <a name="step-3-configure-the-front-end-ip-pool"></a>3단계: 프런트 엔드 IP 풀 구성

각 웹 사이트(contoso.com 및 fabrikam.com)에 대해 부하 분산 장치에 프런트 엔드 IP 풀을 구성합니다.

1. 포털에서 **추가 서비스**를 선택합니다. 필터 상자에 **공용 IP 주소**를 입력한 다음 **공용 IP 주소**를 선택합니다. 다음 창에서 위쪽에 있는 **추가**를 선택합니다.

2. 두 웹 사이트(contoso.com 및 fabrikam.com)에 대해 두 개의 공용 IP 주소(**PublicIP1** 및 **PublicIP2**)를 구성합니다.

   1. 프런트 엔드 IP 주소의 이름을 입력합니다.

   2. **리소스 그룹**에서 VM의 기존 리소스 그룹(예: **contosofabrikam**)을 선택합니다.

   3. **위치**에서 VM과 같은 위치를 선택합니다.

   4. **확인**을 선택합니다.

      공용 IP 주소가 만들어 지면 **공용 IP** 주소에 표시됩니다.

3. <a name="step3-3"></a>포털에서 **추가 서비스**를 선택합니다. 필터 상자에 **부하 분산 장치**를 입력한 다음 **Load Balancer**를 선택합니다. 

4. 프런트 엔드 IP 풀을 추가할 부하 분산 장치(**mylb**)를 선택합니다.

5. **설정**에서 **프런트 엔드 IP 구성**을 선택합니다. 다음 창에서 위쪽에 있는 **추가**를 선택합니다.

6. 프런트 엔드 IP 주소의 이름(예: **contosofe** 또는 **fabrikamfe**)을 입력합니다.

7. <a name="step3-7"></a>**IP 주소**를 선택합니다. **공용 IP 주소 선택**에서 프런트 엔드에 대한 IP 주소(**PublicIP1** 또는 **PublicIP2**)를 선택합니다.

8. 이 섹션의 <a href="#step3-3">3단계</a>에서 <a href="#step3-7">7단계</a>까지 반복하여 두 번째 프런트 엔드 IP 주소를 만듭니다.

프런트 엔드 풀이 구성된 후 부하 분산 장치 **프런트 엔드 IP 구성** 설정 아래에 IP 주소가 표시됩니다. 
    
### <a name="step-4-configure-the-back-end-pool"></a>4단계: 백 엔드 풀 구성

각 웹 사이트(contoso.com 및 fabrikam.com)에 대해 부하 분산 장치에 백 엔드 주소 풀을 구성합니다.
        
1. 포털에서 **추가 서비스**를 선택합니다. 필터 상자에 **부하 분산 장치**를 입력한 다음 **Load Balancer**를 선택합니다.

2. 백 엔드 풀을 추가할 부하 분산 장치(**mylb**)를 선택합니다.

3. **설정**에서 **백 엔드 풀**을 선택합니다. 백 엔드 풀의 이름(예: **contosopool** 또는 **fabrikampool**)을 입력합니다. 다음 창에서 위쪽에 있는 **추가**를 선택합니다. 

4. **Associated to**(연결 대상)에서 **가용성 집합**을 선택합니다.

5. **가용성 집합**에서 **myAvailset**을 선택합니다.

6. 두 VM에 대한 대상 네트워크 IP 구성을 추가합니다. 

    ![부하 분산 장치에 대한 백 엔드 풀 구성](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. **대상 가상 머신**의 경우 백 엔드 풀에 추가하려는 VM(예: **VM1** 또는 **VM2**)을 선택합니다.

    2. **네트워크 IP 구성**의 경우 이전 단계에서 선택한 VM에 대한 보조 NIC의 IP 구성(예: **VM1NIC2-ipconfig2** 또는 **VM2NIC2-ipconfig2**)을 선택합니다.

7. **확인**을 선택합니다.

백 엔드 풀이 구성된 후 부하 분산 장치 **백 엔드 풀** 설정 아래에 주소가 표시됩니다.

### <a name="step-5-configure-the-health-probe"></a>5단계: 상태 프로브 구성

부하 분산 장치의 상태 프로브를 구성합니다.

1. 포털에서 **추가 서비스**를 선택합니다. 필터 상자에 **부하 분산 장치**를 입력한 다음 **Load Balancer**를 선택합니다.

2. 상태 프로브를 추가할 부하 분산 장치(**mylb**)를 선택합니다.

3. **설정**에서 **상태 프로브**를 선택합니다. 다음 창에서 위쪽에 있는 **추가**를 선택합니다. 

4. 상태 프로브의 이름(예: **HTTP**)을 입력합니다. **확인**을 선택합니다.

### <a name="step-6-configure-load-balancing-rules"></a>6단계: 부하 분산 규칙 구성

각 웹 사이트(contoso.com 및 fabrikam.com)에 대한 부하 분산 규칙을 구성합니다.
    
1. <a name="step6-1"></a>**설정**에서 **부하 분산 규칙**을 선택합니다. 다음 창에서 위쪽에 있는 **추가**를 선택합니다. 

2. **이름**에 부하 분산 규칙의 이름(예: contoso.com의 경우 **HTTPc** 또는 fabrikam.com의 경우 **HTTPf**)을 입력합니다.

3. **프런트 엔드 IP 주소**에는 이전에 만든 프런트 엔드 IP 주소(예: **contosofe** 또는 **fabrikamfe**)를 선택합니다.

4. **포트** 및 **백 엔드 포트**에서 기본값인 **80**을 그대로 유지합니다.

5. **부동 IP(Direct Server Return)** 의 경우 **사용 안 함**을 선택합니다.

6. <a name="step6-6"></a>**확인**을 선택합니다.

7. 이 섹션의 <a href="#step6-1">1단계</a>에서 <a href="#step6-6">6단계</a>까지 반복하여 두 번째 부하 분산 장치 규칙을 만듭니다.

규칙이 구성된 후에는 부하 분산 장치 **부하 분산 규칙** 설정 아래에 표시됩니다.

### <a name="step-7-configure-dns-records"></a>7단계: DNS 레코드 구성

마지막 단계로 부하 분산 장치의 각 프런트 엔드 IP 주소를 가리 키도록 DNS 리소스 레코드를 구성합니다. 도메인을 Azure DNS에 호스트할 수 있습니다. Load Balancer와 Azure DNS를 사용하는 방법에 대한 자세한 내용은 [다른 Azure 서비스와 함께 Azure DNS 사용](../dns/dns-for-azure-services.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- Azure에서 부하 분산 서비스를 결합하는 방법에 대한 자세한 내용은 [Azure에서 부하 분산 서비스 사용](../traffic-manager/traffic-manager-load-balancing-azure.md)을 참조하세요.
- 다양 한 유형의 로그를 사용 하 여 부하 분산 장치에서 문제를 해결 하는 방법을 알아봅니다 [Azure Monitor는 Azure Load Balancer에 대 한 로그](../load-balancer/load-balancer-monitor-log.md)합니다.
