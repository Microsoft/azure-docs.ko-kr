---
title: Azure Backup Server를 사용하여 VMware VM 백업
description: Azure Backup Server를 사용하여 VMware vCenter/ESXi 서버에서 실행 중인 VMware VM을 백업할 수 있습니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 6fb95d43bff79ca91988549114daa91e5d41f358
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650150"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Azure Backup Server를 사용하여 VMware VM 백업

이 문서에서는 Azure Backup Server를 사용하여 VMware ESXi 호스트/vCenter Server에서 실행 중인 VMware VM을 Azure로 백업하는 방법을 설명합니다.

이 문서에서는 다음 작업을 수행하는 방법을 설명합니다.

- Azure Backup Server가 HTTPS를 통해 VMware 서버와 통신할 수 있도록 보안 채널을 설정합니다.
- Azure Backup Server에서 VMware 서버에 액세스하는 데 사용하는 VMware 계정을 설정합니다.
- Azure Backup에 계정 자격 증명을 추가합니다.
- Azure Backup Server에 vCenter 또는 ESXi 서버를 추가합니다.
- 백업할 VMware VM을 포함하는 보호 그룹을 설정하고 백업 설정을 지정한 다음, 백업을 예약합니다.

## <a name="before-you-start"></a>시작하기 전에
- 백업이 지원되는 vCenter/ESXi 버전(버전 6.5, 6.0 및 5.5)을 실행 중인지 확인합니다.
- Azure Backup Server를 설정했는지 확인합니다. 설정하지 않은 경우 시작하기 전에 [설정](backup-azure-microsoft-azure-backup.md)합니다. 최신 업데이트를 사용하여 Azure Backup Server를 실행해야 합니다.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server에 대한 보안 연결 만들기

기본적으로 Azure Backup Server는 HTTPS를 통해 VMware 서버와 통신합니다. HTTPS 연결을 설정하려면 VMware CA(인증 기관) 인증서를 다운로드하여 Azure Backup Server에서 가져옵니다.


### <a name="before-you-start"></a>시작하기 전에

- HTTPS를 사용하지 않으려는 경우 [기본 설정을 사용하지 않도록 설정](backup-azure-backup-server-vmware.md)할 수 있습니다.
- 일반적으로 vSphere Web Client를 사용하여 Azure Backup Server 머신의 브라우저에서 vCenter/ESXi 서버로 연결합니다. 처음 이 작업을 수행할 때는 연결이 안전하지 않으며 다음 메시지가 표시됩니다.
- Azure Backup Server에서 백업을 처리하는 방법을 파악하는 것이 중요합니다.
    - 첫 번째 단계로 Azure Backup Server는 데이터를 로컬 디스크 스토리지에 백업합니다. Azure Backup Server가 보호된 데이터에 대한 디스크 복구 지점을 저장하는 디스크 및 볼륨 세트인 스토리지 풀이 Azure Backup Server에서 사용됩니다. 스토리지 풀은 DAS(직접 연결된 스토리지), 파이버 채널 SAN, iSCSI 스토리지 디바이스 또는 SAN일 수 있습니다. VMware VM 데이터를 로컬로 백업하기에 충분한 스토리지가 있는지 확인하는 것이 중요합니다.
    - 그러면 Azure Backup Server는 로컬 디스크 스토리지에서 Azure로 백업합니다.
    - [도움말 보기](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need)를 통해 필요한 스토리지 공간 크기를 파악합니다. 이 정보는 DPM을 위한 것이지만 Azure Backup Server에도 사용할 수 있습니다.

### <a name="set-up-the-certificate"></a>인증서 설정

다음과 같이 보안 채널을 설정합니다.

1. Azure Backup Server의 브라우저에 vSphere Web Client URL을 입력합니다. 로그인 페이지가 나타나지 않으면 연결 및 브라우저 프록시 설정을 확인합니다.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. vSphere Web Client 로그인 페이지에서 **신뢰할 수 있는 루트 CA 인증서 다운로드**를 클릭합니다.

    ![신뢰할 수 있는 루트 CA 인증서 다운로드](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. **download**라는 파일이 다운로드됩니다. 브라우저에 따라 파일을 열거나 저장할 것인지 묻는 메시지가 나타납니다.

    ![CA 인증서 다운로드](./media/backup-azure-backup-server-vmware/download-certs.png)

4. 이 파일을 Azure Backup Server 머신에 zip 확장명으로 저장합니다.

5. **download.zip**을 마우스 오른쪽 단추로 클릭하고 > **모두 추출**을 클릭합니다. 다음을 포함하는 zip 파일의 콘텐츠가 **certs** 폴더로 추출됩니다.
   - .0 및 .1과 같은 숫자가 매겨진 시퀀스로 시작하는 확장명을 가진 루트 인증서 파일.
   - CRL 파일은 .r0 또는 .r1과 같은 시퀀스로 시작하는 확장명을 갖습니다. CRL 파일은 인증서와 연결되어 있습니다.

     ![다운로드한 인증서](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. **certs** 폴더에서 루트 인증서 파일을 마우스 오른쪽 단추로 클릭하고 > **이름 바꾸기**를 클릭합니다.

    ![루트 인증서 이름 바꾸기](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. 루트 인증서의 확장명을 .crt로 변경하고 확인합니다. 파일 아이콘이 루트 인증서를 나타내는 아이콘으로 변경됩니다.

7. 루트 인증서를 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 **인증서 설치**를 선택합니다.

8. **인증서 가져오기 마법사**에서 **로컬 머신**을 인증서의 대상으로 선택하고 **다음**을 클릭합니다. 컴퓨터 변경을 허용할지 묻는 메시지가 표시되면 확인합니다.

    ![마법사 시작](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. **인증서 저장소** 페이지에서 **모든 인증서를 다음 저장소에 저장**을 선택한 다음 **찾아보기**를 클릭하여 인증서 저장소를 선택합니다.

     ![인증서 스토리지](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. **인증서 저장소 선택**에서 **신뢰할 수 있는 루트 인증 기관**을 인증서 대상 폴더로 선택하고 **확인**을 클릭합니다.

    ![인증서 대상 폴더](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. **인증서 가져오기 마법사 완료**에서 폴더를 확인하고 **마침**을 클릭합니다.

    ![인증서가 적절한 폴더에 있는지 확인](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. 인증서 가져오기가 확인된 후 vCenter Server에 로그인하여 연결이 안전한지 확인합니다.




### <a name="disable-default-https"></a>기본 HTTPS 사용 안 함

조직 내에 보안 경계가 있고 VMware 서버와 Azure Backup Server 머신 간에 HTTPS 프로토콜을 사용하지 않으려는 경우 다음과 같이 HTTPS를 사용하지 않도록 설정합니다.
1. 다음 텍스트를 복사하여 .txt 파일에 붙여넣습니다.

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. 파일 이름을 **DisableSecureAuthentication.reg**로 지정하여 Azure Backup Server 머신에 저장합니다.

3. 레지스트리 항목을 활성화하려면 파일을 두 번 클릭합니다.


## <a name="create-a-vmware-role"></a>VMware 역할 만들기

v-Center Server/ESXi 호스트에 액세스할 수 있는 권한을 가진 사용자 계정이 Azure Backup Server에 필요합니다. 특정 권한을 가진 VMware 역할을 만든 다음, 이 역할에 사용자 계정을 연결합니다.

1. vCenter Server(또는 vCenter Server를 사용하지 않는 경우 ESXi 호스트)에 로그인합니다.
2. **탐색기** 패널에서 **관리**를 클릭합니다.

    ![관리](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. **관리** > **역할**에서 역할 추가 아이콘(+ 기호)을 클릭합니다.

    ![역할 추가](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. **역할 만들기** > **역할 이름**에 *BackupAdminRole*을 입력합니다. 역할 이름은 원하는 대로 사용할 수 있지만 이름은 역할의 목적에 대해 쉽게 인식할 수 있어야 합니다.

5. 아래 표에 요약된 대로 권한을 선택하고 **확인**을 클릭합니다.  새 역할이 **역할** 패널의 목록에 나타납니다.
   - 부모 레이블 옆의 아이콘을 클릭하여 부모를 확장하고 자식 권한을 봅니다.
   - VirtualMachine 권한을 선택하려면 부모 자식 계층으로 여러 개의 수준을 이동해야 합니다.
   - 부모 권한 내 모든 자식 권한을 선택하지 않아도 됩니다.

     ![부모 자식 권한 계층 구조](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>역할 권한
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>VMware 계정 만들기

1. vCenter Server **탐색기** 패널에서 **사용자 및 그룹**을 클릭합니다. vCenter Server를 사용하지 않는 경우 해당 ESXi 호스트에 계정을 만듭니다.

    ![사용자 및 그룹 옵션](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    합니다 **vCenter 사용자 및 그룹** 패널을 표시 합니다.


2. **vCenter 사용자 및 그룹** 패널에서 **사용자** 탭을 선택한 다음 사용자 추가 아이콘(+ 기호)을 클릭합니다.

     ![vCenter 사용자 및 그룹 패널](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. **새 사용자** 대화 상자에서 사용자 정보를 추가하고 > **확인**을 클릭합니다. 이 절차에서 사용자 이름은 BackupAdmin입니다.

    ![새 사용자 대화 상자](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. 사용자 계정을 역할과 연결하려면 **탐색기** 패널에서 **전역 권한**을 클릭합니다. **전역 권한** 패널에서 **관리** 탭을 선택한 다음 추가 아이콘(+ 기호)을 클릭합니다.

    ![전역 권한 패널](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. **전역 권한 루트 - 권한 추가**에서 **추가**를 클릭하여 사용자 또는 그룹을 선택합니다.

    ![사용자 또는 그룹 선택](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. **사용자/그룹 선택**에서 **BackupAdmin** > **추가**를 선택합니다. **사용자**에서 *domain\username* 형식이 사용자 계정에 사용됩니다. 다른 도메인을 사용하려면 **도메인** 목록에서 원하는 도메인을 선택합니다. **확인**을 클릭하여 선택한 사용자를 **권한 추가** 대화 상자에 추가합니다.

    ![BackupAdmin 사용자 추가](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  **할당된 역할**의 드롭다운 목록에서 **BackupAdminRole** > **확인**을 선택합니다.

    ![사용자를 역할에 할당](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


**전역 권한** 패널의 **관리** 탭에서 새 사용자 계정 및 연결된 역할이 목록에 표시됩니다.


## <a name="add-the-account-on-azure-backup-server"></a>Azure Backup Server에 계정 추가


1. Azure Backup Server를 엽니다. 바탕 화면에서 아이콘을 찾을 수 없는 경우 앱 목록에서 Microsoft Azure Backup을 엽니다.

    ![Azure Backup Server 아이콘](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Azure Backup Server 콘솔에서 **관리** >  **프로덕션 서버** > **VMware 관리**를 클릭합니다.

    ![Azure Backup Server 콘솔](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. **자격 증명 관리** 대화 상자에서 **추가**를 클릭합니다.

    ![Azure Backup Server 자격 증명 관리 대화 상자](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. **인증서 추가**에서 새 자격 증명의 이름과 설명을 입력하고 VMware 서버에서 정의한 사용자 이름과 암호를 지정합니다. Contoso Vcenter 자격 증명이란 이름은 이 절차에서 자격 증명을 식별하는 데 사용됩니다. VMware 서버와 Azure Backup Server가 있는 도메인이 동일하지 않으면 사용자 이름에 도메인을 지정합니다.

    ![Azure Backup Server 자격 증명 추가 대화 상자](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. **추가**를 클릭하여 새 자격 증명을 추가합니다.

    ![Azure Backup Server 자격 증명 관리 대화 상자](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>vCenter Server 추가

Azure Backup Server에 vCenter Server를 추가합니다.


1. Azure Backup Server 콘솔에서 **관리** > **프로덕션 서버** > **추가**를 클릭합니다.

    ![프로덕션 서버 추가 마법사 열기](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. **프로덕션 서버 추가 마법사** > **프로덕션 서버 유형 선택** 페이지에서 **VMware 서버**를 선택하고 **다음**을 클릭합니다.

     ![프로덕션 서버 추가 마법사](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. **컴퓨터 선택** **서버 이름/IP 주소**에서 VMware 서버의 FQDN 또는 IP 주소를 지정합니다. 모든 ESXi 서버를 동일한 vCenter가 관리하는 경우 vCenter 이름을 지정합니다. 그렇지 않으면 ESXi 호스트를 추가합니다.

    ![VMware 서버 지정](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **SSL 포트**에 VMware 서버와 통신하는 데 사용되는 포트를 입력합니다. 443이 기본 포트지만, VMware 서버가 다른 포트에서 수신 대기하는 경우에는 변경할 수 있습니다.

5. **자격 증명 지정**에서 이전에 만든 자격 증명을 선택합니다.

    ![자격 증명 지정](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. **추가**를 클릭하여 VMware 서버를 서버 목록에 추가합니다. 그런 후 **Next** 를 클릭합니다.

    ![VMWare 서버 및 자격 증명 추가](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **요약** 페이지에서 **추가**를 클릭하여 VMware 서버를 Azure Backup Server에 추가합니다. 새 서버가 즉시 추가되며, VMware 서버에 에이전트가 필요하지 않습니다.

    ![Azure Backup Server에 VMware 서버 추가](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. **마침** 페이지에서 설정을 확인합니다.

   ![마침 페이지](./media/backup-azure-backup-server-vmware/summary-screen.png)

vCenter 서버에서 관리하지 않는 ESXi 호스트가 여러 개 있거나 vCenter Server 인스턴스가 여러 개 있는 경우 마법사를 다시 실행하여 서버를 추가해야 합니다.




## <a name="configure-a-protection-group"></a>보호 그룹 구성

백업용 VMware VM을 추가합니다. 보호 그룹은 여러 VM을 수집하고 동일한 데이터 보존 및 백업 설정을 그룹의 모든 VM에 적용합니다.


1. Azure Backup Server 콘솔에서 **보호** > **새로 만들기**를 클릭합니다.

    ![새 보호 그룹 만들기 마법사 열기](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. **새 보호 그룹 만들기** 마법사 시작 페이지에서 **다음**을 클릭합니다.

    ![새 보호 그룹 만들기 마법사 대화 상자](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. **보호 그룹 형식 선택** 페이지에서 **서버**를 선택하고 **다음**을 클릭합니다. **그룹 구성원 선택** 페이지가 나타납니다.

1. **그룹 구성원 선택**에서 > 백업할 VM(또는 VM 폴더)을 선택합니다. 그런 후 **Next** 를 클릭합니다.

    - 폴더를 선택하면 해당 폴더 내의 VM 또는 폴더도 백업되도록 선택됩니다. 백업하지 않으려는 폴더 또는 VM을 선택 취소할 수 있습니다.
1. VM 또는 폴더가 이미 백업 중인 경우에는 선택할 수 없습니다. 이 기능은 VM에 대해 중복 복구 지점이 생성되지 않도록 합니다. .

     ![그룹 구성원 선택](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. **데이터 보호 방법 선택** 페이지에서 보호 그룹의 이름과 보호 설정을 입력합니다. Azure에 백업하려면 단기 보호를 **디스크**로 설정하고 온라인 보호를 사용하도록 설정합니다. 그런 후 **Next** 를 클릭합니다.

    ![데이터 보호 방법 선택](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. **단기 목표 지정**에서 데이터를 디스크에 백업하는 기간을 지정합니다.
   - **보존 범위**에서 디스크 복구 지점을 유지하는 기간(일)을 지정합니다.
   - **동기화 빈도**에서 디스크 복구 지점의 사용 빈도를 지정합니다.
       - 백업 간격을 설정하지 않으려는 경우 각 복구 지점이 예약되기 직전에 백업이 실행되도록 **복구 지점 직전**을 선택할 수 있습니다.
       - 단기 백업은 전체 백업이며 증분이 아닙니다.
       - 단기 백업이 수행되는 시간/날짜를 변경하려면 **수정**을 클릭합니다.

     ![단기 목표 지정](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. **디스크 할당 검토**에서 VM 백업용으로 VM에 대해 제공된 디스크 공간을 검토합니다.

   - 권장되는 디스크 할당은 지정된 보존 범위, 워크로드의 형식 및 보호된 데이터의 크기를 기반으로 합니다. 필요에 따라 변경하고 **다음**을 클릭합니다.
   - **데이터 크기:** 보호 그룹의 데이터 크기입니다.
   - **디스크 공간:** 보호 그룹에 권장되는 디스크 공간의 크기입니다. 이 설정을 수정하려는 경우 총 공간을 각 데이터 원본의 예상 확장량보다 약간 더 크게 할당해야 합니다.
   - **데이터 공동 배치:** 공동 배치를 켜면, 보호되는 여러 데이터 원본이 단일 복제본 및 복구 지점 볼륨에 매핑될 수 있습니다. 공동 배치는 모든 워크로드에 지원되는 것은 아닙니다.
   - **자동 증가:** 이 설정을 켜면 보호된 그룹의 데이터가 초기 할당량을 초과할 경우 Azure Backup Server가 25%까지 디스크 크기 증가를 시도합니다.
   - **스토리지 풀 세부 정보:** 총 디스크 크기 및 남아 있는 디스크 크기를 포함한 스토리지 풀의 상태를 표시합니다.

     ![디스크 할당 검토](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. **복제본 만들기 방법 선택** 페이지에서 초기 백업을 수행할 방법을 지정하고 **다음**을 클릭합니다.
   - 기본 설정은 **네트워크를 통해 자동으로** 및 **지금**입니다.
   - 기본값을 사용하는 경우 사용량이 적은 시간을 지정하는 것이 좋습니다. **나중에**를 선택하고 날짜와 시간을 지정합니다.
   - 대용량 데이터이거나 네트워크 상태가 최적화되지 않은 경우 이동식 미디어를 사용하여 데이터를 오프라인으로 복제하는 것을 고려하세요.

     ![복제본 만들기 방법 선택](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. **일관성 확인 옵션**에서 일관성 확인을 자동화할 방법 및 시기를 선택합니다. 그런 후 **Next** 를 클릭합니다.
      - 복제 데이터가 일관성을 잃은 경우 또는 설정된 일정에 따라 일관성 확인을 실행할 수 있습니다.
      - 자동 일관성 확인을 구성하지 않으려면 수동 검사를 실행할 수 있습니다. 이 작업을 수행하려면 보호 그룹을 마우스 오른쪽 단추로 클릭하고 > **일관성 확인 수행**을 클릭합니다.

1. **온라인 보호 데이터 지정** 페이지에서 백업할 VM 또는 VM 폴더를 선택합니다. 구성원을 개별적으로 선택하거나 **모두 선택**을 클릭하여 모든 구성원을 선택할 수 있습니다. 그런 후 **Next** 를 클릭합니다.

      ![온라인 보호 데이터 지정](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. **온라인 백업 예약 지정** 페이지에서 로컬 스토리지의 데이터를 Azure로 백업할 빈도를 지정합니다.

    - 일정에 따라 데이터에 대한 클라우드 복구 지점이 생성됩니다. 그런 후 **Next** 를 클릭합니다.
    - 복구 지점이 생성되면 Azure에서 Recovery Services 자격 증명 모음으로 전송됩니다.

      ![온라인 백업 일정 지정](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. **온라인 보존 정책 지정** 페이지에서 매일, 매주, 매월 및 매년 백업을 통해 만들어진 복구 지점을 Azure에 유지할 기간을 지정합니다. **다음**을 클릭합니다.

    - Azure에서 데이터를 유지할 수 있는 기간에 대한 시간 제한은 없습니다.
    - 보호된 인스턴스당 9999개 이하의 복구 지점을 사용해야 한다는 점이 유일한 제한입니다. 이 예제에서 보호되는 인스턴스는 VMware 서버입니다.

      ![온라인 보존 정책 지정](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. **요약** 페이지에서 설정을 검토한 다음, **그룹 만들기**를 클릭합니다.

     ![보호 그룹 구성원 및 설정 요약](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

에 백업 vSphere 6.7 다음을 수행 합니다.

- DPM 서버에서 TLS 1.2 사용
  >[!Note]
  >VMWare 6.7 이상 통신 프로토콜로 TLS를 사용 했습니다.

- 다음과 같이 레지스트리 키를 설정 합니다.  

  Windows 레지스트리 편집기 버전 5.00

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 s"SchUseStrongCrypto"=dword:00000001


## <a name="next-steps"></a>다음 단계

백업을 설정할 때 발생하는 문제를 해결하려면 [Azure Backup Server 문제 해결 가이드](./backup-azure-mabs-troubleshoot.md)를 검토합니다.
