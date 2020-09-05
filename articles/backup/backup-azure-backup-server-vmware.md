---
title: Azure Backup Server를 사용하여 VMware VM 백업
description: 이 문서에서는 Azure Backup Server를 사용 하 여 VMware vCenter/ESXi 서버에서 실행 되는 VMware Vm을 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: e18b5c51446446103a91ef7d6a00277c2b41db77
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017569"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Azure Backup Server를 사용하여 VMware VM 백업

이 문서에서는 Azure Backup Server를 사용하여 VMware ESXi 호스트/vCenter Server에서 실행 중인 VMware VM을 Azure로 백업하는 방법을 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

- Azure Backup Server가 HTTPS를 통해 VMware 서버와 통신할 수 있도록 보안 채널을 설정합니다.
- Azure Backup Server에서 VMware 서버에 액세스하는 데 사용하는 VMware 계정을 설정합니다.
- Azure Backup에 계정 자격 증명을 추가합니다.
- Azure Backup Server에 vCenter 또는 ESXi 서버를 추가합니다.
- 백업할 VMware VM을 포함하는 보호 그룹을 설정하고 백업 설정을 지정한 다음, 백업을 예약합니다.

## <a name="before-you-start"></a>시작하기 전에

- 백업에 대해 지원 되는 vCenter/ESXi의 버전을 실행 하 고 있는지 확인 합니다. [여기](./backup-mabs-protection-matrix.md)에서 지원 매트릭스를 참조 하세요.
- Azure Backup Server를 설정했는지 확인합니다. 설정하지 않은 경우 시작하기 전에 [설정](backup-azure-microsoft-azure-backup.md)합니다. 최신 업데이트를 사용하여 Azure Backup Server를 실행해야 합니다.
- 다음 네트워크 포트가 열려있는지 확인합니다.
  - MABS와 vCenter 간의 TCP 443
  - MABS와 ESXi 호스트 간의 TCP 443 및 TCP 902

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server에 대한 보안 연결 만들기

기본적으로 Azure Backup Server는 HTTPS를 통해 VMware 서버와 통신합니다. HTTPS 연결을 설정하려면 VMware CA(인증 기관) 인증서를 다운로드하여 Azure Backup Server에서 가져옵니다.

### <a name="before-you-begin"></a>시작하기 전에

- HTTPS를 사용 하지 않으려는 경우 [모든 VMware 서버에 대해 https 인증서 유효성 검사를 사용 하지 않도록 설정할](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)수 있습니다.
- 일반적으로 vSphere Web Client를 사용하여 Azure Backup Server 머신의 브라우저에서 vCenter/ESXi 서버로 연결합니다. 처음으로이 작업을 수행 하는 경우 연결이 안전 하지 않으며 다음이 표시 됩니다.
- Azure Backup Server에서 백업을 처리하는 방법을 파악하는 것이 중요합니다.
  - 첫 번째 단계로 Azure Backup Server는 데이터를 로컬 디스크 스토리지에 백업합니다. Azure Backup Server가 보호된 데이터에 대한 디스크 복구 지점을 저장하는 디스크 및 볼륨 세트인 스토리지 풀이 Azure Backup Server에서 사용됩니다. 스토리지 풀은 DAS(직접 연결된 스토리지), 파이버 채널 SAN, iSCSI 스토리지 디바이스 또는 SAN일 수 있습니다. VMware VM 데이터의 로컬 백업을 위한 충분 한 저장소가 있는지 확인 하는 것이 중요 합니다.
  - 그러면 Azure Backup Server는 로컬 디스크 스토리지에서 Azure로 백업합니다.
  - [도움말 보기](/system-center/dpm/create-dpm-protection-groups#figure-out-how-much-storage-space-you-need)를 통해 필요한 스토리지 공간 크기를 파악합니다. 이 정보는 DPM을 위한 것이지만 Azure Backup Server에도 사용할 수 있습니다.

### <a name="set-up-the-certificate"></a>인증서 설정

다음과 같이 보안 채널을 설정합니다.

1. Azure Backup Server의 브라우저에 vSphere Web Client URL을 입력합니다. 로그인 페이지가 나타나지 않으면 연결 및 브라우저 프록시 설정을 확인합니다.

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. VSphere 웹 클라이언트 로그인 페이지에서 **신뢰할 수 있는 루트 CA 인증서 다운로드**를 선택 합니다.

    ![신뢰할 수 있는 루트 CA 인증서 다운로드](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. **download**라는 파일이 다운로드됩니다. 브라우저에 따라 파일을 열거나 저장할 것인지 묻는 메시지가 나타납니다.

    ![CA 인증서 다운로드](./media/backup-azure-backup-server-vmware/download-certs.png)

4. 이 파일을 Azure Backup Server 머신에 zip 확장명으로 저장합니다.

5. **download.zip**  >  **모두 추출**을 마우스 오른쪽 단추로 클릭 합니다. 다음을 포함하는 zip 파일의 콘텐츠가 **certs** 폴더로 추출됩니다.
   - .0 및 .1과 같은 숫자가 매겨진 시퀀스로 시작하는 확장명을 가진 루트 인증서 파일.
   - CRL 파일은 .r0 또는 .r1과 같은 시퀀스로 시작하는 확장명을 갖습니다. CRL 파일은 인증서와 연결되어 있습니다.

    ![다운로드한 인증서](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. **certs** 폴더에서 루트 인증서 파일을 마우스 오른쪽 단추로 클릭하고 > **이름 바꾸기**를 클릭합니다.

    ![루트 인증서 이름 바꾸기](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. 루트 인증서의 확장명을 .crt로 변경하고 확인합니다. 파일 아이콘이 루트 인증서를 나타내는 아이콘으로 변경됩니다.

8. 루트 인증서를 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 **인증서 설치**를 선택합니다.

9. **인증서 가져오기 마법사**에서 **로컬 컴퓨터** 를 인증서의 대상으로 선택 하 고 **다음**을 선택 합니다. 컴퓨터 변경을 허용할지 묻는 메시지가 표시되면 확인합니다.

    ![마법사 시작](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. **인증서 저장소** 페이지에서 **모든 인증서를 다음 저장소에**저장을 선택한 다음 **찾아보기** 를 선택 하 여 인증서 저장소를 선택 합니다.

    ![인증서 스토리지](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. **인증서 저장소 선택**에서 인증서의 대상 폴더로 **신뢰할 수 있는 루트 인증 기관** 을 선택 하 고 **확인**을 선택 합니다.

    ![인증서 대상 폴더](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. **인증서 가져오기 마법사 완료**에서 폴더를 확인 한 다음 **마침**을 선택 합니다.

    ![인증서가 적절한 폴더에 있는지 확인](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. 인증서 가져오기가 확인된 후 vCenter Server에 로그인하여 연결이 안전한지 확인합니다.

### <a name="disable-https-certificate-validation"></a>HTTPS 인증서 유효성 검사 사용 안 함

조직 내에서 보안 경계가 있고 VMware 서버와 Azure Backup Server 컴퓨터 간에 HTTPS 프로토콜을 사용 하지 않으려는 경우 다음과 같이 HTTPS를 사용 하지 않도록 설정 합니다.

1. 다음 텍스트를 복사하여 .txt 파일에 붙여넣습니다.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. 파일 이름을 **DisableSecureAuthentication.reg**로 지정하여 Azure Backup Server 머신에 저장합니다.

3. 파일을 두 번 클릭하여 레지스트리 항목을 활성화합니다.

## <a name="create-a-vmware-role"></a>VMware 역할 만들기

v-Center Server/ESXi 호스트에 액세스할 수 있는 권한을 가진 사용자 계정이 Azure Backup Server에 필요합니다. 특정 권한을 가진 VMware 역할을 만든 다음, 이 역할에 사용자 계정을 연결합니다.

1. vCenter Server(또는 vCenter Server를 사용하지 않는 경우 ESXi 호스트)에 로그인합니다.
2. **탐색기** 패널에서 **관리**를 선택 합니다.

    ![관리](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. **관리**  >  **역할**에서 역할 추가 아이콘 (+ 기호)을 선택 합니다.

    ![역할 추가](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. **역할**  >  **역할 이름**만들기에서 *backupadminrole*을 입력 합니다. 역할 이름은 원하는 대로 사용할 수 있지만 이름은 역할의 목적에 대해 쉽게 인식할 수 있어야 합니다.

5. 아래 표에 요약 된 대로 권한을 선택한 다음 **확인**을 선택 합니다.  새 역할이 **역할** 패널의 목록에 나타납니다.
   - 부모 레이블 옆에 있는 아이콘을 선택 하 여 부모를 확장 하 고 자식 권한을 봅니다.
   - VirtualMachine 권한을 선택하려면 부모 자식 계층으로 여러 개의 수준을 이동해야 합니다.
   - 부모 권한 내 모든 자식 권한을 선택하지 않아도 됩니다.

    ![부모 자식 권한 계층 구조](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>역할 권한

다음 표에서 만든 사용자 계정에 할당해야 하는 권한을 확인할 수 있습니다.

| vCenter 6.5 사용자 계정에 대한 권한                          | vCenter 6.7 사용자 계정에 대한 권한                            |
|----------------------------------------------------------------------------|----------------------------------------------------------------------------|
| Datastore cluster.Configure a datastore cluster                           | Datastore cluster.Configure a datastore cluster                           |
| Datastore.AllocateSpace                                                    | Datastore.AllocateSpace                                                    |
| Datastore.Browse datastore                                                 | Datastore.Browse datastore                                                 |
| Datastore.Low-level file operations                                        | Datastore.Low-level file operations                                        |
| Global.Disable methods                                                     | Global.Disable methods                                                     |
| Global.Enable methods                                                      | Global.Enable methods                                                      |
| Global.Licenses                                                            | Global.Licenses                                                            |
| Global.Log event                                                           | Global.Log event                                                           |
| Global.Manage custom attributes                                            | Global.Manage custom attributes                                            |
| Global.Set custom attribute                                                | Global.Set custom attribute                                                |
| Host.Local operations.Create virtual machine                               | Host.Local operations.Create virtual machine                               |
| Network.Assign network                                                     | Network.Assign network                                                     |
| Resource. Assign virtual machine to resource pool                          | Resource. Assign virtual machine to resource pool                          |
| vApp.Add virtual machine                                                   | vApp.Add virtual machine                                                   |
| vApp.Assign resource pool                                                  | vApp.Assign resource pool                                                  |
| vApp.Unregister                                                            | vApp.Unregister                                                            |
| VirtualMachine.Configuration. Add Or Remove Device                         | VirtualMachine.Configuration. Add Or Remove Device                         |
| Virtual machine.Configuration.Disk lease                                   | Virtual machine.Configuration.Acquire disk lease                           |
| Virtual machine.Configuration.Add new disk                                 | Virtual machine.Configuration.Add new disk                                 |
| Virtual machine.Configuration.Advanced                                     | Virtual machine.Configuration.Advanced configuration                       |
| Virtual machine.Configuration.Disk change tracking                         | Virtual machine.Configuration.Toggle disk change tracking                  |
| Virtual machine.Configuration.Host USB device                              | Virtual machine.Configuration.Configure Host USB device                    |
| Virtual machine.Configuration.Extend virtual disk                          | Virtual machine.Configuration.Extend virtual disk                          |
| Virtual machine.Configuration.Query unowned files                          | Virtual machine.Configuration.Query unowned files                          |
| Virtual machine.Configuration.Swapfile placement                           | Virtual machine.Configuration.Change Swapfile placement                    |
| Virtual machine.Guest Operations.Guest Operation Program Execution         | Virtual machine.Guest Operations.Guest Operation Program Execution         |
| Virtual machine.Guest Operations.Guest Operation Modifications             | Virtual machine.Guest Operations.Guest Operation Modifications             |
| Virtual machine.Guest Operations.Guest Operation Queries                   | Virtual machine.Guest Operations.Guest Operation Queries                   |
| Virtual machine .Interaction .Device connection                            | Virtual machine .Interaction .Device connection                            |
| Virtual machine .Interaction .Guest operating system management by VIX API | Virtual machine .Interaction .Guest operating system management by VIX API |
| Virtual machine .Interaction .Power Off                                    | Virtual machine .Interaction .Power Off                                    |
| Virtual machine .Inventory.Create new                                      | Virtual machine .Inventory.Create new                                      |
| Virtual machine .Inventory.Remove                                          | Virtual machine .Inventory.Remove                                          |
| Virtual machine .Inventory.Register                                        | Virtual machine .Inventory.Register                                        |
| Virtual machine .Provisioning.Allow disk access                            | Virtual machine .Provisioning.Allow disk access                            |
| Virtual machine .Provisioning.Allow file access                            | Virtual machine .Provisioning.Allow file access                            |
| Virtual machine .Provisioning.Allow read-only disk access                  | Virtual machine .Provisioning.Allow read-only disk access                  |
| Virtual machine .Provisioning.Allow virtual machine download               | Virtual machine .Provisioning.Allow virtual machine download               |
| Virtual machine .Snapshot management. Create snapshot                      | Virtual machine .Snapshot management. Create snapshot                      |
| Virtual machine .Snapshot management.Remove Snapshot                       | Virtual machine .Snapshot management.Remove Snapshot                       |
| Virtual machine .Snapshot management.Revert to snapshot                    | Virtual machine .Snapshot management.Revert to snapshot                    |

> [!NOTE]
> 다음 표에는 vCenter 6.0 및 vCenter 5.5 사용자 계정에 대한 권한이 나와 있습니다.

| vCenter 6.0 사용자 계정에 대한 권한 | vCenter 5.5 사용자 계정에 대한 권한 |
| --- | --- |
| Datastore.AllocateSpace | Network.Assign |
| Global.Manage custom attributes | Datastore.AllocateSpace |
| Global.Set custom attribute | VirtualMachine.Config.ChangeTracking |
| Host.Local operations.Create virtual machine | VirtualMachine.State.RemoveSnapshot |
| Network. Assign network | VirtualMachine.State.CreateSnapshot |
| Resource. Assign virtual machine to resource pool | VirtualMachine.Provisioning.DiskRandomRead |
| Virtual machine.Configuration.Add new disk | VirtualMachine.Interact.PowerOff |
| Virtual machine.Configuration.Advanced | VirtualMachine.Inventory.Create |
| Virtual machine.Configuration.Disk change tracking | VirtualMachine.Config.AddNewDisk |
| Virtual machine.Configuration.Host USB device | VirtualMachine.Config.HostUSBDevice |
| Virtual machine.Configuration.Query unowned files | VirtualMachine.Config.AdvancedConfig |
| Virtual machine.Configuration.Swapfile placement | VirtualMachine.Config.SwapPlacement |
| Virtual machine.Interaction.Power Off | Global.ManageCustomFields |
| Virtual machine.Inventory. Create new |   |
| Virtual machine.Provisioning.Allow disk access |   |
| Virtual machine.Provisioning. Allow read-only disk access |   |
| Virtual machine.Snapshot management.Create snapshot |   |
| Virtual machine.Snapshot management.Remove Snapshot |   |

## <a name="create-a-vmware-account"></a>VMware 계정 만들기

1. VCenter Server **탐색기** 패널에서 **사용자 및 그룹**을 선택 합니다. vCenter Server를 사용하지 않는 경우 해당 ESXi 호스트에 계정을 만듭니다.

    ![사용자 및 그룹 옵션](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **VCenter 사용자 및 그룹** 패널이 나타납니다.

2. **VCenter 사용자 및 그룹** 패널에서 **사용자** 탭을 선택한 다음 사용자 추가 아이콘 (+ 기호)을 선택 합니다.

    ![vCenter 사용자 및 그룹 패널](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. **새 사용자** 대화 상자에서 사용자 정보를 추가하고 > **확인**을 클릭합니다. 이 절차에서 사용자 이름은 BackupAdmin입니다.

    ![새 사용자 대화 상자](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. 사용자 계정을 역할과 연결 하려면 **탐색기** 패널에서 **전역 권한**을 선택 합니다. **전역 권한** 패널에서 **관리** 탭을 선택한 다음 추가 아이콘 (+ 기호)을 선택 합니다.

    ![전역 권한 패널](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. **전역 권한 루트-추가 권한**에서 **추가** 를 선택 하 여 사용자 또는 그룹을 선택 합니다.

    ![사용자 또는 그룹 선택](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. **사용자/그룹 선택**에서 **BackupAdmin** > **추가**를 선택합니다. **사용자**에서 *domain\username* 형식이 사용자 계정에 사용됩니다. 다른 도메인을 사용하려면 **도메인** 목록에서 원하는 도메인을 선택합니다. **확인** 을 선택 하 여 선택한 사용자를 **권한 추가** 대화 상자에 추가 합니다.

    ![BackupAdmin 사용자 추가](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. **할당된 역할**의 드롭다운 목록에서 **BackupAdminRole** > **확인**을 선택합니다.

    ![사용자를 역할에 할당](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

**전역 권한** 패널의 **관리** 탭에서 새 사용자 계정 및 연결된 역할이 목록에 표시됩니다.

## <a name="add-the-account-on-azure-backup-server"></a>Azure Backup Server에 계정 추가

1. Azure Backup Server를 엽니다. 바탕 화면에서 아이콘을 찾을 수 없는 경우 앱 목록에서 Microsoft Azure Backup을 엽니다.

    ![Azure Backup Server 아이콘](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Azure Backup Server 콘솔에서 **관리**  >   **프로덕션 서버**  >  **VMware 관리**를 선택 합니다.

    ![Azure Backup Server 콘솔](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. **자격 증명 관리** 대화 상자에서 **추가**를 선택 합니다.

    ![자격 증명 관리 대화 상자](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. **자격 증명 추가**에서 새 자격 증명에 대 한 이름 및 설명을 입력 하 고 VMware 서버에서 정의한 사용자 이름 및 암호를 지정 합니다. Contoso Vcenter 자격 증명이란 이름은 이 절차에서 자격 증명을 식별하는 데 사용됩니다.** VMware 서버와 Azure Backup Server가 있는 도메인이 동일하지 않으면 사용자 이름에 도메인을 지정합니다.

    ![Azure Backup Server 자격 증명 추가 대화 상자](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. **추가** 를 선택 하 여 새 자격 증명을 추가 합니다.

    ![새 자격 증명 추가](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>vCenter Server 추가

Azure Backup Server에 vCenter Server를 추가합니다.

1. Azure Backup Server 콘솔에서 **관리**  >  **프로덕션 서버**  >  **추가**를 선택 합니다.

    ![프로덕션 서버 추가 마법사 열기](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. **프로덕션 서버 추가 마법사에서**  >  **프로덕션 서버 유형 선택** 페이지에서 **VMware 서버**를 선택 하 고 **다음**을 선택 합니다.

    ![프로덕션 서버 추가 마법사](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. **컴퓨터 선택**  **서버 이름/IP 주소**에서 VMware 서버의 FQDN 또는 IP 주소를 지정합니다. 모든 ESXi 서버를 동일한 vCenter가 관리하는 경우 vCenter 이름을 지정합니다. 그렇지 않으면 ESXi 호스트를 추가합니다.

    ![VMware 서버 지정](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **SSL 포트**에 VMware 서버와 통신하는 데 사용되는 포트를 입력합니다. 443이 기본 포트지만, VMware 서버가 다른 포트에서 수신 대기하는 경우에는 변경할 수 있습니다.

5. **자격 증명 지정**에서 이전에 만든 자격 증명을 선택합니다.

    ![자격 증명 지정](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. **추가** 를 선택 하 여 VMware 서버를 서버 목록에 추가 합니다. **다음**을 선택합니다.

    ![VMWare 서버 및 자격 증명 추가](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **요약** 페이지에서 **추가** 를 선택 하 여 Azure Backup Server에 VMware 서버를 추가 합니다. 새 서버가 즉시 추가되며, VMware 서버에 에이전트가 필요하지 않습니다.

    ![Azure Backup Server에 VMware 서버 추가](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. **마침** 페이지에서 설정을 확인합니다.

   ![마침 페이지](./media/backup-azure-backup-server-vmware/summary-screen.png)

vCenter 서버에서 관리하지 않는 ESXi 호스트가 여러 개 있거나 vCenter Server 인스턴스가 여러 개 있는 경우 마법사를 다시 실행하여 서버를 추가해야 합니다.

## <a name="configure-a-protection-group"></a>보호 그룹 구성

백업용 VMware VM을 추가합니다. 보호 그룹은 여러 VM을 수집하고 동일한 데이터 보존 및 백업 설정을 그룹의 모든 VM에 적용합니다.

1. Azure Backup Server 콘솔에서 **보호**를 선택 하 > **새로 만들기**를 선택 합니다.

    ![새 보호 그룹 만들기 마법사 열기](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. **새 보호 그룹 만들기** 마법사 시작 페이지에서 **다음**을 선택 합니다.

    ![새 보호 그룹 만들기 마법사 대화 상자](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. **보호 그룹 종류 선택** 페이지에서 **서버** 를 선택 하 고 **다음**을 선택 합니다. **그룹 구성원 선택** 페이지가 나타납니다.

1. **그룹 구성원 선택**에서 백업 하려는 vm (또는 vm 폴더)을 선택 합니다. **다음**을 선택합니다.

    - 폴더를 선택하면 해당 폴더 내의 VM 또는 폴더도 백업되도록 선택됩니다. 백업하지 않으려는 폴더 또는 VM을 선택 취소할 수 있습니다.
1. VM 또는 폴더가 이미 백업 중인 경우에는 선택할 수 없습니다. 이렇게 하면 VM에 대해 중복 복구 지점이 생성 되지 않습니다.

    ![그룹 구성원 선택](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. **데이터 보호 방법 선택** 페이지에서 보호 그룹의 이름과 보호 설정을 입력합니다. Azure에 백업하려면 단기 보호를 **디스크**로 설정하고 온라인 보호를 사용하도록 설정합니다. **다음**을 선택합니다.

    ![데이터 보호 방법 선택](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. **단기 목표 지정**에서 데이터를 디스크에 백업하는 기간을 지정합니다.
   - **보존 범위**에서 디스크 복구 지점을 유지하는 기간(일)을 지정합니다.
   - **동기화 빈도**에서 디스크 복구 지점의 사용 빈도를 지정합니다.
       - 백업 간격을 설정 하지 않으려면 각 복구 지점이 예약 되기 직전에 백업이 실행 되도록 **복구 지점** 직전을 확인할 수 있습니다.
       - 단기 백업은 전체 백업이며 증분이 아닙니다.
       - 단기 백업이 발생 하는 시간/날짜를 변경 하려면 **수정** 을 선택 합니다.

         ![단기 목표 지정](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. **디스크 할당 검토**에서 VM 백업용으로 VM에 대해 제공된 디스크 공간을 검토합니다.

   - 권장되는 디스크 할당은 지정된 보존 범위, 워크로드의 형식 및 보호된 데이터의 크기를 기반으로 합니다. 필요한 항목을 변경한 후 **다음**을 선택 합니다.
   - **데이터 크기:** 보호 그룹의 데이터 크기입니다.
   - **디스크 공간:** 보호 그룹에 권장되는 디스크 공간의 크기입니다. 이 설정을 수정 하려면 각 데이터 원본이 증가 하는 금액 보다 약간 더 큰 총 공간을 할당 해야 합니다.
   - **데이터 공동 배치:** 공동 배치를 사용하는 경우 보호되는 여러 데이터 원본은 단일 복제본 및 복구 지점 볼륨에 매핑할 수 있습니다. 공동 배치는 모든 워크로드에 지원되는 것은 아닙니다.
   - **자동 증가:** 이 설정을 사용 하는 경우 보호 된 그룹의 데이터가 초기 할당을 보다 커지면 Azure Backup Server는 디스크 크기를 25% 증가 하려고 시도 합니다.
   - **스토리지 풀 세부 정보:** 총 디스크 크기 및 남아 있는 디스크 크기를 포함한 스토리지 풀의 상태를 표시합니다.

    ![디스크 할당 검토](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. **복제본 만들기 방법 선택** 페이지에서 초기 백업을 수행할 방법을 지정한 후 **다음**을 선택 합니다.
   - 기본 설정은 **네트워크를 통해 자동으로** 및 **지금**입니다.
   - 기본값을 사용하는 경우 사용량이 적은 시간을 지정하는 것이 좋습니다. **나중에**를 선택하고 날짜와 시간을 지정합니다.
   - 대용량 데이터이거나 네트워크 상태가 최적화되지 않은 경우 이동식 미디어를 사용하여 데이터를 오프라인으로 복제하는 것을 고려하세요.

    ![복제본 만들기 방법 선택](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. **일관성 확인 옵션**에서 일관성 확인을 자동화할 방법 및 시기를 선택합니다. **다음**을 선택합니다.
      - 복제 데이터가 일관성을 잃은 경우 또는 설정된 일정에 따라 일관성 확인을 실행할 수 있습니다.
      - 자동 일관성 확인을 구성하지 않으려면 수동 검사를 실행할 수 있습니다. 이 작업을 수행하려면 보호 그룹을 마우스 오른쪽 단추로 클릭하고 > **일관성 확인 수행**을 클릭합니다.

1. **온라인 보호 데이터 지정** 페이지에서 백업할 VM 또는 VM 폴더를 선택합니다. 멤버를 개별적으로 선택 하거나 **모두 선택** 을 선택 하 여 모든 멤버를 선택할 수 있습니다. **다음**을 선택합니다.

    ![온라인 보호 데이터 지정](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. **온라인 백업 예약 지정** 페이지에서 로컬 스토리지의 데이터를 Azure로 백업할 빈도를 지정합니다.

    - 일정에 따라 데이터에 대한 클라우드 복구 지점이 생성됩니다. **다음**을 선택합니다.
    - 복구 지점이 생성 되 면 Azure에서 Recovery Services 자격 증명 모음으로 전송 됩니다.

    ![온라인 백업 일정 지정](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. **온라인 보존 정책 지정** 페이지에서 매일, 매주, 매월 및 매년 백업을 통해 만들어진 복구 지점을 Azure에 유지할 기간을 지정합니다. 그런 후 **다음**을 선택 합니다.

    - Azure에서 데이터를 유지할 수 있는 기간에 대한 시간 제한은 없습니다.
    - 보호된 인스턴스당 9999개 이하의 복구 지점을 사용해야 한다는 점이 유일한 제한입니다. 이 예제에서 보호되는 인스턴스는 VMware 서버입니다.

    ![온라인 보존 정책 지정](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. **요약** 페이지에서 설정을 검토 한 후 **그룹 만들기**를 선택 합니다.

    ![보호 그룹 구성원 및 설정 요약](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-parallel-backups"></a>VMware 병렬 백업

>[!NOTE]
> 이 기능은 MABS V3 UR1에 적용 됩니다.

이전 버전의 MABS에서는 보호 그룹 간에만 병렬 백업이 수행 되었습니다. MABS V3 UR1를 사용 하면 단일 보호 그룹 내의 모든 VMWare Vm 백업이 병렬 되므로 VM 백업 속도가 빨라집니다. 모든 VMWare 델타 복제 작업은 동시에 실행 됩니다. 기본적으로 병렬로 실행할 작업 수는 8로 설정 됩니다.

아래 표시 된 대로 레지스트리 키를 사용 하 여 작업 수를 수정할 수 있습니다 (기본적으로 표시 되지 않음, 추가 해야 함).

**키 경로**: `Software\Microsoft\Microsoft Data Protection Manager\Configuration\ MaxParallelIncrementalJobs\VMWare`<BR>
**키 유형**: DWORD (32 비트) 값입니다.

> [!NOTE]
> 작업 수를 더 높은 값으로 수정할 수 있습니다. 작업 번호를 1로 설정 하면 복제 작업이 순차적으로 실행 됩니다. 더 높은 값으로 설정하려면 VMWare 성능을 고려해야 합니다. 사용 중인 리소스의 수와 VMWare vSphere 서버에 필요한 추가 사용을 고려 하 여 병렬로 실행할 델타 복제 작업 수를 확인 합니다. 또한이 변경은 새로 만든 보호 그룹에만 적용 됩니다. 기존 보호 그룹의 경우 보호 그룹에 일시적으로 다른 VM을 추가 해야 합니다. 이렇게 하면 보호 그룹 구성을 적절 하 게 업데이트 해야 합니다. 절차가 완료 된 후 보호 그룹에서이 VM을 제거할 수 있습니다.

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

VSphere 6.7를 백업 하려면 다음을 수행 합니다.

- DPM 서버에서 TLS 1.2 사용

>[!NOTE]
>VMWare 6.7는 TLS를 통신 프로토콜로 사용할 수 있었습니다.

- 다음과 같이 레지스트리 키를 설정합니다.

```text
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001
```

## <a name="exclude-disk-from-vmware-vm-backup"></a>VMware VM 백업에서 디스크 제외

> [!NOTE]
> 이 기능은 MABS V3 UR1에 적용 됩니다.

MABS V3 UR1를 사용 하 여 VMware VM 백업에서 특정 디스크를 제외할 수 있습니다. 구성 스크립트 **ExcludeDisk.ps1** 는에 있습니다 `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin folder` .

디스크 제외를 구성하려면 다음 단계를 수행합니다.

### <a name="identify-the-vmware-vm-and-disk-details-to-be-excluded"></a>제외할 VMWare VM 및 디스크 세부 정보 식별

  1. VMware 콘솔에서 디스크를 제외 하려는 VM 설정으로 이동 합니다.
  2. 제외하려는 디스크를 선택하 고 해당 디스크에 대한 경로를 확인합니다.

        예를 들어 TestVM4에서 하드 디스크 2를 제외하려면 하드 디스크 2의 경로는 **[datastore1] TestVM4/TestVM4\_1.vmdk**입니다.

        ![제외 될 하드 디스크](./media/backup-azure-backup-server-vmware/test-vm.png)

### <a name="configure-mabs-server"></a>MABS 서버 구성

VMware VM이 보호 되도록 구성 된 MABS 서버로 이동 하 여 디스크 제외를 구성 합니다.

  1. MABS 서버에서 보호 되는 VMware 호스트의 세부 정보를 가져옵니다.

        ```powershell
        $psInfo = get-DPMProductionServer
        $psInfo
        ```

        ```output
        ServerName   ClusterName     Domain            ServerProtectionState
        ----------   -----------     ------            ---------------------
        Vcentervm1                   Contoso.COM       NoDatasourcesProtected
        ```

  2. VMware 호스트를 선택하고 VMware 호스트에 대한 VM 보호를 나열합니다.

        ```powershell
        $vmDsInfo = get-DPMDatasource -ProductionServer $psInfo[0] -Inquire
        $vmDsInfo
        ```

        ```output
        Computer     Name     ObjectType
        --------     ----     ----------
        Vcentervm1  TestVM2      VMware
        Vcentervm1  TestVM1      VMware
        Vcentervm1  TestVM4      VMware
        ```

  3. 디스크를 제외하려는 VM을 선택합니다.

        ```powershell
        $vmDsInfo[2]
        ```

        ```output
        Computer     Name      ObjectType
        --------     ----      ----------
        Vcentervm1   TestVM4   VMware
        ```

  4. 디스크를 제외 하려면 폴더로 이동 하 `Bin` 고 다음 매개 변수를 사용 하 여 *ExcludeDisk.ps1* 스크립트를 실행 합니다.

        > [!NOTE]
        > 이 명령을 실행 하기 전에 MABS 서버에서 DPMRA 서비스를 중지 합니다. 그렇지 않으면 스크립트가 success를 반환 하지만 제외 목록을 업데이트 하지는 않습니다. 서비스를 중지하기 전에 진행 중인 작업이 없는지 확인합니다.

     **제외에서 디스크를 추가/제거하려면 다음 명령을 실행합니다.**

      ```powershell
      ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-Add|Remove] "[Datastore] vmdk/vmdk.vmdk"
      ```

     **예**:

     TestVM4에 대 한 디스크 제외를 추가 하려면 다음 명령을 실행 합니다.

       ```powershell
      C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Add "[datastore1] TestVM4/TestVM4\_1.vmdk"
       ```

      ```output
       Creating C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\excludedisk.xml
       Disk : [datastore1] TestVM4/TestVM4\_1.vmdk, has been added to disk exclusion list.
      ```

  5. 디스크가 제외에 추가 되었는지 확인 합니다.

     **특정 VM에 대한 기존 제외를 보려면 다음 명령을 실행합니다.**

        ```powershell
        ./ExcludeDisk.ps1 -Datasource $vmDsInfo[0] [-view]
        ```

     **예제**

        ```powershell
        C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -view
        ```

        ```output
        <VirtualMachine>
        <UUID>52b2b1b6-5a74-1359-a0a5-1c3627c7b96a</UUID>
        <ExcludeDisk>[datastore1] TestVM4/TestVM4\_1.vmdk</ExcludeDisk>
        </VirtualMachine>
        ```

     이 VM에 대 한 보호를 구성한 후에는 제외 된 디스크가 보호 중에 나열 되지 않습니다.

        > [!NOTE]
        > 이미 보호 된 VM에 대해 이러한 단계를 수행 하는 경우 제외를 위해 디스크를 추가한 후 일관성 확인을 수동으로 실행 해야 합니다.

### <a name="remove-the-disk-from-exclusion"></a>제외에서 디스크 제거

제외에서 디스크를 제거 하려면 다음 명령을 실행 합니다.

```powershell
C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin> ./ExcludeDisk.ps1 -Datasource $vmDsInfo[2] -Remove "[datastore1] TestVM4/TestVM4\_1.vmdk"
```

## <a name="next-steps"></a>다음 단계

백업을 설정할 때 발생하는 문제를 해결하려면 [Azure Backup Server 문제 해결 가이드](./backup-azure-mabs-troubleshoot.md)를 검토합니다.
