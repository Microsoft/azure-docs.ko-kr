---
title: Azure 모범 사례의 관리 솔루션 | Microsoft Docs
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: d6d2414935bb5d1f095ad2b200acafa97b3b9b32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596648"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Azure에서 관리 솔루션을 만들기 위한 모범 사례(미리 보기)
> [!NOTE]
> 현재 미리 보기 상태로 제공되는 Azure에서 관리 솔루션을 만드는 예비 설명서입니다. 아래 설명된 스키마는 변경될 수 있습니다.  

이 문서에서는 Azure에서 [관리 솔루션 파일을 만드는](solutions-solution-file.md) 모범 사례를 제공합니다.  이 정보는 추가 모범 사례가 있으면 업데이트됩니다.

## <a name="data-sources"></a>데이터 원본
- 데이터 원본은 [Resource Manager 템플릿을 사용하여 구성](../../azure-monitor/platform/template-workspace-configuration.md)할 수 있으나 솔루션 파일에 포함되지 않아야 합니다.  그 이유는 데이터 원본을 구성하는 것이 현재 idempotent가 아니어서 솔루션이 사용자의 작업 영역에서 기존 구성을 덮어쓸 수 있기 때문입니다.<br><br>예를 들어 솔루션에 애플리케이션 이벤트 로그의 경고 및 오류 이벤트가 필요할 수 있습니다.  이것을 솔루션에서 데이터 원본으로 지정하면 사용자가 해당 작업 영역에서 이 데이터 원본을 구성한 경우 정보 이벤트가 제거될 위험이 있습니다.  모든 이벤트를 포함하는 경우 사용자의 작업 영역에서 과도한 정보 이벤트를 수집하게 될 수 있습니다.

- 솔루션에 표준 데이터 원본 중 하나의 데이터가 필요한 경우 다음 필수 구성 요소로 정의해야 합니다.  설명서에 따르면 고객은 자체적으로 데이터 원본을 구성해야 합니다.  
- 솔루션의 모든 보기에 [데이터 흐름 확인](../../azure-monitor/platform/view-designer-tiles.md) 메시지를 추가하여 필요한 데이터를 수집하기 위해 구성해야 하는 데이터 원본을 사용자에게 지시합니다.  이 메시지는 필요한 데이터를 찾을 수 없을 경우 보기의 타일에 표시됩니다.


## <a name="runbooks"></a>Runbook
- 일정에 따라 실행해야 하는 솔루션의 각 Runbook에 대해 [Automation 일정](../../automation/automation-schedules.md)을 추가합니다.
- Log Analytics 리포지토리에 데이터를 쓰는 Runbook이 사용할 솔루션에 [IngestionAPI 모듈](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5)을 포함합니다.  솔루션이 제거되더라도 유지되도록 해당 리소스를 [참조](solutions-solution-file.md#solution-resource)하도록 솔루션을 구성합니다.  이렇게 하면 여러 솔루션이 모듈을 공유할 수 있습니다.
- [Automation 변수](../../automation/automation-schedules.md)를 사용하여 나중에 변경하려고 할 수 있는 값을 솔루션에 추가합니다.  솔루션이 변수를 포함하도록 구성되더라도 해당 값을 여전히 변경할 수 있습니다.

## <a name="views"></a>뷰
- 모든 솔루션은 사용자 포털에 표시되는 단일 보기를 포함해야 합니다.  보기에는 다른 데이터 집합을 나타내는 여러 [시각화 요소](../../azure-monitor/platform/view-designer-parts.md)가 포함될 수 있습니다.
- 솔루션의 모든 보기에 [데이터 흐름 확인](../../azure-monitor/platform/view-designer-tiles.md) 메시지를 추가하여 필요한 데이터를 수집하기 위해 구성해야 하는 데이터 원본을 사용자에게 지시합니다.
- 솔루션이 제거되면 제거될 수 있게 보기를 [포함](solutions-solution-file.md#solution-resource)하도록 솔루션을 구성합니다.

## <a name="alerts"></a>경고
- 사용자가 솔루션을 설치할 때 받는 사람을 정의할 수 있도록 솔루션 파일에 매개 변수로 받는 사람 목록을 정의합니다.
- 사용자의 구성을 변경할 수 있게 경고 규칙을 [참조](solutions-solution-file.md#solution-resource)하도록 솔루션을 구성합니다.  받는 사람 목록 수정, 경고의 임계값 변경 또는 경고 규칙을 사용하지 않도록 설정 등의 변경을 수행할 수도 있습니다. 


## <a name="next-steps"></a>다음 단계
* [관리 솔루션 디자인 및 구축](solutions-creating.md) 기본 프로세스를 안내합니다.
* [솔루션 파일을 만드는 방법](solutions-solution-file.md)을 알아보세요.
* 관리 솔루션에 [저장된 검색 및 경고를 추가](solutions-resources-searches-alerts.md)합니다.
* 관리 솔루션에 대한 [보기를 추가](solutions-resources-views.md)합니다.
* 관리 솔루션에 [Automation runbook 및 기타 리소스를 추가](solutions-resources-automation.md)합니다.

