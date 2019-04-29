---
title: App Service Environment에서 앱을 확장하는 방법 - Azure
description: App Service Environment에서 앱 확장
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6e683eb07b690d7d5680b7a4d429d1150f22f67e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767713"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>App Service Environment에서 앱 확장
Azure App Service에서는 일반적으로 다음 세 가지를 확장할 수 있습니다.

* 가격 계획
* 작업자 크기 
* 인스턴스 수

ASE에서는 가격 계획을 선택하거나 변경할 필요가 없습니다.  기능적인 측면에서 이미 프리미엄 가격 기능 수준이기 때문입니다.  

작업자 크기와 관련하여 ASE 관리자는 고정 크기 대신 각 작업자 풀에 사용할 계산 리소스의 크기를 할당할 수 있습니다.  따라서 필요한 경우 P4 계산 리소스가 있는 작업자 풀 1과 P1 계산 리소스가 있는 작업자 풀 2를 둘 수 있습니다.  크기 순이 아니어도 됩니다.  크기 및 해당 가격 책정에 대한 자세한 내용은 [Azure App Service 가격 책정][AppServicePricing]을 참조하세요.  따라서 App Service Environment에서는 웹앱 및 App Service 계획의 확장 옵션에 다음 항목만 남게 됩니다.

* 작업자 풀 선택
* 인스턴스 수

한 항목을 변경하는 작업은 ASE 호스트된 App Service 계획에 대해 표시되는 적절한 UI를 통해 수행됩니다.  

![][1]

ASP가 있는 작업자 풀의 사용 가능한 계산 리소스 수를 초과하여 ASP를 확장할 수 없습니다.  해당 작업자 풀에 계산 리소스가 필요한 경우 ASE 관리자에게 추가해 달라고 요청해야 합니다.  ASE를 다시 구성하는 방법에 대한 자세한 내용은 [App Service Environment를 구성하는 방법][HowtoConfigureASE]을 참조하세요.  일정 또는 메트릭에 따라 용량을 추가하기 위해 ASE 자동 크기 조정 기능을 활용할 수도 있습니다.  ASE 환경 자체에 대한 자동 크기 조정 구성에 대한 자세한 내용은 [App Service 환경에 대한 자동 크기 조정을 구성하는 방법][ASEAutoscale]을 참조하세요.

다양한 작업자 풀의 계산 리소스를 사용하여 여러 앱 서비스 계획을 만들거나, 동일한 작업자 풀을 사용할 수 있습니다.  예를 들어 작업자 풀 1에 사용 가능한 계산 리소스 10개가 있는 경우 6개의 계산 리소스를 사용하여 하나의 앱 서비스 계획을 만들고 나머지 4개의 계산 리소스를 사용하는 두 번째 앱 서비스를 만들 수 있습니다.

### <a name="scaling-the-number-of-instances"></a>인스턴스 수 확장
App Service Environment에서 웹앱을 처음 만드는 경우 1개의 인스턴스로 시작합니다.  그런 다음, 앱에 대한 추가 계산 리소스를 제공하기 위해 추가 인스턴스로 확장할 수 있습니다.   

ASE의 용량이 충분한 경우에는 매우 간단합니다.  확장할 사이트를 유지하는 App Service 계획으로 이동하여 크기 조정을 선택합니다.  그러면 수동으로 ASP 프로그램에 대한 확장을 설정하거나 ASP에 대한 자동 크기 조정 규칙을 구성하는 UI가 열립니다.  수동으로 앱을 확장하려면 ***확장 기준***을 ***수동으로 입력한 인스턴스 수***로 설정하기면 하면 됩니다.  여기에서 원하는 수량으로 슬라이더를 끌거나 슬라이더 옆에 있는 상자에 입력합니다.  

![][2] 

ASE의 ASP에 대한 자동 크기 조정 규칙은 일반적인 경우와 동일하게 작동합니다.  ***확장 기준***에 있는 ***CPU 비율***을 선택하여 CPU 비율에 따라 ASP에 대한 자동 크기 조정 규칙을 만들거나 ***일정 및 성능 규칙***을 사용하여 좀 더 복잡한 규칙을 만들 수 있습니다.  자동 크기 조정 구성에 관하여 전체 세부 정보를 자세히 보려면 [Azure App Service에서 앱 확장][AppScale] 가이드를 참조하세요. 

### <a name="worker-pool-selection"></a>작업자 풀 선택
앞에서 언급한 것처럼 작업자 풀 선택은 ASP UI에서 액세스됩니다.  확장하려는 ASP의 블레이드를 열고 작업자 풀을 선택합니다.  App Service Environment에서 구성한 모든 작업자 풀이 표시됩니다.  작업자 풀이 하나뿐인 경우에는 하나의 풀만 나열됩니다.  ASP가 있는 작업자 풀을 변경하려면 App Service 계획을 이동할 작업자 풀을 선택하기만 하면 됩니다.  

![][3]

한 작업자 풀에서 다른 작업자 풀로 ASP를 이동하기 전에 ASP에 대해 적절한 용량이 있는지 확인하는 것이 중요합니다.  작업자 풀 목록에는 작업자 풀 이름이 나열될 뿐만 아니라 해당 작업자 풀에서 사용 가능한 작업자 수도 표시됩니다.  App Service 계획을 포함할 수 있는 충분한 인스턴스가 있는지 확인합니다.  이동할 작업자 풀에 추가 계산 리소스가 필요한 경우 ASE 관리자에게 추가하도록 요청합니다.  

> [!NOTE]
> 한 작업자 풀에서 ASP를 이동하면 해당 ASP에서 앱이 콜드 부팅됩니다.  이렇게 하면 앱이 새 계산 리소스에서 시작할 때 요청이 느리게 실행될 수 있습니다.  Azure App Service에서 [애플리케이션 준비 기능][AppWarmup]을 사용하여 콜드 부팅을 방지할 수 있습니다.  앱이 새 계산 리소스에서 콜드 시작하는 경우에 초기화 프로세스가 호출되기 때문에 문서에서 설명하는 애플리케이션 초기화 모듈은 콜드 시작에서도 작동합니다. 
> 
> 

## <a name="getting-started"></a>시작
App Service 환경을 시작하려면 [App Service 환경을 만드는 방법][HowtoCreateASE]을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
