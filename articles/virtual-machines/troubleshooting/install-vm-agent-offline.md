---
title: 오프라인 모드에서 Azure VM 에이전트 설치 | Microsoft Docs
description: 오프라인 모드에서 Azure VM 에이전트를 설치하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e9fc8351b5e9a4f2274f0906d4071f86dcbcff26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640669"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>오프라인 모드에서 Azure 가상 머신 에이전트 설치 

Azure VM 에이전트(가상 머신 에이전트)는 로컬 관리자 암호 재설정 및 푸시 스크립트 같은 유용한 기능을 제공합니다. 이 문서에서는 오프라인 Windows VM(가상 머신)용 VM 에이전트를 설치하는 방법을 보여줍니다. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>오프라인 모드에서 VM 에이전트 설치를 사용하는 시기

다음과 같은 시나리오에서는 오프라인 모드에서 VM 에이전트를 설치합니다.

- 배포된 Azure VM에 VM 에이전트가 설치되지 않았거나 에이전트가 작동하지 않습니다.
- VM의 관리자 암호가 기억 나지 않거나 VM에 액세스할 수 없습니다.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>오프라인 모드에서 VM 에이전트를 설치하는 방법

다음 단계에 따라 오프라인 모드에서 VM 에이전트를 설치합니다.

> [!NOTE]
> 오프라인 모드에서 VM 에이전트를 설치하는 프로세스를 자동화할 수 있습니다.
> 이렇게 하려면 [Azure VM 복구 스크립트](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md)를 사용합니다. Azure VM 복구 스크립트를 사용하려는 경우 다음 프로세스를 사용할 수 있습니다.
> 1. 스크립트를 사용해 관련 VM의 OS 디스크를 복구 VM에 연결하는 방식으로 1단계를 건너뜁니다.
> 2. 2~10단계를 진행하여 완화 내용을 적용합니다.
> 3. 스크립트를 사용해 VM을 다시 빌드하는 방식으로 11단계를 건너뜁니다.
> 4. 12단계를 진행합니다.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>1단계: VM의 OS 디스크를 다른 VM에 데이터 디스크로 연결

1.  VM을 삭제합니다. VM을 삭제할 때 **디스크 유지** 옵션을 선택해야 합니다.

2.  OS 디스크를 (_문제 해결사_ VM으로 알려진) 다른 VM에 데이터로 연결합니다. 자세한 내용은 [Azure Portal에서 Windows VM에 데이터 디스크 연결](../windows/attach-managed-disk-portal.md)을 참조하세요.

3.  문제 해결사 VM에 연결합니다. **컴퓨터 관리** > **디스크 관리**를 엽니다. OS 디스크가 온라인 상태이고 드라이브 문자가 디스크 파티션에 할당되었는지 확인합니다.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>2단계: Azure VM 에이전트를 설치하도록 OS 디스크 수정

1.  문제 해결사 VM에 대한 원격 데스크톱 연결을 만듭니다.

2.  연결한 OS 디스크에서 \windows\system32\config 폴더를 찾습니다. 롤백이 필요한 경우 이 폴더에 있는 모든 파일을 복사합니다.

3.  **레지스트리 편집기**를 시작합니다(regedit.exe).

4.  **HKEY_LOCAL_MACHINE** 키를 선택합니다. 메뉴에서 **파일** > **Hive 로드**를 선택합니다.

    ![Hive를 로드합니다.](./media/install-vm-agent-offline/load-hive.png)

5.  연결한 OS 디스크에서 \windows\system32\config\SYSTEM 폴더를 찾습니다. Hive 이름으로 **BROKENSYSTEM**을 입력합니다. 새 레지스트리 Hive는 **HKEY_LOCAL_MACHINE** 키 아래에 표시됩니다.

6.  연결한 OS 디스크에서 Browse to the \windows\system32\config\SOFTWARE 폴더를 찾습니다. Hive 소프트웨어 이름으로 **BROKENSOFTWARE**를 입력합니다.

7. 연결된 OS 디스크에 VM 에이전트가 설치된 경우 현재 구성의 백업을 수행합니다. 연결된 OS 디스크에 VM 에이전트가 설치되지 않은 경우 다음 단계로 이동합니다.
      
    1. \windowsazure 폴더 이름을 \windowsazure.old로 바꿉니다.

    2. 다음 레지스트리를 내보냅니다.
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  문제 해결사 VM에서 기존 파일을 VM 에이전트 설치의 리포지토리로 사용합니다. 다음 단계를 완료합니다.

    1. 문제 해결사 VM에서 다음 하위 키를 레지스트리 형식(.reg)으로 내보냅니다. 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![레지스트리 하위 키 내보내기](./media/install-vm-agent-offline/backup-reg.png)

    2. 레지스트리 파일을 편집합니다. 각 파일에서 항목 값 **SYSTEM**을 **BROKENSYSTEM**으로 변경하고(다음 이미지처럼) 파일을 저장합니다. 현재 VM 에이전트의 **ImagePath**를 기억하세요. 연결된 OS 디스크에 해당 폴더를 복사해야 합니다. 

        ![레지스트리 하위 키 값 변경](./media/install-vm-agent-offline/change-reg.png)

    3. 각 레지스트리 파일을 두 번 클릭하여 레지스트리 파일을 리포지토리로 가져옵니다.

    4. 다음 세 하위 키를 성공적으로 **BROKENSYSTEM** Hive로 가져왔는지 확인합니다.
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. 연결된 OS 디스크에 현재 VM 에이전트의 설치 폴더를 복사합니다. 

        1.  연결한 OS 디스크의 루트 경로에서 WindowsAzure라는 폴더를 만듭니다.

        2.  문제 해결사 VM의 C:\WindowsAzure로 이동하여 C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX 이름의 모든 폴더를 찾습니다. 최신 버전 번호가 있는 GuestAgent 폴더를 C:\WindowsAzure에서 연결된 된 OS 디스크의 WindowsAzure 폴더로 복사 합니다. 폴더가 복사됐는지 확실하지 않은 경우 모든 GuestAgent 폴더를 복사합니다. 다음 이미지에서는 연결된 OS 디스크에 복사되는 GuestAgent 폴더의 예를 보여줍니다.

             ![GuestAgent 폴더 복사](./media/install-vm-agent-offline/copy-files.png)

9.  **BROKENSYSTEM**을 선택합니다. 메뉴에서 **파일** > **Hive 언로드**를 선택합니다.

10.  **BROKENSOFTWARE**를 선택합니다. 메뉴에서 **파일** > **Hive 언로드**를 선택합니다.

11.  OS 디스크를 분리하고, OS 디스크를 사용하여 VM을 다시 만듭니다.

12.  VM에 액세스합니다. RdAgent가 실행 중이고 로그가 생성됩니다.

Resource Manager 배포 모델을 사용하여 VM을 만든 경우 작업이 완료됩니다.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>클래식 VM에 ProvisionGuestAgent 속성 사용

클래식 모델을 사용하여 VM을 만든 경우 Azure PowerShell 모듈을 사용하여 **ProvisionGuestAgent** 속성을 업데이트합니다. 이 속성은 VM에 VM 에이전트가 설치되었다는 사실을 Azure에 알려줍니다.

**ProvisionGuestAgent** 속성을 설정하려면 Azure PowerShell에서 다음 명령을 실행합니다.

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

`Get-AzureVM` 명령을 실행합니다. **GuestAgentStatus** 속성은 이제 데이터로 채워집니다.

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>다음 단계

- [Azure 가상 머신 에이전트 개요](../extensions/agent-windows.md)
- [Windows용 가상 머신 확장 및 기능](../extensions/features-windows.md)
