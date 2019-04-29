---
title: Azure Backup 중앙 보고 콘텐츠 팩 업데이트
description: Power BI의 Azure Backup 콘텐츠 팩 업데이트에 대한 정보를 제공합니다.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 87bc7ed3bb59266484858f534080fa87a7230981
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641918"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>Azure Backup 중앙 보고 콘텐츠 팩 업데이트 

[Azure Backup 콘텐츠 팩](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi)을 사용하면 중앙 백업 관련 보고서를 확인할 수 있습니다. 콘텐츠 팩은 더 많은 기능을 추가하고 버그를 수정하기 위해 정기적으로 업데이트됩니다. 이 문서에서는 콘텐츠 팩을 업데이트하는 방법을 설명합니다. 또한 업데이트를 연기하고 시간별로 수행된 업데이트를 확인하는 방법도 설명합니다.

## <a name="get-updates-to-the-content-pack"></a>콘텐츠 팩 업데이트 가져오기

### <a name="get-the-updated-content-pack"></a>업데이트된 콘텐츠 팩 가져오기
콘텐츠 팩 복사본을 변경하지 않은 경우 콘텐츠 팩은 자동 업데이트됩니다. 콘텐츠 팩이 변경되면 Power BI에 알림이 수신되며 전자 메일 알림도 수신됩니다. 편리할 때 업데이트된 콘텐츠 팩을 가져오도록 선택할 수 있습니다. 

### <a name="postpone-the-update"></a>업데이트 연기
[사용자 지정 작업 영역](https://youtu.be/26zyOtyHPJM?t=1m57s)에 콘텐츠 팩을 가져오는 것이 좋습니다. 이제 보고서를 편집할 수 있습니다.
위에서 설명한 대로 콘텐츠 팩이 변경되면 PowerBI에 알림이 표시됩니다. 나중에 콘텐츠 팩을 가져오도록 선택할 수 있습니다. 

## <a name="coming-soon"></a>서비스 예정
   
Azure Backup 콘텐츠 팩은 더 많은 워크로드를 지원하도록 업데이트됩니다. 이러한 워크로드에는 System Center Data Protection Manager 및 IaaS VM 백업용 Azure SQL Database가 포함됩니다. 즉, 현재 지원되는 Azure Backup 및 Azure VM Backup과 더불어 이 워크로드가 추가로 지원됩니다. 따라서 모든 백업 데이터를 중앙 위치 한 곳에서 확인하고 분석할 수 있습니다. 조직의 요구에 맞게 [보고서를 사용자 지정](https://youtu.be/26zyOtyHPJM)할 수도 있습니다.

Azure Backup 콘텐츠 팩과 함께 제공되는 미리 구성된 보고서는 현재 새롭게 변경되고 있습니다. 이 변경을 통해 여러 워크로드에서 보고서를 더욱 유용하게 활용할 수 있을 것입니다. 아래에는 제공 예정인 보고서 집합의 스크린샷이 나와 있습니다.

### <a name="summary"></a>요약
   
![요약](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>결제

![결제](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>준수

![준수](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Backup 항목
![Backup 항목](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>경고

![경고](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>교육

![교육](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>다음 단계

* [조직 전체에서 보고서 공유](https://youtu.be/26zyOtyHPJM)
* [Azure Backup - FAQ](backup-azure-backup-faq.md)
