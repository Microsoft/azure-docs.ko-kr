---
title: Azure Backup Server에서 Modern Backup Storage 사용
description: Azure Backup Server의 새로운 기능에 대해 알아봅니다. 이 문서에서는 Backup Server 설치를 업그레이드하는 방법을 설명합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 621d071f98701ff3a949f4172fef1d13819d7192
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813118"
---
# <a name="add-storage-to-azure-backup-server"></a>Azure Backup Server에 저장소 추가

Azure Backup Server V2 이상은 스토리지를 50% 절약할 수 있고, 백업이 3배 더 빨라지고, 스토리지를 더 효율적으로 사용할 수 있는 Modern Backup Storage를 지원합니다. 저장소에서 워크로드를 인식할 수도 있습니다.

> [!NOTE]
> Modern Backup Storage를 사용하려면 Windows Server 2016에서 Backup Server V2 또는 V3을, Windows Server 2019에서 V3을 실행해야 합니다.
> Backup Server V2를 이전 버전의 Windows Server에서 실행하면 Azure Backup Server는 Modern Backup Storage를 사용할 수 없습니다. 대신에 Backup Server V1에서 보호하는 것처럼 워크로드를 보호합니다. 자세한 내용은 Backup Server 버전 [보호 매트릭스](backup-mabs-protection-matrix.md)를 참조하세요.

## <a name="volumes-in-backup-server"></a>Backup Server의 볼륨

Backup Server V2는 스토리지 볼륨을 허용합니다. 볼륨을 추가하면 Backup Server에서는 볼륨 형식을 Modern Backup Storage에 필요한 ReFS(Resilient File System)로 지정합니다. 볼륨을 추가하고 필요한 경우 나중에 확장하려면 이 워크플로를 사용하는 것이 좋습니다.

1.  VM에 Backup Server를 설치합니다.
2.  저장소 풀의 가상 디스크에 볼륨을 만듭니다.
    1.  저장소 풀에 디스크를 추가하고 간단한 레이아웃으로 가상 디스크를 만듭니다.
    2.  다른 디스크를 추가하고 가상 디스크를 확장합니다.
    3.  가상 디스크에 볼륨을 만듭니다.
3.  Backup Server에 볼륨을 추가합니다.
4.  워크로드 인식 저장소를 구성합니다.

## <a name="create-a-volume-for-modern-backup-storage"></a>Modern Backup Storage에 대한 볼륨 만들기

볼륨이 포함된 Backup Server V2 이상을 디스크 스토리지로 사용하면 스토리지를 지속적으로 제어할 수 있습니다. 볼륨은 단일 디스크일 수 있습니다. 하지만 나중에 저장소를 확장하려는 경우에는 저장소 공간을 사용하여 만들어진 디스크에서 볼륨을 만듭니다. 이렇게 하면 백업 저장소용 볼륨을 확장하려는 경우 도움이 될 수 있습니다. 이 섹션에서는 이 설정을 통해 볼륨을 만드는 모범 사례를 제공합니다.

1. 서버 관리자에서 **파일 및 Storage 서비스** > **볼륨** > **스토리지 풀**을 선택합니다. **실제 디스크**에서 **새 저장소 풀**을 선택합니다.

    ![새 저장소 풀 만들기](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. **작업** 드롭다운 상자에서 **새 가상 디스크**를 선택합니다.

    ![가상 디스크 추가](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. 저장소 풀을 선택하고 나서 **실제 디스크 추가**를 선택합니다.

    ![실제 디스크 추가](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. 실제 디스크를 선택하고 나서 **가상 디스크 확장**을 선택합니다.

    ![가상 디스크 확장](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. 가상 디스크를 선택하고 나서 **새 볼륨**을 선택합니다.

    ![새 볼륨 만들기](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. **서버 및 디스크 선택** 대화 상자에서 서버와 새 디스크를 선택합니다. 그다음에 **다음**을 선택합니다.

    ![서버 및 디스크 선택](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Backup Server 디스크 저장소에 볼륨 추가

Backup Server에 볼륨을 추가하려면 **관리** 창에서 저장소를 다시 검사하고 나서 **추가**를 선택합니다. Backup Server 저장소에 추가할 수 있는 모든 볼륨 목록이 표시됩니다. 사용 가능한 볼륨이 선택한 볼륨 목록에 추가된 후 해당 볼륨에 이름을 지정하면 관리하는 데 도움이 될 수 있습니다. 이러한 볼륨의 형식을 ReFS로 지정하여 Backup Server에서 Modern Backup Storage의 이점을 활용할 수 있게 하려면 **확인**을 선택합니다.

![사용 가능한 볼륨 추가](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>워크로드 인식 저장소 설정

워크로드 인식 저장소를 사용하면 특정 종류의 워크로드를 선별적으로 저장하는 볼륨을 선택할 수 있습니다. 예를 들어 빈번한 대규모 백업이 필요한 워크로드만 저장하도록 많은 수의 IOPS(초당 입/출력 작업)를 지원하는 확장 볼륨을 설정할 수 있습니다. 예를 들어 트랜잭션 로그가 포함된 SQL Server가 있습니다. VM과 같이 덜 빈번하게 백업되는 다른 워크로드는 저비용 볼륨으로 백업할 수 있습니다.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Azure Backup Server에서 저장소 풀에 있는 볼륨의 속성을 업데이트 하는 Update-dpmdiskstorage PowerShell cmdlet을 사용 하 여 워크 로드 인식 저장소를 설정할 수 있습니다. 

구문

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
다음 스크린샷은 PowerShell 창의 Update-DPMDiskStorage cmdlet을 보여 줍니다.

![PowerShell 창의 Update-DPMDiskStorage 명령](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

PowerShell을 사용하여 변경하는 내용은 Backup Server 관리자 콘솔에 반영됩니다.

![관리자 콘솔의 디스크 및 볼륨](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Modern Backup Storage로 레거시 스토리지 마이그레이션
Backup Server V2로 업그레이드하거나 이 버전을 설치하고 운영 체제를 Windows Server 2016으로 업그레이드한 후 Modern Backup Storage를 사용하도록 보호 그룹을 업데이트합니다. 기본적으로 보호 그룹은 변경되지 않습니다. 보호 그룹은 처음에 설정된 대로 계속 작동합니다.

Modern Backup Storage를 사용하도록 보호 그룹을 업데이트하는 것은 선택 사항입니다. 보호 그룹을 업데이트하려면 데이터 보존 옵션을 사용하여 모든 데이터 원본의 보호를 중지합니다. 그다음에 데이터 원본을 새 보호 그룹에 추가합니다.

1. 관리자 콘솔에서 **보호** 기능을 선택합니다. **보호 그룹 구성원** 목록에서 구성원을 마우스 오른쪽 단추로 클릭하고 **구성원 보호 중지**를 선택합니다.

   ![구성원 보호 중지](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. **그룹에서 제거** 대화 상자에서 저장소 풀의 사용된 디스크 공간 및 사용 가능한 공간을 검토합니다. 기본값은 디스크에서 복구 지점을 유지하고 관련 보존 정책에 따라 만료되도록 허용하는 것입니다. **확인**을 클릭합니다.

   사용된 디스크 공간을 사용 가능한 저장소 풀로 즉시 반환하려면 **디스크의 복제본 삭제** 확인란을 선택하여 해당 구성원과 연결된 백업 데이터(및 복구 지점)를 삭제합니다.

   ![그룹에서 제거 대화 상자](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Modern Backup Storage를 사용하는 보호 그룹을 만듭니다. 보호되지 않는 데이터 원본을 포함합니다.

## <a name="add-disks-to-increase-legacy-storage"></a>디스크를 추가하여 레거시 저장소 늘리기

Backup Server에서 레거시 저장소를 사용하려면 디스크를 추가하여 레거시 저장소를 늘려야 합니다.

디스크 저장소를 추가하려면:

1. 관리자 콘솔에서 **관리** > **디스크 저장소** > **추가**를 선택합니다.

    ![디스크 저장소 추가 대화 상자](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. **디스크 저장소 추가** 대화 상자에서 **디스크 추가**를 선택합니다.

5. 사용 가능한 디스크 목록에서 추가할 디스크를 선택하고, **추가**를 선택하고 나서, **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계
Backup Server를 설치한 후 서버를 준비하는 방법을 알아보거나 워크로드 보호를 시작합니다.

- [Backup Server 워크로드 준비](backup-azure-microsoft-azure-backup.md)
- [Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
- [Backup Server를 사용하여 SQL Server 백업](backup-azure-sql-mabs.md)
