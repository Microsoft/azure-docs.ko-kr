---
title: '자습서: Windows Server로 항목 복구'
description: 이 자습서에서는 MARS(Microsoft Azure Recovery Services Agent) 에이전트를 사용하여 Azure에서 Windows Server로 항목을 복구하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263115"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Azure에서 Windows Server로 파일 복구

Azure Backup을 사용하면 Windows Server의 백업에서 개별 항목을 복구할 수 있습니다. 개별 파일 복구는 실수로 삭제된 파일을 신속하게 복원해야 하는 경우에 유용합니다. 이 자습서에서는 MARS(Microsoft Azure Recovery Services) 에이전트를 사용하여 Azure에서 이미 수행한 백업에서 항목을 복구하는 방법을 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 개별 항목의 복구 시작
> * 복구 지점 선택
> * 복구 지점에서 항목 복원

이 자습서에서는 [Windows Server를 Azure에 백업](backup-windows-with-mars-agent.md)하는 단계를 이미 수행했으며 Azure에 Windows Server 파일의 백업이 하나 이상 있다고 가정합니다.

## <a name="initiate-recovery-of-individual-items"></a>개별 항목의 복구 시작

Microsoft Azure Backup이라는 유용한 사용자 인터페이스 마법사가 MARS(Microsoft Azure Recovery Services) 에이전트와 함께 설치됩니다. Microsoft Azure Backup 마법사는 MARS(Microsoft Azure Recovery Services) 에이전트와 함께 작동하여 Azure에 저장된 복구 지점에서 백업 데이터를 검색합니다. Microsoft Azure Backup 마법사를 사용하여 Windows Server에 복원할 파일 또는 폴더를 식별하세요.

1. **Microsoft Azure Backup** 스냅인을 엽니다. **Microsoft Azure Backup**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.

    ![Microsoft Azure Backup 스냅인](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. 마법사에서 에이전트 콘솔의 **작업 창**에 있는 **데이터 복구**를 선택하여 **데이터 복구** 마법사를 시작합니다.

    ![데이터 복구 선택](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. **시작** 페이지에서 **이 서버(서버 이름)** 를 선택하고 **다음**을 클릭합니다.

4. **복구 모드 선택** 페이지에서 **개별 파일 및 폴더**를 선택한 후, **다음**을 선택하여 복구 지점 선택 프로세스를 시작합니다.

5. **볼륨 및 날짜 선택** 페이지에서 복원할 파일 또는 폴더가 포함된 볼륨을 선택하고 **탑재**를 선택합니다. 날짜를 선택하고 복구 지점에 해당하는 드롭다운 메뉴에서 시간을 선택합니다. **굵게** 표시된 날짜는 해당 날짜에 하나 이상의 사용 가능한 복구 지점이 있음을 나타냅니다.

    ![볼륨 및 날짜 선택](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    **탑재**를 선택하면 Azure Backup에서 복구 지점을 디스크로 사용할 수 있게 만듭니다. 디스크에서 파일을 찾아 복구합니다.

## <a name="restore-items-from-a-recovery-point"></a>복구 지점에서 항목 복원

1. 복구 볼륨이 탑재되면 **찾아보기**를 선택하여 Windows 탐색기를 열고 복구할 파일과 폴더를 찾습니다.

    ![찾아보기 선택](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    복구 볼륨에서 직접 파일을 열고 내용 확인할 수 있습니다.

2. Windows 탐색기에서 복원할 파일 및 폴더를 복사하여 서버의 원하는 위치에 붙여넣습니다.

    ![파일 및 폴더 복사](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. 파일 및 폴더 복원이 완료되면 **데이터 복구** 마법사의 **찾아보기 및 복구 파일** 페이지에서 **분리**를 선택합니다.

    ![분리 선택](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. **예**를 선택하여 볼륨 분리를 확인합니다.

    스냅샷이 분리되면 에이전트 콘솔의 **작업** 창에 **작업 완료됨**이 표시됩니다.

## <a name="next-steps"></a>다음 단계

Windows Server 데이터를 Azure로 백업 및 복원하는 방법에 대한 자습서를 완료했습니다. Azure Backup에 대한 자세한 내용은 암호화된 가상 머신 백업에 대한 PowerShell 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [암호화된 VM 백업](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
