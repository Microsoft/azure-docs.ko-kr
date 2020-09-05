---
title: WSFC 및 Azure 공유 디스크를 사용 하는 SAP ASCS/SCS 다중 SID HA | Microsoft Docs
description: WSFC 및 Azure 공유 디스크를 사용 하는 SAP ASCS/SCS 인스턴스의 다중 SID 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4856b2578a007f72aeeec64588ac7f9c58158de
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861184"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>Windows server 장애 조치 (failover) 클러스터링 및 Azure 공유 디스크를 사용 하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성

> ![Windows OS][Logo_Windows] Windows
>

이 문서에서는 Azure 공유 디스크를 사용 하 여 기존 WSFC (Windows Server 장애 조치 (Failover) 클러스터링) 클러스터에 추가 SAP ASCS/SCS 클러스터형 인스턴스를 설치 하 여 단일 ASCS/SCS 설치에서 SAP 다중 SID 구성으로 이동 하는 방법을 중점적으로 설명 합니다. 이 프로세스가 완료되면 SAP 다중 SID 클러스터가 구성됩니다.

## <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

현재 SAP ASCS/SCS 인스턴스에 대 한 Azure 공유 디스크로 Azure 프리미엄 SSD 디스크를 사용할 수 있습니다. 다음과 같은 제한 사항이 적용 됩니다.

-  [Azure Ultra disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) 는 SAP 워크 로드에 대 한 Azure 공유 디스크로 지원 되지 않습니다. 현재 가용성 집합에서 Azure Ultra Disk를 사용 하 여 Azure Vm을 사용할 수 없습니다.
-  프리미엄 SSD 디스크가 있는 [Azure 공유 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared) 는 가용성 집합의 vm 에서만 지원 됩니다. 가용성 영역 배포에서는 지원 되지 않습니다. 
-  Azure 공유 디스크 값 [Maxshares](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable?tabs=azure-cli#disk-sizes) 는 공유 디스크를 사용할 수 있는 클러스터 노드 수를 결정 합니다. 일반적으로 SAP ASCS/SCS 인스턴스의 경우 Windows 장애 조치 (Failover) 클러스터에서 두 노드를 구성 하므로의 값을 `maxShares` 2로 설정 해야 합니다.
-  모든 SAP ASCS/SCS 클러스터 Vm은 동일한 [Azure 근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)에 배포 되어야 합니다.   
   PPG 없이 Azure 공유 디스크를 사용 하 여 가용성 집합에 Windows 클러스터 Vm을 배포할 수 있지만, PPG를 사용 하면 Azure 공유 디스크와 클러스터 Vm의 물리적인 근접 한 공간을 확보 하 여 Vm과 저장소 계층 간의 대기 시간을 줄일 수 있습니다.    

Azure 공유 디스크의 제한 사항에 대 한 자세한 내용은 Azure 공유 디스크 설명서의 [제한 사항](https://docs.microsoft.com/azure/virtual-machines/linux/disks-shared#limitations) 섹션을 참조 하세요.  

> [!IMPORTANT]
> Azure 공유 디스크를 사용 하 여 SAP ASCS/SCS Windows 장애 조치 (Failover) 클러스터를 배포 하는 경우 배포는 하나의 저장소 클러스터에서 단일 공유 디스크로 작동 한다는 점에 유의 해야 합니다. Azure 공유 디스크가 배포 되는 저장소 클러스터에 문제가 있는 경우 SAP ASCS/SCS 인스턴스가 영향을 받습니다.  

> [!IMPORTANT]
> 설치 프로그램은 다음 조건을 충족해야 합니다.
> * 각 DBMS(데이터베이스 관리 시스템) SID에는 해당하는 고유한 전용 WSFC 클러스터가 있어야 합니다.  
> * 하나의 SAP 시스템 SID에 속하는 SAP 애플리케이션 서버에는 고유한 전용 VM이 있어야 합니다.  
> * 동일한 클러스터에서 큐에 넣기 복제 서버 1과 큐에 넣기 복제 서버 2를 함께 사용할 수 없습니다.  


## <a name="supported-os-versions"></a>지원된 OS 버전

Windows Server 2016 및 Windows Server 2019이 모두 지원 됩니다 (최신 데이터 센터 이미지 사용).

다음과 같이 **Windows Server 2019 Datacenter**를 사용 하는 것이 좋습니다.
- Windows 2019 장애 조치 (Failover) 클러스터 서비스가 Azure를 인식 합니다.
- Azure 일정 이벤트를 모니터링 하 여 Azure 호스트 유지 관리 및 향상 된 환경에 대 한 통합 및 인식 기능이 추가 되었습니다.
- 분산 네트워크 이름 (기본 옵션)을 사용할 수 있습니다. 따라서 클러스터 네트워크 이름에 대 한 전용 IP 주소를 가질 필요가 없습니다. 또한 Azure 내부 Load Balancer에서이 IP 주소를 구성할 필요가 없습니다. 

## <a name="architecture"></a>아키텍처

다중 SID 구성에서는 큐에 넣기 복제 서버 1 (ERS1)과 큐에 넣기 복제 서버 2 (ERS2)가 모두 지원 됩니다.  ERS1와 ERS2의 혼합은 동일한 클러스터에서 지원 되지 않습니다. 

1. 첫 번째 예제에서는 ERS1 아키텍처를 사용 하는 두 개의 SAP Sid를 보여 줍니다.

   - SAP SID1는 공유 디스크, ERS1에 배포 됩니다. ERS 인스턴스는 로컬 호스트와 로컬 드라이브에 설치 됩니다.
     SAP SID1에는 Azure 내부 부하 분산 장치에 구성 된 자체 (가상) IP 주소 (SID1 (A) SCS I P 1)가 있습니다.

   - SAP SID2는 공유 디스크, ERS1에 배포 됩니다. ERS 인스턴스는 로컬 호스트와 로컬 드라이브에 설치 됩니다.
     SAP SID2에는 Azure 내부 부하 분산 장치에도 구성 된 자체 (가상) IP 주소 (SID2 (A) SCS IP2)가 있습니다.

![고가용성 SAP ASCS/SCS 인스턴스-ERS1 구성을 사용 하는 두 개의 인스턴스][sap-ha-guide-figure-6007]

2. 두 번째 예제에서는 ERS2 아키텍처를 사용 하는 두 개의 SAP Sid를 보여 줍니다. 

   - ERS2를 사용 하는 SAP SID1는 클러스터 된 상태에서 로컬 드라이브에 배포 됩니다.  
     SAP SID1에는 Azure 내부 부하 분산 장치에 구성 된 자체 (가상) IP 주소 (SID1 (A) SCS I P 1)가 있습니다.
     Sap SID1 시스템에서 사용 하는 SAP ERS2에는 Azure 내부 부하 분산 장치에 구성 된 자체 (가상) IP 주소 (SID1 ERS2 IP2)가 있습니다.

   - ERS2를 사용 하는 SAP SID2는 클러스터 된 상태에서 로컬 드라이브에 배포 됩니다.  
     SAP SID2에는 Azure 내부 부하 분산 장치에 구성 된 자체 (가상) IP 주소 (SID2 (A) SCS P 3)가 있습니다.
     Sap SID2 시스템에서 사용 하는 SAP ERS2에는 Azure 내부 부하 분산 장치에 구성 된 자체 (가상) IP 주소 (SID2 ERS2 IP4)가 있습니다.

   여기에는 총 4 개의 가상 IP 주소가 있습니다.  
   - SID1 (A) SCS I P 1
   - SID2 ERS2 IP2
   - SID2 (A) SCS P 3
   - SID2 ERS2 IP4

![고가용성 SAP ASCS/SCS 인스턴스-ERS1 및 ERS2 구성을 사용 하는 두 개의 인스턴스][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>인프라 준비

**기존 클러스터형** sap **PR1** ascs/SCS 인스턴스와 함께 새 sap SID **p r 2**를 설치 합니다.  

### <a name="host-names-and-ip-addresses"></a>호스트 이름 및 IP 주소

| 호스트 이름 역할 | 호스트 이름 | 고정 IP 주소 | 가용성 집합 | 근접 배치 그룹 |
| --- | --- | --- |---| ---|
| 첫 번째 클러스터 노드 ASCS/SCS 클러스터 |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 두 번째 클러스터 노드 ASCS/SCS 클러스터 |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| 클러스터 네트워크 이름 | pr1clust |10.0.0.42 (Win 2016 클러스터에**만** 해당) | 해당 없음 | 해당 없음 |
| **SID1** ASCS 클러스터 네트워크 이름 | pr1-ascscl |10.0.0.43 | 해당 없음 | 해당 없음 |
| **SID1** ERS 클러스터 네트워크 이름 (ERS2에**만** 해당) | pr1-erscl |10.0.0.44 | 해당 없음 | 해당 없음 |
| **SID2** ASCS 클러스터 네트워크 이름 | p r 2-ascscl |에서 10.0.0.45 | 해당 없음 | 해당 없음 |
| **SID2** ERS 클러스터 네트워크 이름 (ERS2에**만** 해당) | pr1-erscl |10.0.0.46 | 해당 없음 | 해당 없음 |

### <a name="create-azure-internal-load-balancer"></a>Azure 내부 부하 분산 장치 만들기

SAP ASCS, SAP SCS 및 새 SAP ERS2 가상 호스트 이름 및 가상 IP 주소를 사용 합니다. Azure에서 [부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 는 가상 IP 주소를 사용 하는 데 필요 합니다. [표준 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)를 사용 하는 것이 좋습니다. 

두 번째 SAP SID ASCS/SCS/ERS 인스턴스 **p r 2**에 대 한 구성을 기존 부하 분산 장치에 추가 해야 합니다. 첫 번째 SAP SID **PR1** 의 구성이 이미 준비 되어 있어야 합니다.  

**은 SCS P R 2 [instance number 02]**
- 프런트 엔드 구성
    - 정적 ASCS/SCS IP 주소 **에서 10.0.0.45**
- 백 엔드 구성  
    이미 준비 됨-Vm이 이미 백 엔드 풀에 추가 된 동안 SAP SID **PR1** 를 구성 하는 중입니다.
- 프로브 포트
    - 포트 620**nr** [**62002**] 프로토콜 (TCP), 간격 (5), 비정상 임계값 (2)에 대 한 기본 옵션을 그대로 둡니다.
- 부하 분산 규칙
    - 표준 Load Balancer를 사용하는 경우 HA 포트를 선택합니다.
    - 기본 Load Balancer를 사용하는 경우 다음 포트에 대한 부하 분산 규칙을 만듭니다.
        - 32**nr** TCP [**3202**]
        - 36**nr** TCP [**3602**]
        - 39**nr** TCP [**3902**]
        - 81**nr** TCP [**8102**]
        - 5**nr**13 TCP [**50213**]
        - 5**nr**14 TCP [**50214**]
        - 5**nr**16 TCP [**50216**]
        - **P r 2** Ascs 프런트 엔드 IP, 상태 프로브 및 기존 백 엔드 풀과 연결 합니다.  

    - 유휴 시간 제한 (분)이 최대값인 30으로 설정 되어 있고 부동 IP (direct server return)가 사용 하도록 설정 되어 있는지 확인 합니다.

**ERS2 P R 2 [인스턴스 번호 12]** 

큐에 대기 중인 복제 서버 2 (ERS2)도 클러스터 됨에 따라 SAP ASCS/SCS IP 외에도 Azure ILB에서 ERS2 가상 IP 주소를 구성 해야 합니다. 이 섹션은 **p r 2**에 대해 큐에 넣기 복제 서버 2 아키텍처를 사용 하는 경우에만 적용 됩니다.  
- 새 프런트 엔드 구성
    - 정적 SAP ERS2 IP 주소 **10.0.0.46**

- 백 엔드 구성  
  Vm이 ILB 백 엔드 풀에 이미 추가 되었습니다.  

- 새 프로브 포트
    - 포트 621**nr**  [**62112**] 프로토콜 (TCP), 간격 (5), 비정상 임계값 (2)에 대 한 기본 옵션을 그대로 둡니다.

- 새 부하 분산 규칙
    - 표준 Load Balancer를 사용하는 경우 HA 포트를 선택합니다.
    - 기본 Load Balancer를 사용하는 경우 다음 포트에 대한 부하 분산 규칙을 만듭니다.
        - 32**nr** TCP [**3212**]
        - 33**nr** TCP [**3312**]
        - 5**nr**13 TCP [**51212**]
        - 5**nr**14 TCP [**51212**]
        - 5**nr**16 TCP [**51212**]
        - **P r 2** ERS2 프런트 엔드 IP, 상태 프로브 및 기존 백 엔드 풀과 연결 합니다.  

    - 유휴 시간 제한 (분)이 max 값 (예: 30)으로 설정 되어 있고 부동 IP (direct server return)가 사용 하도록 설정 되어 있는지 확인 합니다.


### <a name="create-and-attach-second-azure-shared-disk"></a>두 번째 Azure 공유 디스크 만들기 및 연결

클러스터 노드 중 하나에서이 명령을 실행 합니다. 리소스 그룹, Azure 지역, SAPSID 등에 대 한 값을 조정 해야 합니다.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>PowerShell을 사용 하 여 공유 디스크 포맷
1. 디스크 번호를 가져옵니다. 클러스터 노드 중 하나에서 PowerShell 명령을 실행 합니다.

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. 디스크를 포맷 합니다. 이 예제에서는 디스크 번호 3입니다. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. 이제 디스크가 클러스터 디스크로 표시 되는지 확인 합니다.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. 클러스터에 디스크를 등록 합니다.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기

1. Windows DNS 관리자에서 새 SAP ASCS/SCS 인스턴스의 가상 호스트 이름에 대 한 DNS 항목을 만듭니다.  
   DNS의 가상 호스트 이름에 할당 하는 IP 주소는 Azure Load Balancer에서 할당 한 IP 주소와 동일 해야 합니다.  

   ![SAP ASCS/SCS 클러스터 가상 이름 및 IP 주소에 대 한 DNS 항목을 _Define 합니다.][sap-ha-guide-figure-6009]
 
   _SAP ASCS/SCS 클러스터 가상 이름 및 IP 주소에 대 한 DNS 항목을 정의 합니다._

2. 클러스터 된 인스턴스인 SAP 큐에 넣기 복제 서버 2를 사용 하는 경우 ERS2에 대 한 가상 호스트 이름도 DNS에 예약 해야 합니다. 
   DNS에서 ERS2의 가상 호스트 이름에 할당 하는 IP 주소는 Azure Load Balancer에서 할당 한 IP 주소와 동일 해야 합니다.  

   ![SAP ERS2 클러스터 가상 이름 및 IP 주소에 대 한 DNS 항목을 _Define 합니다.][sap-ha-guide-figure-6010]
 
   _SAP ERS2 클러스터 가상 이름 및 IP 주소에 대 한 DNS 항목을 정의 합니다._

3. 가상 호스트 이름에 할당 된 IP 주소를 정의 하려면 **DNS 관리자**  >  **도메인**을 선택 합니다.

   ![SAP ASCS/SCS 및 ERS2 클러스터 구성에 대 한 새 가상 이름 및 IP 주소][sap-ha-guide-figure-6011]

   _SAP ASCS/SCS 및 ERS2 클러스터 구성에 대 한 새 가상 이름 및 TCP/IP 주소_

## <a name="sap-installation"></a>SAP 설치 

### <a name="install-the-sap-first-cluster-node"></a> SAP 첫 번째 클러스터 노드 설치

SAP에 설명 된 설치 절차를 따릅니다. 설치 시작 옵션 "첫 번째 클러스터 노드"를 확인 하 고 "클러스터 공유 디스크"를 구성 옵션으로 선택 합니다.  
새로 만들기 공유 디스크를 선택 합니다.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a> ASCS/SCS 인스턴스의 SAP 프로필 수정

큐에 넣기 복제 서버 1을 실행 하는 경우 아래 설명 된 대로 SAP 프로필 매개 변수를 추가 `enque/encni/set_so_keepalive` 합니다. 이 프로필 매개 변수는 연결이 너무 오랫동안 유휴 상태일 때 SAP 작업 프로세스와 큐에 넣기 서버 사이의 연결이 닫히지 않도록 합니다. ERS2에는 SAP 매개 변수가 필요 하지 않습니다. 

1. ERS1를 사용 하는 경우이 프로필 매개 변수를 SAP ASCS/SCS 인스턴스 프로필에 추가 합니다.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   ERS1 및 ERS2 둘 다에 대해 `keepalive` SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)에 설명 된 대로 OS 매개 변수를 설정 해야 합니다.   

2. SAP 프로필 매개 변수 변경 내용을 적용 하려면 SAP ASCS/SCS 인스턴스를 다시 시작 합니다.

### <a name="configure-probe-port-on-the-cluster-resource"></a>클러스터 리소스에서 프로브 포트 구성

내부 부하 분산 장치의 프로브 기능을 사용하여 전체 클러스터 구성이 Azure Load Balancer에서 작동하도록 합니다. 일반적으로 Azure 내부 부하 분산 장치는 참여하는 가상 머신 간에 동일하게 들어오는 작업 부하를 분산합니다.

그러나 하나의 인스턴스만 활성 상태가 되므로 일부 클러스터 구성에서는 작동하지 않습니다. 다른 인스턴스는 수동 상태이므로 워크로드를 받아들일 수 없습니다. 프로브 기능을 사용 하면 Azure 내부 부하 분산 장치가 활성 상태인 인스턴스를 감지 하 고 활성 인스턴스만 대상으로 할 때 도움이 됩니다.  

> [!IMPORTANT]
> 이 예제 구성에서 **ProbePort** 는 620**Nr**로 설정 됩니다. 숫자가 **02** 인 SAP ascs 인스턴스의 경우 620**02**입니다.
> SAP 인스턴스 번호 및 SAP SID와 일치 하도록 구성을 조정 해야 합니다.

프로브 포트를 추가 하려면 클러스터 Vm 중 하나에서이 PowerShell 모듈을 실행 합니다.

- 인스턴스 번호가 **02** 인 SAP ASC/SCS 인스턴스의 경우 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- ERS2를 사용 하는 경우이 고, 인스턴스 번호 **12**는 클러스터형입니다. ERS1에 대 한 프로브 포트는 클러스터 되지 않으므로 구성할 필요가 없습니다.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 함수 코드는 `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` 다음과 같습니다.
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>SAP 설치 계속

1. SAP 설치 가이드에 설명 된 프로세스에 따라 데이터베이스 인스턴스를 설치 합니다.  
2. SAP 설치 가이드에 설명 된 단계를 수행 하 여 두 번째 클러스터 노드에 SAP를 설치 합니다.  
3. PAS를 호스팅하도록 지정한 가상 컴퓨터에 SAP 기본 PAS (기본 응용 프로그램 서버) 인스턴스를 설치 합니다.   
   SAP 설치 가이드에 설명 된 프로세스를 따릅니다. Azure와는 관련이 없습니다.
4. SAP 응용 프로그램 서버 인스턴스를 호스팅하도록 지정 된 가상 컴퓨터에 추가 SAP 응용 프로그램 서버를 설치 합니다.  
   SAP 설치 가이드에 설명 된 프로세스를 따릅니다. Azure와는 관련이 없습니다. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>SAP ASCS/SCS 인스턴스 장애 조치 (failover) 테스트
앞서 설명한 장애 조치 (failover) 테스트에서는 SAP ASCS가 노드 A에서 활성화 되어 있다고 가정 합니다.  

1. SAP 시스템이 노드 A에서 노드 B로 성공적으로 장애 조치 (failover) 할 수 있는지 확인 합니다. 이 예제에서는 SAPSID **p r 2**에 대해 테스트가 수행 됩니다.  
   각 SAPSID를 다른 클러스터 노드로 성공적으로 이동할 수 있는지 확인 합니다.   
   \<SID\>클러스터 노드 a에서 클러스터 노드 B로 SAP 클러스터 그룹의 장애 조치 (failover)를 시작 하려면 다음 옵션 중 하나를 선택 합니다.
    - 장애 조치(failover) 클러스터 관리자  
    - 장애 조치 클러스터 PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Windows 게스트 운영 체제 내에서 클러스터 노드 A를 다시 시작합니다. 그러면 \<SID\> 노드 A에서 노드 B로 SAP 클러스터 그룹의 자동 장애 조치 (failover)가 시작 됩니다.  
3. Azure Portal에서 클러스터 노드 A를 다시 시작합니다. 그러면 \<SID\> 노드 A에서 노드 B로 SAP 클러스터 그룹의 자동 장애 조치 (failover)가 시작 됩니다.  
4. Azure PowerShell을 사용하여 클러스터 노드 A를 다시 시작합니다. 그러면 \<SID\> 노드 A에서 노드 B로 SAP 클러스터 그룹의 자동 장애 조치 (failover)가 시작 됩니다.

## <a name="next-steps"></a>다음 단계

* [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치 (failover) 클러스터 및 공유 디스크를 사용 하 여 SAP HA 용 Azure 인프라 준비][sap-high-availability-infrastructure-wsfc-shared-disk]
* [SAP ASCS/SCS 인스턴스에 대한 Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP NetWeaver HA 설치][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md