---
title: Azure Site Recovery를 사용하여 Azure로 재해 복구 훈련 실행 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용하여 온-프레미스에서 Azure로 재해 복구 훈련을 실행하는 방법에 대해 알아봅니다.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 6d8ad71894444b3759e506c50244b592ac1f8aac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471488"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Azure로 재해 복구 훈련 실행 


이 문서에서는 Site Recovery 테스트 장애 조치(Failover)를 사용하여 Azure에 대해 재해 복구 훈련을 실행하는 방법을 설명합니다.  

데이터 손실 또는 가동 중지 없이 복제 및 재해 복구 전략의 유효성을 검사하기 위해 테스트 장애 조치(Failover)를 실행합니다. 테스트 장애 조치(Failover)는 진행 중인 복제 또는 프로덕션 환경에 영향을 주지 않습니다. 특정 VM(가상 머신) 또는 여러 VM을 포함하는 [복구 계획](site-recovery-create-recovery-plans.md)에 대해 테스트 장애 조치(Failover)를 실행할 수 있습니다.


## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행
이 절차는 복구 계획에 대한 테스트 장애 조치를 실행하는 방법을 설명합니다. 단일 VM에 대한 테스트 장애 조치(failover)를 실행하려면 [여기](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)에 설명된 단계를 따르세요.

![테스트 장애 조치(Failover)](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. Azure Portal의 Site Recovery에서 **복구 계획** > *recoveryplan_name* > **테스트 장애 조치(Failover)** 를 클릭합니다.
2. 장애 조치(failover)할 **복구 지점**을 선택합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
    - **가장 최근에 처리됨**: 이 옵션은 계획의 모든 VM을 Site Recovery에서 처리된 최신 복구 지점으로 장애 조치(failover)합니다. 특정 VM에 대한 최신 복구 지점을 보려면 VM 설정에서 **최신 복구 지점**을 선택하세요. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO(복구 시간 목표)를 제공합니다.
    - **최신 앱 일치**: 이 옵션은 계획의 모든 VM을 Site Recovery에서 처리된 최신 애플리케이션 일치 복구 지점으로 장애 조치(failover)합니다. 특정 VM에 대한 최신 복구 지점을 보려면 VM 설정에서 **최신 복구 지점**을 선택하세요.
    - **최신**: 이 옵션은 Site Recovery 서비스로 보낸 모든 데이터를 먼저 처리한 다음, 각 VM에 대한 복구 지점을 만든 후에 해당 복구 지점으로 장애 조치(failover)합니다. 이 옵션은 장애 조치(failover) 후에 생성된 VM은 장애 조치(failover)가 트리거되었을 때 Site Recovery로 복제된 모든 데이터를 보유하므로 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
    - **최신 다중 VM이 처리됨**: 이 옵션은 다중 VM 일치가 사용하도록 설정된 하나 이상의 VM이 포함된 복구 계획에 사용할 수 있습니다. 이 설정이 사용하도록 설정된 VM은 최신 일반 다중 VM 일치 복구 지점으로 장애 조치(Failover)됩니다. 다른 VM은 처리된 최신 복구 지점으로 장애 조치(Failover)됩니다.  
    - **최신 다중 VM 앱 일치**: 이 옵션은 다중 VM 일치가 사용하도록 설정된 하나 이상의 VM이 포함된 복구 계획에 사용할 수 있습니다. 복제 그룹에 속하는 VM은 공통된 최신 다중 VM 애플리케이션 일관성 복구 지점으로 장애 조치(failover)됩니다. 다른 VM은 최신 애플리케이션 일치 복구 지점으로 장애 조치(Failover)됩니다.
    - **사용자 지정**: 이 옵션은 특정 VM을 특정 복구 지점으로 장애 조치(failover)하는 데 사용합니다.
3. 테스트 VM을 만들 Azure Virtual Network를 선택합니다.

    - Site Recovery는 VM의 **Compute 및 네트워크** 설정에서 제공한 것과 동일한 이름 및 IP 주소를 사용하여 서브넷에 테스트 VM을 만들려고 시도합니다.
    - 동일한 이름의 서브넷을 테스트 장애 조치(Failover)에 사용된 Azure Virtual Network에서 사용할 수 없는 경우 사전순으로 첫 번째 서브넷에 테스트 VM이 만들어집니다.
    - 해당 서브넷에서 동일한 IP 주소를 사용할 수 없는 경우 VM은 서브넷의 사용 가능한 다른 IP 주소를 받습니다. [자세히 알아보기](#create-a-network-for-test-failover).
4. Azure에 장애 조치(Failover)하고 데이터 암호화를 사용하는 경우 **암호화 키**에서 공급자 설치 중에 암호화를 사용하도록 설정할 때 발행된 인증서를 선택합니다. 암호화를 사용할 수 없는 경우이 단계를 무시할 수 있습니다.
5. **작업** 탭에서 장애 조치 진행 상황을 추적합니다. Azure 포털에서 테스트 복제본 컴퓨터가 표시되어야 합니다.
6. Azure VM에서 RDP 연결을 시작하려면 장애 조치(Failover)된 VM의 네트워크 인터페이스에서 [공용 IP를 추가](https://aka.ms/addpublicip)해야 합니다.
7. 모든 항목이 예상대로 작동할 경우 **테스트 장애 조치(Failover) 정리**를 클릭합니다. 그러면 테스트 장애 조치(Failover) 중에 만들어진 VM이 삭제됩니다.
8. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다.


![테스트 장애 조치(Failover)](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

테스트 장애 조치(Failover)가 트리거되면 다음 작업이 수행됩니다.

1. **필수 조건**: 필수 구성 요소 확인을 실행하여 장애 조치(failover)에 필요한 모든 조건이 충족되었는지 확인합니다.
2. **장애 조치(failover)**: Azure VM이 만들어질 수 있도록 장애 조치(Failover)가 데이터를 처리하고 준비합니다.
3. **최신**: 최신 복구 지점을 선택한 경우 서비스로 전송된 데이터에서 복구 지점이 만들어집니다.
4. **시작**: 이 단계는 이전 단계에서 처리된 데이터를 사용하여 Azure 가상 머신을 만듭니다.

### <a name="failover-timing"></a>장애 조치(Failover) 시간

다음 시나리오에서는 장애 조치(Failover) 중에 완료하는 데 8~10분 정도 소요되는 추가적인 중간 단계가 필요합니다.

* 9.8 이전의 모바일 서비스 버전을 사용하는 VMware VM
* 물리적 서버
* VMware Linux VM
* 물리적 서버로 보호되는 Hyper-V VM
* 다음 드라이버가 부팅 드라이버가 아닌 VMware VM
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* DHCP 또는 고정 IP 주소 사용 여부와 관계없이 DHCP를 사용할 수 없는 VMware VM

다른 모든 경우에는 이러한 중간 단계가 필요하지 않으며 장애 조치(Failover)에 소요되는 시간이 훨씬 짧습니다.


## <a name="create-a-network-for-test-failover"></a>테스트 장애 조치(Failover)를 위한 네트워크 만들기

테스트 장애 조치(Failover)를 위해 각 VM에 대한 **Compute 및 네트워크** 설정의 특정 프로덕션 복구 사이트 네트워크와는 격리된 네트워크를 선택합니다. 사용자가 만드는 Azure Virtual Network는 기본적으로 다른 네트워크와 격리됩니다. 테스트 네트워크는 프로덕션 네트워크와 비슷해야 합니다.

- 테스트 네트워크에는 프로덕션 네트워크와 같은 수의 서브넷이 있어야 합니다. 서브넷의 이름은 같아야 합니다.
- 테스트 네트워크에는 동일한 IP 주소 범위를 사용해야 합니다.
- 테스트 네트워크의 DNS를 **Compute 및 네트워크** 설정의 DNS VM에 대해 지정된 IP 주소로 업데이트합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations)을 읽어보세요.


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>복구 사이트의 프로덕션 네트워크로의 테스트 장애 조치(Failover)

프로덕션 네트워크와는 별개인 테스트 네트워크를 사용하는 것이 좋지만 프로덕션 네트워크에서 재해 복구 훈련을 테스트하려는 경우에는 다음에 유의하세요.

- 테스트 장애 조치(Failover)를 실행할 때 기본 VM이 종료되어 있는지 확인합니다. 이렇게 하지 않으면 ID가 같은 두 VM이 동시에 존재하며 동일한 네트워크에서 동시에 실행됩니다. 이로 인해 예기치 않은 결과가 발생할 수 있습니다.
- 테스트 장애 조치(Failover)용으로 만든 VM에서 변경한 내용은 장애 조치(Failover)를 정리하면 손실됩니다. 즉, 이러한 변경 내용이 기본 VM에 다시 복제되지 않습니다.
- 프로덕션 환경에서 테스트를 수행하면 프로덕션 애플리케이션이 가동 중지됩니다. 사용자는 테스트 장애 조치(Failover)가 진행 중일 때 VM에서 실행되는 앱을 사용하지 않아야 합니다.  



## <a name="prepare-active-directory-and-dns"></a>Active Directory 및 DNS 준비

애플리케이션 테스트를 위해 테스트 장애 조치(Failover)를 실행하려면 테스트 환경에서 프로덕션 Active Directory 환경의 복사본이 필요합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(Failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations)을 읽어보세요.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>장애 조치(Failover) 후 Azure VM에 연결할 준비

장애 조치(Failover) 후 RDP/SSH를 사용하여 Azure VM에 연결하려면 표에 요약된 요구 사항을 따르세요.

**장애 조치(Failover)** | **위치**: | **actions**
--- | --- | ---
**Windows를 실행하는 Azure VM** | 장애 조치(Failover) 전에 온-프레미스 컴퓨터에서 | 인터넷을 통해 Azure VM에 액세스하려면 RDP를 활성화하고, TCP 및 UDP 규칙이 **공용**에 추가되었는지 그리고 **Windows 방화벽** > **허용되는 앱**에서 모든 프로필에 대해 RDP가 허용되는지 확인합니다.<br/><br/> 사이트 간 연결을 통해 Azure VM에 액세스하려면 컴퓨터에서 RDP를 활성화하고, **도메인 및 개인** 네트워크의 **Windows 방화벽** -> **허용되는 앱 및 기능**에서 RDP를 허용해야 합니다.<br/><br/>  운영 체제의 SAN 정책이 **OnlineAll**로 설정되어 있는지 확인합니다. [자세히 알아보기](https://support.microsoft.com/kb/3031135).<br/><br/> 장애 조치를 트리거할 때 VM에 보류 중인 Windows 업데이트가 없는지 확인합니다. 장애 조치 시 Windows 업데이트가 시작될 수 있으며, 업데이트를 완료할 때까지 VM에 로그인할 수 없습니다.
**Windows를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM |  VM에 대한 [공용 IP 주소를 추가](https://aka.ms/addpublicip)합니다.<br/><br/> 장애 조치된 VM 및 해당 VM이 연결된 Azure 서브넷에 대한 네트워크 보안 그룹 규칙에서 RDP 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> **부트 진단**을 확인하여 VM에 대한 스크린샷을 검토합니다.<br/><br/> 연결할 수 없는 경우 VM이 실행 중인지 확인하고 해당 [문제 해결 팁](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)을 검토합니다.
**Linux를 실행하는 Azure VM** | 장애 조치(Failover) 전에 온-프레미스 컴퓨터에서 | VM의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되어 있는지 확인합니다.<br/><br/> 방화벽 규칙이 SSH 연결을 허용하는지 확인합니다.
**Linux를 실행하는 Azure VM** | 장애 조치(failover) 후 Azure VM | 장애 조치(Failover)된 VM 그리고 해당 VM이 연결된 Azure 서브넷의 네트워크 보안 그룹 규칙이 SSH 포트로 들어오는 연결을 허용해야 합니다.<br/><br/> VM에 대한 [공용 IP 주소를 추가](https://aka.ms/addpublicip)합니다.<br/><br/> **부트 진단**에서 VM에 대한 스크린샷을 검토합니다.<br/><br/>

[여기](site-recovery-failover-to-azure-troubleshoot.md)에 설명된 단계에 따라 장애 조치(failover) 후 연결 문제를 해결합니다.

## <a name="next-steps"></a>다음 단계
재해 복구 훈련을 마친 후에 다른 유형의 [장애 조치(Failover)](site-recovery-failover.md)에 대해 자세히 알아봅니다.
