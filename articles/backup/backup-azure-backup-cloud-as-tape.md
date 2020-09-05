---
title: 테이프 인프라를 교체 하는 방법
description: Azure에서 데이터를 백업 및 복원할 수 있는 테이프와 유사한 의미 체계 Azure Backup 제공 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 4174868d391eda70ce4921deee63b8bacf672b4c
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757237"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>장기 스토리지를 테이프에서 Azure 클라우드로 이동

Azure Backup 및 System Center Data Protection Manager 고객은 다음을 수행할 수 있습니다.

* 해당 조직의 요구에 가장 적합한 일정으로 데이터를 백업합니다.
* 더 긴 기간 동안 백업 데이터를 보존 합니다.
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
2. **주간 보존 정책**: 자정 및 오후 6 시에 수행 되는 백업은 4 주 동안 보존 됩니다.
3. **월별 보존 정책**: 각 월의 마지막 토요일 자정 및 오후 6 시에 수행 된 백업은 12 개월 동안 유지 됩니다.
4. **연도별 보존 정책**: 3 월의 마지막 토요일 자정에 수행 된 백업은 10 년 동안 보존 됩니다.

앞의 도표에서 "보존 지점"(데이터를 복원할 수 있는 지점)의 총 수는 다음과 같이 계산됩니다.

* 7일 동안 매일 2지점 = 복구 지점 14개
* 4주 동안 매주 2지점 = 복구 지점 8개
* 12개월 동안 매달 2지점 = 복구 지점 24개
* 10년 동안 매년 1지점 = 복구 지점 10개

복구 지점의 총 수는 56개입니다.

> [!NOTE]
> Azure Backup를 사용 하 여 보호 된 인스턴스당 최대 9999 개의 복구 시점을 만들 수 있습니다. 보호된 인스턴스는 Azure로 백업하는 컴퓨터, 서버(실제 또는 가상) 또는 워크로드입니다.
>

## <a name="advanced-configuration"></a>고급 구성

앞의 화면에서 **수정** 을 클릭하면 보존 일정을 더 유연하게 지정할 수 있습니다.

![정책 수정 창](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>다음 단계

Azure Backup에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Backup 소개](./backup-overview.md)
* [Azure Backup 시도](./backup-windows-with-mars-agent.md)
