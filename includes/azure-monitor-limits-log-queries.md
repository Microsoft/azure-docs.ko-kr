---
title: 포함 파일
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796034"
---
| 제한 | 설명 |
|:---|:---|
| 쿼리 언어 | Azure Monitor는 Azure 데이터 탐색기와 동일한 [Kusto 쿼리 언어](/azure/kusto/query/) 를 사용 합니다. Azure Monitor에서 지원 되지 않는 KQL 언어 요소에 대한 [Azure Monitor 로그 쿼리 언어 차이점](../articles/azure-monitor/log-query/data-explorer-difference.md) 을 참조 하세요. |
| Azure 지역 | 로그가 여러 Azure 지역에서 Log Analytics 작업 영역에 걸쳐 있을 때 과도 한 오버 헤드가 발생할 수 있습니다. 자세한 내용은 [쿼리 제한](../articles/azure-monitor/log-query/scope.md#query-limits) 을 참조 하세요. |
| 리소스 간 쿼리 | 100으로 제한 된 단일 쿼리에서 Application Insights 리소스 및 Log Analytics 작업 영역의 최대 수입니다.<br>뷰 디자이너에서는 리소스 간 쿼리가 지원 되지 않습니다.<br>로그 경고의 리소스 간 쿼리는 새 scheduledQueryRules API에서 지원 됩니다.<br>자세한 내용은 [리소스 간 쿼리 제한](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) 을 참조 하세요. |
| 쿼리 제한 | 사용자는 여러 작업 영역에서 30 초 당 200 쿼리로 제한 됩니다. 이 제한은 Azure 대시보드 및 Log Analytics 작업 영역 요약 페이지와 같은 시각화 요소에 의해 시작 된 쿼리 또는 프로그래밍 쿼리에 적용 됩니다. |
