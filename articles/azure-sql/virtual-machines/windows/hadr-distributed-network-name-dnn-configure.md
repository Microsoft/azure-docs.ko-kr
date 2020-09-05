---
title: 분산 네트워크 이름 구성 (DNN)
description: DNN (분산 네트워크 이름)을 구성 하 여 Azure VM FCI (장애 조치 (failover) 클러스터 인스턴스)에서 SQL Server로 트래픽을 라우팅하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8eb9caf466148e43266c4be9cf1308da15fb67f2
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245539"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>FCI에 대 한 분산 네트워크 이름 구성 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Virtual Machines에서 분산 네트워크 이름 (DNN)은 트래픽을 적절 한 클러스터 된 리소스로 라우팅하는 데 사용 됩니다. Azure Load Balancer 필요 없이 VNN (가상 네트워크 이름) 보다 SQL Server FCI (장애 조치 (failover) 클러스터 인스턴스)에 연결 하는 더 쉬운 방법을 제공 합니다. 이 기능은 현재 미리 보기 상태 이며 SQL Server 2019 CU2 이상 및 Windows Server 2016 이상 에서만 사용할 수 있습니다. 

이 문서에서는 고가용성 및 HADR (재해 복구)을 위해 Azure Vm의 SQL Server를 사용 하 여 FCIs로 트래픽을 라우팅하는 DNN를 구성 하는 방법을 설명 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하기 전에 다음이 준비되어 있어야 합니다.

- 분산 네트워크 이름이 [HADR 솔루션에 대 한 적절 한 연결 옵션](hadr-cluster-best-practices.md#connectivity)이라고 결정 했습니다.
- [장애 조치 (failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)를 구성 했습니다. 
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps)을 설치 했습니다. 

## <a name="create-dnn-resource"></a>DNN 리소스 만들기 

DNN 리소스는 SQL Server FCI와 동일한 클러스터 그룹에 만들어집니다. PowerShell을 사용 하 여 FCI 클러스터 그룹 내에 DNN 리소스를 만듭니다. 

다음 PowerShell 명령은 리소스 이름이 인 SQL Server FCI 클러스터 그룹에 DNN 리소스를 추가 합니다 `<dnnResourceName>` . 리소스 이름은 리소스를 고유 하 게 식별 하는 데 사용 됩니다. 사용자에 게 적합 하 고 클러스터에서 고유한 것을 사용 합니다. 리소스 형식은 여야 합니다 `Distributed Network Name` . 

`-Group`값은 분산 네트워크 이름을 추가 하려는 SQL Server FCI에 해당 하는 클러스터 그룹의 이름 이어야 합니다. 기본 인스턴스의 경우 일반적인 형식은 `SQL Server (MSSQLSERVER)` 입니다. 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

예를 들어 `dnn-demo` 기본 SQL Server FCI에 대 한 DNN 리소스를 만들려면 다음 PowerShell 명령을 사용 합니다.

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>클러스터 DNN DNS 이름 설정

클러스터의 DNN 리소스에 대 한 DNS 이름을 설정 합니다. 그런 다음 클러스터는이 값을 사용 하 여 현재 SQL Server FCI를 호스팅하고 있는 노드로 트래픽을 라우팅합니다. 
 
클라이언트는 DNS 이름을 사용 하 여 SQL Server FCI에 연결 합니다. 고유한 값을 선택할 수 있습니다. 기존 FCI가 이미 있고 클라이언트 연결 문자열을 업데이트 하지 않으려는 경우, 클라이언트가 이미 사용 하 고 있는 현재 VNN을 사용 하도록 DNN를 구성할 수 있습니다. 이렇게 하려면 DNS에 DNN를 설정 하기 전에 [VNN의 이름을 변경](#rename-the-vnn) 해야 합니다.


이 명령을 사용 하 여 DNN에 대 한 DNS 이름을 설정 합니다. 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName`값은 클라이언트가 SQL Server FCI에 연결 하는 데 사용 하는 값입니다. 예를 들어 클라이언트를에 연결 하려면 `FCIDNN` 다음 PowerShell 명령을 사용 합니다.

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

이제 클라이언트는 `FCIDNN` SQL Server FCI에 연결할 때 연결 문자열에 입력 합니다. 

   > [!WARNING]
   > 현재 VNN (가상 네트워크 이름)은 FCI 인프라의 필수 구성 요소 이므로 삭제 하지 마세요. 


### <a name="rename-the-vnn"></a>VNN 이름 바꾸기 

기존 가상 네트워크 이름이 있는 경우 클라이언트에서이 값을 계속 사용 하 여 SQL Server FCI에 연결 하도록 하려면 현재 VNN의 이름을 자리 표시자 값으로 바꾸어야 합니다. 현재 VNN의 이름을 바꾼 후 DNN에 대 한 DNS 이름 값을 VNN으로 설정할 수 있습니다. 

VNN의 이름을 바꾸는 경우 몇 가지 제한 사항이 적용 됩니다. 자세한 내용은 [FCI 이름 바꾸기](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)를 참조 하세요.

비즈니스에 현재 VNN을 사용 하지 않아도 되는 경우이 섹션을 건너뜁니다. VNN의 이름을 바꾼 후 [클러스터 DNN DNS 이름을 설정](#set-cluster-dnn-dns-name)합니다. 

   
## <a name="set-dnn-resource-online"></a>DNN 리소스를 온라인으로 설정

DNN 리소스의 이름을 적절 하 게 지정 하 고 클러스터에서 DNS 이름 값을 설정한 후 PowerShell을 사용 하 여 클러스터에서 DNN 리소스를 온라인으로 설정 합니다. 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

예를 들어 DNN 리소스를 시작 하려면 `dnn-demo` 다음 PowerShell 명령을 사용 합니다. 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>가능한 소유자 구성

기본적으로 클러스터는 DNN DNS 이름을 클러스터의 모든 노드에 바인딩합니다. 그러나 SQL Server FCI의 일부가 아닌 클러스터의 노드는 가능한 DNN 소유자 목록에서 제외 해야 합니다. 

가능한 소유자를 업데이트 하려면 다음 단계를 수행 합니다.

1. 장애 조치(Failover) 클러스터 관리자에서 DNN 리소스로 이동 합니다. 
1. DNN 리소스를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택 합니다. 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="속성 명령이 강조 표시 된 DNN 리소스에 대 한 바로 가기 메뉴입니다.":::
1. 장애 조치 (failover) 클러스터 인스턴스에 참여 하지 않는 노드에 대 한 확인란의 선택을 취소 합니다. DNN 리소스에 대 한 가능한 소유자 목록은 SQL Server 인스턴스 리소스에 대 한 가능한 소유자 목록과 일치 해야 합니다. 예를 들어 Data3이 FCI에 참여 하지 않는다고 가정할 경우 다음 이미지는 DNN 리소스의 가능한 소유자 목록에서 Data3를 제거 하는 예제입니다. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="DNN 리소스의 가능한 소유자에 대해 FCI에 참여 하지 않는 노드 옆의 확인란을 선택 취소 합니다.":::

1. **확인**을 선택하여 설정을 저장합니다. 


## <a name="restart-sql-server-instance"></a>SQL Server 인스턴스 다시 시작 

장애 조치(Failover) 클러스터 관리자를 사용 하 여 SQL Server 인스턴스를 다시 시작 합니다. 다음 단계를 수행합니다.

1. 장애 조치(Failover) 클러스터 관리자에서 SQL Server 리소스로 이동 합니다.
1. SQL Server 리소스를 마우스 오른쪽 단추로 클릭 하 고 오프 라인으로 전환 합니다. 
1. 연결 된 모든 리소스가 오프 라인 상태가 된 후 SQL Server 리소스를 마우스 오른쪽 단추로 클릭 하 고 다시 온라인 상태로 만듭니다. 

## <a name="update-connection-string"></a>연결 문자열 업데이트

장애 조치 (failover) 시 빠른 연결을 보장 하려면 `MultiSubnetFailover=True` SQL 클라이언트 버전이 4.6.1 보다 이전인 경우 연결 문자열에를 추가 합니다. 

또한 DNN가 원래 VNN을 사용 하지 않는 경우 SQL Server FCI에 연결 하는 SQL 클라이언트는 연결 문자열을 DNN DNS 이름으로 업데이트 해야 합니다. 이러한 요구 사항을 방지 하기 위해 DNS 이름 값을 VNN의 이름으로 업데이트할 수 있습니다. 하지만 [기존 VNN을 자리 표시자로 먼저 바꾸어야](#rename-the-vnn) 합니다. 

## <a name="test-failover"></a>테스트 장애 조치

클러스터 된 리소스의 장애 조치 (failover)를 테스트 하 여 클러스터 기능의 유효성을 검사 합니다. 

장애 조치 (failover)를 테스트 하려면 다음 단계를 수행 합니다. 

1. RDP를 사용 하 여 SQL Server 클러스터 노드 중 하나에 연결 합니다.
1. **장애 조치(Failover) 클러스터 관리자**를 엽니다. **역할**을 선택합니다. SQL Server FCI 역할을 소유하는 노드를 살펴봅니다.
1. SQL Server FCI 역할을 마우스 오른쪽 단추로 클릭합니다. 
1. **이동**을 선택한 다음 **가장 적합한 노드**를 선택합니다.

**장애 조치(failover) 클러스터 관리자**는 역할을 보여 주며 해당 리소스는 오프라인으로 전환됩니다. 그런 다음 리소스가 이동하고 다른 노드에서 온라인 상태로 다시 전환됩니다.

## <a name="test-connectivity"></a>연결 테스트

연결을 테스트하려면 동일한 가상 네트워크의 다른 가상 머신에 로그인합니다. **SQL Server Management Studio** 를 열고 DNN DNS 이름을 사용 하 여 SQL Server fci에 연결 합니다.

필요한 경우 [SQL Server Management Studio를 다운로드](/sql/ssms/download-sql-server-management-studio-ssms)할 수 있습니다.

## <a name="avoid-ip-conflict"></a>IP 충돌 방지

FCI 리소스에서 사용 하는 VIP (가상 IP) 주소가 Azure의 다른 리소스에 중복으로 할당 되는 것을 방지 하는 선택적 단계입니다. 

이제 고객이 DNN를 사용 하 여 SQL Server FCI에 연결 하지만 FCI 인프라의 필수 구성 요소인 VNN (가상 네트워크 이름) 및 가상 IP를 삭제할 수 없습니다. 그러나 Azure에서 가상 IP 주소를 예약 하는 부하 분산 장치는 더 이상 없으므로 가상 네트워크의 다른 리소스에 FCI가 사용 하는 가상 IP 주소와 동일한 IP 주소가 할당 될 위험이 있습니다. 이로 인해 중복 된 IP 충돌 문제가 발생할 수 있습니다. 

IP 주소를 예약 하도록 APIPA 주소 또는 전용 네트워크 어댑터를 구성 합니다. 

### <a name="apipa-address"></a>APIPA 주소

중복 IP 주소를 사용 하지 않으려면 APIPA 주소 (링크-로컬 주소 라고도 함)를 구성 합니다. 이렇게 하려면 다음 명령을 실행합니다.

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

이 명령에서 "가상 IP 주소"는 클러스터 된 VIP 주소 리소스의 이름이 고 "(169.254.1.1과"는 VIP 주소에 대해 선택한 APIPA 주소입니다. 비즈니스에 가장 적합 한 주소를 선택 합니다. `OverrideAddressMatch=1`APIPA 주소 공간을 포함 하 여 모든 네트워크에서 IP 주소를 허용 하도록 설정 합니다. 

### <a name="dedicated-network-adapter"></a>전용 네트워크 어댑터

또는 Azure에서 가상 IP 주소 리소스에 사용 되는 IP 주소를 예약 하도록 네트워크 어댑터를 구성 합니다. 그러나 이렇게 하면 서브넷 주소 공간의 주소가 사용 되 고 네트워크 어댑터를 다른 용도로 사용 하지 않도록 하는 추가 오버 헤드가 발생 합니다.

## <a name="limitations"></a>제한 사항

- 현재 DNN는 Windows Server 2016에서 SQL Server 2019 CU2 이상 에서만 지원 됩니다. 
- 현재 DNN는 Azure Vm에서 SQL Server 하는 장애 조치 (failover) 클러스터 인스턴스에 대해서만 지원 됩니다. 가용성 그룹 수신기에 대 한 Azure Load Balancer에 가상 네트워크 이름을 사용 합니다.
- DNN를 사용 하 여 다른 SQL Server 기능 및 FCI를 사용 하는 경우 더 많은 고려 사항이 있을 수 있습니다. 자세한 내용은 [DNN 상호 운용성을 사용 하는 Fci](failover-cluster-instance-dnn-interoperability.md)를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

Azure에서 HADR 기능을 SQL Server 하는 방법에 대 한 자세한 내용은 [가용성 그룹](availability-group-overview.md) 및 [장애 조치 (Failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)를 참조 하세요. 또한 고가용성 및 재해 복구를 위한 환경을 구성 하는 [모범 사례](hadr-cluster-best-practices.md) 를 배울 수 있습니다. 

DNN 및 FCI와 함께 사용 하는 경우 일부 특정 SQL Server 기능에 대 한 추가 구성 요구 사항이 있을 수 있습니다. 자세한 내용은 [DNN 상호 운용성을 사용 하는 Fci](failover-cluster-instance-dnn-interoperability.md) 를 참조 하세요. 

