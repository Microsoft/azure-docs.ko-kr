---
title: Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 재해 복구 설정
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 마이그레이션한 후 Azure 지역 간에 재해 복구를 설정하기 위해 컴퓨터를 준비하는 방법을 설명합니다.
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 41fa4f5d042b5c284a58b0fd87486e2309b03053
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581735"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Azure로 마이그레이션한 후 Azure VM에 대해 재해 복구 설정 


[Site Recovery](site-recovery-overview.md) 서비스를 사용하여 [온-프레미스 컴퓨터를 Azure VM으로 마이그레이션](./migrate-tutorial-on-premises-azure.md)하고 이제 보조 Azure 지역에 대한 재해 복구를 위해 VM을 설정하려고 합니다. 이 문서에서는 마이그레이션된 VM에 Azure VM 에이전트가 설치되어 있는지 확인하는 방법 및 마이그레이션 후 더 이상 필요하지 않은 Site Recovery 모바일 서비스를 제거하는 방법을 설명합니다.



## <a name="verify-migration"></a>마이그레이션 확인

재해 복구를 설정하기 전에 마이그레이션이 예상대로 완료되었는지 확인합니다. 마이그레이션을 성공적으로 완료하려면 마이그레이션할 각 컴퓨터에 대해 **마이그레이션 완료** 옵션을 선택해야 합니다. 

## <a name="verify-the-azure-vm-agent"></a>Azure VM 에이전트 확인

각 Azure VM에는 [Azure VM 에이전트](../virtual-machines/extensions/agent-windows.md)가 설치되어 있어야 합니다. Azure VM을 복제하기 위해 Site Recovery는 에이전트에 확장을 설치합니다.

- 컴퓨터에서 9.7.0.0 이상 버전의 Site Recovery 모바일 서비스를 실행하는 경우 Azure VM 에이전트가 Windows VM의 모바일 서비스에 의해 자동으로 설치됩니다. 이전 버전의 모바일 서비스에서는 에이전트를 수동으로 설치합니다.
- Linux VM의 경우 Azure VM 에이전트를 수동으로 설치해야 합니다. 마이그레이션된 컴퓨터에 설치된 모바일 서비스가 v9.6 또는 이전 버전인 경우에만 Azure VM 에이전트를 설치해야 합니다.


### <a name="install-the-agent-on-windows-vms"></a>Windows VM에 에이전트 설치

9\.7.0.0 이전 버전의 Site Recovery 모바일 서비스를 실행하는 경우 또는 에이전트를 수동으로 설치해야 하는 경우에는 다음을 수행합니다.  

1. VM에 대한 관리자 권한이 있는지 확인합니다.
2. [VM 에이전트 설치 프로그램](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다운로드합니다.
3. 설치 관리자 파일을 실행합니다.

#### <a name="validate-the-installation"></a>설치 유효성 검사
에이전트가 설치되어 있는지 확인하려면 다음을 수행합니다.

1. Azure VM의 C:\WindowsAzure\Packages 폴더에 WaAppAgent.exe 파일이 표시되어야 합니다.
2. 파일을 마우스 오른쪽 단추로 클릭하고 **속성** 에서 **세부 정보** 탭을 선택합니다.
3. **제품 버전** 필드에 2.6.1198.718 이상이 표시되는지 확인합니다.

Windows용 에이전트 설치에 대해 [자세히 알아봅니다](../virtual-machines/extensions/agent-windows.md).

### <a name="install-the-agent-on-linux-vms"></a>Linux VM에 에이전트 설치

다음과 같이 [Azure LINUX VM](../virtual-machines/extensions/agent-linux.md) 에이전트를 수동으로 설치합니다.

1. 컴퓨터에 대한 관리자 권한이 있는지 확인합니다.
2. 배포 패키지 리포지토리의 RPM 또는 DEB 패키지를 사용하여 Linux VM 에이전트를 설치하는 것이 좋습니다. 모든 [인증 배포 공급자](../virtual-machines/linux/endorsed-distros.md)는 이미지 및 리포지토리에 Azure Linux 에이전트 패키지를 통합합니다.
    - 배포 리포지토리를 사용할 때만 에이전트를 업데이트할 것을 강력히 권장합니다.
    - GitHub에서 직접 Linux VM 에이전트를 설치하고 업데이트하지 않는 것이 좋습니다.
    -  최신 에이전트를 배포할 수 없는 경우 배포 지원에 문의하여 설치 방법에 대한 지침을 얻으세요. 

#### <a name="validate-the-installation"></a>설치 유효성 검사 

1. **ps -e** 명령을 실행하여 Azure 에이전트가 Linux VM에서 실행되고 있는지 확인합니다.
2. 이 프로세스가 실행되고 있지 않으면 다음 명령을 사용하여 다시 시작합니다.
    - Ubuntu의 경우: **service walinuxagent start**
    - 기타 배포의 경우: **service waagent start**


## <a name="uninstall-the-mobility-service"></a>모바일 서비스 설치 제거

1. 다음 방법 중 하나를 사용하여 Azure VM에서 모바일 서비스를 수동으로 제거합니다. 
    - Windows의 경우 제어판 > **프로그램 추가/제거** 에서 **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버** 를 제거합니다. 관리자 권한의 명령 프롬프트에서 다음을 실행합니다.
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux의 경우 루트 사용자로 로그인합니다. 터미널에서 **/user/local/ASR** 로 이동하여 다음 명령을 실행합니다.
        ```
        ./uninstall.sh -Y
        ```
2. 복제를 구성하기 전에 VM을 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

Azure VM 에이전트에서 Site Recovery 확장에 대한 [문제 해결을 검토](site-recovery-extension-troubleshoot.md)합니다.
Azure VM을 보조 지역에 [신속하게 복제](azure-to-azure-quickstart.md)합니다.
