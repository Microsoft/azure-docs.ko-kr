---
title: Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정 | Microsoft Docs
description: Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: b844c93a1f3e83d682b51db6f9854f11b24d82e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127129"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-not-requiring-a-reboot

> [!NOTE]
> Red Hat Enterprise Linux의 Pacemaker는 Azure Fence Agent를 사용하여 필요한 경우 클러스터 노드를 펜싱합니다. 리소스 중지가 실패하거나 클러스터 노드가 더 이상 서로 통신할 수 없는 경우 장애 조치(failover)에 최대 15분이 걸릴 수 있습니다. 자세한 내용은 [Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711)(RHEL 고가용성 클러스터 멤버로 실행되는 Azure VM이 펜싱되는 데 시간이 너무 오래 걸리거나 VM이 종료되기 전에 펜싱이 실패함/시간 초과됨)을 참조하세요.

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록.
  * Azure VM 크기에 대한 중요한 용량 정보.
  * 지원되는 SAP 소프트웨어 및 OS(운영 체제)와 데이터베이스 조합.
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전.
* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2002167]에는 Red Hat Enterprise Linux에 권장되는 OS 설정이 있습니다.
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포(이 문서)][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)(Pacemaker 클러스터의 SAP HANA 시스템 복제)
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
* Azure 특정 RHEL 설명서:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)(RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)(Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)

## <a name="cluster-installation"></a>클러스터 설치

![RHEL의 Pacemaker 개요](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

다음 항목에는 접두사 **[A]**(모든 노드에 적용됨), **[1]**(노드 1에만 적용됨), **[2]**(노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** 등록

   가상 머신을 등록하고 RHEL 7의 리포지토리가 포함된 풀에 연결합니다.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Azure Marketplace PAYG RHEL 이미지에는 풀을 연결 하면 됩니다 효과적으로 두 번 청구 RHEL 사용량에 대 한: 종 량 제 이미지에 한 번씩 및 한 번 연결 풀에서 RHEL 자격에 대 한 합니다. 이 문제를 완화 하려면 Azure BYOS RHEL를 이미지 이제 제공 합니다. 자세한 내용은 [여기](https://aka.ms/rhel-byos)합니다.

1. **[A]** SAP 리포지토리에 RHEL 사용

   필수 패키지를 설치하려면 다음 리포지토리를 사용하도록 설정합니다.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable="rhel-sap-for-rhel-7-server-rpms"
   </code></pre>

1. **[A]** RHEL HA 추가 기능 설치

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름을 바꿉니다. /etc/hosts를 사용하는 장점은 클러스터가 단일 실패 지점이 될 수 있는 DNS와 무관하다는 점입니다.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** hacluster 암호를 동일한 암호로 변경

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Pacemaker의 방화벽 규칙 추가

   클러스터 노드 간의 모든 클러스터 통신에 다음 방화벽 규칙을 추가합니다.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** 기본 클러스터 서비스 사용

   다음 명령을 실행하여 Pacemaker 서비스를 사용하도록 설정하고 시작합니다.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Pacemaker 클러스터 만들기

   다음 명령을 실행하여 노드를 인증하고 클러스터를 만듭니다. 메모리 보존 유지 관리를 허용하도록 토큰을 30000으로 설정합니다. 자세한 내용은 [Linux에 대한 이 문서][virtual-machines-linux-maintenance]를 참조하세요.

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** 예상 투표 설정

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>STONITH 디바이스 만들기

STONITH 디바이스에서는 서비스 주체를 사용하여 Microsoft Azure에 대해 권한을 부여합니다. 다음 단계에 따라 서비스 주체를 만듭니다.

1. <https://portal.azure.com>으로 이동합니다.
1. 속성 및 여 Directory ID 기록으로 이동 하 여 Azure Active Directory 블레이드를 엽니다 이 ID는 **테넌트 ID**입니다.
1. 앱 등록 클릭
1. 추가를 클릭합니다.
1. 이름을 입력 하 고, 응용 프로그램 유형 "Web app/API"를 선택, 로그온 URL을 입력 (예: http:\//localhost) 만들기를 클릭 합니다.
1. 로그온 URL이 사용되지 않으며, 이 URL은 임의의 올바른 URL이 될 수 있음
1. 새 앱을 선택하고 설정 탭에서 키 클릭
1. 새 키의 설명을 입력하고 “만료되지 않음”을 선택한 다음 저장을 클릭
1. 값을 기록해 둡니다. 서비스 주체의 **암호**로 사용됨
1. 애플리케이션 ID를 적어둡니다. 서비스 주체의 사용자 이름(아래 단계의 **로그인 ID**)으로 사용됨

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 펜스 에이전트에 대한 사용자 지정 역할 만들기

서비스 주체에는 기본적으로 Azure 리소스에 액세스할 권한이 없습니다. 서비스 주체에 클러스터의 모든 가상 머신을 시작 및 중지(할당 취소)하기 위한 권한을 제공해야 합니다. 사용자 지정 역할을 아직 만들지 않은 경우 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) 또는 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)를 사용하여 만들 수 있습니다.

입력 파일에 다음 콘텐츠를 사용합니다. 구독에 맞게 콘텐츠를 조정해야 합니다. 즉 c276fc76-9cd4-44c9-99a7-4fd71546436e 및 e91d47c4-76f3-4271-a796-21b4ecfe3624를 구독의 ID로 교체해야 합니다. 구독이 하나만 있는 경우 AssignableScopes에서 두 번째 항목을 제거합니다.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** 서비스 주체에 사용자 지정 역할 할당

마지막 단원에서 만든 사용자 지정 역할인 "Linux 펜스 에이전트 역할"을 서비스 주체에 할당합니다. 소유자 역할을 더 이상 사용하지 마십시오!

1. https://portal.azure.com으로 이동합니다.
1. 모든 리소스 블레이드 열기
1. 첫 번째 클러스터 노드의 가상 머신 선택
1. 액세스 제어(IAM) 클릭
1. 역할 할당 추가 클릭
1. "Linux 펜스 에이전트 역할"이라는 역할 선택
1. 위에서 만든 애플리케이션의 이름 입력
1. 저장을 클릭합니다.

두 번째 클러스터 노드에 위 단계 반복

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH 디바이스 만들기

가상 머신의 권한을 편집하고 나면 클러스터의 STONITH 디바이스를 구성할 수 있습니다.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

다음 명령을 사용하여 펜스 디바이스를 구성합니다.

> [!NOTE]
> RHEL 호스트 이름 및 Azure 노드 이름이 동일하지 않은 경우에만 ‘pcmk_host_map’ 옵션이 명령에 필요합니다. 명령에서 굵은 섹션을 참조하세요.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** STONITH 디바이스를 사용하도록 설정

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
