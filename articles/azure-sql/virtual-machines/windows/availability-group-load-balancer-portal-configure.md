---
title: 가용성 그룹 수신기 및 부하 분산 장치 구성(Azure Portal)
description: Azure Virtual Machines에서 SQL Server에 대한 Always On 가용성 그룹용 수신기를 만드는 단계별 지침
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 9cf6fa26cec0abbc52a990d71c1c2fcc5d6023e4
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612557"
---
# <a name="configure-a-load-balancer-for-a-sql-server-always-on-availability-group-in-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server Always On 가용성 그룹에 대 한 부하 분산 장치 구성

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


이 문서에서는 Azure Resource Manager를 사용 하 여 실행 되는 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹에 대 한 부하 분산 장치를 만드는 방법을 설명 합니다. SQL Server 인스턴스가 Azure Virtual Machines에 있는 경우 가용성 그룹에 부하 분산 장치가 필요 합니다. 부하 분산 장치는 가용성 그룹 수신기의 IP 주소를 저장합니다. 가용성 그룹이 여러 지역에 분산된 경우 각 지역에 부하 분산 장치가 있어야 합니다.

이 작업을 완료 하려면 리소스 관리자를 실행 하는 Azure Vm에 배포 된 SQL Server Always On 가용성 그룹을 만들어야 합니다. 두 SQL Server 가상 머신은 동일한 가용성 집합에 속해야 합니다. [Microsoft 템플릿](availability-group-azure-marketplace-template-configure.md)을 사용하여 Resource Manager에서 가용성 그룹을 자동으로 만들 수 있습니다. 이 템플릿은 내부 부하 분산 장치를 자동으로 만듭니다. 

원하는 경우 [수동으로 가용성 그룹을 구성](availability-group-manually-configure-tutorial.md)할 수 있습니다.

이 문서를 진행하려면 가용성 그룹이 이미 구성되어 있어야 합니다.  

관련 문서 보기:

* [Azure VM의 Always On 가용성 그룹 구성(GUI)](availability-group-manually-configure-tutorial.md)   
* [Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

이 문서를 통해 Azure Portal에서 부하 분산 장치를 만들고 구성합니다. 프로세스 완료 후에는 가용성 그룹 수신기에 대한 부하 분산 장치에서 IP 주소를 사용하도록 클러스터를 구성합니다.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Azure 포털에서 부하 분산 장치 만들기 및 구성

이 작업 부분에서 다음 단계를 수행 합니다.

1. Azure Portal에서 부하 분산 장치를 만들고 IP 주소를 구성합니다.
2. 백 엔드 풀을 구성합니다.
3. 프로브를 만듭니다. 
4. 부하 분산 규칙을 설정 합니다.

> [!NOTE]
> 여러 리소스 그룹 및 지역에 SQL Server 인스턴스가 있는 경우 리소스 그룹당 한 번씩 각 단계를 두 번 수행합니다.
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>1단계: 부하 분산 장치 만들기 및 IP 주소 구성

먼저, 부하 분산 장치를 만듭니다. 

1. Azure 포털에서 SQL Server 가상 머신을 포함하는 리소스 그룹을 엽니다. 

2. 리소스 그룹에서 **추가**를 선택합니다.

3. **부하 분산 장치**를 검색합니다. 검색 결과에서 **Load Balancer** ( **Microsoft**에서 게시)를 선택 합니다.

4. **부하 분산 장치** 블레이드에서 **만들기**를 선택합니다.

5. **부하 분산 장치 만들기** 대화 상자에서 다음과 같이 부하 분산 장치를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **이름** |부하 분산 장치를 나타내는 텍스트 이름입니다. 예를 들어 **sqlLB**입니다. |
   | **형식** |**내부**: 대부분의 구현에서는 동일한 가상 네트워크 내에 있는 애플리케이션이 가용성 그룹에 연결할 수 있도록 하는 내부 부하 분산 장치를 사용합니다.  </br> **외부**: 애플리케이션이 공용 인터넷 연결을 통해 가용성 그룹에 연결할 수 있도록 합니다. |
   | **SKU** |**Standard**: SQL 인스턴스가 부하 분산 장치와 다른 가용성 집합에 있는 경우 필요 합니다. </br> **기본**: 기본 옵션입니다. |
   | **가상 네트워크** |SQL Server 인스턴스가 있는 가상 네트워크를 선택합니다. |
   | **서브넷** |SQL Server 인스턴스가 있는 서브넷을 선택합니다. |
   | **IP 주소 할당** |**정적** |
   | **개인 IP 주소** |서브넷에서 사용 가능한 IP 주소를 지정합니다. 클러스터에서 수신기를 만들 때 이 IP 주소를 사용합니다. 이 주소는 이 문서 뒷부분의 PowerShell 스크립트에서 `$ILBIP` 변수에 대해 사용됩니다. |
   | **구독** |구독이 여러 개인 경우 이 필드가 나타날 수 있습니다. 이 리소스와 연결할 구독을 선택합니다. 일반적으로 가용성 그룹에 대한 모든 리소스와 동일한 구독입니다. |
   | **리소스 그룹** |SQL Server 인스턴스가 있는 리소스 그룹을 선택합니다. |
   | **위치** |SQL Server 인스턴스가 있는 Azure 위치를 선택합니다. |

6. **만들기**를 선택합니다. 

Azure에서 부하 분산 장치를 만듭니다. 부하 분산 장치는 특정 네트워크, 서브넷, 리소스 그룹 및 위치에 속합니다. Azure에서 작업이 완료되면 Azure에서 부하 분산 장치 설정을 확인합니다. 

### <a name="step-2-configure-the-back-end-pool"></a>2단계: 백 엔드 풀 구성

Azure에서 백 엔드 주소 풀 *백 엔드 풀*이 호출됩니다. 이 경우 백 엔드 풀은 가용성 그룹에 있는 두 SQL Server 인스턴스의 주소입니다. 

1. 리소스 그룹에서 만든 부하 분산 장치를 선택 합니다. 

2. **설정**에서 **백 엔드 풀**을 선택 합니다.

3. **백 엔드 풀**에서 **추가** 를 선택 하 여 백 엔드 주소 풀을 만듭니다. 

4. **백 엔드 풀 추가**에서 **이름**에 백 엔드 풀의 이름을 입력합니다.

5. **가상 컴퓨터**에서 **가상 컴퓨터 추가**를 선택 합니다. 

6. **가상 머신 선택**에서 **가용성 집합 선택**을 선택 하 고 SQL Server 가상 머신이 속한 가용성 집합을 지정 합니다.

7. 가용성 집합을 선택한 후 **가상 머신 선택**을 선택 하 고 가용성 그룹의 SQL Server 인스턴스를 호스팅하는 두 개의 가상 머신을 선택한 다음 **선택**을 선택 합니다. 

8. **확인** 을 선택 하 여 **가상 머신 선택**블레이드를 닫고 **백 엔드 풀을 추가**합니다. 

Azure에서 백 엔드 주소 풀에 대한 설정이 업데이트됩니다. 이제 가용성 집합에는 두 개의 SQL Server 인스턴스에 대한 풀이 있습니다.

### <a name="step-3-create-a-probe"></a>3단계: 프로브 만들기

프로브는 Azure에서 현재 가용성 그룹 수신기를 소유하는 SQL Server 인스턴스를 확인하는 방법을 정의합니다. Azure는 프로브를 만들 때 정의한 포트의 IP 주소를 기반으로 서비스를 프로브합니다.

1. 부하 분산 장치 **설정** 블레이드에서 **상태 프로브**를 선택 합니다. 

2. **상태 프로브** 블레이드에서 **추가**를 선택 합니다.

3. **프로브 추가** 블레이드에서 프로브를 구성합니다. 다음 값을 사용하여 프로브를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **이름** |프로브를 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointProbe**입니다. |
   | **프로토콜** |**TCP** |
   | **포트** |사용 가능한 모든 포트를 사용할 수 있습니다. 예를 들어 *59999*입니다. |
   | **간격** |*5* |
   | **비정상 임계값** |*2* |

4.  **확인**을 선택합니다. 

> [!NOTE]
> 지정한 포트가 두 SQL Server 인스턴스의 방화벽에서 열려 있는지 확인합니다. 두 인스턴스 모두 사용하는 TCP 포트에 대한 인바운드 규칙이 필요합니다. 자세한 내용은 [방화벽 규칙 추가 또는 편집](https://technet.microsoft.com/library/cc753558.aspx)을 참조하세요. 
> 

Azure는 프로브를 만든 후 가용성 그룹에 대한 수신기가 있는 SQL Server 인스턴스를 테스트하는 데 프로브를 사용합니다.

### <a name="step-4-set-the-load-balancing-rules"></a>4 단계: 부하 분산 규칙 설정

부하 분산 규칙은 부하 분산 장치가 트래픽을 SQL Server 인스턴스로 라우트하는 방법을 구성합니다. 이 부하 분산 장치의 경우 한 번에 두 SQL Server 인스턴스 중 하나만 가용성 그룹 수신기 리소스를 소유하므로 Direct Server Return(DSR)이 사용됩니다.

1. 부하 분산 장치 **설정** 블레이드에서 **부하 분산 규칙**을 선택 합니다. 

2. **부하 분산 규칙** 블레이드에서 **추가**를 선택 합니다.

3. **부하 분산 규칙 추가** 블레이드에서 부하 분산 규칙을 구성합니다. 다음 설정을 사용합니다. 

   | 설정 | 값 |
   | --- | --- |
   | **이름** |부하 분산 규칙을 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointListener**입니다. |
   | **프로토콜** |**TCP** |
   | **포트** |*1433* |
   | **백 엔드 포트** |*1433*. 이 규칙은 **부동 IP(Direct Server Return)** 를 사용하므로 이 값은 무시됩니다. |
   | **프로브** |이 부하 분산 장치에 대해 만든 프로브의 이름을 사용합니다. |
   | **세션 지속성** |**없음** |
   | **유휴 제한 시간(분)** |*4* |
   | **부동 IP(Direct Server Return)** |**Enabled** |

   > [!NOTE]
   > 블레이드에서 모든 설정을 보려면 아래로 스크롤해야 할 수도 있습니다.
   > 

4. **확인**을 선택합니다. 

5. Azure에서 부하 분산 규칙이 구성됩니다. 이제 가용성 그룹에 대한 수신기를 호스트하는 SQL Server 인스턴스로 트래픽을 라우트하도록 부하 분산 장치가 구성되었습니다. 

현재 리소스 그룹에는 두 SQL Server 컴퓨터에 모두 연결하는 하나의 부하 분산 장치가 있습니다. 부하 분산 장치에는 SQL Server Always On 가용성 그룹 수신기에 대한 IP 주소도 있으므로 두 컴퓨터 중 하나가 가용성 그룹에 대한 요청에 응답할 수 있습니다.

> [!NOTE]
> SQL Server 인스턴스가 두 개의 별도 지역에 있는 경우 다른 지역에서 단계를 반복합니다. 각 지역마다 하나의 부하 분산 장치가 필요합니다. 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>부하 분산 장치 IP 주소를 사용하도록 클러스터 구성

다음 단계는 클러스터에서 수신기를 구성하고 수신기를 온라인 상태로 전환하는 것입니다. 다음 단계를 수행합니다. 

1. 장애 조치(failover) 클러스터에서 가용성 그룹 수신기 만들기 

2. 수신기를 온라인 상태로 전환합니다.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>5단계: 장애 조치(failover) 클러스터에서 가용성 그룹 수신기 만들기

이 단계에서는 장애 조치(Failover) 클러스터 관리자와 SQL Server Management Studio에서 가용성 그룹 수신기를 수동으로 만듭니다.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>수신기의 구성 확인

클러스터 리소스 및 종속성이 제대로 구성된 경우 SQL Server Management Studio에서 수신기를 볼 수 있습니다. 수신기 포트를 설정 하려면 다음 단계를 수행 합니다.

1. SQL Server Management Studio를 시작한 다음 주 복제본에 연결합니다.

2. **AlwaysOn 고가용성** > **가용성 그룹** > **가용성 그룹 수신기**로 이동합니다.  

    이제 장애 조치(Failover) 클러스터 관리자에서 만든 수신기 이름이 표시됩니다. 

3. 수신기 이름을 마우스 오른쪽 단추로 클릭 한 다음 **속성**을 선택 합니다.

4. **포트** 상자에서 이전에 사용한 $EndpointPort (1433)를 사용 하 여 가용성 그룹 수신기에 대 한 포트 번호를 지정한 다음 **확인**을 선택 합니다.

이제 Resource Manager 모드에서 실행 중인 Azure 가상 머신에 가용성 그룹이 생겼습니다. 

## <a name="test-the-connection-to-the-listener"></a>수신기에 대한 연결 테스트

다음 단계를 수행 하 여 연결을 테스트 합니다.

1. RDP (원격 데스크톱 프로토콜)를 사용 하 여 동일한 가상 네트워크에 있지만 복제본을 소유 하지 않는 SQL Server 인스턴스에 연결 합니다. 이 서버는 클러스터의 다른 SQL Server 인스턴스가 될 수 있습니다.

2. **sqlcmd** 유틸리티를 사용하여 연결을 테스트합니다. 예를 들어 다음 스크립트는 Windows 인증을 사용하는 수신기를 통해 주 복제본에 대한 **sqlcmd** 연결을 설정합니다.

    ```console
    sqlcmd -S <listenerName> -E
    ```

SQLCMD 연결은 주 복제본을 호스트하는 SQL Server 인스턴스에 자동으로 연결합니다. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>추가 가용성 그룹의 IP 주소 만들기

각 가용성 그룹은 별도의 수신기를 사용합니다. 각 수신기는 자체 IP 주소가 있습니다. 동일한 부하 분산 장치를 사용하여 추가 수신기의 IP 주소를 저장하세요. 가용성 그룹을 만든 후에는 부하 분산 장치에 IP 주소를 추가한 다음 수신기를 구성합니다.

Azure Portal를 사용 하 여 부하 분산 장치에 IP 주소를 추가 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 부하 분산 장치가 포함 된 리소스 그룹을 열고 부하 분산 장치를 선택 합니다. 

2. **설정**아래에서 **프런트 엔드 IP 풀**을 선택 하 고 **추가**를 선택 합니다. 

3. **프런트 엔드 IP 주소 추가**에서 프런트 엔드의 이름을 할당합니다. 

4. **가상 네트워크** 및 **서브넷**이 SQL Server 인스턴스와 동일한지 확인합니다.

5. 수신기의 IP 주소를 설정합니다. 
   
   >[!TIP]
   >IP 주소를 고정으로 설정하고 현재 서브넷에서 사용되지 않는 주소를 입력할 수 있습니다. 또는 IP 주소를 동적으로 설정하고 새 프런트 엔드 IP 풀을 저장할 수 있습니다. 이렇게 하면 Azure Portal이 풀에 사용 가능한 IP 주소를 자동으로 할당합니다. 그런 다음 프런트 엔드 IP 풀을 다시 열고 주소 할당을 고정으로 변경할 수 있습니다. 

6. 수신기의 IP 주소를 저장합니다. 

7. 다음 설정을 사용하여 상태 검색을 추가합니다.

   |설정 |값
   |:-----|:----
   |**이름** |프로브를 식별하는 이름입니다.
   |**프로토콜** |TCP
   |**포트** |사용하지 않는 TCP 포트를 모든 가상 머신에서 사용할 수 있어야 합니다. 다른 용도로는 사용할 수 없습니다. 두 수신기가 동일한 프로브 포트를 사용할 수 없습니다. 
   |**간격** |프로브 시도 간격입니다. 기본값(5)을 사용하세요.
   |**비정상 임계값** |이 연속 임계값을 실패하면 가상 머신을 비정상으로 간주합니다.

8. **확인** 을 선택 하 여 프로브를 저장 합니다. 

9. 부하 분산 규칙을 만듭니다. **부하 분산 규칙**을 선택한 다음 **추가**를 선택 합니다.

10. 다음 설정을 사용 하 여 새 부하 분산 규칙을 구성 합니다.

    |설정 |값
    |:-----|:----
    |**이름** |부하 분산 규칙을 식별 하는 이름입니다. 
    |**프런트 엔드 IP 주소** |만든 IP 주소를 선택합니다. 
    |**프로토콜** |TCP
    |**포트** |SQL Server 인스턴스에서 사용하는 포트를 사용합니다. 변경하지 않을 경우 기본 인스턴스는 포트 1433을 사용합니다. 
    |**백 엔드 포트** |**포트**와 동일한 값을 사용합니다.
    |**백 엔드 풀** |SQL Server 인스턴스가 포함된 가상 머신을 포함하는 풀입니다. 
    |**상태 프로브** |만든 프로브를 선택합니다.
    |**세션 지속성** |None
    |**유휴 제한 시간(분)** |기본값(4)
    |**부동 IP(Direct Server Return)** | 사용

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>새 IP 주소를 사용하도록 가용성 그룹 구성

클러스터 구성을 완료하려면 첫 번째 가용성 그룹을 만들 때 수행한 단계를 반복합니다. 즉, [새 IP 주소를 사용하도록 클러스터를 구성](#configure-the-cluster-to-use-the-load-balancer-ip-address)합니다. 

수신기에 대 한 IP 주소를 추가한 후에는 다음 단계를 수행 하 여 추가 가용성 그룹을 구성 합니다. 

1. 새 IP 주소의 프로브 포트가 두 SQL Server 가상 머신에서 모두 열려 있는지 확인합니다. 

2. [클러스터 관리자에서 클라이언트 액세스 지점을 추가합니다](#addcap).

3. [가용성 그룹에 대한 IP 리소스를 구성합니다](#congroup).

   >[!IMPORTANT]
   >IP 주소를 만들 때 부하 분산 장치에 추가한 IP 주소를 사용합니다.  

4. [SQL Server 가용성 그룹 리소스가 클라이언트 액세스 지점에 종속되게 합니다.](#dependencyGroup)

5. [클라이언트 액세스 지점 리소스가 IP 주소에 종속되게 합니다](#listname).
 
6. [PowerShell에서 클러스터 매개 변수를 설정합니다](#setparam).

새 IP 주소를 사용하도록 가용성 그룹을 구성한 후에는 수신기에 대한 연결을 구성합니다. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>분산 가용성 그룹에 대 한 부하 분산 규칙 추가

가용성 그룹이 분산 가용성 그룹에 참여하는 경우 부하 분산 장치에 추가 규칙이 필요합니다. 이 규칙은 분산 가용성 그룹 수신기에서 사용하는 포트를 저장합니다.

>[!IMPORTANT]
>이 단계는 가용성 그룹이 [분산 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)에 참여하는 경우에만 적용됩니다. 

1. 분산 가용성 그룹에 참여하는 각 서버의 분산 가용성 그룹 수신기 TCP 포트에 인바운드 규칙을 만듭니다. 설명서의 많은 예제에서는 5022를 사용합니다. 

1. Azure Portal에서 부하 분산 장치를 선택 하 고 **부하 분산 규칙**을 선택한 다음 **+ 추가**를 선택 합니다. 

1. 다음 설정을 사용하여 부하 분산 규칙을 만듭니다.

   |설정 |값
   |:-----|:----
   |**이름** |분산 가용성 그룹에 대한 부하 분산 규칙을 식별하는 이름입니다. 
   |**프런트 엔드 IP 주소** |가용성 그룹과 동일한 프런트 엔드 IP 주소를 사용합니다.
   |**프로토콜** |TCP
   |**포트** |5022 - [분산 가용성 그룹 엔드포인트 수신기](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)의 포트입니다.</br> 사용 가능한 모든 포트일 수 있습니다.  
   |**백 엔드 포트** | 5022 - **포트**와 동일한 값을 사용합니다.
   |**백 엔드 풀** |SQL Server 인스턴스가 포함된 가상 머신을 포함하는 풀입니다. 
   |**상태 프로브** |만든 프로브를 선택합니다.
   |**세션 지속성** |None
   |**유휴 제한 시간(분)** |기본값(4)
   |**부동 IP(Direct Server Return)** | 사용

분산 가용성 그룹에 참여하는 다른 가용성 그룹의 부하 분산 장치에 대해 이 단계를 반복합니다.

액세스를 제한 하는 Azure 네트워크 보안 그룹이 있는 경우 허용 규칙이 다음을 포함 하는지 확인 합니다.
- 백 엔드 SQL Server VM IP 주소
- AG 수신기에 대 한 부하 분산 장치 부동 IP 주소
- 클러스터 코어 IP 주소 (해당 하는 경우)입니다.

## <a name="next-steps"></a>다음 단계

- [다른 지역의 Azure Virtual Machines에서 SQL Server Always On 가용성 그룹 구성](availability-group-manually-configure-multiple-regions.md)
