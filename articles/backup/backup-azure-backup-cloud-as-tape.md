---
title: 테이프 인프라를 대체하는 방법
description: Azure에서 데이터를 백업하고 복원할 수 있도록 Azure Backup이 테이프와 유사한 의미 체계를 제공하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 695cc2644521384527ecd871f3613a078e987aa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89378443"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>장기 스토리지를 테이프에서 Azure 클라우드로 이동

Azure Backup 및 System Center Data Protection Manager 고객은 다음을 수행할 수 있습니다.

* 해당 조직의 요구에 가장 적합한 일정으로 데이터를 백업합니다.
* 백업 데이터를 더 오랜 기간 동안 보존합니다.
* 장기 보존 시 (테이프 대신) Azure를 사용해 보세요.

이 문서에서는 고객이 백업 및 보존 정책을 사용 하도록 설정하는 방법을 설명합니다. 테이프를 사용하여 장기 보존 요구 사항을 해결하는 고객들은 이제 이 기능을 사용하여 강력하고 실행 가능한 대안을 마련할 수 있습니다. 이 기능은 Azure Backup의 최신 릴리스( [여기](https://aka.ms/azurebackup_agent)에서 사용 가능)에서 사용할 수 있습니다. System Center DPM 고객은 DPM을 Azure Backup 서비스에 사용하려면 먼저 DPM 2012 R2 UR5 이상으로 업데이트해야 합니다.

## <a name="what-is-the-backup-schedule"></a>Backup 일정은 무엇입니까?

백업 일정은 백업 작업의 빈도를 나타냅니다. 예를 들어, 다음 화면의 설정은 백업이 매일 오후 6시와 자정에 수행되는 것을 나타냅니다.

![일별 일정](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

고객은 주간 백업을 예약할 수도 있습니다. 예를 들어, 다음 화면의 설정은 백업이 매주 일요일과 수요일 오전 9시 30분과 새벽 1시에 번갈아서 수행되는 것을 의미합니다.

![주별 일정](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>보존 정책은 무엇입니까?

보존 정책은 백업의 저장 기간을 나타냅니다. 모든 백업 지점에 대한 "일반 정책"을 지정하는 대신 백업이 수행되는 시기에 따라 다른 보존 정책을 지정할 수 있습니다. 예를 들어 매일 수행된 백업 지점(작업 복구 지점으로 사용됨)은 90일 동안 보존됩니다. 감사를 위해 매분기 말에 수행한 백업 지점은 더 오래 유지됩니다.

![보존 정책](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

이 정책에 지정된 "보존 지점"의 총 수는 90(일별 지점) + 40(10년 동안 각 분기별) = 130입니다.

## <a name="example--putting-both-together"></a>예 - 두 가지를 결합

![샘플 화면](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **일 단위 보존 정책**: 매일 수행된 백업이 7일 동안 저장됩니다.
2. **매주 보존 정책**: 매주 토요일 자정과 오후 6시에 수행된 백업이 4주 동안 유지됩니다
3. **매월 보존 정책**: 매월 마지막 주 토요일 자정과 오후 6시에 수행된 백업이 12개월 동안 유지됩니다.
4. **매년 보존 정책**: 매년 3월 마지막 주 토요일 자정에 수행된 백업이 10년 동안 유지됩니다.

앞의 도표에서 "보존 지점"(데이터를 복원할 수 있는 지점)의 총 수는 다음과 같이 계산됩니다.

* 7일 동안 매일 2지점 = 복구 지점 14개
* 4주 동안 매주 2지점 = 복구 지점 8개
* 12개월 동안 매달 2지점 = 복구 지점 24개
* 10년 동안 매년 1지점 = 복구 지점 10개

복구 지점의 총 수는 56개입니다.

> [!NOTE]
> Azure Backup을 사용하면 보호된 인스턴스당 최대 9999개의 복구 지점을 만들 수 있습니다. 보호된 인스턴스는 Azure로 백업하는 컴퓨터, 서버(실제 또는 가상) 또는 워크로드입니다.
>

## <a name="advanced-configuration"></a>고급 구성

이전 화면에서 **수정** 을 선택하면 고객이 보존 일정을 더 유연하게 지정할 수 있습니다.

![정책 수정 창](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>다음 단계

Azure Backup에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Backup 소개](./backup-overview.md)
* [Azure Backup 시도](./backup-windows-with-mars-agent.md)
