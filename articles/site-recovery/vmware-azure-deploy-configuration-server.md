---
title: Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버 배포 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버를 관리하는 방법을 설명합니다.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2019
ms.author: ramamill
ms.openlocfilehash: c25ca8c27b84f34b025ec5abce00c8d8c70e5df6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125698"
---
# <a name="deploy-a-configuration-server"></a>구성 서버 배포

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 배포합니다. 구성 서버는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다. 이 문서에서는 VMware VM을 Azure로 복제할 때 구성 서버를 배포하는 데 필요한 단계를 안내합니다. 물리적 서버 복제를 위해 구성 서버를 설정해야 하는 경우 [이 문서에 따릅니다](physical-azure-set-up-source.md).

> [!TIP]
> [여기](vmware-azure-architecture.md)에서 Azure Site Recovery 아키텍처의 일부로 구성 서버의 역할에 대해 알아볼 수 있습니다.

## <a name="deployment-of-configuration-server-through-ova-template"></a>OVA 템플릿을 통한 구성 서버 배포

구성 서버는 특정 최소 하드웨어 및 크기 조정 요구 사항이 있는 고가용성 VMware VM으로 설정되어야 합니다. 편리하고 쉬운 배포를 위해 Site Recovery에서 다운로드 가능한 OVA(Open Virtualization Application) 템플릿을 제공하여 아래에 나열된 모든 필수 요구 사항을 준수하는 구성 서버를 설정합니다.

## <a name="prerequisites"></a>필수 조건

다음 표에는 구성 서버에 대한 최소 하드웨어 요구 사항이 요약되어 있습니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="azure-active-directory-permission-requirements"></a>Azure Active Directory 사용 권한 요구 사항

사용자가 필요한 **다음 중 하나** 권한이 Azure Site Recovery 서비스를 사용 하 여 구성 서버를 등록 하려면 AAD (Azure Active Directory)에서 설정 합니다.

1. 사용자 응용 프로그램을 만들려면 "응용 프로그램 개발자" 역할이 있어야 합니다.
   1. 을 확인 하려면 Azure portal에 로그인</br>
   1. Azure Active Directory로 이동 > 역할과 관리자</br>
   1. "응용 프로그램 개발자" 역할은 사용자에 게 할당 하는 경우를 확인 합니다. 그렇지 않은 경우이 권한이 있는 사용자를 사용 하거나 연락 [관리자 권한을 사용할 수 있도록](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal#assign-roles)입니다.
    
1. "응용 프로그램 개발자" 역할을 할당할 수 없습니다, 하는 경우 "사용자 응용 프로그램을 등록할 수 있습니다" 플래그 id를 만드는 사용자에 대해 true로 설정 되어 있는지 확인 합니다. 위의 사용 권한을 사용 하도록 설정 하려면
   1. Azure Portal에 로그인
   1. Azure Active Directory로 이동 > 사용자 설정
   1. 아래에서 * * 앱 등록 ","사용자 응용 프로그램을 등록할 수 있습니다"[예]로 선택 해야 합니다.

      ![AAD_application_permission](media/vmware-azure-deploy-configuration-server/AAD_application_permission.png)

> [!NOTE]
> Active Directory 페더레이션 Services(ADFS) 됩니다 **지원 되지 않습니다**합니다. 통해 관리 되는 계정을 사용 하십시오 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)합니다.

## <a name="capacity-planning"></a>용량 계획

구성 서버에 대한 크기 조정 요구 사항은 잠재적 데이터 변경률에 따라 다릅니다. 이 표를 가이드로 참조하세요.

| **CPU** | **메모리** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터** |
| --- | --- | --- | --- | --- |
| 8개 vCPU(2개 소켓 * 4코어 \@ 2.5GHz) |16GB |300GB |500GB 이하 |100대 미만의 컴퓨터를 복제합니다. |
| 12개 vCPU(2개 소켓 * 6코어 \@ 2.5GHz) |18GB |600GB |500GB ~ 1TB |100 ~ 150대 컴퓨터를 복제합니다. |
| 16개 vCPU(2개 소켓 * 8코어 \@ 2.5GHz) |32GB |1TB |1TB ~ 2TB |150 ~ 200대 컴퓨터를 복제합니다. |

둘 이상의 VMware VM을 복제하는 경우 [용량 계획 고려 사항](site-recovery-plan-capacity-vmware.md)을 참조하세요. VMware 복제를 위한 [Deployment Planner 도구](site-recovery-deployment-planner.md)를 실행합니다.

## <a name="download-the-template"></a>템플릿 다운로드

1. 자격 증명 모음에서 **인프라 준비** > **원본**으로 이동합니다.
2. **원본 준비**에서 **+구성 서버**를 선택합니다.
3. **서버 추가**에서 **VMware에 대한 구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. 구성 서버에 대한 OVA(Open Virtualization Application) 템플릿을 다운로드합니다.

   > [!TIP]
   >[Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 직접 최신 버전의 구성 서버 템플릿을 다운로드할 수도 있습니다.

> [!NOTE]
> OVA 템플릿과 함께 제공 라이선스는 평가판 라이선스를 180 일 동안 유효 합니다. Post이 기간 동안 고객 해야 확보 라이선스를 사용 하 여 windows 정품 인증 합니다.

## <a name="import-the-template-in-vmware"></a>VMware에서 템플릿 가져오기

1. VMWare vSphere 클라이언트를 사용하여 VMware vCenter 서버 또는 vSphere ESXi 호스트에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포**를 선택하여 OVF 템플릿 배포 마법사를 시작합니다.

     ![OVF 템플릿](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. **원본 선택**에서 다운로드한 OVF의 위치를 입력합니다.
4. **세부 정보 검토**에서 **다음**을 선택합니다.
5. **이름 및 폴더 선택**과 **구성 선택**에서 기본 설정을 그대로 적용합니다.
6. 최상의 성능을 발휘할 수 있도록 **저장소 선택**의 **가상 디스크 형식 선택**에서 **씩 프로비전 Eager Zeroed**를 선택합니다. 씬 프로비전 옵션을 사용하면 구성 서버의 성능에 영향을 줄 수 있습니다.
7. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
8. **완료 준비**에서 다음을 수행합니다.

    * 기본 설정을 사용하여 VM을 설정하려면 **배포 후 전원 켜기** > **마침**을 선택합니다.

    * 추가 네트워크 인터페이스를 추가하려면 **배포 후 전원 켜기**의 선택을 취소한 다음, **마침**을 선택합니다. 기본적으로 구성 서버 템플릿은 단일 NIC를 사용하여 배포됩니다. 배포 후 NIC를 추가할 수 있습니다.

> [!IMPORTANT]
> 배포 후에는 리소스 구성(메모리/코어 수/CPU 제한)을 변경하거나 구성 서버에 설치된 서비스 또는 파일을 수정/삭제하지 마세요. 이는 Azure 서비스에 대한 구성 서버 등록 및 구성 서버의 성능에 영향을 미칩니다.

## <a name="add-an-additional-adapter"></a>어댑터 추가

구성 서버에 NIC를 추가하려면 자격 증명 모음에 서버를 등록하기 전에 추가하세요. 등록 후에는 어댑터를 추가할 수 없습니다.

1. VSphere 클라이언트 인벤토리에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정 편집**을 선택합니다.
2. **하드웨어**에서 **추가** > **이더넷 어댑터**를 선택합니다. 그런 후 **다음**을 선택합니다.
3. 어댑터 유형 및 네트워크를 선택합니다.
4. VM이 켜질 때 가상 NIC에 연결하려면 **전원이 켜지면 연결**을 선택합니다. 그런 다음, **다음** > **마침** > **확인**을 선택합니다.

## <a name="register-the-configuration-server-with-azure-site-recovery-services"></a>Azure Site Recovery 서비스에 구성 서버 등록

1. VMWare vSphere 클라이언트 콘솔에서 VM을 켭니다.
2. VM이 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다.
4. 처음 로그인하면 몇 초 내에 Azure Site Recovery 구성 도구가 시작됩니다.
5. Site Recovery에 구성 서버를 등록하는 데 사용된 이름을 입력합니다. 그런 후 **다음**을 선택합니다.
6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인**을 선택하여 Azure 구독에 로그인합니다.</br>
    a. 구성 서버를 등록하려는 자격 증명 모음에 자격 증명이 액세스할 수 있어야 합니다.</br>
    b. 선택한 사용자 계정에 Azure에서 응용 프로그램을 만들 수 있는 권한이 있는지 확인 합니다. 필요한 사용 권한이 있도록 지정 하는 지침을 따릅니다 [여기](#azure-active-directory-permission-requirements)합니다.
7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
8. 컴퓨터에 다시 로그인합니다. 몇 초 내에 구성 서버 관리 마법사가 **자동으로** 시작됩니다.

### <a name="configure-settings"></a>설정 구성

1. 구성 서버 관리 마법사에서 **연결 설정**을 선택합니다. 드롭다운 목록에서 먼저 기본 제공 프로세스 서버가 원본 머신에서 Mobility Service의 검색 및 푸시 설치에 사용하는 NIC를 선택한 다음, 구성 서버에서 Azure와의 연결에 사용하는 NIC를 선택합니다. 그런 다음 **저장**을 선택합니다. 구성 된 후이 설정을 변경할 수 없습니다. 구성 서버의 IP 주소는 변경하지 않는 것이 좋습니다. 구성 서버에 할당된 IP가 DHCP IP가 아닌 정적 IP인지 확인합니다.
2. **선택 Recovery Services 자격 증명 모음**에 사용 된 자격 증명을 사용 하 여 Microsoft Azure에 로그인 **6 단계** 의 "[Azure Site Recovery Services를 사용 하 여 구성 서버 등록](#register-the-configuration-server-with-azure-site-recovery-services)" .
3. 로그인 한 후 Azure 구독 및 관련 리소스 그룹 및 자격 증명 모음을 선택 합니다.

    > [!NOTE]
    > Recovery Services 자격 증명 모음은 등록되면 변경할 수 없습니다.
    > 변경 recovery services 자격 증명 모음에는 현재 자격 증명 모음에서 구성 서버의 연결 해제 해야 하 고 구성 서버에서 모든 보호 된 가상 컴퓨터의 복제가 중지 됩니다. [자세히](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) 알아봅니다.

4. **타사 소프트웨어 설치**에서 다음을 수행합니다.

    |시나리오   |수행할 단계  |
    |---------|---------|
    |MySQL을 수동으로 다운로드하고 설치할 수 있나요?     |  예. MySQL 애플리케이션을 다운로드하고, **C:\Temp\ASRSetup** 폴더에 배치한 다음, 수동으로 설치합니다. 이제 사용 약관에 동의하고 **다운로드 및 설치**를 클릭하면 포털에서 *이미 설치되어 있습니다*라고 표시됩니다. 다음 단계로 진행할 수 있습니다.       |
    |MySQL 온라인 다운로드를 방지할 수 있나요?     |   예. MySQL 설치 관리자 애플리케이션을 **C:\Temp\ASRSetup** 폴더에 배치합니다. 사용 약관에 동의하고 **다운로드 및 설치**를 클릭하면 포털에서 추가한 설치 관리자를 사용하고 애플리케이션을 설치합니다. 다음 설치 후 단계로 진행할 수 있습니다.    |
    |Azure Site Recovery를 통해 MySQL을 다운로드하고 설치하려고 합니다.     |  사용권 계약에 동의하고 **다운로드 및 설치**를 클릭합니다. 그러면 다음 설치 후 단계로 진행할 수 있습니다.       |

5. 계속하기 전에 **어플라이언스 구성 유효성 검사**에서 필수 구성 요소가 확인됩니다.
6. **vCenter Server/vSphere ESXi 서버 구성**에 복제하려는 VM이 있는 vCenter 서버 또는 vSphere 호스트의 FQDN 또는 IP 주소를 지정합니다. 서버가 수신 대기하는 포트를 입력합니다. 자격 증명 모음의 VMware 서버에 사용할 이름을 입력합니다.
7. 구성 서버가 VMware 서버에 연결하는 데 사용할 자격 증명을 입력합니다. Site Recovery는 이러한 자격 증명을 사용하여 복제에 사용 가능한 VMware VM을 자동으로 검색합니다. **추가**, **계속**을 차례로 선택합니다. 여기서 입력한 자격 증명은 로컬로 저장됩니다.
8. **가상 머신 자격 증명 구성**에서 복제 중에 모바일 서비스를 자동으로 설치하기 위한 가상 머신의 사용자 이름과 암호를 입력합니다. **Windows** 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다. **Linux**의 경우 루트 계정에 대한 세부 정보를 제공합니다.
9. **구성 완료**를 선택하여 등록을 완료합니다.
10. 등록이 완료되면 Azure Portal을 열고, **Recovery Services 자격 증명 모음** > **관리** > **Site Recovery 인프라** > **구성 서버**에서 구성 서버와 VMware 서버가 나열되어 있는지 확인합니다.

## <a name="upgrade-the-configuration-server"></a>구성 서버 업그레이드

구성 서버를 최신 버전으로 업그레이드하려면 다음 [단계](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)를 따르세요. 모든 Site Recovery 구성 요소를 업그레이드하는 방법에 대한 자세한 지침을 보려면 [여기](service-updates-how-to.md)를 클릭하세요.

## <a name="manage-the-configuration-server"></a>구성 서버 관리

진행 중인 복제가 중단되지 않도록 하려면 구성 서버가 자격 증명 모음에 등록된 후 구성 서버의 IP 주소가 변경되지 않아야 합니다. [여기](vmware-azure-manage-configuration-server.md)에서 일반 구성 서버 관리 작업에 대해 자세히 알아볼 수 있습니다.

## <a name="faq"></a>FAQ

1. OVF를 통해 구성 서버에 제공된 라이선스는 얼마 동안 유효한가요? 라이선스를 다시 활성화하지 않으면 어떻게 되나요?

    OVA 템플릿과 함께 제공 라이선스는 평가판 라이선스를 180 일 동안 유효 합니다. 라이선스가 만료되기 전에 다시 활성화해야 합니다. 그렇지 않으면 구성 서버가 자주 종료되어 복제 작업이 중단될 수 있습니다.

2. 구성 서버가 설치된 VM을 다른 용도로 사용할 수 있나요?

    **아니요**, 구성 서버 용도로만 VM을 사용하는 것이 좋습니다. 재해 복구를 효율적으로 관리하려면 [필수 구성 요소](#prerequisites)에 설명된 사양을 모두 따라야 합니다.
3. 구성 서버에 이미 등록된 자격 증명 모음을 새로 만든 자격 증명 모음으로 전환할 수 있나요?

    **아니요**, 구성 서버에 등록된 후에는 자격 증명 모음을 변경할 수 없습니다.
4. 물리적 컴퓨터와 가상 머신을 모두 보호하는 데 동일한 구성 서버를 사용할 수 있나요?

    **예**, 물리적 머신과 가상 머신을 복제하는 데 동일한 구성 서버를 사용할 수 있습니다. 그러나 물리적 머신은 VMware VM으로만 장애 복구(failback)할 수 있습니다.
5. 구성 서버의 용도는 무엇이고, 어디서 사용되나요?

    구성 서버 및 해당 기능에 대해 자세히 알아보려면 [VMware를 Azure 복제 아키텍처로](vmware-azure-architecture.md)를 참조하세요.
6. 최신 버전의 구성 서버는 어디서 찾을 수 있나요?

    포털을 통해 구성 서버를 업그레이드하는 단계는 [구성 서버 업그레이드](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)를 참조하세요. 모든 Site Recovery 구성 요소를 업그레이드하는 방법에 대한 자세한 지침은 [여기](https://aka.ms/asr_how_to_upgrade)를 참조하세요.
7. 구성 서버의 암호는 어디서 다운로드할 수 있나요?

    암호를 다운로드하려면 [이 문서](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)를 참조하세요.
8. 암호를 변경할 수 있나요?

    **아니요**, **구성 서버의 암호를 변경하지 않는 것**이 좋습니다. 암호를 변경하면 보호되는 머신의 복제가 중단되고 심각한 상태가 됩니다.
9. 자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

    **Recovery Services 자격 증명 모음**에서 **관리** > **Site Recovery 인프라** > **구성 서버**를 통해 다운로드합니다. 서버에서 **등록 키 다운로드**를 선택하여 자격 증명 모음 파일을 다운로드합니다.
10. 기존 구성 서버를 복제하고 복제 오케스트레이션에 사용할 수 있나요?

    **아니요**, 복제된 구성 서버 구성 요소를 사용하는 것은 지원되지 않습니다. 스케일 아웃 프로세스 서버는 복제는 지원 되지 않는 시나리오 이기도합니다. Site Recovery 복제 구성 요소는 진행 중인 복제 영향입니다.

11. 구성 서버의 IP를 변경할 수 있나요?

    **아니요**,구성 서버의 IP 주소는 변경하지 않는 것이 좋습니다. 구성 서버에 할당된 모든 IP가 DHCP IP가 아닌 정적 IP인지 확인합니다.
12. Azure에서 구성 서버를 설정할 수 있나요?

    데이터 전송 대기 시간을 최소화하기 위해 v-Center에서 직접 볼 수 있는 온-프레미스 환경에 구성 서버를 설정하는 것이 좋습니다. [장애 복구](vmware-azure-manage-configuration-server.md#failback-requirements)를 위해 구성 서버의 예약된 백업을 수행할 수 있습니다.

구성 서버에 대한 자세한 FAQ는 [구성 서버의 일반적인 질문에 대한 설명서](vmware-azure-common-questions.md#configuration-server)를 참조하세요.

## <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]



## <a name="next-steps"></a>다음 단계

Azure에 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정합니다.
