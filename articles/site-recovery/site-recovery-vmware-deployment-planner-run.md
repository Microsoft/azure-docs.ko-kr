---
title: Azure로 VMware 재해 복구를 위해 Azure Site Recovery Deployment Planner 실행 | Microsoft Docs
description: 이 문서에서는 Azure로 VMware 재해 복구를 위해 Azure Site Recovery Deployment Planner를 실행하는 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: mayg
ms.openlocfilehash: 3a6c9e50804db573395984b8ba38838eb15b0792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276738"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Azure로 VMware 재해 복구를 위해 Azure Site Recovery Deployment Planner 실행
이 문서는 VMware에서 Azure로의 프로덕션 배포를 위한 Azure Site Recovery의 Deployment Planner 사용자 가이드입니다.


## <a name="modes-of-running-deployment-planner"></a>Deployment Planner 실행 모드
다음 세 가지 모드 중 하나에서 명령줄 도구(ASRDeploymentPlanner.exe)를 실행할 수 있습니다.

1.  [프로파일링](#profile-vmware-vms)
2.  [보고서 생성](#generate-report)
3.  [처리량 가져오기](#get-throughput)

먼저 도구를 프로파일링 모드로 실행하여 VM 데이터 변동 및 IOPS를 수집합니다. 그런 다음 네트워크 대역폭, 저장소 요구 사항 및 DR 비용을 확인하기 위해 도구를 실행하여 보고서를 생성합니다.

## <a name="profile-vmware-vms"></a>VMware VM 프로파일링
프로파일링 모드에서 Deployment Planner 도구는 vCenter 서버/vSphere ESXi 호스트에 연결하여 VM에 대한 성능 데이터를 수집합니다.

* 프로파일링은 프로덕션 VM에 직접 연결되지 않았기 때문에 해당 VM의 성능에 영향을 미치지 않습니다. 모든 성능 데이터는 vCenter 서버/vSphere ESXi 호스트에서 수집됩니다.
* 프로파일링으로 인해 서버에 미치는 영향을 무시할 수 있도록 도구에서 매 15분마다 한 번씩 vCenter 서버/vSphere ESXi 호스트를 쿼리합니다. 도구는 매 분의 성능 카운터 데이터를 저장하므로 이 쿼리 간격은 프로파일링 정확도를 훼손하지 않습니다.

### <a name="create-a-list-of-vms-to-profile"></a>프로필에 VM 목록 만들기
먼저 프로파일링할 VM의 목록이 필요합니다. 다음 절차에서 VMware vSphere PowerCLI 명령을 사용하여 vCenter 서버/vSphere ESXi 호스트의 모든 VM 이름을 가져올 수 있습니다. 또는 친숙한 이름 또는 직접 프로파일링하려는 VM의 IP 주소를 파일에 나열할 수 있습니다.

1. VMware vSphere PowerCLI가 설치 된 VM에 로그인합니다.
2. VMware vSphere PowerCLI 콘솔을 엽니다.
3. 스크립트의 실행 정책이 사용하도록 설정되어 있는지 확인합니다. 사용하지 않도록 설정되어 있으면 관리자 모드에서 VMware vSphere PowerCLI 콘솔을 시작한 후 다음 명령을 실행하여 실행 정책을 사용하도록 설정합니다.

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Connect-VIServer가 cmdlet의 이름으로 인식되지 않으면 필요에 따라 다음 명령을 실행해야 할 수도 있습니다.

            Add-PSSnapin VMware.VimAutomation.Core

5. VCenter 서버/vSphere ESXi 호스트의 모든 VM의 이름을 가져오려면 .txt 파일에 목록을 저장하고 여기에 나열된 두 개의 명령을 실행합니다.
&lsaquo;서버 이름&rsaquo;, &lsaquo;사용자 이름&rsaquo;, &lsaquo;암호&rsaquo;, &lsaquo;outputfile.txt&rsaquo;을 입력 내용으로 바꿉니다.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. 메모장에서 출력 파일을 연 다음 다른 파일에 프로파일링하려는 모든 VM의 이름(예를 들어 ProfileVMList.txt)을 한 줄에 한 개씩 복사합니다. 이 파일은 명령줄 도구의 *-VMListFile* 매개 변수에 대한 입력으로 사용됩니다

    ![Deployment Planner의 VM 이름 목록
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>프로파일링 시작
프로파일링할 VM 목록이 있으면 프로파일링 모드에서 도구를 실행할 수 있습니다. 다음은 프로파일링 모드에서 실행할 도구의 필수 및 선택적 매개 변수의 목록입니다.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| 매개 변수 이름 | 설명 |
|---|---|
| -Operation | StartProfiling |
| -Server | VM을 프로파일링할 vCenter 서버/vSphere ESXi 호스트의 정규화된 도메인 이름 또는 IP 주소입니다.|
| -User | vCenter 서버/vSphere ESXi 호스트에 연결할 사용자 이름입니다. 사용자는 적어도 읽기 전용 액세스 권한을 가지고 있어야 합니다.|
| -VMListFile | 프로파일링할 VM의 목록을 포함하고 있는 파일입니다. 파일 경로는 절대 경로 또는 상대 경로일 수 있습니다. 이 파일에는 VM 이름/IP 주소가 한 줄에 하나씩 있어야 합니다. 파일에 지정된 가상 머신 이름은 vCenter 서버/vSphere ESXi 호스트의 VM 이름과 동일해야 합니다.<br>예를 들어 VMList.txt 파일에는 다음과 같은 VM이 포함되어 있습니다.<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|프로파일링을 실행할 시간(분 단위)입니다. 최소값은 30분입니다.|
|-NoOfHoursToProfile|프로파일링을 실행할 시간입니다.|
| -NoOfDaysToProfile | 프로파일링을 실행할 일 수입니다. 7일 이상 프로파일링을 실행하여 지정된 기간 동안 사용자 환경에서 워크로드 패턴을 관찰하고 정확한 권장 사항을 제공하는 데 사용하도록 하는 것이 좋습니다. |
|-Virtualization|가상화 유형(VMware 또는 Hyper-V)을 지정합니다.|
| -Directory | (선택 사항) 프로파일링 중에 생성된 프로파일링 데이터를 저장하기 위한 범용 명명 규칙(UNC) 또는 로컬 디렉터리 경로입니다. 디렉터리 이름을 지정하지 않으면 현재 경로 아래에 'ProfiledData'라는 디렉터리가 기본 디렉터리로 사용됩니다. |
| -Password | (선택 사항) vCenter server/vSphere ESXi 호스트에 연결하는 데 사용하는 암호입니다. 지금 지정하지 않으면 나중에 명령을 실행할 때 지정하도록 요구하는 메시지가 표시됩니다.|
|-Port|(선택 사항) vCenter/ESXi 호스트에 연결할 포트 번호입니다. 기본 포트는 443입니다.|
|-Protocol| (선택 사항) vCenter에 연결하기 위해 프로토콜을 'http' 또는 'https'로 지정합니다. 기본 프로토콜은 https입니다.|
| -StorageAccountName | (선택 사항) 온-프레미스 환경에서 Azure로의 데이터 복제에서 달성할 수 있는 처리량을 확인하기 위해 사용되는 저장소 계정 이름입니다. 도구에서 이 저장소 계정에 테스트 데이터를 업로드하여 처리량을 계산합니다. 저장소 계정은 GPv1(범용 v1) 형식이어야 합니다. |
| -StorageAccountKey | (선택 사항) 저장소 계정에 액세스하는 데 사용되는 저장소 계정 키입니다. Azure Portal > 저장소 계정 > <*저장소 계정 이름*> > 설정> 액세스 키 > Key1로 이동합니다. |
| -Environment | (선택 사항) 대상 Azure Storage 계정 환경입니다. AzureCloud, AzureUSGovernment, AzureChinaCloud의 3가지 값 중 하나일 수 있습니다. 기본값은 AzureCloud입니다. 대상 Azure 지역이 Azure 미국 정부 또는 Azure 중국 21Vianet 때 매개 변수를 사용 합니다. |


VM을 7일 이상 프로파일링하는 것이 좋습니다. 한 달 내에 변동 패턴이 다양한 경우, 변동폭이 최대인 주에 프로파일링하는 것이 좋습니다. 가장 좋은 방법은 31일 동안 프로파일링하여 보다 나은 권장 사항을 확보하는 것입니다. ASRDeploymentPlanner.exe는 프로파일링 기간 동안 계속 실행됩니다. 도구에서는 프로파일링 시간을 일 단위로 입력합니다. 개념 증명 또는 도구에 대한 빠른 테스트를 위해서 몇 시간 또는 몇 분 동안 프로파일링이 가능합니다. 허용되는 최소 프로파일링 시간은 30분입니다.

프로파일링 중에 구성 서버 또는 프로세스 서버에서 Azure로 복제할 때 Site Recovery에서 달성할 수 있는 처리량을 확인하기 위해 저장소 계정 이름과 키를 선택적으로 전달할 수 있습니다. 저장소 계정 이름과 키가 프로파일링 중에 전달되지 않으면 도구에서 달성할 수 있는 처리량을 계산하지 않습니다.

여러 VM 집합에 대해서는 도구의 여러 인스턴스를 실행할 수 있습니다. VM 이름이 프로파일링 집합에서 반복되지 않도록 합니다. 예를 들어 10개의 VM(VM1 - VM10)을 프로파일링하고 며칠 후에 다른 5개의 VM(VM11 - VM15)을 프로파일링하려는 경우, 두 번째 VM 집합(VM11 - VM15)에 대한 다른 명령줄 콘솔에서 도구를 실행할 수 있습니다. 그러나 두 번째 VM 집합에 첫 번째 프로파일링 인스턴스의 VM 이름이 없어야 하거나 두 번째 실행을 위해 다른 출력 디렉터리를 사용해야 합니다. 도구의 두 인스턴스가 동일한 VM을 프로파일링하고 동일한 출력 디렉터리를 사용하는 경우 생성된 보고서는 올바르지 않습니다.

기본적으로 이 도구는 최대 1,000개의 VM에 대한 보고서를 프로파일링하고 생성하도록 구성됩니다. *ASRDeploymentPlanner.exe.config* 파일에서 MaxVMsSupported 키 값을 변경하여 제한을 변경할 수 있습니다.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
예를 들어 기본 설정을 사용하여 1,500개 VM을 프로파일링하려면 두 개의 VMList.txt 파일을 만듭니다. 하나는 1,000개 VM이 포함된 목록이고, 다른 하나는 500개 VM이 포함된 목록입니다. 두 인스턴스의 VMList1.txt 및 VMList2.txt를 사용 하 여 다른 Azure Site Recovery Deployment Planner를 실행 합니다. 동일한 디렉터리 경로를 사용하여 두 VMList VM의 프로파일링된 데이터를 저장할 수 있습니다.

보고서를 생성하기 위해 도구를 실행하는 서버의 하드웨어 구성, 특히 RAM 크기를 기반으로 하면 메모리가 부족하여 작업이 실패할 수 있음을 확인했습니다. 하드웨어가 좋은 경우 MaxVMsSupported를 더 높은 값으로 변경할 수 있습니다.  

여러 vCenter 서버가 있는 경우 프로파일링을 위해 각 vCenter 서버마다 하나의 ASRDeploymentPlanner 인스턴스를 실행해야 합니다.

VM 구성은 프로파일링 작업을 시작할 때 한 번 캡처되어 VMDetailList.xml이라는 파일에 저장됩니다. 이 정보는 보고서를 생성할 때 사용 됩니다. 프로파일링을 시작할 때부터 종료할 때까지 VM 구성의 변경 내용(예를 들어 코어, 디스크 또는 NIC 수 증가)은 캡처되지 않습니다. 공개 미리 보기에서 프로파일링 과정 중에 프로파일링된 VM 구성이 변경된 경우 보고서를 생성할 때 최신 VM 세부 정보를 가져오기 위한 해결 방법은 다음과 같습니다.

* VMdetailList.xml을 백업하고 현재 위치에서 파일을 삭제합니다.
* 보고서를 생성할 때 -User 및 -Password 인수를 전달합니다.

프로파일링 명령은 프로파일링 디렉터리에 몇 개의 파일을 생성합니다. 파일을 삭제하면 보고서 생성에 영향을 주므로 파일을 삭제하지 마십시오.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>예제 1: 30일 동안 VM 프로파일링 및 온-프레미스에서 Azure로의 처리량 확인하기
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>예 2: 15일 동안 VM 프로파일링

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>예 3: 도구에 대한 빠른 테스트를 위해 60분 동안 VM 프로파일링
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>예 4: 개념 증명을 위해 2시간 동안 VM 프로파일링
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* 도구가 실행 중인 서버를 다시 부팅하거나 충돌이 발생하는 경우 또는 Ctrl + C를 사용하여 도구를 닫는 경우 프로파일링된 데이터가 유지됩니다. 그러나 프로파일링된 데이터의 마지막 15분이 누락되는 경우가 있습니다. 그러한 경우 서버를 다시 시작한 후에 프로파일링 모드에서 도구를 다시 실행합니다.
>* 저장소 계정 이름과 키가 전달되면 도구는 프로파일링의 마지막 단계에서 처리량을 측정합니다. 프로파일링을 완료하기 전에 도구가 닫히면 처리량이 계산되지 않습니다. 보고서를 생성하기 전에 처리량을 확인하려면 명령줄 콘솔에서GetThroughput 작업을 실행할 수 있습니다. 그렇지 않은 경우 생성된 보고서에 처리량 정보가 포함되지 않습니다.


## <a name="generate-report"></a>보고서 생성
이 도구는 배포의 모든 권장 사항을 요약하는 보고서 출력으로 매크로가 사용하도록 설정된 Microsoft Excel 파일(XLSM 파일)을 생성합니다. 보고서 이름은 `DeploymentPlannerReport_<unique numeric identifier>.xlsm` 지정된 된 디렉터리에 배치 합니다.

>[!NOTE]
>보고서 생성에는 Windows PC 또는 Windows Server Excel 2013 이상 필요합니다. 이 컴퓨터에 소수점으로 구성 되어야 합니다 "." 비용 예측을 생성 합니다. 설치 프로그램을 설치한 경우 "," 소수점 기호로 하세요 "변경 날짜, 시간 또는 숫자 형식" 제어판에서 이동한 소수점 기호를 변경 하려면 "추가 설정" 으로"."입니다.

프로파일링이 완료되면 보고서 생성 모드에서 도구를 실행할 수 있습니다. 다음 표는 보고서 생성 모드에서 실행할 필수 및 선택적 도구 매개 변수의 목록을 포함하고 있습니다.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|매개 변수 이름 | 설명 |
|-|-|
| -Operation | GenerateReport |
| -Server |  보고서가 생성될 프로파일링된 VM이 있는 vCenter/vSphere 서버 정규화된 도메인 이름 또는 IP 주소(프로파일링할 때 사용한 것과 동일한 이름 또는 IP 주소 사용)입니다. 프로파일링할 때 vCenter 서버를 사용한 경우 보고서 생성에 vSphere 서버를 사용할 수 없으며, 그 반대의 경우도 마찬가지입니다.|
| -VMListFile | 그에 대한 보고서가 생성될 프로파일링된 VM의 목록을 포함하고 있는 파일입니다. 파일 경로는 절대 경로 또는 상대 경로일 수 있습니다. 이 파일에는 VM 이름 또는 IP 주소가 한 줄에 하나씩 있어야 합니다. 파일에 지정된 VM 이름은 vCenter 서버/vSphere ESXi 호스트의 가상 컴퓨터 이름과 동일해야 하며, 프로파일링 중에 사용된 이름과 일치해야 합니다.|
|-Virtualization|가상화 유형(VMware 또는 Hyper-V)을 지정합니다.|
| -Directory | (선택 사항) 프로파일링된 데이터(프로파일링 중에 생성된 파일)가 저장되는 UNC 또는 로컬 디렉터리 경로입니다. 이 데이터는 보고서를 생성하는 데 필요합니다. 이름을 지정하지 않으면 'ProfiledData' 디렉터리가 사용됩니다. |
| -GoalToCompleteIR | (선택 사항) 프로파일링된 VM의 초기 복제를 완료해야 하는 시간의 수입니다. 생성된 보고서는 지정된 시간 내에 초기 복제를 완료할 수 있는 VM의 수를 제공합니다. 기본값은 72시간입니다. |
| -User | (선택 사항) vCenter/vSphere 서버에 연결하는 데 사용하는 사용자 이름입니다. 이 이름은 보고서에 사용할 디스크 수, 코어 수, NIC 수 등과 같은 VM의 최신 구성 정보를 가져오는 데 사용됩니다. 이름을 제공하지 않으면 프로파일링을 시작할 때 수집되는 구성 정보가 사용됩니다. |
| -Password | (선택 사항) vCenter server/vSphere ESXi 호스트에 연결하는 데 사용하는 암호입니다. 매개 변수로 암호를 지정하지 않으면 나중에 명령을 실행할 때 지정하도록 요구하는 메시지가 표시됩니다. |
|-Port|(선택 사항) vCenter/ESXi 호스트에 연결할 포트 번호입니다. 기본 포트는 443입니다.|
|-Protocol|(선택 사항) vCenter에 연결하기 위해 프로토콜을 'http' 또는 'https'로 지정합니다. 기본 프로토콜은 https입니다.|
| -DesiredRPO | (선택 사항) 원하는 복구 지점 목표(분)입니다. 기본값은 15분입니다.|
| -Bandwidth | 대역폭(Mbps)입니다. 지정된 대역폭에서 달성할 수 있는 RPO를 계산하는 데 사용하는 매개 변수입니다. |
| -StartDate | (선택 사항) MM-DD-YYYY:HH:MM 단위의 시작 날짜 및 시간(24시간 형식)입니다. *StartDate*는 *EndDate*와 함께 지정해야 합니다. StartDate를 지정한 경우 StartDate와 EndDate 사이에 수집한 프로파일링된 데이터에 대한 보고서를 생성합니다. |
| -EndDate | (선택 사항) MM-DD-YYYY:HH:MM 형식(24시간 형식)의 종료 날짜 및 시간입니다. *EndDate*는 *StartDate*와 함께 지정해야 합니다. EndDate를 지정한 경우 StartDate와 EndDate 사이에 수집한 프로파일링된 데이터에 대한 보고서가 생성됩니다. |
| -GrowthFactor | (선택 사항) 백분율로 표시된 증가율입니다. 기본값은 30%입니다. |
| -UseManagedDisks | (선택 사항)UseManagedDisks - 예/아니요. 기본값은 [예]입니다. 단일 저장소 계정에 배치할 수 있는 가상 머신의 수는 가상 머신의 장애 조치/테스트 장애 조치가 관리되지 않는 디스크가 아닌 Managed Disk에서 수행되었음을 고려하여 계산됩니다. |
|-SubscriptionId |(선택 사항) 구독 GUID입니다. 구독, 구독과 연결된 제안 및 특정 대상 Azure 지역의 제안에 기반한 최신 가격과 **지정된 통화**로 비용 예측 보고서를 생성하려면 이 매개 변수가 필요합니다.|
|-TargetRegion|(선택 사항) 복제 대상이 되는 Azure 지역입니다. Azure는 지역마다 비용이 다르기 때문에 특정 대상 Azure 지역으로 보고서를 생성하려면 이 매개 변수를 사용합니다.<br>기본값은 WestUS2 또는 마지막으로 사용된 대상 지역입니다.<br>[지원되는 대상 지역](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) 목록을 참조하세요.|
|-OfferId|(선택 사항) 주어진 구독과 연결된 제안입니다. 기본값은 MS-AZR-0003P(종량제)입니다.|
|-Currency|(선택 사항) 생성된 보고서의 비용 표시에 사용되는 통화입니다. 기본값은 미국 달러($) 또는 마지막 사용한 통화입니다.<br>[지원되는 통화](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies) 목록을 참조하세요.|

기본적으로 이 도구는 최대 1,000개의 VM에 대한 보고서를 프로파일링하고 생성하도록 구성됩니다. *ASRDeploymentPlanner.exe.config* 파일에서 MaxVMsSupported 키 값을 변경하여 제한을 변경할 수 있습니다.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>예제 1: 프로파일링된 데이터가 로컬 드라이브에 있는 경우 기본값으로 보고서 생성
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>예 2: 프로파일링된 데이터가 원격 서버에 있는 경우 보고서 생성
사용자는 원격 디렉터리에서 읽기/쓰기 액세스 권한을 가지고 있어야 합니다.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>예 3: 지정된 시간 내에 IR을 완료하기 위한 특정 대역폭 및 목표로 보고서 생성
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>예 4: 기본값 30% 대신 5% 증가율로 보고서 생성
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>예 5: 프로파일링된 데이터의 하위 집합으로 보고서 생성
예를 들어 30일 동안 프로파일링된 데이터가 있지만 20일 동안만의 보고서를 생성하려고 합니다.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>예 6: 5분 동안의 RPO 보고서 생성
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>예 7: 인도 루피 및 특정 제품 ID로 인도 남부 Azure 지역에 대한 보고서 생성

특정 통화로 비용 보고서를 작성하려면 구독 ID가 필요합니다.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>계산에 사용된 백분위 수 값
**도구가 보고서를 생성할 때 사용하는 프로파일링 중에 수집된 성능 메트릭의 기본 백분위수 값은 얼마인가요?**

도구에서는 모든 VM을 프로파일링하는 동안 수집된 읽기/쓰기 IOPS, 쓰기 IOPS 및 데이터 변동의 95번째 백분위수 값을 기본적으로 사용합니다. 이 메트릭은 사용자의 VM이 일시적 이벤트 때문에 볼 수 있는 100번째 백분위수 급증을 대상 저장소 계정 및 원본 대역폭 요구 사항을 결정하는 데 사용되지 않도록 합니다. 예를 들어 일시적 이벤트는 하루에 한 번 실행하는 백업 작업, 주기적 데이터베이스 인덱싱 또는 분석 보고서 생성 작업 또는 기타 유사한 단기적 시점 이벤트일 수 있습니다.

95번째 백분위수 값을 사용하면 실제 워크로드 특성을 정확히 보여 주며, Azure에서 워크로드를 실행할 때 최상의 성능을 제공합니다. 당사는 사용자가 이 숫자를 변경해야 한다고 예상하지 않습니다. 값을 변경(예를 들어 90번째 백분위수로)하지 않은 경우 기본 폴더의 구성 파일 *ASRDeploymentPlanner.exe.config*를 업데이트하고 기존 프로파일링된 데이터에 대한 새 보고서를 생성하기 위해 저장할 수 있습니다.
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>증가율 고려 사항
**배포를 계획할 때 증가율을 왜 고려해야 하나요?**

시간이 지남에 따라 잠재적 사용량이 증가할 것으로 가정할 때 워크로드 특성의 증가를 고려하는 것이 중요합니다. 보호가 설정된 후 워크로드 특성이 변경되는 경우 보호를 해제하고 다시 사용하도록 설정하지 않고는 다른 저장소 계정으로 전환하여 보호할 수 없기 때문입니다.

예를 들어 현재 VM이 표준 저장소 복제 계정에 적합하다고 가정해 보겠습니다. 다음 3개월 동안 몇 가지 변경 사항이 발생할 가능성이 있습니다.

* VM에서 실행되는 애플리케이션의 사용자 수가 증가합니다.
* 그 결과 VM에서 변동이 증가하므로 Site Recovery 복제가 일어날 수 있도록 VM을 Premium Storage로 이동해야 합니다.
* 따라서 Premium Storage 계정에 대한 보호를 해제하고 다시 사용하도록 설정해야 합니다.

배포 계획 중에 그리고 기본값이 30%인 동안 증가에 대해 계획할 것을 적극 권장합니다. 사용자는 애플리케이션 사용 패턴과 증가 예측에 대한 전문가이며 보고서를 생성하는 동안 이 숫자를 해당 계획에 따라 변경할 수 있습니다. 또한 사용자는 여러 증가율 및 동일한 프로파일링된 데이터가 포함된 여러 보고서를 생성하고 가장 적합한 대상 저장소 및 원본 대역폭 권장 사항을 결정할 수 있습니다.

생성된 Microsoft Excel 보고서에는 다음과 같은 정보가 포함되어 있습니다.

* [온-프레미스 요약](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [권장 사항](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM<->저장소 배치](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [호환되는 VM](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [호환되지 않는 VM](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [비용 예측](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>처리량 가져오기

Site Recovery에서 복제 중에 온-프레미스 환경에서 Azure로 달성할 수 있는 처리량을 추정하려면 GetThroughput 모드에서 도구를 실행합니다. 도구에서 도구가 실행 중인 서버에서 처리량을 계산합니다. 이 서버는 구성 서버 크기 조정 가이드를 기반으로 합니다. Site Recovery 인프라 구성 요소를 온-프레미스에 이미 배포한 경우 구성 서버에서 도구를 실행합니다.

명령줄 콘솔을 열고 Site Recovery 배포 계획 도구 폴더로 이동합니다. 다음 매개 변수를 사용하여 ASRDeploymentPlanner.exe를 실행합니다.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|매개 변수 이름 | 설명 |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|가상화 유형(VMware 또는 Hyper-V)을 지정합니다.|
| -Directory | (선택 사항) 프로파일링된 데이터(프로파일링 중에 생성된 파일)가 저장되는 UNC 또는 로컬 디렉터리 경로입니다. 이 데이터는 보고서를 생성하는 데 필요합니다. 디렉터리 이름을 지정하지 않으면 'ProfiledData' 디렉터리가 사용됩니다. |
| -StorageAccountName | 온-프레미스 환경에서 Azure로의 데이터 복제에서 사용되는 대역폭을 확인하기 위해 사용하는 저장소 계정 이름입니다. 도구에서 이 저장소 계정에 테스트 데이터를 업로드하여 사용되는 대역폭을 찾습니다. 저장소 계정은 GPv1(General-purpose v1) 형식 중 하나여야 합니다.|
| -StorageAccountKey | 저장소 계정에 액세스하는 데 사용되는 저장소 계정 키입니다. Azure Portal > Storage 계정 > <*Storage 계정 이름*> > 설정 > 선택키 > Key1(또는 클래식 Storage 계정의 기본 선택키)로 차례로 이동합니다. |
| -VMListFile | 사용되는 대역폭을 계산하기 위해 프로파일링할 VM의 목록을 포함하고 있는 파일입니다. 파일 경로는 절대 경로 또는 상대 경로일 수 있습니다. 이 파일에는 VM 이름/IP 주소가 한 줄에 하나씩 있어야 합니다. 파일에 지정된 VM 이름은 vCenter 서버/vSphere ESXi 호스트의 VM 이름과 동일해야 합니다.<br>예를 들어 VMList.txt 파일에는 다음과 같은 VM이 포함되어 있습니다.<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (선택 사항) 대상 Azure Storage 계정 환경입니다. AzureCloud, AzureUSGovernment, AzureChinaCloud의 3가지 값 중 하나일 수 있습니다. 기본값은 AzureCloud입니다. 대상 Azure 지역이 Azure 미국 정부 또는 Azure 중국 21Vianet 때 매개 변수를 사용 합니다. |

이 도구는 지정된 디렉터리에 여러 개의 asrvhdfile<#>.vhd(여기서 #은 파일 수) 64MB 파일을 만듭니다. 도구에서 처리량을 확인하기 위해 저장소 계정에 파일을 업로드합니다. 처리량이 측정된 후 저장소 계정과 로컬 서버에서 이러한 파일을 모두 삭제합니다. 도구가 처리량을 계산하는 동안 어떤 이유로든 종료되는 경우 저장소 또는 로컬 서버에서 파일을 삭제하지 않습니다. 따라서 직접 삭제해야 합니다.

처리량은 특정 시점에서 측정되며, 다른 모든 요소가 동일하게 유지된다는 조건 하에 복제 중에 Site Recovery에서 달성할 수 있는 최대 처리량입니다. 예를 들어 애플리케이션이 동일한 네트워크에서 더 많은 대역폭을 사용하기 시작하면 복제 중에 실제 처리량이 달라집니다. 구성 서버에서 GetThroughput 명령을 실행하는 경우 이 도구는 보호된 VM과 진행 중인 복제를 인식하지 못합니다. VM에 높은 데이터 변동이 있을 때 GetThroughput 작업이 실행되는 경우 측정된 처리량의 결과가 다릅니다. 프로파일링 중에 여러 시점에서 도구를 실행하여 다양한 시간에서 어떤 처리량 수준을 달성할 수 있는지 이해하는 것이 좋습니다. 보고서에는 도구에서 마지막으로 측정한 처리량이 표시됩니다.

### <a name="example"></a>예
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> 구성 서버와 동일한 저장소 및 CPU 특성을 포함한 서버에서 도구를 실행합니다.
>
> 복제의 경우 RPO 시간 100%를 충족하기 위해 권장되는 대역폭을 설정합니다. 올바른 대역폭을 설정한 후 도구에서 보고한 달성된 처리량이 증가하지 않으면 다음을 수행합니다.
>
>  1. Site Recovery 처리량을 제한하고 있는 네트워크 QoS(서비스 품질)가 있는지 확인합니다.
>
>  2. 네트워크 대기 시간을 최소화하기 위해 Site Recovery 자격 증명 모음이 물리적으로 지원되는 가장 가까운 Microsoft Azure 지역에 있는지 확인합니다.
>
>  3. 로컬 저장소 특성을 확인하여 하드웨어(예를 들어 SSD HDD)를 향상시킬 수 있는지 여부를 결정합니다.
>
>  4. 프로세스 서버의 Site Recovery 설정을 변경하여 [복제에 사용되는 네트워크 대역폭의 양을 증가](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)시킵니다.

## <a name="next-steps"></a>다음 단계
* [생성된 보고서 분석](site-recovery-vmware-deployment-planner-analyze-report.md)
