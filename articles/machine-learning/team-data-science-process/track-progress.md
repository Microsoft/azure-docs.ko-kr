---
title: Team Data Science Process 프로젝트의 진행률 추적
description: 데이터 과학 그룹 관리자, 팀 리더, 프로젝트 리더가 데이터 과학 프로젝트의 진행률을 추적할 수 있는 방법입니다.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 78a543fabadcc0d4e1766af1bc5c65aac0dadebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91358928"
---
# <a name="track-the-progress-of-data-science-projects"></a>데이터 과학 프로젝트의 진행률 추적

데이터 과학 그룹 관리자, 팀 리더, 프로젝트 리더는 해당 프로젝트의 진행률을 추적할 수 있습니다.  관리자는 완료된 작업, 작업을 수행한 사람, 남은 작업을 알고 싶어합니다.   기대치 관리는 성공의 중요한 요소입니다.

## <a name="azure-devops-dashboards"></a>Azure DevOps 대시보드

Azure DevOps를 사용하는 경우 대시보드를 빌드하여 지정된 Agile 프로젝트와 연결된 작업 및 작업 항목을 추적할 수 있습니다. 대시보드에 대한 자세한 내용은 [대시보드, 보고서, 위젯](/azure/devops/report/dashboards/)을 참조하세요.

Azure DevOps에서 대시보드 및 위젯을 만들고 사용자 지정하는 방법에 대한 지침은 다음 빠른 시작을 참조하세요.

- [대시보드 추가 및 관리](/azure/devops/report/dashboards/dashboards)
- [대시보드에 위젯 추가](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>예제 대시보드

다음은 연결된 리포지토리에 대한 커밋 수를 포함하여 Agile 데이터 과학 프로젝트의 스프린트 작업을 추적하는 간단한 예제 대시보드입니다. 

- **카운트다운** 타일에는 현재 스프린트에서 남은 일수가 표시됩니다. 

- 두 개의 **코드 타일** 에는 지난 7일 동안 두 프로젝트 리포지토리에서 수행된 커밋 수가 표시됩니다. 

- **TDSP 고객 프로젝트에 대한 작업 항목** 에는 모든 작업 항목 및 해당 상태에 대한 쿼리 결과가 표시됩니다. 

- **CFD(누적 흐름 다이어그램)** 에는 닫힌 작업 항목과 활성 작업 항목의 개수가 표시됩니다.

- **번다운(Burndown) 차트** 에는 스프린트에서 남은 시간 대비 완료해야 하는 작업이 표시됩니다.

- **번업(Burnup) 차트** 에는 스프린트의 총 작업량 대비 완료된 작업이 표시됩니다.

![Azure DevOps 대시보드 예제 스크린샷](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>다음 단계

[Team Data Science Process 실행 연습](walkthroughs.md)에는 모든 프로세스 단계를 보여 주는 연습이 나와 있습니다. 연결된 시나리오는 지능형 애플리케이션에 제공되는 클라우드 및 온-프레미스 리소스를 관리하는 방법을 보여 줍니다. 
