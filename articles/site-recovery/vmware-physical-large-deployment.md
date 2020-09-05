---
title: Azure Site Recovery를 사용 하 여 VMware/물리적 재해 복구 크기 조정
description: Azure Site Recovery를 사용 하 여 많은 수의 온-프레미스 VMware Vm 또는 물리적 서버에 대해 Azure에 대 한 재해 복구를 설정 하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 101e42263e46c5a21f26b0fa9cdeed798525fee9
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047086"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>VMware v m/물리적 서버에 대해 대규모 재해 복구 설정

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용 하 여 프로덕션 환경에서 온-프레미스 VMware vm 또는 물리적 서버의 대량 (> 1000)에 대해 Azure에 대 한 재해 복구를 설정 하는 방법을 설명 합니다.


## <a name="define-your-bcdr-strategy"></a>BCDR 전략 정의

BCDR (비즈니스 연속성 및 재해 복구) 전략의 일부로 비즈니스 앱 및 워크 로드에 대 한 Rpo (복구 지점 목표) 및 RTOs (복구 시간 목표)를 정의 합니다. RTO는 연속성 문제를 방지 하기 위해 비즈니스 앱 또는 프로세스를 복원 및 사용 해야 하는 시간 및 서비스 수준을 측정 합니다.
- Site Recovery는 VMware Vm 및 물리적 서버에 대 한 지속적인 복제와 RTO에 대 한 [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 를 제공 합니다.
- VMware Vm에 대 한 대규모 재해 복구를 계획 하 고 필요한 Azure 리소스를 파악 하려면 용량 계산에 사용할 RTO 값을 지정 하면 됩니다.


## <a name="best-practices"></a>모범 사례

대규모 재해 복구에 대 한 몇 가지 일반적인 모범 사례입니다. 이러한 모범 사례는 문서의 다음 섹션에 자세히 설명 되어 있습니다.

- **대상 요구 사항 식별**: 재해 복구를 설정 하기 전에 Azure에서 용량과 리소스 요구를 예측 합니다.
- **Site Recovery 구성 요소 계획**: 예상 용량을 충족 하는 데 필요한 Site Recovery 구성 요소 (구성 서버, 프로세스 서버)를 파악 합니다.
- **하나 이상의 스케일 아웃 프로세스 서버 설정**: 구성 서버에서 기본적으로 실행 되는 프로세스 서버를 사용 하지 않습니다. 
- **최신 업데이트 실행**: Site Recovery 팀은 정기적으로 새 버전의 Site Recovery 구성 요소를 릴리스 하 고 최신 버전을 실행 하 고 있는지 확인 해야 합니다. 이 작업을 돕기 위해 업데이트의 [새로운 기능](site-recovery-whats-new.md) 을 추적 하 고 릴리스 시 [업데이트를 사용 하도록 설정 하 고 설치](service-updates-how-to.md) 합니다.
- **사전 모니터링**: 재해 복구를 시작할 때 복제 된 컴퓨터 및 인프라 리소스의 상태를 사전에 모니터링 해야 합니다.
- **재해 복구 훈련**: 정기적으로 재해 복구 훈련을 실행 해야 합니다. 이러한 작업은 프로덕션 환경에는 영향을 주지 않지만 필요한 경우 Azure로의 장애 조치 (failover)가 예상 대로 작동 하는지 확인 하는 데 도움이 됩니다.



## <a name="gather-capacity-planning-information"></a>용량 계획 정보 수집

온-프레미스 환경에 대 한 정보를 수집 하 여 대상 (Azure) 용량 요구 사항을 평가 하 고 예측할 수 있습니다.
- VMware의 경우 VMware Vm에 대 한 Deployment Planner를 실행 하 여이 작업을 수행 합니다.
- 물리적 서버의 경우 정보를 수동으로 수집 합니다.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>VMware Vm에 대 한 Deployment Planner 실행

Deployment Planner는 VMware 온-프레미스 환경에 대 한 정보를 수집 하는 데 도움이 됩니다.

- Vm에 대 한 일반적인 변동를 나타내는 기간 동안 Deployment Planner를 실행 합니다. 이렇게 하면 보다 정확한 예측 및 권장 사항이 생성 됩니다.
- Planner는 실행 중인 서버의 처리량을 계산 하므로 구성 서버 컴퓨터에서 Deployment Planner를 실행 하는 것이 좋습니다. 처리량 측정에 [대해 자세히 알아보세요](site-recovery-vmware-deployment-planner-run.md#get-throughput) .
- 아직 구성 서버를 설정 하지 않은 경우:
    - Site Recovery 구성 요소에 대 한 [개요를 가져옵니다](vmware-physical-azure-config-process-server-overview.md) .
    - Deployment Planner를 실행 하기 위해 [구성 서버를 설정](vmware-azure-deploy-configuration-server.md)합니다.

그런 후 다음과 같이 Planner를 실행 합니다.

1. Deployment Planner [에 대해 알아봅니다](site-recovery-deployment-planner.md) . 포털에서 최신 버전을 다운로드 하거나 [직접 다운로드할](https://aka.ms/asr-deployment-planner)수 있습니다.
2. Deployment Planner에 대 한 [필수 구성 요소](site-recovery-deployment-planner.md#prerequisites) 및 [최신 업데이트](site-recovery-deployment-planner-history.md) 를 검토 하 고 도구를 [다운로드 하 여 압축을 풉니다](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) .
3. 구성 서버에서 [Deployment Planner를 실행](site-recovery-vmware-deployment-planner-run.md) 합니다.
4. 추정치와 권장 사항을 요약 하 [는 보고서를 생성](site-recovery-vmware-deployment-planner-run.md#generate-report) 합니다.
5. [보고서 권장 사항](site-recovery-vmware-deployment-planner-analyze-report.md) 및 [비용](site-recovery-vmware-deployment-planner-cost-estimation.md)예측을 분석 합니다.

>[!NOTE]
> 기본적으로이 도구는 프로 파일링 되도록 구성 되며 최대 1000 Vm에 대 한 보고서를 생성 합니다. ASRDeploymentPlanner.exe.config 파일에서 MaxVMsSupported 키 값을 늘려서이 제한을 변경할 수 있습니다.

## <a name="plan-target-azure-requirements-and-capacity"></a>대상 (Azure) 요구 사항 및 용량 계획

수집 된 예측이 나 권장 사항을 사용 하 여 대상 리소스 및 용량을 계획할 수 있습니다. VMware Vm에 대 한 Deployment Planner를 실행 한 경우 도움이 되는 다양 한 [보고서 권장 사항을](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) 사용할 수 있습니다.

- **호환 되는 vm**:이 숫자를 사용 하 여 Azure에 대 한 재해 복구에 사용할 수 있는 vm 수를 식별 합니다. 네트워크 대역폭과 Azure 코어에 대 한 권장 사항은이 숫자를 기반으로 합니다.
- **필요한 네트워크 대역폭**: 호환 되는 vm의 델타 복제에 필요한 대역폭을 확인 합니다. 
    - Planner를 실행 하는 경우 원하는 RPO를 분 단위로 지정 합니다. 권장 사항에는 RPO 100% 및 90%의 시간을 충족 하는 데 필요한 대역폭이 표시 됩니다. 
    - 네트워크 대역폭 권장 사항은 Planner에 권장 되는 구성 서버 및 프로세스 서버의 총 수에 필요한 대역폭을 고려 합니다.
- **필수 azure 코어**: 호환 되는 vm 수에 따라 대상 azure 지역에서 필요한 코어 수를 확인 합니다. 코어가 충분 하지 않은 경우 장애 조치 (failover) Site Recovery에서 필요한 Azure Vm을 만들 수 없습니다.
- **권장 VM 일괄 처리 크기**: 권장 되는 일괄 처리 크기는 100%의 RPO를 충족 하는 동안 기본적으로 72 시간 이내에 일괄 처리에 대 한 초기 복제를 완료 하는 기능을 기준으로 합니다. 시간 값을 수정할 수 있습니다.

이러한 권장 사항을 사용 하 여 Azure 리소스, 네트워크 대역폭 및 VM 일괄 처리를 계획할 수 있습니다.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure 구독 및 할당량 계획

대상 구독에서 사용 가능한 할당량이 장애 조치 (failover)를 처리 하기에 충분 한지 확인 하려고 합니다.

**Task** | **세부 정보** | **동작**
--- | --- | ---
**코어 확인** | 사용 가능한 할당량의 코어가 장애 조치 (failover) 시 전체 대상 수를 초과 하지 않는 경우 장애 조치 (failover)가 실패 합니다. | VMware Vm의 경우 대상 구독에 Deployment Planner 핵심 권장 사항을 충족 하는 코어가 충분 한지 확인 합니다.<br/><br/> 물리적 서버의 경우 Azure 코어가 수동 추정치를 충족 하는지 확인 합니다.<br/><br/> 할당량을 확인 하려면 Azure Portal > **구독**에서 **사용량 + 할당량**을 클릭 합니다.<br/><br/> 할당량 향상에 [대해 자세히 알아보세요](../azure-portal/supportability/resource-manager-core-quotas-request.md) .
**장애 조치 (failover) 제한 확인** | 장애 조치 (failover) 되어서는 안됩니다 수가 Site Recovery 장애 조치 (failover) 제한을 초과 합니다. |  장애 조치 (failover)가 제한을 초과 하는 경우 구독을 추가 하 고 여러 구독으로 장애 조치 (failover) 하거나 구독에 대 한 할당량을 늘릴 수 있습니다. 


### <a name="failover-limits"></a>장애 조치 제한

이 제한은 1 시간 이내에 Site Recovery에서 지 원하는 장애 조치 (failover) 수를 나타내며, 컴퓨터당 3 개의 디스크를 가정 합니다.

의미는 무엇 인가요? Azure VM을 시작 하려면 일부 드라이버가 부팅 시작 상태 여야 하 고 DHCP와 같은 서비스가 자동으로 시작 되도록 설정 되어야 합니다.
- 를 준수 하는 컴퓨터에는 이러한 설정이 이미 적용 되어 있습니다.
- Windows를 실행 하는 컴퓨터의 경우 규정 준수를 사전에 확인 하 고 필요한 경우 정책을 준수 하도록 할 수 있습니다. [자세히 알아보기](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux 컴퓨터는 장애 조치 (failover) 시에만 준수 상태로 전환 됩니다.

**컴퓨터가 Azure를 준수 하나요?** | **Azure VM 제한 (관리 디스크 장애 조치 (failover))**
--- | --- 
예 | 2000
예 | 1000

- 제한에는 구독에 대 한 대상 지역에서 최소한의 다른 작업이 진행 중 이라고 가정 합니다.
- 일부 Azure 지역은 더 작으며 약간 낮은 제한이 있을 수 있습니다.

## <a name="plan-infrastructure-and-vm-connectivity"></a>인프라 및 VM 연결 계획

Azure로 장애 조치 (failover) 후에는 온-프레미스에서 수행 되는 작업을 수행 하 고 사용자가 Azure Vm에서 실행 되는 워크 로드에 액세스할 수 있도록 해야 합니다.

- Active Directory 또는 DNS 온-프레미스 인프라를 Azure에 장애 조치 (failover) 하는 방법에 [대해 자세히 알아보세요](site-recovery-active-directory.md#test-failover-considerations) .
- 장애 조치 (failover) 후 Azure Vm에 대 한 연결 준비에 [대해 자세히 알아보세요](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) .



## <a name="plan-for-source-capacity-and-requirements"></a>원본 용량 및 요구 사항 계획

용량 요구 사항을 충족 하기에 충분 한 구성 서버 및 스케일 아웃 프로세스 서버가 있어야 합니다. 대규모 배포를 시작할 때 단일 구성 서버 및 단일 스케일 아웃 프로세스 서버를 사용 하 여 시작 합니다. 지정 된 제한에 도달 하면 서버를 더 추가 합니다.

>[!NOTE]
> VMware Vm의 경우 필요한 구성 및 프로세스 서버에 대 한 몇 가지 권장 사항을 Deployment Planner 있습니다. Deployment Planner 권장 사항을 따르는 대신 다음 절차에 포함 된 표를 사용 하는 것이 좋습니다. 


## <a name="set-up-a-configuration-server"></a>구성 서버 설정
 
구성 서버 용량은 데이터 변동 률이 아니라 복제 하는 컴퓨터 수의 영향을 받습니다. 추가 구성 서버가 필요한 지 여부를 파악 하려면 이러한 정의 된 VM 제한을 사용 합니다.

**CPU** | **메모리** | **캐시 디스크** | **복제 된 컴퓨터 제한**
 --- | --- | --- | ---
vCPU 8대<br> 2 개 소켓 * 4 코어 @ 2.5 g h z | 16GB | 600GB | 최대 550 대의 컴퓨터<br> 각 컴퓨터에 100 GB의 디스크가 각각 3 개 있다고 가정 합니다.

- 이러한 제한은 파일 템플릿을 사용 하 여 설정 된 구성 서버를 기반으로 합니다.
- 이 제한은 기본적으로 구성 서버에서 실행 되는 프로세스 서버를 사용 하 고 있지 않다고 가정 합니다.

새 구성 서버를 추가 해야 하는 경우 다음 지침을 따르세요.

- 서버 템플릿을 사용 하 여 VMware VM 재해 복구를 위한 [구성 서버를 설정](vmware-azure-deploy-configuration-server.md) 합니다.
- 물리적 서버에 대해 구성 서버를 수동으로 설정 하거나, 파일을 사용할 수 없는 VMware 배포에 대해 [구성 서버](physical-azure-set-up-source.md) 를 수동으로 설정 합니다.

구성 서버를 설정할 때 다음 사항에 유의 하십시오.

- 구성 서버를 설정 하는 경우 설치 후에는 변경 되지 않으므로 구독 및 자격 증명 모음이 상주 하는 자격 증명 모음을 고려 하는 것이 중요 합니다. 자격 증명 모음을 변경 해야 하는 경우 자격 증명 모음에서 구성 서버를 분리 하 고 다시 등록 해야 합니다. 이렇게 하면 자격 증명 모음에서 Vm의 복제가 중지 됩니다.
- 여러 네트워크 어댑터를 사용 하 여 구성 서버를 설정 하려면 설정 하는 동안이 작업을 수행 해야 합니다. 자격 증명 모음에 구성 서버를 등록 한 후에는이 작업을 수행할 수 없습니다.

## <a name="set-up-a-process-server"></a>프로세스 서버 설정

프로세스 서버 용량은 복제에 대해 사용 하도록 설정 된 컴퓨터 수를 기준으로 하는 것이 아니라 데이터 변동 비율의 영향을 받습니다.

- 대규모 배포의 경우에는 항상 하나 이상의 스케일 아웃 프로세스 서버가 있어야 합니다.
- 추가 서버가 필요한 지 여부를 파악 하려면 다음 표를 사용 합니다.
- 사양이 가장 높은 서버를 추가 하는 것이 좋습니다. 


**CPU** | **메모리** | **캐시 디스크** | **변동 율**
 --- | --- | --- | --- 
vCPU 12대<br> 2 개 소켓 * 6 코어 @ 2.5 g h z | 24GB | 1GB | 하루 최대 2tb

다음과 같이 프로세스 서버를 설정 합니다.

1. [필수 구성 요소](vmware-azure-set-up-process-server-scale.md#prerequisites)를 검토 합니다.
2. [포털](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)또는 [명령줄](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)에서 서버를 설치 합니다.
3. 새 서버를 사용 하도록 복제 된 컴퓨터를 구성 합니다. 복제 중인 컴퓨터가 이미 있는 경우:
    - 전체 프로세스 서버 작업을 새 프로세스 서버로 [이동할](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) 수 있습니다.
    - 또는 특정 Vm을 새 프로세스 서버로 [이동할](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) 수 있습니다.



## <a name="enable-large-scale-replication"></a>대규모 복제 사용

용량을 계획 하 고 필수 구성 요소 및 인프라를 배포한 후에는 많은 Vm에 대해 복제를 사용 하도록 설정 합니다.

1. 컴퓨터를 일괄 처리로 정렬 합니다. 일괄 처리 내에서 Vm에 대 한 복제를 사용 하도록 설정한 후 다음 일괄 처리로 이동 합니다.

    - VMware Vm의 경우 Deployment Planner 보고서에 [권장 되는 VM 일괄 처리 크기](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) 를 사용할 수 있습니다.
    - 물리적 컴퓨터의 경우 크기와 크기가 비슷한 컴퓨터와 사용 가능한 네트워크 처리량을 기준으로 일괄 처리를 식별 하는 것이 좋습니다. 목표는 동일한 시간 내에 초기 복제를 완료할 가능성이 높은 컴퓨터를 배치 하는 것입니다.
    
2. 컴퓨터에 대 한 디스크 변동 (code)이 크거나 배포의 한도를 초과 하는 경우, 복제 하지 않아도 되는 중요 하지 않은 파일 (예: 로그 덤프 또는 임시 파일)을 컴퓨터에서 이동할 수 있습니다. VMware Vm의 경우 이러한 파일을 별도의 디스크로 이동한 다음 [해당 디스크](vmware-azure-exclude-disk.md) 를 복제에서 제외할 수 있습니다.
3. 복제를 사용 하도록 설정 하기 전에 컴퓨터가 [복제 요구 사항을](vmware-physical-azure-support-matrix.md#replicated-machines)충족 하는지 확인 합니다.
4. [VMware vm](vmware-azure-set-up-replication.md#create-a-policy) 또는 [물리적 서버](physical-azure-disaster-recovery.md#create-a-replication-policy)에 대 한 복제 정책을 구성 합니다.
5. [VMware vm](vmware-azure-enable-replication.md) 또는 [물리적 서버](physical-azure-disaster-recovery.md#enable-replication)에 대해 복제를 사용 하도록 설정 합니다. 그러면 선택한 컴퓨터에 대 한 초기 복제가 시작 됩니다.

## <a name="monitor-your-deployment"></a>배포 모니터링

Vm의 첫 번째 일괄 처리에 대 한 복제를 시작한 후 다음과 같이 배포 모니터링을 시작 합니다.  

1. 재해 복구 관리자를 할당 하 여 복제 된 컴퓨터의 상태를 모니터링 합니다.
2. 복제 된 항목 및 인프라에 대 한 [이벤트를 모니터링](site-recovery-monitor-and-troubleshoot.md) 합니다.
3. 스케일 아웃 프로세스 서버의 [상태를 모니터링](vmware-physical-azure-monitor-process-server.md) 합니다.
4. 보다 쉽게 모니터링할 수 있도록 등록 하 여 이벤트에 대 한 [전자 메일 알림을](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) 받습니다.
5. 정상적인 [재해 복구 훈련](site-recovery-test-failover-to-azure.md)을 수행 하 여 모든 것이 예상 대로 작동 하는지 확인 합니다.


## <a name="plan-for-large-scale-failovers"></a>대규모 장애 조치 (failover) 계획

재해가 발생 한 경우 많은 수의 컴퓨터/워크 로드를 Azure로 장애 조치 (failover) 해야 할 수 있습니다. 다음과 같이 이러한 유형의 이벤트를 준비 합니다.

다음과 같이 장애 조치 (failover)를 미리 준비할 수 있습니다.

- 장애 조치 (failover) 후에 워크 로드를 사용할 수 있도록 하 고 사용자가 Azure Vm에 액세스할 수 있도록 [인프라 및 vm을 준비](#plan-infrastructure-and-vm-connectivity) 합니다.
- 이 문서의 앞 부분에서 [장애 조치 (failover) 제한을](#failover-limits) 확인 합니다. 장애 조치 (failover)가 이러한 제한 범위 내에 포함 되는지 확인 합니다.
- 정기적인 [재해 복구 훈련](site-recovery-test-failover-to-azure.md)을 실행 합니다. 드릴 도와 다음 작업을 수행 합니다.
    - 장애 조치 (failover) 전에 배포에서 간격을 찾습니다.
    - 앱에 대 한 종단 간 RTO를 예상 합니다.
    - 워크 로드에 대해 종단 간 RPO를 예상 합니다.
    - IP 주소 범위 충돌을 식별 합니다.
    - 드릴을 실행 하는 동안에는 프로덕션 네트워크를 사용 하지 말고 드릴 하 고 테스트 장애 조치 (failover)를 정리 하는 것이 좋습니다.

대규모 장애 조치 (failover)를 실행 하려면 다음을 수행 하는 것이 좋습니다.

1. 작업 장애 조치 (failover)에 대 한 복구 계획을 만듭니다.
    - 각 복구 계획은 최대 100 컴퓨터의 장애 조치 (failover)를 트리거할 수 있습니다.
    - [자세히 알아봅니다](recovery-plan-overview.md) .
2. Azure에서 수동 작업을 자동화 하기 위해 복구 계획에 Azure Automation runbook 스크립트를 추가 합니다. 일반적인 작업에는 부하 분산 장치 구성, DNS 업데이트 등이 포함 됩니다. [자세한 정보](site-recovery-runbook-automation.md)
2. 장애 조치 (failover) 전에 Azure 환경을 준수 하도록 Windows 컴퓨터를 준비 합니다. 를 준수 하는 컴퓨터의 경우 [장애 조치 (Failover) 제한이](#plan-azure-subscriptions-and-quotas) 더 높습니다. Runbook에 대해 [자세히 알아보세요](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) .
4.  복구 계획과 함께 [AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet을 사용 하 여 장애 조치 (failover)를 트리거합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)
