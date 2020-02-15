---
title: Azure Monitor 뷰 디자이너에서 통합 문서 변환 요약 및 액세스
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: e49b4c0220b6c00fe3a7a6e1cab219364a2a89b7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171242"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>뷰 디자이너에서 통합 문서 변환 요약 및 액세스
[뷰 디자이너](view-designer.md) 는 Azure Monitor 기능으로, 차트, 목록 및 타임 라인을 사용 하 여 Log Analytics 작업 영역에서 데이터를 시각화 하는 데 도움이 되는 사용자 지정 보기를 만들 수 있습니다. 이러한 기능은 추가 기능을 제공 하는 통합 문서와 함께 단계적으로 제공 됩니다. 이 문서에서는 통합 문서에 액세스 하는 데 필요한 개요 요약 및 사용 권한을 만드는 방법을 자세히 설명 합니다.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Azure 대시보드에서 작업 영역 요약 만들기
뷰 디자이너 사용자는 보기 집합을 나타내는 개요 타일을 사용 하는 데 익숙할 수 있습니다. 보기 디자이너 작업 영역 요약과 같은 시각적 개요를 유지 하기 위해 통합 문서는 [Azure Portal 대시보드에](../../azure-portal/azure-portal-dashboards.md)고정 될 수 있는 고정 된 단계를 제공 합니다. 작업 영역 요약의 개요 타일과 마찬가지로 고정 된 통합 문서 항목은 통합 문서 보기에 직접 연결 됩니다.

Azure 대시보드를 통해 제공 되는 높은 수준의 사용자 지정 기능을 활용 하 여 자동 새로 고침, 이동, 크기 조정 및 고정 된 항목 및 시각화에 대 한 추가 필터링을 수행할 수 있습니다. 

![대시보드](media/view-designer-conversion-access/dashboard.png)

새 Azure 대시보드를 만들거나 기존 대시보드를 선택 하 여 통합 문서 항목의 고정을 시작 합니다.

개별 항목을 고정 하려면 특정 단계에 대해 고정 아이콘을 사용 하도록 설정 해야 합니다. 이렇게 하려면 단계에 해당 하는 **편집** 단추를 선택 하 고 기어 아이콘을 선택 하 여 **고급 설정을**엽니다. **이 단계에서 항상 고정 아이콘을 표시**하는 옵션을 선택 하면 단계의 오른쪽 위에 고정 아이콘이 표시 됩니다. 이 pin을 사용 하면 개요 타일과 같이 특정 시각화를 대시보드에 고정할 수 있습니다.

![Pin 단계](media/view-designer-conversion-access/pin-step.png)


통합 문서 또는 전체 통합 문서 콘텐츠에서 여러 시각화를 대시보드에 고정할 수도 있습니다. 전체 통합 문서를 고정 하려면 위쪽 도구 모음에서 **편집** 을 선택 하 여 **편집 모드**를 전환 합니다. 통합 문서에서 전체 통합 문서 항목 또는 개별 단계와 시각화를 모두 고정할 수 있는 고정 아이콘이 표시 됩니다.

![모두 고정](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>권한 공유 및 보기 
통합 문서는 개인 또는 공유 문서 중 하나를 사용할 수 있습니다. 기본적으로 저장 된 통합 문서는 **내 보고서**에 저장 됩니다. 즉, 작성자만이 통합 문서를 볼 수 있습니다.

**편집 모드**에 있는 동안 상단 도구 모음에서 **공유** 아이콘을 선택 하 여 통합 문서를 공유할 수 있습니다. 통합 문서를 **공유 보고서**로 이동 하 라는 메시지가 표시 됩니다. 그러면 통합 문서에 대 한 직접 액세스를 제공 하는 링크가 생성 됩니다.

사용자가 공유 통합 문서를 보려면 통합 문서가 저장 된 구독 및 리소스 그룹에 모두 액세스할 수 있어야 합니다.

![구독 기반 액세스](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>다음 단계

- [일반 작업](view-designer-conversion-tasks.md)