---
title: Azure Backup을 사용하여 하이브리드 백업을 보호하기 위한 보안 기능
description: Azure Backup의 보안 기능을 사용하여 백업을 좀 더 안전하게 만드는 방법 알아보기
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: trinadhk
ms.openlocfilehash: 2ba94963238cd5ee96df5c178a072addc5ddd75e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620690"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Azure Backup을 사용하여 하이브리드 백업을 보호하기 위한 보안 기능
맬웨어, 랜섬웨어 및 침입과 같은 보안 문제에 대한 우려는 증가하고 있습니다. 이러한 보안 문제는 돈과 데이터 측면 모두에서 비용이 많이 들 수 있습니다. 이러한 공격을 방지하기 위해 Azure Backup은 이제 하이브리드 백업을 보호하는 데 도움이 되는 보안 기능을 제공합니다. 이 문서는 Azure Recovery Services 에이전트와 Azure Backup Server를 사용하여 이러한 기능을 활성화하고 사용하는 방법을 다룹니다. 이러한 기능으로는 다음이 포함됩니다.

- **방지**. 암호 변경과 같은 중요한 작업이 수행될 때마다 추가적인 인증 계층이 제공됩니다. 이 유효성 검사는 유효한 Azure 자격 증명을 가진 사용자만 이러한 작업을 수행할 수 있는지 확인합니다.
- **경고**. 백업 데이터 삭제와 같은 중요한 작업이 수행될 때마다 구독 관리자에게 전자 메일 알림이 전송됩니다. 이 전자 메일은 사용자에게 이러한 작업을 신속하게 알립니다.
- **복구**. 삭제된 백업 데이터는 삭제일로부터 추가 14일 동안 보존됩니다. 이를 통해 지정된 기간 내에는 데이터를 복구할 수 있게 되므로 공격이 발생하더라도 데이터가 손실되지 않습니다. 또한 데이터 손상으로부터 보호하기 위해 더 많은 수의 최소 복구 지점이 유지됩니다.

> [!NOTE]
> IaaS(Infrastructure as a Service) VM 백업으로 인프라를 사용하는 경우 보안 기능은 사용하지 말아야 합니다. 이러한 기능은 아직 IaaS VM 백업에 사용할 수 없으므로 사용하도록 설정해도 영향을 미치지 않습니다. 다음을 사용하는 경우에만 보안 기능을 사용하도록 설정해야 합니다. <br/>
>  * **Azure Backup 에이전트**. 최소 에이전트 버전 2.0.9052. 이러한 기능을 사용하도록 설정하면 중요한 작업을 수행하기 위해 이 에이전트 버전으로 업그레이드해야 합니다. <br/>
>  * **Azure Backup Server**. Azure Backup Server 업데이트 1을 포함한 최소 Azure Backup 에이전트 버전 2.0.9052. <br/>
>  * **System Center Data Protection Manager**. Data Protection Manager 2012 R2 UR12 또는 Data Protection Manager 2016 UR2를 포함한 최소 Azure Backup 에이전트 버전 2.0.9052. <br/>


> [!NOTE]
> 이러한 기능은 Recovery Services 자격 증명 모음에만 사용할 수 있습니다. 새로 만든 모든 Recovery Services 자격 증명 모음은 기본적으로 이러한 기능을 사용합니다. 기존 Recovery Services 자격 증명 모음의 경우 사용자가 다음 섹션에 설명한 단계를 사용하여 이러한 기능을 사용하도록 설정해야 합니다. 기능을 사용하도록 설정하면 자격 증명 모음에 등록되어 있는 Recovery Services 에이전트 컴퓨터, Azure Backup Server 인스턴스 및 Data Protection Manager 서버에 모두 적용됩니다. 이 기능의 설정은 일회성 작업이며 사용하도록 설정한 후에는 사용하지 않도록 설정할 수 없습니다.
>

## <a name="enable-security-features"></a>보안 기능 사용
Recovery Services 자격 증명 모음을 만드는 경우 모든 보안 기능을 사용할 수 있습니다. 기존 자격 증명 모음으로 작업하는 경우 다음 단계를 수행하여 보안 기능을 사용하도록 설정합니다.

1. Azure 자격 증명을 사용하여 Azure Portal에 로그인합니다.
2. **찾아보기**를 선택하고 **Recovery Services**를 입력합니다.

    ![Azure Portal의 찾아보기 옵션의 스크린샷](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    복구 서비스 자격 증명 모음의 목록이 표시됩니다. 이 목록에서 자격 증명 모음을 선택합니다. 선택한 자격 증명 모음 대시보드가 열립니다.
3. 자격 증명 모음 아래쪽에 나타나는 항목 목록의 **설정**에서 **속성**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 옵션의 스크린샷](./media/backup-azure-security-feature/vault-list-properties.png)
4. **보안 설정**에서 **업데이트**를 클릭합니다.

    ![Recovery Services 자격 증명 모음 속성의 스크린샷](./media/backup-azure-security-feature/security-settings-update.png)

    업데이트 링크를 클릭하면 해당 기능을 사용하도록 설정하고 요약을 볼 수 있는 **보안 설정** 블레이드가 열립니다.
5. **Azure Multi-Factor Authentication을 구성했습니까?** 드롭다운 목록에서 값을 선택하여 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)을 사용하도록 설정했는지 확인합니다. 이 기능을 사용하도록 설정했으면 Azure Portal에 로그인하는 동안 다른 디바이스(예: 휴대폰)에서 인증을 받을지 묻는 메시지가 표시됩니다.

   Backup에서 중요한 작업을 수행할 때 Azure Portal에서 사용할 수 있는 보안 PIN을 입력해야 합니다. Azure Multi-Factor Authentication을 사용하도록 설정하면 보안 계층이 추가됩니다. 유효한 Azure 자격 증명을 가지며 두 번째 디바이스에서 인증을 받은 인증된 사용자만 Azure Portal에 액세스할 수 있습니다.
6. 보안 설정을 저장하려면 **사용**을 선택하고 **저장**을 클릭합니다. 이전 단계에서 **Azure Multi-Factor Authentication을 구성했습니까?** 목록에서 값을 선택한 후에만 **사용**을 선택할 수 있습니다.

    ![보안 설정의 스크린샷](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>삭제된 백업 데이터 복구
Backup은 추가로 14일 동안 삭제된 백업 데이터를 유지하고, **백업 데이터 삭제 작업으로 백업 중지**를 수행한 경우에도 백업 데이터를 즉시 삭제하지 않습니다. 이 데이터를 14일 기간 내 복원하려면 사용 중인 항목에 따라 다음 단계를 수행합니다.

**Azure Recovery Services 에이전트** 사용자의 경우:

1. 백업이 발생하는 컴퓨터를 계속 사용할 수 있는 경우 삭제한 데이터 원본을 다시 보호하고, Azure Recovery Services의 [동일한 컴퓨터로 데이터 복구](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine)를 사용하여 모든 이전 복구 지점에서 복구합니다.
2. 이 컴퓨터를 사용할 수 없는 경우 [다른 컴퓨터로 복구](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)를 사용하여 다른 Azure Recovery Services 컴퓨터를 통해 이 데이터를 가져옵니다.

**Azure Backup Server** 사용자:

1. 백업이 발생하는 서버를 계속 사용할 수 있는 경우 삭제된 데이터 원본을 다시 보호하고 **데이터 복구** 기능을 사용하여 모든 이전 복구 지점에서 복구합니다.
2. 이 서버를 사용할 수 없는 경우 [다른 Azure Backup Server에서 데이터 복구](backup-azure-alternate-dpm-server.md)를 사용하여 다른 Azure Backup Server 인스턴스를 통해 이 데이터를 가져옵니다.

**Data Protection Manager** 사용자의 경우:

1. 백업이 발생하는 서버를 계속 사용할 수 있는 경우 삭제된 데이터 원본을 다시 보호하고 **데이터 복구** 기능을 사용하여 모든 이전 복구 지점에서 복구합니다.
2. 이 서버를 사용할 수 없는 경우 [외부 DPM 추가](backup-azure-alternate-dpm-server.md)를 사용하여 다른 Data Protection Manager 서버를 통해 이 데이터를 가져옵니다.

## <a name="prevent-attacks"></a>공격을 방지
유효한 사용자만 다양한 작업을 수행할 수 있는지 확인하기 위한 검사가 추가되었습니다. 여기에는 추가적인 인증 계층 제공 및 복구를 위한 최소 보존 범위 유지 관리가 포함됩니다.

### <a name="authentication-to-perform-critical-operations"></a>중요한 작업을 수행하기 위한 인증
중요한 작업에 대한 추가 인증 계층 제공의 일부로, **데이터 삭제로 보호 중지** 및 **암호 변경** 작업을 수행할 때 보안 PIN을 입력하라는 메시지가 표시됩니다.

> [!NOTE]
> 
> 현재 DPM 및 MABS에서 **데이터 삭제로 보호 중지**에 보안 핀이 지원되지 않습니다.

이 PIN을 받으려면 다음을 수행합니다.

1. Azure 포털에 로그인합니다.
2. **Recovery Services 자격 증명 모음** > **설정** > **속성**을 찾습니다.
3. **보안 PIN** 아래에서 **생성**을 클릭합니다. Azure Recovery Services 에이전트 사용자 인터페이스에 입력하는 PIN을 포함한 블레이드가 열립니다.
    이 PIN은 5분 동안만 유효하며 해당 시간이 지나면 자동으로 생성됩니다.

### <a name="maintain-a-minimum-retention-range"></a>최소 보존 범위 유지 관리
항상 사용 가능한 유효한 복구 지점이 존재하도록 하기 위해 다음과 같은 검사가 추가되었습니다.

- 일일 보존의 경우 최소 **7**일 보존이 수행되어야 합니다.
- 주간 보존의 경우 최소 **4**주 보존이 수행되어야 합니다.
- 월간 보존의 경우 최소 **3**개월 보존이 수행되어야 합니다.
- 연간 보존의 경우 최소 **1**년 보존이 수행되어야 합니다.

## <a name="notifications-for-critical-operations"></a>중요한 작업에 대한 알림
일반적으로 중요한 작업을 수행할 때마다 구독 관리자는 작업에 대한 세부 정보가 포함된 전자 메일 알림을 받게 됩니다. Azure Portal을 사용하면 이러한 알림에 대한 전자 메일 받는 사람을 추가로 구성할 수 있습니다.

이 문서에 언급된 보안 기능은 대상이 지정된 공격에 대해 방어 메커니즘을 제공합니다. 무엇보다도 공격이 발생하는 경우 이러한 기능을 사용하면 데이터를 복구할 수 있습니다.

## <a name="troubleshooting-errors"></a>문제 해결 오류
| 작업(Operation) | 오류 세부 정보 | 해결 방법 |
| --- | --- | --- |
| 정책 변경 |백업 정책을 수정할 수 없습니다. 오류: 내부 서비스 오류[0x29834]로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |**원인:**<br/>이 오류는 보안 설정이 활성화되고 위에서 지정된 최소값 미만으로 보존 범위를 줄이려고 하고 지원되지 않는 버전에 있는 경우 발생합니다(지원되는 버전은 이 문서의 첫 번째 참고에 지정됨). <br/>**권장 작업:**<br/> 이 경우 정책 관련 업데이트를 계속 진행할 수 있도록 지정된 최소 보존 기간(매일 7일, 매주 4주, 매달 3주 또는 매년 1년) 이상으로 보존 기간을 설정해야 합니다. 필요에 따라 선호되는 방법은 백업 에이전트, Azure Backup Server 및/또는 DPM UR을 업데이트하여 모든 보안 업데이트를 활용하는 것입니다. |
| 암호 변경 |입력한 보안 PIN이 잘못되었습니다. (ID: 100130) 이 작업을 완료하려면 올바른 보안 PIN을 입력합니다. |**원인:**<br/> 이 오류는 중요한 작업(예: 암호 변경)을 수행하는 동안 잘못되거나 만료된 보안 PIN을 입력하는 경우 발생합니다. <br/>**권장 작업:**<br/> 작업을 완료하려면 유효한 보안 PIN을 입력해야 합니다. PIN을 가져오려면 Azure Portal에 로그인하고 [Recovery Services 자격 증명 모음] > [설정] > [속성] > [보안 PIN 생성]으로 이동합니다. 이 PIN을 사용하여 암호를 변경합니다. |
| 암호 변경 |작업이 실패했습니다. ID: 120002 |**원인:**<br/>이 오류는 보안 설정이 활성화되고 암호를 변경하려고 하고 지원되지 않는 버전에 있는 경우 발생합니다(유효한 버전은 이 문서의 첫 번째 참고에 지정됨).<br/>**권장 작업:**<br/> 암호를 변경하려면 먼저 백업 에이전트를 최소 버전 최소 2.0.9052로, Azure Backup 서버를 최소 업데이트 1로, DPM을 최소 DPM 2012 R2 UR12 또는 DPM 2016 UR2로 업데이트한 다음(아래 다운로드 링크) 유효한 보안 PIN을 입력해야 합니다. PIN을 가져오려면 Azure Portal에 로그인하고 Recovery Services 자격 증명 모음 > 설정 > 속성 > 보안 PIN 생성으로 이동합니다. 이 PIN을 사용하여 암호를 변경합니다. |

## <a name="next-steps"></a>다음 단계
* [Azure Recovery Services 자격 증명 모음을 시작](backup-azure-vms-first-look-arm.md)하여 이러한 기능을 사용하도록 설정합니다.
* [최신 Azure Recovery Services 에이전트를 다운로드](https://aka.ms/azurebackup_agent)하여 공격으로부터 Windows 컴퓨터 및 백업 데이터를 보호합니다.
* [최신 Azure Backup Server를 다운로드](https://aka.ms/latest_azurebackupserver)하여 공격으로부터 워크로드 및 백업 데이터를 보호합니다.
* [System Center 2012 R2 Data Protection Manager용 UR12를 다운로드](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)하거나 [System Center 2016 Data Protection Manager용 UR2를 다운로드](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager)하여 공격으로부터 워크로드 및 백업 데이터를 보호합니다.
