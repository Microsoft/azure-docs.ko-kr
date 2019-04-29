---
title: Azure에서 관리 솔루션 빌드 | Microsoft Docs
description: 관리 솔루션에는 고객이 Log Analytics 작업 영역에 추가할 수 있는 Azure의 패키지된 관리 시나리오가 포함되어 있습니다.  이 문서에서는 자체 환경에 사용할 관리 솔루션 또는 고객에게 제공할 관리 솔루션을 만드는 방법에 대해 자세히 설명합니다.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef1af4d3d27bc098341a4de716e293557baa946a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595821"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Azure에서 관리 솔루션 디자인 및 빌드(Preview)
> [!NOTE]
> 현재 Preview로 제공되는 Azure의 사용자 지정 솔루션 만들기에 대한 예비 설명서입니다. 아래 설명된 스키마는 변경될 수 있습니다.

[관리 솔루션]( solutions.md)은 고객이 Log Analytics 작업 영역에 추가할 수 있는 패키지된 관리 시나리오를 제공합니다.  이 문서에서는 가장 일반적인 요구 사항에 적합한 관리 솔루션을 설계하고 만들기 위한 기본 프로세스를 제공합니다.  관리 솔루션을 처음 만드는 경우 이 프로세스를 시작 지점으로 사용한 다음, 요구 사항이 진화함에 따라 더 복잡한 솔루션에 대한 개념을 활용할 수 있습니다.

## <a name="what-is-a-management-solution"></a>관리 솔루션이란?

관리 솔루션에는 특정 관리 시나리오를 달성하기 위해 함께 작동하는 Azure 리소스가 포함되어 있습니다.  이 솔루션은 설치할 때 포함된 리소스를 설치하고 구성하는 방법에 대한 세부 정보가 포함된 [리소스 관리 템플릿](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)으로 구현됩니다.

기본 전략은 Azure 환경에서 개별 구성 요소를 작성하여 관리 솔루션을 시작하는 것입니다.  기능이 제대로 작동하면 [관리 솔루션 파일]( solutions-solution-file.md)에 패키징할 수 있습니다. 


## <a name="design-your-solution"></a>솔루션 디자인
관리 솔루션에 대한 가장 일반적인 패턴은 다음 다이어그램에서 보여 줍니다.  이 패턴의 다른 구성 요소에 대해서는 아래에서 설명합니다.

![관리 솔루션 개요](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>데이터 원본
솔루션 설계의 첫 번째 단계는 Log Analytics 리포지토리에서 필요한 데이터를 결정하는 것입니다.  이 데이터는 [데이터 원본](../../azure-monitor/platform/agent-data-sources.md) 또는 [다른 솔루션]( solutions.md)에서 수집할 수 있거나, 솔루션에서 수집할 수 있는 프로세스를 제공해야 할 수도 있습니다.

[Log Analytics의 데이터 원본](../../azure-monitor/platform/agent-data-sources.md)에서 설명한 대로 데이터 원본은 Log Analytics 리포지토리에서 다양한 방법으로 수집할 수 있습니다.  여기에는 Windows 및 Linux 클라이언트의 성능 카운터 외에도 Windows 이벤트 로그의 이벤트 또는 Syslog로 생성된 이벤트가 포함됩니다.  또한 Azure Monitor에서 수집한 Azure 리소스에서도 데이터를 수집할 수 있습니다.  

사용 가능한 데이터 원본 중 하나를 통해 액세스할 수 없는 데이터가 필요한 경우, REST API를 호출할 수 있는 모든 클라이언트에서 Log Analytics 리포지토리에 데이터를 쓸 수 있는 [HTTP 데이터 수집기 API](../../azure-monitor/platform/data-collector-api.md)를 사용할 수 있습니다 .  관리 솔루션의 가장 일반적인 사용자 지정 데이터 수집 방법은 Azure 또는 외부 리소스에서 필요한 데이터를 수집하고 데이터 수집기 API를 사용하여 리포지토리에 쓰는 [Azure Automation Runbook](../../automation/automation-runbook-types.md)을 만드는 것입니다.  

### <a name="log-searches"></a>로그 검색
[로그 검색](../../azure-monitor/log-query/log-query-overview.md)은 Log Analytics 리포지토리에서 데이터를 추출하고 분석하는 데 사용됩니다.  사용자가 리포지토리에서 데이터의 임시 분석을 수행할 수 있는 것 외에도 보기 및 경고에서 사용합니다.  

모든 보기 또는 경고에서 사용하지 않더라도 사용자에게 도움이 되는 것으로 판단되는 쿼리를 정의해야 합니다.  이러한 쿼리는 포털에서 저장된 검색으로 사용할 수 있으며, 사용자 지정 보기의 [쿼리 목록 시각화 요소](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part)에도 포함할 수 있습니다.

### <a name="alerts"></a>경고
[Log Analytics의 경고](../../azure-monitor/platform/alerts-overview.md)는 리포지토리의 데이터에 대한 [로그 검색](#log-searches)을 통해 로그를 통해 문제를 식별합니다.  사용자에게 알리거나 응답에서 작업을 자동으로 실행합니다. 애플리케이션에 대한 다양한 경고 조건을 식별하고, 해당 경고 규칙을 솔루션 파일에 포함해야 합니다.

잠재적으로 자동화된 프로세스로 문제를 해결할 수 있는 경우 일반적으로 Azure Automation에서 Runbook을 만들어 이 수정 작업을 수행합니다.  대부분의 Azure 서비스는 Runbook에서 이러한 기능을 수행하는 데 활용하는 [cmdlet](/powershell/azure/overview)으로 관리될 수 있습니다.

경고에 대한 응답으로 외부 기능이 솔루션에 필요한 경우 [웹후크 응답](../../azure-monitor/platform/alerts-metric.md)을 사용할 수 있습니다.  이렇게 하면 경고에서 정보를 보내는 외부 웹 서비스를 호출할 수 있습니다.

### <a name="views"></a>뷰
Log Analytics의 보기는 Log Analytics 리포지토리의 데이터를 시각화하는 데 사용됩니다.  각 솔루션에는 일반적으로 사용자의 주 대시보드에 표시되는 [타일](../../azure-monitor/platform/view-designer-tiles.md)이 있는 단일 보기가 포함됩니다.  보기에는 사용자에게 수집된 데이터의 다양한 시각화를 제공하기 위해 많은 [시각화 요소](../../azure-monitor/platform/view-designer-parts.md)가 포함될 수 있습니다.

[뷰 디자이너를 사용하여 사용자 지정 보기를 만들면](../../azure-monitor/platform/view-designer.md) 나중에 솔루션 파일에 포함하여 내보낼 수 있습니다.  


## <a name="create-solution-file"></a>솔루션 파일 만들기
솔루션에 포함될 구성 요소를 구성하고 테스트하면 [솔루션 파일을 만들 수 있습니다]( solutions-solution-file.md).  파일의 다른 리소스와 관계가 있는 [솔루션 리소스]( solutions-solution-file.md#solution-resource)를 포함하는 [Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md)에 솔루션 구성 요소를 구현합니다.  


## <a name="test-your-solution"></a>솔루션 테스트
솔루션을 개발하는 동안 작업 영역에서 솔루션을 설치하고 테스트해야 합니다.  [Resource Manager 템플릿을 테스트하고 설치](../../azure-resource-manager/resource-group-template-deploy.md)하는 데 사용할 수 있는 방법 중 하나를 사용하여 이 작업을 수행할 수 있습니다.

## <a name="publish-your-solution"></a>솔루션 게시
솔루션을 완료하고 테스트한 후에는 다음 원본을 통해 고객이 솔루션을 사용할 수 있도록 할 수 있습니다.

- **Azure 퀵 스타트 템플릿** -  [Azure 퀵 스타트 템플릿](https://azure.microsoft.com/resources/templates/)은 GitHub를 통해 커뮤니티에서 제공한 Resource Manager 템플릿 집합입니다.  [참여 가이드](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)(영문)의 정보에 따라 솔루션을 사용할 수 있게 만들 수 있습니다.
- **Azure Marketplace**.  [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)를 사용하면 다른 개발자, ISV 및 IT 전문가에게 솔루션을 배포하고 판매할 수 있습니다.  [Azure Marketplace에 제품을 게시하고 관리하는 방법](../../marketplace/marketplace-publishers-guide.md)에서 솔루션을 Azure Marketplace에 게시하는 방법에 대해 자세히 알아볼 수 있습니다.



## <a name="next-steps"></a>다음 단계
* 관리 솔루션의 [솔루션 파일을 만드는 방법]( solutions-solution-file.md)에 대해 알아봅니다.
* [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)에 대해 자세히 알아봅니다.
* [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates)에서 다양한 Resource Manager 템플릿 샘플을 검색합니다.
