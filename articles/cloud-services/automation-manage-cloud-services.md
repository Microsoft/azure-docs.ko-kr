---
title: Azure Automation을 사용하여 Azure Cloud Services 관리 | Microsoft Docs
description: Azure Automation 서비스를 사용하여 대규모 Azure Cloud Services를 관리하는 방법을 알아봅니다.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''
ms.assetid: 3789810a-2892-4eef-bf29-c781c1b5af48
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: timlt
ms.openlocfilehash: b3660901c86dd644369e6d1913e825cbd5ea316b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623207"
---
# <a name="managing-azure-cloud-services-using-azure-automation"></a>Azure Automation을 사용하여 Azure Cloud Services 관리
이 가이드에서는 Azure Automation 서비스 및 이 서비스를 사용하여 Azure Cloud Services 관리를 간소화하는 방법을 소개합니다.

## <a name="what-is-azure-automation"></a>Azure Automation 정의
[Azure Automation](https://azure.microsoft.com/services/automation/)은 프로세스 자동화를 통해 클라우드 관리를 간소화하기 위한 Azure 서비스입니다. Azure Automation을 통해 장기 실행 작업, 수동 작업, 오류가 발생하기 쉬운 작업 및 빈번하게 반복되는 작업을 자동화하여 조직의 안정성, 효율성 및 가치 창출 시간을 향상시킬 수 있습니다.

Azure Automation은 조직이 성장함에 따라 요구를 충족하기 위해 크기가 조정되는 안정성과 가용성 높은 워크플로 실행 엔진을 제공합니다. Azure Automation에서는 작업이 정확히 필요한 시간에 수행되도록 수동, 타사 시스템 또는 예약된 간격에 의해 프로세스를 시작할 수 있습니다.

Azure Automation에 의해 자동으로 실행되도록 클라우드 관리 작업을 이동하여 작업 오버헤드를 줄이고 IT/DevOps 직원들이 비즈니스 가치를 추가하는 작업에 집중할 수 있게 합니다.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Azure Automation을 통해 Azure Cloud Services 관리 향상
[Azure PowerShell 도구](/powershell/)에서 사용할 수 있는 PowerShell cmdlet을 통해 Azure Automation에서 Azure Cloud Services를 관리할 수 있습니다. Azure Automation에서는 이러한 클라우드 서비스 PowerShell cmdlet을 기본적으로 사용할 수 있으므로 서비스 내에서 클라우드 서비스 관리 작업을 모두 수행할 수 있습니다. Azure Automation에서 이러한 cmdlet을 다른 Azure 서비스용 cmdlet과 연결하여 Azure 서비스와 타사 시스템 간의 복잡한 작업을 자동화할 수도 있습니다.

Azure Cloud Services를 관리하는 Azure Automation을 사용하는 몇 가지 예제는 다음과 같습니다.

* [Azure Blob Storage에서 cscfg 또는 cspkg가 업데이트될 때마다 클라우드 서비스의 연속 배포](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [클라우드 서비스 인스턴스를 병렬로 다시 부팅할 때 한 번에 하나의 도메인 업그레이드](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>다음 단계
Azure Automation의 기본 사항과 Azure Automation을 사용하여 클라우드 서비스를 관리하는 방법을 알아보았으므로 이제 다음 링크에 따라 Azure Automation에 대해 자세히 알아보세요.

* [Azure Automation 개요](../automation/automation-intro.md)
* [내 첫 번째 runbook](../automation/automation-first-runbook-graphical.md)
* [Azure Automation 학습 맵](https://azure.microsoft.com/documentation/learning-paths/automation/)
