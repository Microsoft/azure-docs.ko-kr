---
title: Azure Monitor 로그 쿼리의 컴퓨터 그룹 | Microsoft Docs
description: Azure Monitor의 컴퓨터 그룹을 사용하여 로그 쿼리의 범위를 특정 컴퓨터 집합으로 지정할 수 있습니다.  이 문서에서는 컴퓨터 그룹을 만드는 데 사용할 수 있는 몇 가지 방법과 로그 쿼리에 사용하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: c2babb5a86d69881b6a76c6dceae80a24a891f6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740997"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리에서 컴퓨터 그룹
Azure Monitor의 컴퓨터 그룹을 사용하여 [로그 쿼리](../log-query/log-query-overview.md)의 범위를 특정 컴퓨터 집합으로 지정할 수 있습니다.  각 그룹에는 사용자가 정의를 사용하거나 여러 원본에서 그룹을 가져와 컴퓨터가 채워집니다.  로그 쿼리에 그룹을 포함하면 결과가 그룹의 컴퓨터와 일치하는 레코드로 제한됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>컴퓨터 그룹 만들기
Azure Monitor에서 다음 표의 방법 중 하나를 사용하여 컴퓨터 그룹을 만들 수 있습니다.  각 방법에 대한 자세한 내용은 아래 섹션에서 설명합니다. 

| 방법 | 설명 |
|:--- |:--- |
| 로그 쿼리 |컴퓨터 목록을 반환하는 로그 쿼리를 만듭니다. |
| 로그 검색 API |로그 검색 API를 사용하여 로그 쿼리 결과에 따라 프로그래밍 방식으로 컴퓨터 그룹을 만듭니다. |
| Active Directory |Active Directory 도메인의 구성원인 에이전트 컴퓨터의 그룹 구성원을 자동으로 검색하고 Azure Monitor에서 각 보안 그룹에 대한 그룹을 만듭니다. (Windows 머신에만 해당)|
| 구성 관리자 | System Center Configuration Manager에서 컬렉션을 가져오고 Azure Monitor에서 각 컬렉션에 대한 그룹을 만듭니다. |
| Windows Server 업데이트 서비스 |WSUS 서버나 클라이언트에서 대상 그룹을 자동으로 검색하고 Azure Monitor에서 각 대상 그룹에 대한 그룹을 만듭니다. |

### <a name="log-query"></a>로그 쿼리
로그 쿼리에서 생성된 컴퓨터 그룹에는 사용자가 정의한 쿼리에서 반환된 모든 컴퓨터가 포함됩니다.  이 쿼리는 컴퓨터 그룹이 사용될 때마다 실행되므로 그룹이 만들어진 이후의 모든 변경 내용이 반영됩니다.  

컴퓨터 그룹에 대해 어떤 쿼리라도 사용할 수 있지만, `distinct Computer`을 사용하여 별개의 컴퓨터 집합을 반환해야 합니다.  다음은 컴퓨터 그룹에 대해 사용할 수 있는 일반적인 예제 쿼리입니다.

    Heartbeat | where Computer contains "srv" | distinct Computer

다음 프로시저를 사용하여 Azure Portal의 로그 검색에서 컴퓨터 그룹을 만듭니다.

1. Azure Portal의 **Azure Monitor** 메뉴에서 **로그**를 클릭합니다.
1. 그룹에 포함하려는 컴퓨터를 반환하는 쿼리를 만들고 실행합니다.
1. 화면 위쪽에서 **저장**을 클릭합니다.
1. **다른 이름으로 저장**을 **함수**로 변경하고 **이 쿼리를 컴퓨터 그룹으로 저장**을 선택합니다.
1. 표에 설명된 컴퓨터 그룹의 각 속성에 대한 값을 제공하고 **저장**을 클릭합니다.

다음 표는 컴퓨터 그룹을 정의하는 속성을 설명합니다.

| 자산 | 설명 |
|:---|:---|
| 이름   | 포털에 표시할 쿼리의 이름입니다. |
| 함수 별칭 | 쿼리에서 컴퓨터 그룹을 식별하는 데 사용되는 고유한 별칭입니다. |
| Category       | 포털에서 쿼리를 구성할 범주입니다. |


### <a name="active-directory"></a>Active Directory
Active Directory 그룹 멤버 자격을 가져오도록 Azure Monitor를 구성하는 경우 Log Analytics 에이전트를 사용하여 Windows 도메인 가입 컴퓨터의 그룹 멤버 자격을 분석합니다.  컴퓨터 그룹은 Azure Monitor에서 Active Directory의 각 보안 그룹에 대해 만들어지며 각 Windows 컴퓨터는 구성원으로 속한 보안 그룹에 해당하는 컴퓨터 그룹에 추가됩니다.  이 멤버 자격은 4시간 간격으로 계속 업데이트됩니다.  

> [!NOTE]
> 가져온 Active Directory 그룹에는 Windows 머신만 포함됩니다.

Azure Portal의 Log Analytics 작업 영역에 있는 **고급 설정**에서 Active Directory 보안 그룹을 가져오도록 Azure Monitor를 구성합니다.  **컴퓨터 그룹**, **Active Directory**, 및 **컴퓨터에서 Active Directory 그룹 멤버 자격을 가져오기**를 차례로 선택합니다.  추가 구성은 필요 없습니다.

![Active Directory의 컴퓨터 그룹](media/computer-groups/configure-activedirectory.png)

그룹을 가져올 때는 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

### <a name="windows-server-update-service"></a>Windows Server 업데이트 서비스
WSUS 그룹 멤버 자격을 가져오도록 Azure Monitor를 구성하면 Log Analytics 에이전트를 사용하여 컴퓨터의 대상 그룹 멤버 자격을 분석합니다.  클라이언트 쪽 대상을 사용하는 경우 Azure Monitor에 연결되고 WSUS 대상 그룹에 속해 있는 모든 컴퓨터의 그룹 멤버 자격을 Azure Monitor로 가져옵니다. 서버 쪽 대상을 사용하는 경우 그룹 멤버 자격 정보를 Azure Monitor로 가져오려면 WSUS 서버에 Log Analytics 에이전트를 설치해야 합니다.  이 멤버 자격은 4시간 간격으로 계속 업데이트됩니다. 

Azure Portal의 Log Analytics 작업 영역에 있는 **고급 설정**에서 WSUS 그룹을 가져오도록 Azure Monitor를 구성합니다.  **컴퓨터 그룹**, **WSUS**, 및 **WSUS 그룹 멤버 자격을 가져오기**를 차례로 선택합니다.  추가 구성은 필요 없습니다.

![WSUS의 컴퓨터 그룹](media/computer-groups/configure-wsus.png)

그룹을 가져올 때는 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
구성 관리자 컬렉션 멤버 자격을 가져오도록 Azure Monitor를 구성하면 각 컬렉션에 대한 컴퓨터 그룹을 만듭니다.  컬렉션 멤버 자격 정보는 컴퓨터 그룹을 최신 상태로 유지하기 위해 3시간 마다 검색됩니다. 

구성 관리자 컬렉션을 가져오려면 먼저 [Configuration Manager를 Azure Monitor에 연결](collect-sccm.md)해야 합니다.  그런 다음, Azure Portal의 Log Analytics 작업 영역에 있는 **고급 설정**에서 가져오기를 구성할 수 있습니다.  **컴퓨터 그룹**, **SCCM**, 및 **구성 관리자 컬렉션 멤버 자격 가져오기**를 차례로 선택합니다.  추가 구성은 필요 없습니다.

![SCCM의 컴퓨터 그룹](media/computer-groups/configure-sccm.png)

컬렉션이 들여오기 될 때 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

## <a name="managing-computer-groups"></a>컴퓨터 그룹 관리
Azure Portal의 Log Analytics 작업 영역에 있는 **고급 설정**에서 로그 쿼리 또는 로그 검색 API를 통해 생성된 컴퓨터 그룹을 볼 수 있습니다.  **컴퓨터 그룹**을 선택한 후 **저장된 그룹**을 선택합니다.  

**제거** 열에서 **x**를 클릭하여 컴퓨터 그룹을 삭제합니다.  그룹에 대한 **멤버 보기** 아이콘을 클릭하여 멤버를 반환하는 그룹의 로그 검색을 실행합니다.  컴퓨터 그룹을 수정할 수는 없지만 대신 삭제한 다음 수정된 설정으로 다시 만들어야 합니다.

![저장된 컴퓨터 그룹](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>로그 쿼리에 컴퓨터 그룹 사용
일반적으로 다음 구문을 사용하여 해당 별칭을 함수로 취급함으로써 로그 쿼리에서 생성된 컴퓨터 그룹을 쿼리에 사용합니다.

  `Table | where Computer in (ComputerGroup)`

예를 들어 mycomputergroup이라는 컴퓨터 그룹에서 컴퓨터에 대해서만 UpdateSummary 레코드를 반환하기 위해 다음을 사용할 수 있습니다.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


가져온 컴퓨터 그룹 및 포함된 컴퓨터는 **ComputerGroup** 테이블에 저장됩니다.  예를 들어 다음 쿼리는 Active Directory의 도메인 컴퓨터 그룹에 포함된 컴퓨터 목록을 반환합니다. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

다음 쿼리는 도메인 컴퓨터의 컴퓨터에 대해서만 UpdateSummary 레코드를 반환합니다.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>컴퓨터 그룹 레코드
Active Directory 또는 WSUS에서 만든 각 컴퓨터 그룹 멤버 자격에 대한 레코드가 Log Analytics 작업 영역에 생성됩니다.  이 레코드의 형식은 **ComputerGroup**이며 다음 표의 속성을 갖습니다.  로그 쿼리 기반의 컴퓨터 그룹에 대한 레코드는 만들어지지 않습니다.

| 자산 | 설명 |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |멤버 컴퓨터의 이름입니다. |
| `Group` |그룹의 이름입니다. |
| `GroupFullName` |원본 및 원본 이름을 포함하는 그룹에 대한 전체 경로입니다. |
| `GroupSource` |그룹을 수집해 온 원본입니다. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |그룹을 수집해 온 원본의 이름입니다.  Active Directory의 경우 도메인 이름이 됩니다. |
| `ManagementGroupName` |SCOM 에이전트의 관리 그룹 이름입니다.  다른 에이전트의 경우 AOI-\<작업 영역 ID\>입니다. |
| `TimeGenerated` |컴퓨터 그룹이 만들어졌거나 업데이트된 날짜 및 시간입니다. |

## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다.  

