---
title: Azure Monitor의 IIS 로그 | Microsoft Docs
description: IIS(인터넷 정보 서비스)는 Azure Monitor에서 수집할 수 있는 로그 파일에 사용자 활동을 저장합니다.  이 문서에서는 IIS 로그 수집을 구성하는 방법을 설명하고, Azure Monitor에 생성되는 레코드에 대한 자세한 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 402cd4723791c0bc33db22c8857d1b785862f596
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614495"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Azure Monitor에서 IIS 로그 수집
IIS(인터넷 정보 서비스)는 Azure Monitor에서 수집할 수 있고 [로그 데이터](data-platform.md)로 저장되는 로그 파일에 사용자 활동을 저장합니다.

![IIS 로그](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS 로그 구성
Azure Monitor는 IIS에서 생성된 로그 파일의 항목을 수집하므로 [로깅에 대해 IIS를 구성](https://technet.microsoft.com/library/hh831775.aspx)해야 합니다.

Azure Monitor는 W3C 형식으로 저장된 IIS 로그 파일만 지원하며 사용자 지정 필드 또는 IIS 고급 로깅을 지원하지 않습니다. NCSA 또는 IIS 네이티브 형식의 로그는 수집하지 않습니다.

[고급 설정 메뉴](agent-data-sources.md#configuring-data-sources)에서 Azure Monitor의 IIS 로그를 구성합니다.  **W3C 형식 IIS 로그 파일 수집**을 선택하는 것 외에 다른 구성은 필요 없습니다.


## <a name="data-collection"></a>데이터 수집
Azure Monitor는 로그가 닫히고 새 로그가 만들어질 때마다 각 에이전트에서 IIS 로그 항목을 수집합니다. 이 빈도는 기본적으로 하루에 한 번인 IIS 사이트에 대한 **로그 파일 롤오버 일정** 설정에 의해 컨트롤됩니다. 예를 들어 설정이 **시간별**인 경우 Azure Monitor는 매시간 로그를 수집합니다.  설정이 **일별**인 경우 Azure Monitor는 24시간마다 로그를 수집합니다.


## <a name="iis-log-record-properties"></a>IIS 로그 레코드 속성
IIS 로그 레코드는 **W3CIISLog** 형식이며, 다음 표의 속성이 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| Computer |이벤트가 수집된 컴퓨터의 이름입니다. |
| cIP |클라이언트의 IP 주소입니다. |
| csMethod |GET 또는 POST와 같은 요청 메서드입니다. |
| csReferer |사용자가 현재 사이트로 이동하는 데 사용된 링크가 있던 사이트입니다. |
| csUserAgent |클라이언트의 브라우저 유형입니다. |
| csUserName |서버에 액세스한 인증된 사용자의 이름입니다. 익명 사용자는 하이픈으로 표시됩니다. |
| csUriStem |웹 페이지와 같은 요청의 대상입니다. |
| csUriQuery |클라이언트가 수행하려고 한 쿼리입니다(있는 경우). |
| ManagementGroupName |Operations Manager 에이전트의 관리 그룹 이름.  다른 에이전트의 경우 AOI-\<작업 영역 ID\>입니다. |
| RemoteIPCountry |클라이언트의 IP 주소 국가입니다. |
| RemoteIPLatitude |클라이언트 IP 주소의 위도입니다. |
| RemoteIPLongitude |클라이언트 IP 주소의 경도입니다. |
| scStatus |HTTP 상태 코드입니다. |
| scSubStatus |하위 상태 오류 코드입니다. |
| scWin32Status |Windows 상태 코드입니다. |
| sIP |웹 서버의 IP 주소입니다. |
| SourceSystem |OpsMgr |
| sPort |클라이언트가 연결된 서버의 포트입니다. |
| sSiteName |IIS 사이트의 이름입니다. |
| TimeGenerated |항목이 로깅된 날짜 및 시간입니다. |
| TimeTaken |요청을 처리할 시간의 길이(밀리초)입니다. |

## <a name="log-queries-with-iis-logs"></a>IIS 로그를 사용한 로그 쿼리
다음 표에는 IIS 로그 레코드를 검색하는 로그 쿼리의 여러 예제가 나와 있습니다.

| 쿼리 | 설명 |
|:--- |:--- |
| W3CIISLog |모든 IIS 로그 레코드 |
| W3CIISLog &#124; where scStatus==500 |반환 상태가 500인 모든 IIS 로그 레코드입니다. |
| W3CIISLog &#124; summarize count() by cIP |클라이언트 IP 주소별 IIS 로그 항목 수 |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |Count의 별 IIS 로그 항목 URL 호스트 www\.contoso.com입니다. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |각 IIS 컴퓨터에서 받은 총 바이트 수 |

## <a name="next-steps"></a>다음 단계
* 분석을 위해 다른 [데이터 원본](agent-data-sources.md)을 수집하도록 Azure Monitor를 구성합니다.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다.