---
title: Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 97ba976c841e192646ebb484fe4d004556e34c54
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640840"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)
DCR (데이터 수집 규칙)은 Azure Monitor에 들어오는 데이터를 정의 하 고이를 보내야 하는 위치를 지정 합니다. 이 문서에서는 Azure Monitor 에이전트를 사용 하 여 가상 머신에서 데이터를 수집 하는 데이터 수집 규칙을 만드는 방법을 설명 합니다.

데이터 수집 규칙에 대 한 자세한 설명은 [Azure Monitor의 데이터 수집 규칙 (미리 보기)](data-collection-rule-overview.md)을 참조 하세요.

> [!NOTE]
> 이 문서에서는 현재 미리 보기 상태인 Azure Monitor 에이전트를 사용 하 여 가상 컴퓨터의 데이터를 구성 하는 방법을 설명 합니다. 일반 공급 되는 에이전트에 대 한 설명과이 에이전트를 사용 하 여 데이터를 수집 하는 방법에 대 한 [Azure Monitor 에이전트 개요](agents-overview.md) 를 참조 하세요.


## <a name="dcr-associations"></a>DCR 연결
가상 컴퓨터에 DCR를 적용 하려면 가상 컴퓨터에 대 한 연결을 만듭니다. 가상 컴퓨터는 여러 개의 가상 컴퓨터에 연결 되어 있을 수 있으며, DCR에 연결 된 가상 컴퓨터가 여러 개 있을 수 있습니다. 이를 통해 특정 요구 사항과 일치 하는 일련의 단일 항목을 정의 하 고 적용 되는 가상 컴퓨터에만 적용할 수 있습니다. 

예를 들어 lob (기간 업무) 응용 프로그램을 실행 하는 가상 머신 집합과 SQL Server를 실행 하는 다른 환경에서 환경을 가정 합니다. 모든 가상 컴퓨터에 적용 되는 하나의 기본 데이터 수집 규칙 및 lob (기간 업무) 응용 프로그램 및 SQL Server에 대 한 데이터를 수집 하는 별도의 데이터 수집 규칙이 있을 수 있습니다. 데이터 수집 규칙에 대 한 가상 컴퓨터 연결은 다음 다이어그램과 유사 하 게 표시 됩니다.

![연결](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>Azure Portal을 사용하여 만들기
Azure Portal를 사용 하 여 데이터 수집 규칙을 만들고 구독의 가상 컴퓨터를 해당 규칙에 연결할 수 있습니다. Azure Monitor 에이전트가 자동으로 설치 되 고 아직 설치 되지 않은 가상 컴퓨터에 대해 관리 id가 만들어집니다.

Azure Portal **Azure Monitor** 메뉴의 **설정** 섹션에서 **데이터 수집 규칙** 을 선택 합니다. **추가** 를 클릭 하 여 새 데이터 수집 규칙 및 할당을 추가 합니다.

[![데이터 수집 규칙](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

**추가** 를 클릭 하 여 새 규칙 및 연결 집합을 만듭니다. **규칙 이름을** 입력 하 고 **구독** 및 **리소스 그룹**을 지정 합니다. DCR를 만들 위치를 지정 합니다. 가상 컴퓨터와 해당 연결은 테 넌 트의 모든 구독 또는 리소스 그룹에 있을 수 있습니다.

[![데이터 수집 규칙 기본 사항](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

**가상 컴퓨터** 탭에서 데이터 수집 규칙이 적용 되어야 하는 가상 컴퓨터를 추가 합니다. Azure Monitor 에이전트는 아직 설치 하지 않은 가상 컴퓨터에 설치 됩니다.

[![데이터 수집 규칙 가상 컴퓨터](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

**수집 및 배달** 탭에서 **데이터 원본 추가** 를 클릭 하 여 데이터 원본 및 대상 집합을 추가 합니다. **데이터 원본 유형을**선택 하면 선택할 항목에 해당 하는 세부 정보가 표시 됩니다. 성능 카운터의 경우 미리 정의 된 개체 집합과 샘플링 주기 중에서 선택할 수 있습니다. 이벤트의 경우 로그 나 기능 집합 및 심각도 수준을 선택할 수 있습니다. 

[![데이터 원본 기본](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


다른 로그 및 성능 카운터를 지정 하려면 **사용자 지정**을 선택 합니다. 그런 다음 수집할 특정 값에 대 한 [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) 를 지정할 수 있습니다. 예제는 [샘플 DCR](data-collection-rule-overview.md#sample-data-collection-rule) 을 참조 하세요.

[![데이터 원본 사용자 지정](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

**대상** 탭에서 데이터 원본에 대 한 대상을 하나 이상 추가 합니다. Windows 이벤트 및 Syslog 데이터 원본은 Azure Monitor 로그에만 보낼 수 있습니다. 성능 카운터는 Azure Monitor 메트릭과 Azure Monitor 로그에 모두 보낼 수 있습니다.

[![대상](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

**데이터 원본 추가** 를 클릭 한 다음 **+ 만들기를 검토** 하 여 데이터 수집 규칙 및 vm 집합과의 연결에 대 한 세부 정보를 검토 합니다. 만들기 **를 클릭** 하 여 만듭니다.

> [!NOTE]
> 데이터 수집 규칙 및 연결이 생성 되 면 대상에 데이터를 전송 하는 데 최대 5 분이 걸릴 수 있습니다.

## <a name="createusingrestapi"></a>REST API를 사용하여 만들기
REST API를 사용 하 여 DCR 및 연결을 만들려면 다음 단계를 수행 합니다. 
1. [SAMPLE DCR](data-collection-rule-overview.md#sample-data-collection-rule)에 표시 된 JSON 형식을 사용 하 여 DCR 파일을 수동으로 만듭니다.
2. [REST API](https://docs.microsoft.com/rest/api/monitor/datacollectionrules/create#examples)를 사용 하 여 규칙을 만듭니다.
3. [REST API](https://docs.microsoft.com/rest/api/monitor/datacollectionruleassociations/create#examples)를 사용 하 여 데이터 수집 규칙에 대 한 각 가상 머신에 대 한 연결을 만듭니다.

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 에이전트](azure-monitor-agent-overview.md)에 대해 자세히 알아보세요.
- [데이터 수집 규칙](data-collection-rule-overview.md)에 대해 자세히 알아보세요.
