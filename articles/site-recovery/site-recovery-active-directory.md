---
title: Azure Site Recovery를 사용하여 Active Directory 및 DNS에 대한 재해 복구 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Active Directory 및 DNS에 대한 재해 복구 솔루션을 구현하는 방법에 대해 설명합니다.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 58e360bb355c7faf9608b00dd65b14f27aca4367
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038849"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Active Directory 및 DNS에 대한 재해 복구 설정

SharePoint, Dynamics AX 및 SAP와 같은 엔터프라이즈 애플리케이션이 올바르게 작동하려면 Active Directory 및 DNS 인프라가 필요합니다. 애플리케이션에 대한 재해 복구 솔루션을 설정할 때 애플리케이션이 제대로 작동하도록 하려면 다른 애플리케이션 구성 요소를 복구하기 전에 Active Directory 및 DNS를 복구해야 합니다.

[Site Recovery](site-recovery-overview.md)를 사용하여 Active Directory에 대한 재해 복구 계획을 만들 수 있습니다. 중단되는 경우 장애 조치(failover)를 시작할 수 있습니다. 몇 분 안에 Active Directory를 가동 및 실행할 수 있습니다. 주 사이트에서 SharePoint 및 SAP와 같은 여러 애플리케이션에 Active Directory를 배포한 경우 전체 사이트를 장애 조치(failover)할 수 있습니다. 먼저 Site Recovery를 사용하여 Active Directory를 장애 조치(failover)합니다. 그런 다음, 애플리케이션별 복구 계획을 사용하여 다른 애플리케이션을 장애 조치(failover)합니다.

이 문서에서는 Active Directory에 대한 재해 복구 솔루션을 만드는 방법을 설명합니다. 필수 구성 요소 및 장애 조치(failover) 지침을 포함합니다. 시작하기 전에 Active Directory와 Site Recovery에 대해 잘 알고 있어야 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure에 복제하는 경우 구독, Azure Virtual Network, 저장소 계정 및 Recovery Services 자격 증명 모음을 비롯한 [Azure 리소스를 준비](tutorial-prepare-azure.md)합니다.
* 모든 구성 요소에 대한 [지원 요구 사항](site-recovery-support-matrix-to-azure.md)을 검토합니다.

## <a name="replicate-the-domain-controller"></a>도메인 컨트롤러 복제

- 도메인 컨트롤러 또는 DNS를 호스트하는 하나 이상의 VM에 Site Recovery 복제를 설정해야 합니다.
- 환경에 여러 도메인 컨트롤러가 있는 경우 대상 사이트에도 추가 도메인 컨트롤러를 설치해야 합니다. 추가 도메인 컨트롤러는 Azure 또는 보조 온-프레미스 데이터 센터에 있을 수 있습니다.
- 약간의 애플리케이션과 단일 도메인 컨트롤러가 있는 경우 전체 사이트를 함께 장애 조치(failover)할 수 있습니다. 이 경우 Site Recovery를 사용하여 도메인 컨트롤러를 대상 사이트(Azure 또는 보조 온-프레미스 데이터 센터)에 복제하는 것이 좋습니다. [테스트 장애 조치(failover)](#test-failover-considerations)에도 동일한 복제 도메인 컨트롤러 또는 DNS 가상 머신을 사용할 수 있습니다.
- - 환경에 많은 애플리케이션과 둘 이상의 도메인 컨트롤러가 있거나 애플리케이션 몇 개를 동시에 장애 조치(failover)하려는 경우 Site Recovery로 도메인 컨트롤러 가상 머신을 복제하는 동시에 대상 사이트(Azure 또는 보조 온-프레미스 데이터 센터)에 추가 도메인 컨트롤러를 설정하는 것이 좋습니다. [테스트 장애 조치(failover)](#test-failover-considerations)의 경우 Site Recovery에서 복제한 도메인 컨트롤러를 사용할 수 있습니다. 장애 조치(failover)의 경우 대상 사이트의 추가 도메인 컨트롤러를 사용할 수 있습니다.

## <a name="enable-protection-with-site-recovery"></a>Site Recovery를 사용하여 보호 사용

Site Recovery를 사용하여 도메인 컨트롤러 또는 DNS를 호스트하는 가상 머신을 보호할 수 있습니다.

### <a name="protect-the-vm"></a>VM 보호
Site Recovery를 사용하여 복제된 도메인 컨트롤러는 [테스트 장애 조치(failover)](#test-failover-considerations)에 사용됩니다. 다음 요구 사항을 충족하는지 확인합니다.

1. 도메인 컨트롤러가 글로벌 카탈로그 서버입니다.
2. 도메인 컨트롤러는 테스트 장애 조치(failover)하는 동안 필요한 역할에 대한 FSMO 역할 소유자여야 합니다. 그렇지 않으면 이러한 역할은 장애 조치(failover) 후 [점유](https://aka.ms/ad_seize_fsmo)되어야 합니다.

### <a name="configure-vm-network-settings"></a>VM 네트워크 설정 구성
도메인 컨트롤러 또는 DNS를 호스트하는 가상 머신의 경우 Site Recovery에서 복제된 가상 머신의 **계산 및 네트워크** 설정 아래에서 네트워크 설정을 구성합니다. 이렇게 하면 장애 조치(failover) 후 가상 머신이 올바른 네트워크에 연결됩니다.

## <a name="protect-active-directory"></a>Active Directory 보호

### <a name="site-to-site-protection"></a>사이트-사이트 보호
보조 사이트에 도메인 컨트롤러를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용된 도메인과 동일한 이름을 지정합니다. **Active Directory 사이트 및 서비스** 스냅인을 사용하여 사이트가 추가된 사이트 링크 개체에서 설정을 구성할 수 있습니다. 사이트 링크에서 설정을 구성하면 둘 이상의 사이트에서 복제가 실행되는 시기와 빈도를 관리할 수 있습니다. 자세한 내용은 [사이트 간 복제 일정 예약](https://technet.microsoft.com/library/cc731862.aspx)을 참조하세요.

### <a name="site-to-azure-protection"></a>사이트-Azure 보호
먼저 Azure 가상 네트워크에서 도메인 컨트롤러를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용된 도메인과 동일한 이름을 지정합니다.

그런 다음, Azure에서 DNS 서버를 사용하도록 가상 네트워크에 대한 DNS 서버를 재구성합니다.

![Azure 네트워크](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure 간 보호
먼저 Azure 가상 네트워크에서 도메인 컨트롤러를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용된 도메인과 동일한 이름을 지정합니다.

그런 다음, Azure에서 DNS 서버를 사용하도록 가상 네트워크에 대한 DNS 서버를 재구성합니다.

## <a name="test-failover-considerations"></a>테스트 장애 조치 시 고려 사항
프로덕션 워크로드에 영향을 미치지 않도록 방지하기 위해 테스트 장애 조치(failover)가 프로덕션 네트워크에서 격리된 네트워크에서 발생합니다.

대부분의 애플리케이션에는 도메인 컨트롤러 또는 DNS 서버가 필요합니다. 따라서 애플리케이션을 장애 조치(failover)하기 전에 테스트 장애 조치(failover)에 사용할 격리된 네트워크에서 도메인 컨트롤러를 만들어야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 Site Recovery를 사용하여 도메인 컨트롤러 또는 DNS를 호스트하는 가상 머신을 복제하는 것입니다. 그런 다음 도메인 컨트롤러 가상 머신의 테스트 장애 조치(failover)를 실행한 후 애플리케이션에 대한 복구 계획의 테스트 장애 조치(failover)를 실행합니다. 그 방법은 다음과 같습니다.

1. Site Recovery를 사용하여 도메인 컨트롤러 또는 DNS를 호스트하는 가상 머신을 [복제](vmware-azure-tutorial.md)합니다.
2. 격리된 네트워크를 만듭니다. Azure에서 만드는 모든 가상 네트워크는 기본적으로 다른 네트워크에서 격리됩니다. 이 네트워크의 IP 주소 범위를 프로덕션 네트워크에서 사용하는 IP 주소 범위와 동일하게 사용하는 것이 좋습니다. 이 네트워크에서 사이트-사이트 연결을 사용하지 마십시오.
3. 격리된 네트워크의 DNS IP 주소를 제공합니다. DNS 가상 머신을 가져올 것으로 예상되는 IP 주소를 사용합니다. Azure로 복제하는 경우 장애 조치(failover)에 사용되는 가상 머신의 IP 주소를 제공합니다. IP 주소를 입력하려면 복제된 가상 머신의 **계산 및 네트워크** 설정에서 **대상 IP** 설정을 선택합니다.

    ![Azure 테스트 네트워크](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery는 가상 머신의 **계산 및 네트워크** 설정에서 제공한 것과 동일한 IP 주소를 사용하여 동일한 이름의 서브넷에 테스트 가상 머신을 만들려고 시도합니다. 테스트 장애 조치(failover)에 제공된 Azure Virtual Network에서 이름이 동일한 서브넷을 사용할 수 없는 경우 사전순으로 첫 번째 서브넷에 테스트 가상 머신이 만들어집니다.
    >
    > 대상 IP 주소가 선택한 서브넷에 포함되는 경우 Site Recovery는 대상 IP 주소를 사용하여 테스트 장애 조치(failover) 가상 머신을 만들려고 시도합니다. 대상 IP가 선택한 서브넷에 포함되지 않는 경우 선택한 서브넷에서 다음으로 사용 가능한 IP를 사용하여 테스트 장애 조치(failover) 가상 머신이 만들어집니다.
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>보조 사이트에 테스트 장애 조치(failover)

1. 다른 온-프레미스에 복제 중이고 DHCP를 사용하는 경우 [테스트 장애 조치(failover)의 DNS 및 DHCP를 설정](hyper-v-vmm-test-failover.md#prepare-dhcp)합니다.
2. 격리된 네트워크에서 실행하는 도메인 컨트롤러 가상 머신의 테스트 장애 조치(failover)를 수행합니다. 테스트 장애 조치(failover)를 수행하려면 도메인 컨트롤러 가상 머신에서 사용 가능한 최신 *애플리케이션 일치* 복구 지점을 사용합니다.
3. 애플리케이션이 실행되는 가상 머신을 포함하고 있는 복구 계획에 대한 테스트 장애 조치(failover)를 실행합니다.
4. 테스트를 완료한 후 도메인 컨트롤러 가상 머신에서 *테스트 장애 조치(failover)를 정리*합니다. 이 단계는 테스트 장애 조치(failover)에 대해 생성된 도메인 컨트롤러를 삭제합니다.


### <a name="remove-references-to-other-domain-controllers"></a>다른 도메인 컨트롤러에 대한 참조 제거
테스트 장애 조치(failover)를 시작하는 경우 테스트 네트워크에 있는 모든 도메인 컨트롤러를 포함하지 않습니다. 프로덕션 환경에 존재하는 다른 도메인 컨트롤러에 대한 참조를 제거하려면 누락된 도메인 컨트롤러에 대해 [FSMO Active Directory 역할을 점유](https://aka.ms/ad_seize_fsmo)하고 [메타데이터 정리](https://technet.microsoft.com/library/cc816907.aspx)를 수행해야 할 수도 있습니다.


### <a name="issues-caused-by-virtualization-safeguards"></a>가상화 세이프가드로 인한 문제

> [!IMPORTANT]
> 이 섹션에 설명된 구성의 일부는 표준 또는 기본 도메인 컨트롤러 구성이 아닙니다. 프로덕션 도메인 컨트롤러를 이렇게 변경하지 않으려면 테스트 장애 조치(failover)에 사용할 Site Recovery 전용 도메인 컨트롤러를 만들 수 있습니다. 해당 도메인 컨트롤러만 이렇게 변경하면 됩니다.  
>
>

Windows Server 2012부터 [AD DS(Active Directory Domain Services)에 추가 세이프가드가 기본적으로 제공됩니다](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). 이러한 세이프가드는 기본 하이퍼바이저 플랫폼이 **VM-GenerationID**를 지원하는 경우 USN 롤백으로부터 가상화된 도메인 컨트롤러를 보호합니다. Azure는 **VM-GenerationID**를 지원합니다. 이로 인해 Azure Virtual Machines에서 Windows Server 2012 이상을 실행하는 도메인 컨트롤러에는 이러한 추가 세이프가드가 있습니다.


**VM-GenerationID**를 다시 설정할 때 AD DS 데이터베이스의 **InvocationID**도 다시 설정됩니다. 또한 RID 풀이 삭제 되 하 고 sysvol 폴더는 신뢰할 수 없음으로 표시 됩니다. 자세한 내용은 [Active Directory Domain Services 가상화 소개](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) 및 [안전하게 DFSR 가상화](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)를 참조하세요.

Azure로 장애 조치(failover)를 수행하면 **VM-GenerationID**가 다시 설정될 수 있습니다. **VM-GenerationID**가 다시 설정되면 Azure에서 도메인 컨트롤러 가상 머신이 시작될 때 추가 세이프가드를 트리거합니다. 이 발생할 수는 *지연이* 도메인 컨트롤러 가상 컴퓨터에 로그인 할 수 있는 합니다.

이 도메인 컨트롤러는 테스트 장애 조치(failover)에만 사용되므로 가상화 세이프가드가 필요하지 않습니다. 도메인 컨트롤러 가상 머신의 **VM-GenerationID** 값이 변경되지 않도록 하려면 온-프레미스 도메인 컨트롤러에서 다음 DWORD의 값을 **4**로 변경하면 됩니다.


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>가상화 세이프가드의 증상

테스트 장애 조치(failover) 후 가상화 세이프가드가 트리거되는 경우 다음과 같은 증상 중 하나 이상이 나타날 수 있습니다.  

* **GenerationID** 값이 변경됩니다.

    ![Generation ID 변경](./media/site-recovery-active-directory/Event2170.png)

* **InvocationID** 값이 변경됩니다.

    ![Invocation ID 변경](./media/site-recovery-active-directory/Event1109.png)

* Sysvol 폴더 및 NETLOGON 공유에 사용할 수 없습니다.

    ![Sysvol 폴더 공유](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs sysvol 폴더](./media/site-recovery-active-directory/Event13565.png)

* DFSR 데이터베이스가 삭제됩니다.

    ![DFSR 데이터베이스가 삭제됩니다.](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>테스트 장애 조치(failover)를 수행하는 동안 도메인 컨트롤러 문제 해결

> [!IMPORTANT]
> 이 섹션에 설명된 구성의 일부는 표준 또는 기본 도메인 컨트롤러 구성이 아닙니다. 프로덕션 도메인 컨트롤러를 이렇게 변경하지 않으려면 Site Recovery 테스트 장애 조치(failover) 전용 도메인 컨트롤러를 만들 수 있습니다. 해당 전용 도메인 컨트롤러만 이렇게 변경하면 됩니다.  
>
>

1. 명령 프롬프트에서 폴더 sysvol 및 NETLOGON 폴더가 공유 되 고 있는지 여부를 확인 하려면 다음 명령을 실행 합니다.

    `NET SHARE`

2. 명령 프롬프트에서 다음 명령을 실행하여 도메인 컨트롤러가 제대로 작동하는지 확인합니다.

    `dcdiag /v > dcdiag.txt`

3. 출력 로그에서 다음 텍스트를 찾습니다. 도메인 컨트롤러가 제대로 작동하는지 확인하는 텍스트입니다.

    * "passed test Connectivity"
    * "passed test Advertising"
    * "passed test MachineAccount"

위의 조건이 충족되면 도메인 컨트롤러가 제대로 작동하는 것으로 볼 수 있습니다. 그렇지 않은 경우 다음 단계를 완료합니다.

1. 도메인 컨트롤러의 정식 복원을 수행합니다. 다음 정보를 숙지하세요.
    * [FRS 복제](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)를 권장하지는 않지만 사용 중이라면 신뢰할 수 있는 복원 단계를 수행합니다. 프로세스는 [BurFlags 레지스트리 키를 사용하여 파일 복제 서비스 다시 초기화](https://support.microsoft.com/kb/290762)에 설명되어 있습니다.

        BurFlags에 대한 자세한 내용은 블로그 게시물 [D2 및 D4: What is it for?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/)(D2 및 D4의 용도)를 참조하세요.
    * DFSR 복제를 사용하는 경우 신뢰할 수 있는 복원 단계를 완료합니다. 프로세스에 설명 되어 [DFSR 복제 sysvol 폴더 (예: "D4/D2" FRS 용)에 대 한 신뢰할 수 있는 도메인과 신뢰할 수 없는 동기화를 강제로](https://support.microsoft.com/kb/2218556)입니다.

        Powershell 함수를 사용할 수도 있습니다. 자세한 내용은 [DFSR-SYSVOL authoritative/non-authoritative restore PowerShell functions](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)(DFSR SYSVOL 신뢰할 수 있는/신뢰할 수 없는 복원 PowerShell 함수)를 참조하세요.

2. 온-프레미스 도메인 컨트롤러에서 다음 레지스트리 키를 **0**으로 설정하여 초기 동기화 요구 사항을 바이패스합니다. DWORD가 없으면 **매개 변수** 노드에서 만들 수 있습니다.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    자세한 내용은 [DNS 이벤트 ID 4013 문제 해결: DNS 서버가 AD 통합 DNS 영역을 로드할 수 없습니다](https://support.microsoft.com/kb/2001093)를 참조하세요.

3. 글로벌 카탈로그 서버를 사용하여 사용자 로그온을 확인해야 한다는 요구 사항을 해제합니다. 이 작업을 수행하려면 온-프레미스 도메인 컨트롤러에서 다음 레지스트리 키를 **1**로 설정합니다. DWORD가 없으면 **Lsa** 노드에서 만들 수 있습니다.

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    자세한 내용은 [글로벌 카탈로그 서버를 사용하여 사용자 로그온을 확인해야 한다는 요구 사항을 해제하는 방법](https://support.microsoft.com/kb/241789)을 참조하세요.

### <a name="dns-and-domain-controller-on-different-machines"></a>다른 컴퓨터에서 DNS 및 도메인 컨트롤러

동일한 VM에서 도메인 컨트롤러 및 DNS를 실행 중인 경우 이 프로시저를 건너뛰어도 됩니다.


DNS가 도메인 컨트롤러와 동일한 VM에 있지 않은 경우 테스트 장애 조치(failover)를 위한 DNS VM을 만들어야 합니다. 새 DNS 서버를 사용하고 모든 필요한 영역을 만들 수 있습니다. 예를 들어, Active Directory 도메인이 contoso.com인 경우 이름이 contoso.com인 DNS 영역을 만들 수 있습니다. 다음과 같이 Active Directory에 해당하는 항목을 DNS에서 업데이트해야 합니다.

1. 복구 계획의 다른 가상 머신을 시작하기 전에 이러한 설정이 준비되었는지 확인합니다.
   * 영역 이름은 포리스트 루트 이름 영역을 따서 지어야 합니다.
   * 영역 파일을 백업해야 합니다.
   * 보안 및 비보안 업데이트에 대한 영역을 활성화해야 합니다.
   * 도메인 컨트롤러를 호스트하는 가상 머신의 확인자는 DNS 가상 머신의 IP 주소를 가리켜야 합니다.

2. 도메인 컨트롤러를 호스트하는 VM에서 다음 명령을 실행합니다.

    `nltest /dsregdns`

3. 다음 명령을 실행하여 DNS 서버에서 영역을 추가하고 비보안 업데이트를 허용하고 DNS에 영역에 대한 항목을 추가합니다.

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>다음 단계
[Azure Site Recovery로 엔터프라이즈 워크로드를 보호하는 방법](site-recovery-workload.md)에 대해 자세히 알아봅니다.
