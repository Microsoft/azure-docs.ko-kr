---
title: 포함 파일
description: 포함 파일
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 74fe531c07aa871b06b5d0773f7e8fb0ade80be4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850637"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기
Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면:

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

    ![모든 서비스 선택](./media/backup-create-rs-vault/click-all-services.png)

3. **모든 서비스** 대화 상자에서 **Recovery Services**를 입력합니다. 입력 내용에 따라 리소스 목록이 필터링됩니다. 리소스 목록에서 **Recovery Services 자격 증명 모음**을 선택합니다.

    ![Recovery Services 자격 증명 모음 입력 및 선택](./media/backup-create-rs-vault/all-services.png)

    구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.
    
4. **Recovery Services 자격 증명 모음** 대시보드에서 **추가**를 선택합니다.

    ![Recovery Services 자격 증명 모음 추가](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Recovery Services 자격 증명 모음** 대화 상자가 열립니다. **이름**, **구독**, **리소스 그룹** 및 **이름**에 대한 값을 입력합니다.

    ![Recovery Services 자격 증명 모음 구성](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **이름**: 자격 증명 모음을 식별 하는 친숙 한 이름을 입력 합니다. 이름은 Azure 구독에 고유해야 합니다. 2자 이상 50자 이하의 이름을 지정합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다.
   - **구독**: 사용할 구독을 선택 합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택한 다음, 드롭다운 목록 상자에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/resource-group-overview.md)를 참조하세요.
   - **Location**: 자격 증명 모음에 대 한 지리적 지역을 선택 합니다. 가상 머신을 보호할 자격 증명 모음을 만들려면, 자격 증명 모음이 **반드시** 가상 머신과 동일한 지역에 있어야 합니다.

      > [!IMPORTANT]
      > VM의 위치를 잘 모를 경우 대화 상자를 닫습니다. 포털에서 가상 머신의 목록으로 이동합니다. 가상 머신이 여러 지역에 있는 경우 각 지역에 Recovery Services 자격 증명 모음을 만듭니다. 첫 번째 위치에서 자격 증명 모음을 만든 후에 다른 위치에 대한 자격 증명 모음을 만듭니다. 백업 데이터를 저장할 저장소 계정을 지정하지 않아도 됩니다. Recovery Services 자격 증명 모음 및 Azure Backup 서비스는 이를 자동으로 처리합니다.
      >
      >

5. Recovery Services 자격 증명 모음을 만들 준비가 되면 **만들기**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기](./media/backup-create-rs-vault/click-create-button.png)

    Recovery Services 자격 증명 모음을 만드는 데 어느 정도 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 모서리에 있는 **알림** 영역에서 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에서 볼 수 있습니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

     ![백업 자격 증명 모음 목록 새로 고침](./media/backup-create-rs-vault/refresh-button.png)
