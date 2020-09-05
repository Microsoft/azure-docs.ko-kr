---
title: Azure Site Recovery를 사용 하 여 VMware/물리적 서버에 대 한 모바일 에이전트 관리
description: Azure Site Recovery 서비스를 사용 하 여 Azure에 VMware Vm 및 물리적 서버 재해 복구를 위한 모바일 서비스 에이전트를 관리 합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: e65fecb70b7e3adf009396c0daa00fc50b81b519
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604524"
---
# <a name="manage-the-mobility-agent"></a>모바일 에이전트 관리 

Azure에 대 한 VMware Vm 및 물리적 서버 재해 복구를 위해 Azure Site Recovery를 사용 하는 경우 서버에서 모바일 에이전트를 설정 합니다. 모바일 에이전트는 보호 된 컴퓨터, 구성 서버/확장 프로세스 서버 간의 통신을 조정 하 고 데이터 복제를 관리 합니다. 이 문서에서는 모바일 에이전트를 배포한 후 관리 하는 일반적인 작업을 요약 합니다.

>[!TIP]
>특정 OS/Linux 배포판 설치 관리자를 다운로드 하려면 [여기](vmware-physical-mobility-service-overview.md#locate-installer-files)의 지침을 참조 하세요. 포털에서 자동으로 업데이트 하려면 설치 관리자를 다운로드할 필요가 없습니다. [ASR은 구성 서버에서 설치 관리자를 자동으로 페치 하 고 에이전트를 업데이트](#update-mobility-service-from-azure-portal)합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Azure Portal에서 모바일 서비스 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.
    1. , [9.36 버전](https://support.microsoft.com/help/4578241/) 이상, SUSE Linux Enterprise Server 11 s p 4의 경우 [구성 서버 및 스케일 아웃 프로세스 서버에서](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-server)최신 설치 관리자를 사용할 수 있는지 확인 합니다.
1. 포털에서 자격 증명 모음 > **복제된 항목**을 엽니다.
1. 구성 서버를 최신 버전으로 업데이트한 경우 “새 Site Recovery 복제 에이전트 업데이트를 사용할 수 있습니다. 설치하려면 클릭하세요.”라는 알림이 표시됩니다.

     ![복제된 항목 창](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 알림을 클릭하고 **에이전트 업데이트**에서 Mobility 서비스를 업그레이드하려는 머신을 선택합니다. 그런 다음 **확인**을 클릭합니다.

     ![복제된 항목 VM 목록](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 선택한 머신 각각에 대한 Mobility Service 업데이트 작업이 시작됩니다. 모바일 에이전트가 버전의 configuration server로 업데이트 됩니다. 예를 들어 구성 서버가 버전 9.33에 있는 경우 보호 된 VM의 모바일 에이전트도 9.33 버전으로 업데이트 됩니다.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Windows server의 powershell 스크립트를 통해 모바일 서비스 업데이트

시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.

다음 스크립트를 사용 하 여 power shell cmdlet을 통해 서버에서 모바일 서비스를 업그레이드 합니다.

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>각 보호 된 서버에서 모바일 서비스를 수동으로 업데이트

1. 시작에 앞서, 보호되는 머신에서 Mobility 서비스를 업데이트하기 전에 배포의 일부인 구성 서버, 확장 프로세스 서버 및 마스터 대상 서버를 업데이트했는지 확인하세요.

2. 서버의 운영 체제에 따라 [에이전트 설치 관리자를 찾습니다](vmware-physical-mobility-service-overview.md#locate-installer-files) .

>[!IMPORTANT]
> 한 Azure 지역에서 다른 지역으로 Azure IaaS VM을 복제 하는 경우이 방법을 사용 하지 마세요. 사용 가능한 모든 옵션에 대 한 자세한 내용은 [microsoft의 지침](azure-to-azure-autoupdate.md) 을 참조 하세요.

3. 보호 된 컴퓨터에 설치 파일을 복사 하 고 실행 하 여 모바일 에이전트를 업데이트 합니다.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>모바일 서비스의 푸시 설치에 사용 되는 계정 업데이트

Site Recovery를 배포하여 Mobility 서비스의 푸시 설치를 사용하도록 설정한 경우, 복제가 머신에 대해 사용하도록 설정되었을 때 Site Recovery 프로세스 서버가 머신에 액세스하여 서비스를 설치하는 데 사용하는 계정을 지정했습니다. 이 계정에 대한 자격 증명을 업데이트하려면 [이 지침](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 따릅니다.

## <a name="uninstall-mobility-service"></a>모바일 서비스 제거

### <a name="on-a-windows-machine"></a>Windows 머신

UI 또는 명령 프롬프트에서 제거합니다.

- **UI에서**: 머신의 제어판에서 **프로그램**을 선택합니다. **모바일 서비스/마스터 대상 서버 제거 Microsoft Azure Site Recovery**선택  >  **Uninstall**합니다.
- **명령 프롬프트에서**: 머신에서 관리자 권한으로 명령 프롬프트 창을 엽니다. 다음 명령을 실행합니다. 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Linux 머신
1. Linux 머신에서 **루트** 사용자로 로그인합니다.
2. 터미널에서/Usr/local/asr로 이동 합니다.
3. 다음 명령을 실행합니다.
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>원본 컴퓨터에 Site Recovery VSS 공급자 설치

응용 프로그램 일관성 점수를 생성 하려면 원본 컴퓨터에 VSS 공급자 Azure Site Recovery 필요 합니다. 푸시 설치를 통해 공급자를 설치 하지 못한 경우에는 아래 지침에 따라 수동으로 설치 합니다.

1. 관리 cmd 창을 엽니다.
2. 모바일 서비스 설치 위치로 이동 합니다. (예: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent)
3. InMageVSSProvider_Uninstall 스크립트를 실행 합니다. 서비스가 이미 있는 경우이를 제거 합니다.
4. InMageVSSProvider_Install 스크립트를 실행 하 여 VSS 공급자를 수동으로 설치 합니다.

## <a name="next-steps"></a>다음 단계

- [VMware Vm에 대 한 재해 복구 설정](vmware-azure-tutorial.md)
- [물리적 서버에 대한 재해 복구 설정](physical-azure-disaster-recovery.md)
