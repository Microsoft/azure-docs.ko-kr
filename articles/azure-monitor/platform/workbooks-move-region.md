---
title: Azure Monitor 통합 문서-영역 이동
description: 통합 문서를 다른 지역으로 이동 하는 방법
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d3b8bfbef544e754f684421daa847f1724435d53
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875818"
---
# <a name="move-an-azure-workbook-to-another-region"></a>다른 지역으로 Azure 통합 문서 이동

이 문서에서는 Azure 통합 문서 리소스를 다른 Azure 지역으로 이동 하는 방법을 설명 합니다. 여러 가지 이유로 리소스를 다른 지역으로 이동할 수 있습니다. 예를 들어, 새 Azure 지역을 활용 하 여 특정 지역 에서만 사용할 수 있는 기능 또는 서비스를 배포 하거나, 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나, 용량 계획 요구 사항에 대 한 응답으로 배포할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

* 대상 지역에서 통합 문서가 지원 되는지 확인 합니다.

* 이러한 지침은 `microsoft.insights/workbooks` Azure Monitor에 저장 된 공유 통합 문서 () 및 개인 통합 문서 ( `microsoft.insights/myworkbooks` )와 대부분의 리소스 유형에 적용 됩니다.

  그러나 Application Insights 리소스 유형에 특별히 연결 된 통합 문서에 대해서는 해당 통합 문서가 Application Insights 리소스가 저장 된 Azure 지역에 저장 됩니다.

  이러한 통합 문서는 개별적으로 다른 지역으로 이동할 수 없습니다.

## <a name="move"></a>이동

Azure 통합 문서를 이동 하는 가장 간단한 방법은 포털 UI의 통합 문서 도구에서 "다른 이름으로 저장"을 사용 하는 것입니다.

1. 통합 문서 뷰어에서 대상 통합 문서를 엽니다.
2. "편집" 도구 모음 단추를 사용 하 여 편집 모드를 시작 합니다.
3. "다른 이름으로 저장" 도구 모음 단추를 사용 합니다.
4. 저장 양식에서 통합 문서에 대 한 이름 및 원하는 지역을 선택 합니다. 구독, 리소스 그룹 및 공유에 대 한 다른 필드가 적절 한지 확인 합니다.

   > [!NOTE]
   > 중복 된 이름을 사용 하지 않으려면 통합 문서에 새 이름을 사용 해야 할 수도 있습니다.

5. 저장합니다. 

## <a name="verify"></a>확인

Azure 통합 문서 찾아보기 UI를 사용 하 여 새 통합 문서를 찾습니다. 위치가 대상 위치 인지 확인 합니다.

## <a name="clean-up"></a>정리

새 지역에서 통합 문서를 만든 후에는 이전 지역에서 원래 통합 문서를 삭제 합니다.
1. 통합 문서 뷰어에서 원래 통합 문서를 엽니다.
2. "편집" 도구 모음 단추를 사용 하 여 편집 모드를 시작 합니다.
3. 도구 편집 드롭다운 (연필 아이콘)에서 "통합 문서 삭제"를 선택 합니다.

새 지역으로 가져오도록 통합 문서 이름을 바꾼 경우 편집 모드 도구 모음의 편집 도구 드롭다운 "이름 바꾸기" 항목을 사용 하 여 원래 통합 문서를 삭제 한 후 통합 문서의 이름을 이전 이름으로 바꿀 수 있습니다.

## <a name="next-steps"></a>다음 단계

통합 문서 대신 통합 문서 템플릿을 이동 해야 하나요? [Azure 통합 문서 템플릿을 다른 지역으로 이동](./workbook-templates-move-region.md)하는 방법을 참조 하세요.

ARM 템플릿을 통해 [통합 문서 및 통합 문서 템플릿을 배포](./workbooks-automate.md) 하는 방법을 참조 하세요.
