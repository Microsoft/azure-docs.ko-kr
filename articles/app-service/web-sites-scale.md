---
title: 기능 및 용량 강화 - Azure App Service | Microsoft Docs
description: Azure App Service에서 앱을 강화하여 용량 및 기능을 추가하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832157"
---
# <a name="scale-up-an-app-in-azure"></a>Azure에서 앱 강화

> [!NOTE]
> 새 **PremiumV2** 계층은 CPU, SSD 저장소 속도를 개선하며 기존 가격 계층의 메모리 대 코어 비율을 두 배로 높입니다. 성능상의 이점 덕분에 더 적은 인스턴스에서 앱을 실행하여 비용을 절감할 수 있습니다. **PremiumV2** 계층으로 확장하려면 [App Service에 대한 PremiumV2 계층 구성](app-service-configure-premium-tier.md)을 참조하세요.
>

이 문서에서는 Azure App Service에서 앱의 크기를 조정하는 방법에 대해 설명합니다. 크기를 조정하는 두 개의 워크플로(강화, 규모 확장)가 있으며 이 문서에서는 강화 워크플로를 다룹니다.

* [강화](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 더 많은 CPU, 메모리, 디스크 공간 및 추가 기능(전용 VM(가상 머신), 사용자 지정 도메인 및 인증서, 스테이징 슬롯, 자동 크기 조정 등)을 사용할 수 있습니다. 앱이 속한 App Service 계획의 가격 책정 계층을 변경하여 강화합니다.
* [규모 확장](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): 앱을 실행하는 VM 인스턴스 수가 증가합니다.
  가격 책정 계층에 따라 최대 20개의 인스턴스로 확장할 수 있습니다. [고립](environment/intro.md) 계층에서 **App Service 환경**은 규모 확장 수를 100개 인스턴스까지 증대합니다. 규모 확장에 대한 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md)을 참조하세요. 자동 크기 조정을 사용하는 방법을 찾아볼 수 있으며 이는 미리 정의된 규칙 및 일정에 따라 자동으로 인스턴스 개수를 조정합니다.

크기 조정 설정을 적용하는 데 몇 초밖에 걸리지 않으며 [App Service 계획](../app-service/overview-hosting-plans.md)의 모든 앱에 영향을 줍니다.
코드를 변경하거나 애플리케이션을 다시 배포할 필요가 없습니다.

개별 App Service 계획의 가격 책정 및 기능에 대한 자세한 내용은 [App Service 가격 정보](https://azure.microsoft.com/pricing/details/web-sites/)를 참조하세요.  

> [!NOTE]
> **무료** 계층에서 App Service 계획을 전환하기 전에 먼저 Azure 구독에 대한 [지출 한도](https://azure.microsoft.com/pricing/spending-limits/) 를 제거해야 합니다. Microsoft Azure App Service 구독 옵션을 보거나 변경하려면 [Microsoft Azure 구독][azuresubscriptions]을 참조하세요.
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>가격 책정 계층 강화
1. 브라우저에서 [Azure Portal][portal]을 엽니다.
2. App Service 앱 페이지에서 **모든 설정**을 클릭한 다음 **강화**를 클릭합니다.
   
    ![Azure 앱을 강화하기 위해 이동합니다.][ChooseWHP]
3. 사용자 계층을 선택한 다음, **적용**을 클릭합니다.
   
    작업이 완료되면 **알림** 탭에 **성공**이 녹색으로 깜박입니다.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>규모 관련 리소스
앱이 Azure SQL Database, Azure Storage 등의 다른 서비스에 종속된 경우 요구에 따라 해당 리소스를 개별적으로 강화할 수도 있습니다. 이러한 리소스는 App Service 계획에서 관리되지 않습니다.

1. **필수 항목**에서 **리소스 그룹** 링크를 클릭합니다.
   
    ![Azure 앱의 관련 리소스를 강화합니다.](./media/web-sites-scale/RGEssentialsLink.png)
2. **리소스 그룹** 페이지의 **요약** 부분에서 크기를 조정하려는 리소스를 클릭합니다. 다음 스크린샷은 SQL Database 리소스 및 Azure Storage 리소스를 보여 줍니다.
   
    ![리소스 그룹 페이지로 이동하여 Azure 앱을 강화합니다.](./media/web-sites-scale/ResourceGroup.png)
3. SQL Database 리소스의 경우 **설정** > **가격 책정 계층**을 클릭하여 가격 책정 계층을 조정합니다.
   
    ![Azure 앱에 대한 SQL Database 백 엔드를 강화합니다.](./media/web-sites-scale/ScaleDatabase.png)
   
    또한 SQL Database 인스턴스에 [지역에서 복제](../sql-database/sql-database-geo-replication-overview.md) 를 설정합니다.
   
    Azure Storage 리소스의 경우 **설정** > **구성**을 클릭하여 스토리지 옵션을 강화합니다.
   
    ![Azure 앱에서 사용하는 Azure Storage 계정을 강화합니다](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>가격 책정 계층 비교
각 가격 책정 계층의 VM 크기 등, 상세 정보는 [App Service 가격 책정 정보](https://azure.microsoft.com/pricing/details/web-sites/)를 참조하세요.
각 계층의 서비스 한도, 할당량, 제약 조건과 지원 기능은 [App Service 제한](../azure-subscription-service-limits.md#app-service-limits)을 참조하세요.

<a name="Next Steps"></a>

## <a name="next-steps"></a>다음 단계
* 가격 책정, 지원 및 SLA에 대한 자세한 내용은 다음 링크를 방문하세요.
  
    [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Microsoft Azure 지원 계획](https://azure.microsoft.com/support/plans/)
  
    [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)
  
    [SQL Database 가격 정보](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Microsoft Azure를 위한 Virtual Machine 및 클라우드 서비스 크기][vmsizes]
  
* 확장성 있고 복원력이 뛰어난 아키텍처 빌드를 포함하여 Azure App Service 모범 사례에 대한 자세한 내용은 [모범 사례: Azure App Service Web Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/)를 참조하세요.
* App Service 앱 크기 조정에 대한 비디오는 다음 리소스를 참조하세요.
  
  * [Azure Websites 크기를 조정하는 방법 - 스테판 스차코우(Stefan Schackow)](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Azure Websites, CPU 또는 예약 리소스 크기 자동 조정 - 스테판 스차코우(Stefan Schackow)](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Azure Websites 크기 조정 방법 - 스테판 스차코우(Stefan Schackow)](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
