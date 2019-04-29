---
title: 경고 인스턴스 관리
description: Azure 전체에서 경고 인스턴스 관리
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: cb93f38c05156d7ab5acb89ffff810949583e507
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551875"
---
# <a name="manage-alert-instances"></a>경고 인스턴스 관리
Azure Monitor의 [통합 경고 환경](https://aka.ms/azure-alerts-overview)을 통해, 이제 Azure 전체에서 여러 구독에 대한 모든 유형의 경고를 한 곳에서 볼 수 있습니다. 이 문서에서는 경고 인스턴스를 보는 방법과 포털에서 문제 해결을 위해 특정 경고 인스턴스를 찾는 방법을 설명합니다.

1. 경고 페이지를 방문하는 세 가지 방법이 있습니다.

   + [포털](https://portal.azure.com/)에서 **모니터**를 선택하고 모니터 섹션 아래에서 **경고**를 선택합니다.  
     ![모니터링](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + 특정 **리소스**의 컨텍스트 내에서 경고로 이동할 수 있습니다. 리소스가 열리면 해당 목차를 탐색하여 모니터링 섹션으로 이동한 다음, 해당 특정 리소스의 경고에 대해 사전 필터링된 방문 페이지에서 **경고**를 선택합니다.
   
     ![모니터링](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + 특정 **리소스 그룹**의 컨텍스트 내에서 경고로 이동할 수 있습니다. 리소스 그룹이 열리면 해당 목차를 탐색하여 모니터링 섹션으로 이동한 다음, 해당 특정 리소스 그룹의 경고에 대해 사전 필터링된 방문 페이지에서 **경고**를 선택합니다.    
   
     ![모니터링](media/alerts-managing-alert-instances/alert-rg.JPG)

1. Azure 전체의 모든 경고 인스턴스에 대한 개요를 제공하는 **경고 요약** 페이지가 표시됩니다. **여러 구독**(최대 5개)을 선택하거나 **리소스 그룹**, 특정 **리소스** 또는 **시간 범위**에서 필터링하여 요약 보기를 수정할 수 있습니다. 총 경고 또는 심각도 범위 중 하나를 클릭하여 경고의 목록 보기로 이동합니다.     
   ![경고 요약](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. **모든 경고** 페이지가 표시되며, Azure 전체의 모든 경고 인스턴스가 나열됩니다. 경고 알림에서 포털로 이동하는 경우 필터를 사용하여 해당 특정 경고 인스턴스로 범위를 좁힐 수 있습니다. **참고**: 심각도 범위 중 하나를 클릭하여 페이지로 이동한 경우 방문 시 목록이 해당 심각도로 사전 필터링됩니다. 이전 페이지에서 사용할 수 있었던 필터와 별도로, 모니터 서비스(예: 메트릭용 플랫폼), 모니터 조건(발생함 또는 해결됨), 심각도, 경고 상태(새로 만들기/확인됨/닫힘) 또는 스마트 그룹 ID를 기준으로 필터링할 수도 있습니다.

   ![All Alerts](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  심각도 범위 중 하나를 클릭하여 페이지로 이동한 경우 이 페이지를 방문할 때 목록이 해당 심각도로 사전 필터링됩니다.
 
1. 경고 인스턴스를 클릭하면 **경고 세부 정보** 페이지가 열리며, 해당 특정 경고 인스턴스에 대한 정보를 살펴볼 수 있습니다.   
   ![경고 세부 정보](media/alerts-managing-alert-instances/alert-details.jpg)  

