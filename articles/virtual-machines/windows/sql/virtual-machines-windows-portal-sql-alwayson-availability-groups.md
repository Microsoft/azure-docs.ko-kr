---
title: Azure Resource Manager VM의 고가용성 설정 | Microsoft Docs
description: 이 자습서에서는 Azure Resource Manager 모드에서 Azure Virtual Machines로 Always On 가용성 그룹을 만드는 방법을 보여 줍니다.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: bddc83d55c8909412f7f935a4324a6f316a82cd7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129556"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Always On 가용성 그룹에서 Azure Virtual Machines을 자동으로 구성 합니다. Resource Manager

이 자습서에서는 Azure Resource Manager 가상 머신을 사용하는 SQL Server 가용성 그룹을 만드는 방법을 보여 줍니다. 자습서에서는 Azure 블레이드를 사용하여 템플릿을 구성합니다. 이 자습서를 살펴보면서 기본 설정을 검토하고 필요한 설정을 입력하며 포털에서 블레이드를 업데이트할 수 있습니다.

전체 자습서에서는 Azure Virtual Machines에서 다음 요소를 포함하는 SQL Server 가용성 그룹을 만듭니다.

* 프런트 엔드 및 백 엔드 서브넷을 비롯한 여러 서브넷을 포함하는 가상 네트워크
* Active Directory 도메인을 포함하는 두 개의 도메인 컨트롤러
* SQL Server를 실행하고 백 엔드 서브넷에 배포되고 Active Directory 도메인에 가입된 두 개의 가상 머신
* 노드 과반수 쿼럼 모델을 포함하는 3-노드 장애 조치 클러스터
* 가용성 데이터베이스의 두 개의 동기 커밋 복제본이 포함된 가용성 그룹

다음 그림은 전체 솔루션을 나타냅니다.

![Azure에서 가용성 그룹에 대한 Test Lab 아키텍처](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

이 솔루션의 모든 리소스는 단일 리소스 그룹에 속합니다.

자습서를 시작하기 전에 다음을 확인하세요.

* Azure 계정이 있습니다. 계정이 없는 경우 [평가판 계정에 등록](https://azure.microsoft.com/pricing/free-trial/)합니다.
* GUI를 사용하여 가상 머신 갤러리에서 SQL Server 가상 머신을 프로비전하는 방법을 이미 알고 있습니다. 자세한 내용은 [Azure에서 SQL Server 가상 머신 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.
* 가용성 그룹을 확실하게 이해하고 있습니다. 자세한 내용은 [Always On 가용성 그룹(SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요.

> [!NOTE]
> SharePoint와 가용성 그룹을 사용하는 것에 관심이 있는 경우 [SharePoint 2013에 대해 SQL Server 2012 Always On 가용성 그룹 구성](https://technet.microsoft.com/library/jj715261.aspx)을 참조하세요.
>
>

이 자습서에서는 다음 작업에 Azure Portal을 사용합니다.

* 포털에서 Always On 템플릿 선택
* 템플릿 설정 검토 및 환경에 대한 몇 가지 구성 설정 업데이트
* 환경 전체를 생성하면서 Azure 모니터링
* 도메인 컨트롤러에 연결한 후 SQL Server를 실행하는 서버에 연결

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>갤러리에서 클러스터 프로비전
Azure는 전체 솔루션에 대한 갤러리 이미지를 제공합니다. 템플릿을 찾으려면 다음을 수행합니다.

1. 계정을 사용하여 Azure Portal에 로그인합니다.
2. Azure Portal에서 **리소스 만들기**를 클릭하여 **새로 만들기** 창을 엽니다.
3. **새로 만들기** 창에서 **AlwaysOn**을 검색합니다.
   ![AlwaysOn 템플릿 찾기](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. 검색 결과에서 **SQL Server AlwaysOn 클러스터**를 찾습니다.
   ![AlwaysOn 템플릿](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. **배포 모델 선택**에서 **Resource Manager**를 선택합니다.

### <a name="basics"></a>기본 사항
**기본 사항**을 클릭하고 다음 설정을 구성합니다.

* **관리자 사용자 이름**은 도메인 관리자 권한이 있고 SQL Server의 두 인스턴스 모두에서 SQL Server sysadmin 고정된 서버 역할의 멤버인 사용자 계정입니다. 이 자습서에서는 **DomainAdmin**을 사용합니다.
* **암호** 는 도메인 관리자 계정의 암호입니다. 복잡한 암호를 사용합니다. 암호를 확인합니다.
* **구독**은 가용성 그룹에 대해 배포된 모든 리소스를 실행하기 위해 Azure에서 청구하는 구독입니다. 계정에 여러 구독이 있는 경우 다른 구독을 지정할 수 있습니다.
* **리소스 그룹**은 이 템플릿으로 만든 모든 Azure 리소스가 속하는 그룹의 이름입니다. 이 자습서에서는 **SQL-HA-RG**를 사용합니다. 자세한 내용은 [Azure Resource Manager 개요](../../../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
* **위치**는 이 자습서에서 리소스를 만들 Azure 지역입니다. Azure 지역을 선택합니다.

다음 스크린샷은 완료된 **기본 사항** 블레이드입니다.

![기본 사항](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

**확인**을 클릭합니다.

### <a name="domain-and-network-settings"></a>도메인 및 네트워크 설정
이 Azure 갤러리 템플릿은 도메인 및 도메인 컨트롤러를 만듭니다. 또한 네트워크와 두 개의 서브넷을 만듭니다. 템플릿은 기존 도메인 또는 가상 네트워크에서 서버를 만들 수 없습니다. 다음 단계는 도메인 및 네트워크 설정을 구성합니다.

**도메인 및 네트워크 설정** 블레이드에서 도메인 및 네트워크 설정에 사전 설정된 값을 검토합니다.

* **포리스트 루트 도메인 이름**은 클러스터를 호스팅하는 Active Directory 도메인의 도메인 이름입니다. 자습서에는 **contoso.com**을 사용합니다.
* **Virtual Network 이름** 은 Azure Virtual Network에 대한 네트워크 이름입니다. 자습서에서는 **autohaVNET**을 사용합니다.
* **도메인 컨트롤러 서브넷 이름** 은 도메인 컨트롤러를 호스팅하는 일부 가상 네트워크의 이름입니다. **subnet-1**을 사용합니다. 이 서브넷은 주소 접두사 **10.0.0.0/24**를 사용합니다.
* **SQL Server 서브넷 이름**은 SQL Server 및 파일 공유 감시를 실행하는 서버를 호스팅하는 가상 네트워크의 일부분의 이름입니다. **subnet-2**를 사용합니다. 이 서브넷은 주소 접두사 **10.0.1.0/26**을 사용합니다.

Azure의 가상 네트워크에 대한 자세한 내용은 [가상 네트워크 개요](../../../virtual-network/virtual-networks-overview.md)를 참조하세요.  

**도메인 및 네트워크 설정**은 다음 스크린샷과 같은 모양입니다.

![도메인 및 네트워크 설정](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

필요한 경우에 이러한 값을 변경할 수 있습니다. 이 자습서에서는 미리 설정된 값을 사용합니다.

설정을 검토한 다음 **확인**을 클릭합니다.

### <a name="availability-group-settings"></a>가용성 그룹 설정
**가용성 그룹 설정** 에서 가용성 그룹 및 수신기에 미리 설정된 값을 검토합니다.

* **가용성 그룹 이름** 은 가용성 그룹에 대해 클러스터된 리소스 이름입니다. 이 자습서에서는 **Contoso-ag**를 사용합니다.
* **가용성 그룹 수신기 이름**은 클러스터 및 내부 부하 분산 장치에서 사용됩니다. SQL Server에 연결된 클라이언트는 이 이름을 사용하여 데이터베이스의 적절한 복제본에 연결할 수 있습니다. 이 자습서에서는 **Contoso-listener**를 사용합니다.
* **가용성 그룹 수신기 포트**는 SQL Server 수신기의 TCP 포트를 지정합니다. 이 자습서에는 기본 포트 **1433**을 사용합니다.

필요한 경우에 이러한 값을 변경할 수 있습니다. 이 자습서에서는 미리 설정된 값을 사용합니다.  

![가용성 그룹 설정](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

**확인**을 클릭합니다.

### <a name="virtual-machine-size-storage-settings"></a>가상 컴퓨터 크기, 저장소 설정
**VM 크기, 저장소 설정**에서 SQL Server 가상 머신 크기를 선택하고 다른 설정을 검토합니다.

* **SQL Server 가상 머신 크기**는 SQL Server를 실행하는 두 가상 머신의 크기입니다. 워크로드에 적합한 가상 머신 크기를 선택합니다. 자습서에서 이 환경을 빌드하는 경우 **DS2**를 사용합니다. 프로덕션 워크로드에서 워크로드를 지원할 수 있는 가상 머신 크기를 선택합니다. 대부분의 프로덕션 워크로드에서는 **DS4** 이상이 필요합니다. 템플릿은 이 크기의 두 가상 머신을 빌드하고 각 컴퓨터에 SQL Server를 설치합니다. 자세한 내용은 [가상 머신의 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

> [!NOTE]
> Azure는 SQL Server의 Enterprise Edition을 설치합니다. 비용은 버전 및 가상 머신 크기에 따라 다릅니다. 현재 비용에 대한 자세한 내용은 [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)을 참조하세요.
>
>

* **도메인 컨트롤러 가상 머신 크기**는 도메인 컨트롤러에 대한 가상 머신 크기입니다. 이 자습서에서는 **D2**를 사용합니다.
* **파일 공유 감시 가상 머신 크기** 는 파일 공유 감시에 대한 가상 머신 크기입니다. 이 자습서에서는 **A1**을 사용합니다.
* **SQL Storage 계정**은 SQL Server 데이터 및 운영 체제 디스크를 유지하는 Storage 계정의 이름입니다. 이 자습서에서는 **alwaysonsql01**을 사용합니다.
* **DC Storage 계정** 은 도메인 컨트롤러에 대한 Storage 계정의 이름입니다. 이 자습서에서는 **alwaysondc01**을 사용합니다.
* **SQL Server 데이터 디스크 크기** (TB 단위)는 SQL Server 데이터 디스크의 크기(TB 단위)입니다. 1~4 사이에서 숫자를 지정합니다. 이 자습서에서는 **1**을 사용합니다.
* **저장소 최적화**는 워크로드 형식에 따라 SQL Server 가상 머신에 대한 특정 저장소 구성 설정을 설정합니다. 이 시나리오에서 모든 SQL Server 가상 머신은 읽기 전용으로 설정된 Azure 디스크 호스트 캐시로 Premium Storage를 사용합니다. 또한 이러한 세 가지 설정 중 하나를 선택하여 워크로드에 대한 SQL Server 설정을 최적화할 수 있습니다.

  * **일반 워크로드**는 특정 구성 설정을 설정하지 않음
  * **트랜잭션 처리**는 추적 플래그 1117 및 1118을 설정함
  * **데이터 웨어하우징**은 추적 플래그 1117 및 610을 설정함

이 자습서에서는 **일반 워크로드**를 사용합니다.

![VM 크기 저장소 설정](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

설정을 검토한 다음 **확인**을 클릭합니다.

#### <a name="a-note-about-storage"></a>저장소에 대한 정보
추가 최적화는 SQL Server 데이터 디스크의 크기에 따라 달라집니다. 데이터 디스크의 각 테라바이트에 대해 Azure는 1TB Premium Storage를 더 추가합니다. 서버에 2TB 이상이 필요한 경우 템플릿은 각 SQL Server 가상 머신에 저장소 풀을 만듭니다. 저장소 풀은 여러 디스크가 높은 용량, 복원력 및 성능을 제공하도록 구성된 저장소 가상화의 형태입니다.  그런 다음 템플릿은 저장소 풀에 저장소 공간을 만들고 운영 체제에 단일 데이터 디스크를 표시합니다. 템플릿은 SQL Server에서 이 디스크를 데이터 디스크로 지정합니다. 템플릿은 다음 설정을 사용하여 SQL Server에 대한 저장소 풀을 조정합니다.

* 줄무늬 크기는 가상 디스크에 대한 인터리빙 설정입니다. 트랜잭션 워크로드에는 64KB가 사용되고, 데이터 웨어하우징 워크로드에는 256KB가 사용됩니다.
* 복원력은 단순합니다(복원력 없음).

> [!NOTE]
> Azure Premium Storage는 로컬로 중복되며 단일 지역 내에 세 개의 데이터 복사본을 유지하므로 스토리지 풀에 추가 복원력이 필요하지 않습니다.
>
>

* 열 개수는 저장소 풀의 디스크 수와 같습니다.

저장소 공간 및 저장소 풀에 대한 자세한 내용은 다음을 참조하세요.

* [저장소 공간 개요](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server Backup 및 Storage 풀](https://technet.microsoft.com/library/dn390929.aspx)

SQL Server 구성 모범 사례에 대한 자세한 내용은 [Azure 가상 머신의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 참조하세요.

### <a name="sql-server-settings"></a>SQL 서버 설정
**SQL Server 설정**에서 SQL Server 가상 머신 이름 접두사, SQL Server 버전, SQL Server 서비스 계정과 암호 및 SQL 자동 패치 유지 관리 일정을 검토하고 수정합니다.

* **SQL Server 이름 접두사**는 각 SQL Server 가상 머신의 이름을 만드는 데 사용됩니다. 이 자습서에서는 **sqlserver**를 사용합니다. 템플릿에서는 SQL Server 가상 머신 이름을 *sqlserver-0* 및 *sqlserver-1*로 지정합니다.
* **SQL Server 버전**은 SQL Server의 버전입니다. 이 자습서에서는 **SQL Server 2014**를 사용합니다. 또한 **SQL Server 2012** 또는 **SQL Server 2016**을 선택할 수 있습니다.
* **SQL Server 서비스 계정 사용자 이름** 은 SQL Server 서비스에 대한 도메인 계정 이름입니다. 이 자습서에서는 **sqlservice**를 사용합니다.
* **암호** 는 SQL Server 서비스 계정에 대한 암호입니다.  복잡한 암호를 사용합니다. 암호를 확인합니다.
* **SQL 자동 패치 유지 관리 일정**은 Azure에서 SQL Server를 자동으로 패치하는 요일을 식별합니다. 이 자습서에서는 **일요일**을 입력합니다.
* **SQL 자동 패치 유지 관리 시작 시간**은 Azure 지역에서 자동 패치가 시작되는 시각입니다.

> [!NOTE]
> 각 가상 머신에 대한 패치 기간은 한 시간씩 지연됩니다. 서비스 중단을 방지하기 위해 한 번에 하나의 가상 컴퓨터만 패치됩니다.
>
>

![SQL 서버 설정](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

설정을 검토한 다음 **확인**을 클릭합니다.

### <a name="summary"></a>요약
요약 페이지에서 Azure가 설정을 확인합니다. 또한 템플릿을 다운로드할 수 있습니다. 요약을 검토합니다. **확인**을 클릭합니다.

### <a name="buy"></a>구입
이 마지막 블레이드에는 **사용 약관** 및 **개인정보취급방침**이 포함됩니다. 이 정보를 검토합니다. Azure에서 가상 머신 및 가용성 그룹에 필요한 기타 모든 리소스 만들기를 시작할 준비가 되면 **만들기**를 클릭합니다.

Azure Portal에서 리소스 그룹 및 모든 리소스를 만듭니다.

## <a name="monitor-deployment"></a>배포 모니터링
Azure 포털에서 배포 진행률을 모니터링합니다. 배포를 나타내는 아이콘이 Azure Portal 대시보드에 자동으로 고정됩니다.

![Azure 대시보드](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>SQL Server에 연결
SQL Server의 새 인스턴스는 인터넷에 연결된 IP 주소가 있는 가상 머신에서 실행됩니다. 원격 데스크톱을 각 SQL Server 가상 컴퓨터에 직접 RDP할 수 있습니다.

SQL Server에 RDP하려면 다음 단계를 수행합니다.

1. Azure Portal 대시보드에서 배포가 완료되었는지 확인합니다.
2. **리소스**를 클릭합니다.
3. **리소스** 블레이드에서 SQL Server를 실행하는 가상 머신 중 한 컴퓨터 이름인 **sqlserver-0**을 클릭합니다.
4. **sqlserver-0**에 대한 블레이드에서 **연결**을 클릭합니다. 브라우저에서 원격 연결 개체를 열거나 저장할지 여부를 묻습니다. **열기**를 클릭합니다.
5. **원격 데스크톱 연결**에서 이 원격 연결의 게시자를 식별할 수 없다고 경고할 수 있습니다. **Connect**를 클릭합니다.
6. Windows 보안은 기본 도메인 컨트롤러의 IP 주소에 연결할 자격 증명을 입력하라는 메시지를 표시합니다. **다른 계정 사용**을 클릭합니다. **사용자 이름**에 **contoso\DomainAdmin**을 입력합니다. 템플릿에서 관리자 사용자 이름을 설정할 때 이 계정을 구성했습니다. 템플릿을 구성할 때 선택한 복잡한 암호를 사용합니다.
7. **원격 데스크톱**에서 원격 컴퓨터의 보안 인증서의 문제로 인해 원격 컴퓨터를 인증할 수 없다고 경고할 수 있습니다. 보안 인증서 이름이 표시됩니다. 이 자습서를 따른 경우 이름은 **sqlserver-0.contoso.com**이 됩니다. **예**를 클릭합니다.

이제 RDP를 사용하여 SQL Server 가상 머신에 연결되었습니다. SQL Server Management Studio를 열고 SQL Server의 기본 인스턴스에 연결하여 가용성 그룹이 구성되는지 확인합니다.
