---
title: Azure VM 백업에서 파일 및 폴더 복구
description: 이 문서에서는 Azure 가상 머신 복구 지점에서 파일 및 폴더를 복구하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/01/2019
ms.custom: references_regions
ms.openlocfilehash: fd68c33e4425d717837923b90119d42569a1f003
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89178523"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Azure Virtual Machine 백업에서 파일 복구

Azure Backup에서는 복구 지점이라고도 하는 Azure VM 백업에서 [Azure VM(가상 머신) 및 디스크](./backup-azure-arm-restore-vms.md)를 복원하는 기능을 제공합니다. 이 문서에서는 Azure VM Backup에서 파일 및 폴더를 어떻게 복구할 수 있는지 설명합니다. 파일 및 폴더 복원은 리소스 관리자 모델을 사용 하 여 배포 되 고 Recovery Services 자격 증명 모음으로 보호 되는 Azure Vm에만 사용할 수 있습니다.

> [!NOTE]
> 이 기능은 Resource Manager 모델을 사용하여 배포된 Azure VM에서 사용 가능하며 Recovery Services 자격 증명 모음에 대해 보호됩니다.
> 암호화 된 VM 백업에서 파일 복구는 지원 되지 않습니다.
>

## <a name="mount-the-volume-and-copy-files"></a>볼륨 탑재 및 파일 복사

복구 지점에서 파일 또는 폴더를 복원하려면 가상 머신으로 이동하고 원하는 복구 지점을 선택합니다.

1. [Azure Portal](https://portal.Azure.com) 에 로그인 하 고 왼쪽 창에서 **Virtual machines**를 선택 합니다. 가상 머신 목록에서 가상 머신을 선택하여 해당 가상 머신의 대시보드를 엽니다.

2. 가상 컴퓨터의 메뉴에서 **백업** 을 선택 하 여 백업 대시보드를 엽니다.

    ![Recovery Services 자격 증명 모음 백업 항목 열기](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. 백업 대시보드 메뉴에서 **파일 복구**를 선택 합니다.

    ![파일 복구 선택](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    **파일 복구** 메뉴가 열립니다.

    ![파일 복구 메뉴](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. **복구 지점 선택** 드롭다운 메뉴에서 원하는 파일이 있는 복구 지점을 선택합니다. 기본적으로 최신 복구 지점을 선택합니다.

5. 복구 지점에서 파일을 복사 하는 데 사용 되는 소프트웨어를 다운로드 하려면 **실행 파일 다운로드** (Windows azure vm의 경우) 또는 **스크립트 다운로드** (Linux azure vm의 경우 python 스크립트가 생성 됨)를 선택 합니다.

    ![다운로드 실행 파일](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure가 실행 파일 또는 스크립트를 로컬 컴퓨터로 다운로드합니다.

    ![실행 파일 또는 스크립트에 대한 다운로드 메시지](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    실행 파일 또는 스크립트를 관리자 권한으로 실행하려면 다운로드한 파일을 컴퓨터에 저장하는 것이 좋습니다.

6. 실행 파일 또는 스크립트는 암호로 보호되어 암호가 필요합니다. **파일 복구** 메뉴에서 복사 단추를 선택 하 여 암호를 메모리로 로드 합니다.

    ![생성된 암호](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. 스크립트를 실행하는 데 [적합한 머신](#selecting-the-right-machine-to-run-the-script)이 필요합니다. 적합한 머신이 스크립트를 다운로드한 머신인 경우 다운로드 섹션을 계속 진행하면 됩니다. 다운로드 위치(일반적으로 *Downloads* 폴더)에서 실행 파일 또는 스크립트를 마우스 오른쪽 단추로 클릭하고 관리자 자격 증명을 사용하여 실행합니다. 메시지가 표시되면 암호를 입력하거나 메모리의 암호를 붙여넣고 **Enter** 키를 누릅니다. 올바른 암호를 입력하면 스크립트가 복구 지점에 연결됩니다.

    ![실행 파일 출력](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Linux 머신의 경우 python 스크립트가 생성됩니다. 스크립트를 다운로드하여 관련/호환 Linux 서버에 복사해야 합니다. 스크립트를 실행하려면 ```chmod +x <python file name>```을 사용하여 권한을 수정해야 할 수도 있습니다. 그런 다음, ```./<python file name>```을 사용하여 python 파일을 실행합니다.

스크립트가 성공적으로 실행될 수 있도록 [액세스 요구 사항](#access-requirements) 섹션을 참조하세요.

### <a name="identifying-volumes"></a>볼륨 식별

#### <a name="for-windows"></a>Windows의 경우

실행 파일을 실행하면 운영 체제는 새 볼륨을 탑재하고 드라이브 문자를 할당합니다. Windows 탐색기 또는 파일 탐색기를 사용하여 해당 드라이브를 탐색할 수 있습니다. 볼륨에 할당된 드라이브 문자가 원래 가상 머신과 다를 수 있습니다. 하지만 볼륨 이름은 그대로 유지됩니다. 예를 들어 원래 가상 머신의 볼륨이 "Data Disk(E:`\`)"인 경우 해당 볼륨을 로컬 컴퓨터에서 "Data Disk('아무 문자':`\`)로 연결할 수 있습니다. 해당하는 파일 또는 폴더를 찾을 때까지 스크립트 출력에 나와 있는 모든 볼륨을 탐색합니다.  

   ![연결 된 복구 볼륨](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Linux의 경우

Linux에서 복구 지점의 볼륨은 스크립트가 실행되는 폴더에 탑재됩니다. 연결된 디스크, 볼륨 및 해당 탑재 경로는 적절하게 표시됩니다. 이러한 탑재 경로는 루트 수준 액세스 권한이 있는 사용자에게 표시됩니다. 스크립트 출력에서 언급한 볼륨을 통해 찾습니다.

  ![Linux 파일 복구 메뉴](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>연결 닫기

파일을 식별하고 로컬 스토리지 위치에 복사한 후에는 추가 드라이브를 제거(또는 분리)합니다. 드라이브를 분리 하려면 Azure Portal의 **파일 복구** 메뉴에서 **디스크 분리**를 선택 합니다.

![디스크 분리](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

디스크가 분리되면 메시지가 표시됩니다. 디스크를 제거할 수 있도록 연결을 새로 고치는 데 몇 분이 소요될 수 있습니다.

Linux에서 복구 지점에 대한 연결이 단절된 후 OS는 해당 탑재 경로를 자동으로 제거하지 않습니다. 탑재 경로는 "분리된" 볼륨으로 존재하고 사용자가 볼 수 있지만, 파일에 액세스하거나 파일에 데이터를 쓰려고 시도할 때 오류가 throw됩니다. 수동으로 제거할 수도 있습니다. 스크립트는 실행 시 모든 이전 복구 지점에서 존재하는 이러한 볼륨을 식별하고 승인 시 정리합니다.

> [!NOTE]
> 필요한 파일이 복원 된 후 연결이 닫 혔는 지 확인 합니다. 이는 특히 스크립트가 실행 되는 컴퓨터도 백업용으로 구성 된 시나리오에서 중요 합니다. 연결이 아직 열려 있으면 "UserErrorUnableToOpenMount" 오류로 인해 후속 백업이 실패할 수 있습니다. 이는 탑재 된 드라이브/볼륨을 사용할 수 있는 것으로 간주 하 고 액세스 하는 경우 기본 저장소, 즉 iSCSI 대상 서버를 사용할 수 없기 때문에 실패할 수 있기 때문에 발생 합니다. 연결을 정리 하면 이러한 드라이브/볼륨이 제거 되므로 백업 하는 동안 사용할 수 없습니다.

## <a name="selecting-the-right-machine-to-run-the-script"></a>스크립트를 실행할 적절한 머신을 선택합니다.

스크립트가 성공적으로 다운로드된 후에는 스크립트를 실행할 머신이 적절한 머신인지 확인해야 합니다. 다음은 머신에서 충족해야 하는 요구 사항입니다.

### <a name="original-backed-up-machine-versus-another-machine"></a>원래 백업 머신을 다른 머신과 비교

1. 백업된 머신이 대용량 디스크 VM인 경우, 즉, 디스크 수가 16개를 초과하거나 각 디스크가 4TB보다 큰 경우에는 스크립트를 **다른 머신에서 실행**하고 [이 요구 사항](#file-recovery-from-virtual-machine-backups-having-large-disks)을 충족해야 합니다.
1. 백업된 머신이 대용량 디스크 VM이 아닌 경우에도 [이 시나리오](#special-configurations)에서는 동일한 백업된 VM에서 스크립트를 실행할 수 없습니다.

### <a name="os-requirements-on-the-machine"></a>머신의 OS 요구 사항

스크립트를 실행해야 하는 머신은 [이 OS 요구 사항](#system-requirements)을 충족해야 합니다.

### <a name="access-requirements-for-the-machine"></a>머신의 액세스 요구 사항

스크립트를 실행해야 하는 머신은 [이 액세스 요구 사항](#access-requirements)을 충족해야 합니다.

## <a name="special-configurations"></a>특수 구성

### <a name="dynamic-disks"></a>동적 디스크

보호된 Azure VM에 다음과 같은 특성 중 하나 또는 달 다를 포함한 볼륨이 있는 경우 동일한 VM에서 실행 스크립트를 실행할 수 없습니다.

- 여러 디스크에 걸쳐 있는 볼륨(스팬 및 스트라이프 볼륨)
- 동적 디스크의 장애 조치 볼륨(미러 및 RAID-5 볼륨)

대신, 호환되는 운영 체제로 다른 컴퓨터에서 실행 가능한 스크립트를 실행합니다.

### <a name="windows-storage-spaces"></a>Windows 스토리지 공간

Windows 스토리지 공간은 스토리지를 가상화할 수 있는 Windows 기술입니다. Windows 스토리지 공간을 사용하면 업계 표준 디스크를 스토리지 풀로 그룹화할 수 있습니다. 그런 다음 해당 스토리지 풀의 사용 가능한 공간을 사용하여 스토리지 공간이라는 가상 디스크를 만들 수 있습니다.

보호된 Azure VM에서 Windows 스토리지 공간을 사용하는 경우 동일한 VM에서는 실행 가능한 스크립트를 실행할 수 없습니다. 대신, 호환되는 운영 체제로 다른 컴퓨터에서 실행 가능한 스크립트를 실행합니다.

### <a name="lvmraid-arrays"></a>LVM/RAID 배열

Linux에서 LVM(논리 볼륨 관리자) 및/또는 소프트웨어 RAID 배열은 여러 디스크에 걸쳐 논리 볼륨을 관리하는 데 사용됩니다. 보호된 Linux VM에서 LVM 및/또는 RAID 배열을 사용하는 경우 동일한 VM에서 스크립트를 실행할 수 없습니다. 대신 호환되는 OS로 다른 컴퓨터에서 스크립트를 실행하고 보호된 VM의 파일 시스템을 지원합니다.

다음 스크립트 출력은 파티션 형식으로 LVM 및/또는 RAID 배열 디스크 및 볼륨을 표시합니다.

   ![Linux LVM 출력 메뉴](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

이 파티션을 온라인 상태로 만들려면 다음 섹션의 명령을 실행합니다.

#### <a name="for-lvm-partitions"></a>LVM 파티션의 경우

스크립트가 실행 되 면 LVM 파티션이 스크립트 출력에 지정 된 실제 볼륨/디스크에 탑재 됩니다. 프로세스는

1. 실제 볼륨 또는 디스크에서 고유한 볼륨 그룹 이름 목록을 가져옵니다.
2. 그런 다음 해당 볼륨 그룹의 논리 볼륨을 나열 합니다.
3. 그런 다음 원하는 경로에 논리 볼륨을 탑재 합니다.

##### <a name="listing-volume-group-names-from-physical-volumes"></a>실제 볼륨에서 볼륨 그룹 이름 나열

볼륨 그룹 이름을 나열 하려면:

```bash
pvs -o +vguuid
```

이 명령은 모든 실제 볼륨 (스크립트를 실행 하기 전에 존재 하는 볼륨 포함), 해당 볼륨 그룹 이름 및 볼륨 그룹의 고유한 사용자 Id (Uuid)를 나열 합니다. 명령의 샘플 출력은 다음과 같습니다.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdf   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN

  /dev/sdd   datavg_db lvm2 a--   <1.50t <396.50g dhWL1i-lcZS-KPLI-o7qP-AN2n-y2f8-A1fWqN
```

첫 번째 열 (PV)에는 실제 볼륨이 표시 되 고 후속 열에는 관련 볼륨 그룹 이름, 형식, 특성, 크기, 사용 가능한 공간 및 볼륨 그룹의 고유 ID가 표시 됩니다. 명령 출력에는 모든 실제 볼륨이 표시 됩니다. 스크립트 출력을 참조 하 고 백업과 관련 된 볼륨을 확인 합니다. 위의 예제에서 스크립트 출력은/hv/sd 및/dev/sdd.를 표시 합니다. 따라서 *datavg_db* 볼륨 그룹은 스크립트에 속하고 *Appvg_new* 볼륨 그룹은 컴퓨터에 속합니다. 최종 개념은 고유한 볼륨 그룹 이름에 하나의 고유 ID가 있어야 하는지 확인 하는 것입니다.

###### <a name="duplicate-volume-groups"></a>중복 볼륨 그룹

스크립트를 실행 한 후 볼륨 그룹 이름에 2 개의 Uuid가 있을 수 있는 시나리오가 있습니다. 이는 스크립트가 실행 되는 컴퓨터의 볼륨 그룹 이름과 백업 된 VM에서 동일 함을 의미 합니다. 그런 다음 백업 된 Vm 볼륨 그룹의 이름을 변경 해야 합니다. 아래 예제를 살펴보세요.

```bash
PV         VG        Fmt  Attr PSize   PFree    VG UUID

  /dev/sda4  rootvg    lvm2 a--  138.71g  113.71g EtBn0y-RlXA-pK8g-de2S-mq9K-9syx-B29OL6

  /dev/sdc   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sde   APPvg_new lvm2 a--  <75.00g   <7.50g njdUWm-6ytR-8oAm-8eN1-jiss-eQ3p-HRIhq5

  /dev/sdg   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdh   APPvg_new lvm2 a--  <75.00g  508.00m lCAisz-wTeJ-eqdj-S4HY-108f-b8Xh-607IuC

  /dev/sdm2  rootvg    lvm2 a--  194.57g  127.57g efohjX-KUGB-ETaH-4JKB-MieG-EGOc-XcfLCt
```

스크립트 출력에는 연결 된 것 처럼/sv/pverg,/dev/svh,/dev/svm이 표시 됩니다. 따라서 해당 VG 이름은 Appvg_new 및 rootvg입니다. 그러나 동일한 이름이 컴퓨터의 VG 목록에도 있습니다. 하나의 VG 이름에 두 개의 Uuid가 있는지 확인할 수 있습니다.

이제 스크립트 기반 볼륨의 이름을 VG 이름 (예:/hv/sdg,/dev/shh,/dev/sdm2.)으로 이름을 변경 해야 합니다. 볼륨 그룹의 이름을 바꾸려면 다음 명령을 사용 합니다.

```bash
vgimportclone -n rootvg_new /dev/sdm2
vgimportclone -n APPVg_2 /dev/sdg /dev/sdh
```

이제 고유한 Id를 가진 모든 VG 이름이 있습니다.

###### <a name="active-volume-groups"></a>활성 볼륨 그룹

스크립트의 볼륨에 해당 하는 볼륨 그룹이 활성화 되어 있는지 확인 합니다. 다음 명령은 활성 볼륨 그룹을 표시 하는 데 사용 됩니다. 스크립트의 관련 볼륨 그룹이이 목록에 있는지 확인 합니다.

```bash
vgdisplay -a
```  

그렇지 않으면 다음 명령을 사용 하 여 볼륨 그룹을 활성화 합니다.

```bash
#!/bin/bash
vgchange –a y  <volume-group-name>
```

##### <a name="listing-logical-volumes-within-volume-groups"></a>볼륨 그룹 내의 논리적 볼륨 나열

스크립트와 관련 된 다양 한 VGs의 활성 목록을 가져온 후에는 다음 명령을 사용 하 여 해당 볼륨 그룹에 있는 논리 볼륨을 나열할 수 있습니다.

```bash
#!/bin/bash
lvdisplay <volume-group-name>
```

이 명령은 각 논리 볼륨의 경로를 ' LV 경로 '로 표시 합니다.

##### <a name="mounting-logical-volumes"></a>논리 볼륨 탑재

선택한 경로에 논리 볼륨을 탑재하려면 다음을 수행합니다.

```bash
#!/bin/bash
mount <LV path from the lvdisplay cmd results> </mountpath>
```

> [!WARNING]
> ' 탑재 '를 사용 하지 마세요. 이 명령은 '/etc/fstab '에 설명 된 모든 장치를 탑재 합니다. 이는 중복 된 장치가 탑재 될 수 있음을 의미할 수 있습니다. 데이터는 스크립트에 의해 생성 된 장치로 리디렉션되고 데이터를 유지 하지 않을 수 있으므로 데이터 손실이 발생할 수 있습니다.

#### <a name="for-raid-arrays"></a>RAID 배열의 경우

다음 명령은 모든 RAID 디스크에 대한 세부 정보를 표시합니다.

```bash
#!/bin/bash
mdadm –detail –scan
```

 관련 RAID 디스크는 `/dev/mdm/<RAID array name in the protected VM>`으로 표시됩니다.

RAID 디스크에 실제 볼륨이 있는 경우 탑재 명령을 사용합니다.

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

RAID 디스크에 다른 LVM이 구성되어 있는 경우 LVM 파티션에 대한 이전 절차를 사용하되, RAID 디스크 이름 대신 볼륨 이름을 사용합니다.

## <a name="system-requirements"></a>시스템 요구 사항

### <a name="for-windows-os"></a>Windows OS의 경우

다음 표에서는 서버와 컴퓨터 운영 체제 간의 호환성을 보여 줍니다. 파일을 복구할 경우 이전 또는 미래 운영 체제 버전으로 파일을 복원할 수 없습니다. 예를 들어 Windows Server 2016 VM의 파일을 Windows Server 2012 또는 Windows 8 컴퓨터로 복원할 수 없습니다. VM의 파일을 같은 서버 운영 체제 또는 호환되는 클라이언트 운영 체제로 복원할 수 있습니다.

|서버 OS | 호환되는 클라이언트 OS  |
| --------------- | ---- |
| Windows Server 2019    | 윈도우 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Linux OS

Linux에서 파일을 복원하는 데 사용하는 컴퓨터의 OS는 보호된 가상 머신의 파일 시스템을 지원해야 합니다. 스크립트를 실행할 컴퓨터를 선택할 때 컴퓨터에 호환되는 OS가 있는지 확인하고 다음 테이블에 나타난 버전 중 하나를 사용합니다.

|Linux OS | 버전  |
| --------------- | ---- |
| Ubuntu | 12.04 이상 |
| CentOS | 6.5 이상  |
| RHEL | 6.7 이상 |
| Debian | 7 이상 |
| Oracle Linux | 6.4 이상 |
| SLES | 12 이상 |
| openSUSE | 42.2 이상 |

> [!NOTE]
> SLES 12 SP4 OS를 사용 하는 컴퓨터에서 파일 복구 스크립트를 실행 하는 동안 몇 가지 문제가 발견 되었습니다. SLES 팀으로 조사 하 고 있습니다.
> 현재 SLES 12 SP2 및 SP3 OS 버전을 사용하는 머신에서 파일 복구 스크립트를 실행할 수 있습니다.
>

스크립트는 복구 지점에 안전하게 연결하고 실행하기 위해 Python 및 bash 구성 요소가 필요합니다.

|구성 요소 | 버전  |
| --------------- | ---- |
| bash | 4 이상 |
| python | 2.6.6 이상  |
| TLS | 1.2가 지원되어야 합니다.  |

## <a name="access-requirements"></a>액세스 요구 사항

제한 된 액세스 권한이 있는 컴퓨터에서 스크립트를 실행 하는 경우 다음에 대 한 액세스 권한이 있는지 확인 합니다.

- `download.microsoft.com`
- 복구 서비스 Url (지역 이름은 Recovery Services 자격 증명 모음이 있는 지역을 나타냄)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com` (Azure 공용 지역의 경우)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Azure 중국 21Vianet의 경우)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(Azure 미국 정부의 경우)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(Azure 독일의 경우)
- 아웃바운드 포트 53(DNS), 443, 3260

> [!NOTE]
>
> - 다운로드한 스크립트 파일 이름의 URL이 **지역 이름**으로 채워집니다. 예: 다운로드 한 스크립트 이름은 \' \' \_ \' ContosoVM_wcus_12345678와 같은 VMname geoname \' _ \' GUID \' *ContosoVM_wcus_12345678* 로 시작 합니다.
> - URL은 <https://pod01-rec2.wcus.backup.windowsazure.com>"입니다.
>

Linux의 경우 스크립트는 복구 지점에 연결하는 데 'open-iscsi' 및 'lshw' 구성 요소가 필요합니다. 스크립트가 실행되는 컴퓨터에 구성 요소가 없으면 스크립트에서는 구성 요소를 설치할 권한을 요청합니다. 동의하여 필요한 구성 요소를 설치 합니다.

스크립트가 실행되는 머신과 복구 지점의 데이터 간에 보안 채널을 구축하는 데 사용되는 구성 요소를 다운로드하려면 `download.microsoft.com`에 대한 액세스 권한이 필요합니다.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>대용량 디스크가 있는 가상 머신 백업에서 파일 복구

이 섹션에서는 16 개 이상의 디스크를 사용 하는 Azure 가상 컴퓨터의 백업에서 파일 복구를 수행 하는 방법 또는 각 디스크 크기가 4 TB를 초과 하는 방법에 대해 설명 합니다.

파일 복구 프로세스는 백업에서 모든 디스크를 연결 하므로 대량 디스크 수 (>16) 또는 대량 디스크 > (각각 4 TB)를 사용 하는 경우 다음 작업을 수행 하는 것이 좋습니다.

- 파일 복구에 사용할 별도의 복원 서버(Azure VM D2v3 VM)를 유지합니다. 이 복원 서버를 파일 복구에만 사용할 수 있으며 필요하지 않으면 종료할 수 있습니다. 원래 머신에 복구하면 VM 자체에 상당한 영향을 주기 때문에 이 방법은 권장하지 않습니다.
- 그런 다음, 스크립트를 한 번 실행하여 파일 복구 작업이 성공했는지 확인합니다.
- 파일 복구 프로세스가 중단되면(디스크가 탑재되지 않거나 탑재되었지만 표시되지 않으면) 다음 단계를 수행합니다.
  - 복원 서버가 Windows VM인 경우:
    - OS가 WS 2012 이상인지 확인합니다.
    - 레지스트리 키가 아래의 복원 서버에서 제안하는 대로 설정되었는지 확인하고 서버를 다시 부팅합니다. GUID 옆에 있는 숫자의 범위는 0001-0005입니다. 다음 예제에서는 0004입니다. 매개 변수 섹션이 나올 때까지 레지스트리 키 경로를 탐색합니다.

    ![레지스트리 키 변경](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- 복원 서버가 Linux VM인 경우:
  - /etc/iscsi/iscsid.conf 파일에서 설정을 다음과 같이 변경합니다.
    - `node.conn[0].timeo.noop_out_timeout = 5`  받는 사람 `node.conn[0].timeo.noop_out_timeout = 30`
- 위와 같이 변경한 후 스크립트를 다시 실행합니다. 이렇게 변경하면 파일 복구가 성공할 가능성이 높습니다.
- 사용자가 스크립트를 다운로드할 때마다 Azure Backup은 다운로드의 복구 지점을 준비하는 프로세스를 시작합니다. 대용량 디스크인 경우 이 프로세스에 상당한 시간이 소요됩니다. 요청이 연속으로 발생하면 대상 준비가 다운로드 스파이럴 상태에 빠집니다. 따라서 포털/PowerShell/CLI에서 스크립트를 다운로드하고 20-30분 정도 기다렸다가(경험적 추론) 실행하는 것이 좋습니다. 이 시점에 대상은 스크립트에서 연결할 준비가 되어 있어야 합니다.
- 파일 복구 후 포털로 돌아가서 볼륨을 탑재할 수 없는 복구 지점에 대해 **디스크 분리** 를 선택 합니다. 기본적으로 이 단계는 기존 프로세스/세션을 모두 정리하고 복구 가능성을 높입니다.

## <a name="troubleshooting"></a>문제 해결

가상 머신에서 파일을 복구하는 동안 문제가 생기는 경우 다음 표에서 추가 정보를 확인하세요.

| 오류 메시지/시나리오 | 가능한 원인 | 권장 작업 |
| ------------------------ | -------------- | ------------------ |
| Exe 출력: *대상에 연결하는 동안 예외가 catch되었습니다.* | 스크립트가 복구 지점에 액세스할 수 없습니다.    | 머신이 [이전 액세스 요구 사항](#access-requirements)을 충족하는지 확인하세요. |  
| Exe 출력: *iSCSI 세션을 통해 대상이 이미 로그인되었습니다.* | 동일한 컴퓨터에서 스크립트가 이미 실행되었고 드라이브가 연결되었습니다. | 복구 지점의 볼륨이 이미 연결되었습니다. 원본 VM의 동일한 드라이브 문자로 탑재 되지 않을 수 있습니다. 파일 탐색기에서 사용 가능한 모든 볼륨을 탐색하여 파일을 찾습니다. |
| Exe 출력: *디스크가 포털을 통해 분리되었고 12시간 제한을 초과했으므로 이 스크립트는 유효하지 않습니다. 포털에서 새 스크립트를 다운로드하세요.* |    디스크가 포털에서 분리되었거나 12시간 제한을 초과했습니다. | 이 exe는 현재 유효하지 않으며 실행할 수 없습니다. 특정 시점 복구 파일에 액세스하려면 포털에서 새 exe를 찾으세요.|
| exe가 실행되는 머신에서: 분리 단추를 클릭하면 새 볼륨이 분리되지 않습니다. | 머신에서 iSCSI 초기자가 응답하지 않거나 대상에 대한 연결을 새로 고치지 않고 캐시를 유지하지 않습니다. |  **분리**를 클릭한 후에 잠시 대기합니다. 새 볼륨이 분리되지 않으면 모든 볼륨을 탐색하세요. 모든 볼륨을 탐색하면 초기자가 강제로 연결을 새로 고침하고, 디스크를 사용할 수 없다는 오류 메시지와 함께 볼륨이 분리됩니다.|
| Exe 출력: 스크립트가 성공적으로 실행되지만 스크립트 출력에 "New volumes attached(새 볼륨 연결됨)"가 표시되지 않습니다. |    일시적인 오류입니다.    | 볼륨은 이미 연결되었습니다. Explorer를 열어 탐색합니다. 스크립트를 실행할 때마다 동일한 머신을 사용하는 경우 머신을 다시 시작하면 목록이 후속 exe 실행에 표시됩니다. |
| Linux 특정: 원하는 볼륨을 볼 수 없습니다. | 스크립트가 실행되는 컴퓨터의 OS는 보호된 VM의 기본 파일 시스템을 인식하지 못할 수도 있습니다. | 복구 지점이 충돌 일관적인지 아니면 파일 일관적인지 확인합니다. 파일 일관적인 경우 OS가 보호된 VM의 파일 시스템을 인식하는 다른 머신에서 스크립트를 실행하세요. |
| Windows 특정: 원하는 볼륨을 볼 수 없습니다. | 디스크가 연결되었을 수도 있지만 볼륨이 구성되지 않았습니다. | 디스크 관리 화면에서 복구 지점과 관련된 추가 디스크를 식별합니다. 이러한 디스크가 오프 라인 상태 이면 디스크를 마우스 오른쪽 단추로 클릭 하 고 **온라인**을 선택 하 여 온라인 상태로 전환 해 보세요.|

## <a name="security"></a>보안

이 섹션에서는 Azure VM 백업에서 파일 복구를 구현하는 데 사용되는 다양한 보안 조치에 대해 설명합니다.

### <a name="feature-flow"></a>기능 흐름

이 기능은 전체 VM 또는 VM 디스크를 복원할 필요 없이 최소한의 단계로 VM 데이터에 액세스할 수 있도록 만들어졌습니다. VM 데이터에 대한 액세스는 스크립트(아래와 같이 실행하면 복구 볼륨을 탑재하는)를 통해 제공되며 모든 보안 구현의 기초를 형성합니다.

  ![보안 기능 흐름](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>보안 구현

#### <a name="select-recovery-point-who-can-generate-script"></a>복구 지점(스크립트를 생성할 수 있는) 선택

이 스크립트는 VM 데이터에 대한 액세스를 제공하므로, 이 스크립트를 생성할 수 있는 사람을 제어하는 것이 가장 중요합니다. 스크립트를 생성하려면 Azure Portal에 로그인하여 [RBAC에 대한 권한을 부여](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)받아야 합니다.

파일 복구에는 VM 복원 및 디스크 복원과 동일한 수준의 권한 부여가 필요합니다. 즉, 권한 있는 사용자만 VM 데이터를 살펴보고 스크립트를 생성할 수 있습니다.

생성된 스크립트는 Azure Backup 서비스에 대한 공식 Microsoft 인증서를 사용하여 서명됩니다. 스크립트가 변조되었다는 것은 서명이 손상되었다는 뜻이며, 스크립트를 실행하려는 시도가 있으면 OS에서는 이것을 잠재적 위험으로 강조 표시합니다.

#### <a name="mount-recovery-volume-who-can-run-script"></a>복구 볼륨(스크립트를 실행할 수 있는) 탑재

오직 관리자만이 스크립트를 실행할 수 있으며 관리자 모드에서 실행해야 합니다. 스크립트는 미리 생성된 단계 세트만 실행하고 외부 소스의 입력을 허용하지 않습니다.

스크립트를 실행 하려면 Azure Portal 또는 PowerShell/CLI에서 스크립트를 생성할 때 권한이 부여 된 사용자 에게만 표시 되는 암호가 필요 합니다. 이는 스크립트를 다운로드 하는 권한 있는 사용자도 스크립트 실행을 담당 하는 것입니다.

#### <a name="browse-files-and-folders"></a>파일 및 폴더 찾기

이 스크립트는 파일 및 폴더를 찾아보기 위해 컴퓨터의 iSCSI 초기자를 사용 하 고 iSCSI 대상으로 구성 된 복구 지점에 연결 합니다. 여기서는 누군가가 어느 한/모든 구성 요소를 모방/스푸핑하려는 시나리오를 생각해 볼 수 있습니다.

각 구성 요소가 서로를 인증하도록 상호 CHAP 인증 메커니즘을 사용하겠습니다. 즉, 가짜 초기자가 iSCSI 대상에 연결하고 가짜 대상이 스크립트가 실행되는 머신에 연결하기가 매우 어렵습니다.

복구 서비스와 머신 간의 데이터 흐름은 스크립트가 실행되는 머신에서 TCP를 통해 보안 TLS 터널([TLS 1.2가 지원되어야 함](#system-requirements))을 빌드하여 보호됩니다.

부모/백업 된 VM에 있는 모든 ACL (파일 Access Control 목록)은 탑재 된 파일 시스템에도 유지 됩니다.

스크립트는 복구 지점에 대한 읽기 전용 액세스 권한을 제공하며 12시간 동안만 유효합니다. 이전에 액세스를 제거 하려는 경우 Azure Portal/PowerShell/CLI에 로그인 하 고 해당 특정 복구 지점에 대해 **디스크 분리** 를 수행 합니다. 그러면 스크립트가 즉시 무효화됩니다.

## <a name="next-steps"></a>다음 단계

- 파일을 복원하는 동안 문제가 발생하는 경우 [문제 해결](#troubleshooting) 섹션을 참조하세요.
- [PowerShell을 통해 파일 복원](./backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) 방법을 알아보세요.
- [Azure CLI를 통해 파일 복원](./tutorial-restore-files.md) 방법을 알아보세요.
- VM을 복원한 후에는 [백업 관리](./backup-azure-manage-vms.md) 방법을 알아보세요.
