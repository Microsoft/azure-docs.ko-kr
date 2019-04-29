---
title: 502 잘못된 게이트웨이, 503 서비스를 사용할 수 없음 오류 수정 - Azure App Service | Microsoft Docs
description: Azure App Service에 호스트된 앱의 502 잘못된 게이트웨이 및 503 서비스를 사용할 수 없음 오류를 해결합니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 잘못된 게이트웨이, 503 서비스를 사용할 수 없음, 오류 503, 오류 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5edd3e51e83b5ab324d1e110a1882b20d935a9b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60833070"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Azure App Service의 “502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음” HTTP 오류 해결
“502 잘못된 게이트웨이” 및 “503 서비스를 사용할 수 없음” 오류는 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에 호스트된 앱에서 일반적으로 나타나는 오류입니다. 이 문서는 이러한 오류를 해결하는 데 도움이 됩니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 이동한 다음 **지원 받기**를 클릭합니다.

## <a name="symptom"></a>증상
앱을 찾아볼 때 HTTP “502 잘못된 게이트웨이” 또는 HTTP “503 서비스를 사용할 수 없음” 오류를 반환합니다.

## <a name="cause"></a>원인
이 문제는 애플리케이션 수준 문제가 원인이 되는 경우가 많습니다. 예를 들어:

* 긴 요청 시간
* 높은 메모리/CPU를 사용하는 애플리케이션
* 예외로 인해 충돌하는 애플리케이션

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>"502 잘못된 게이트웨이" 및 "503 서비스를 사용할 수 없음" 오류를 해결하기 위한 문제 해결 단계
문제 해결은 해결하는 순서대로 세 가지로 구분할 수 있습니다.

1. [애플리케이션 작동을 관찰 및 감시](#observe)
2. [데이터 수집](#collect)
3. [문제 완화](#mitigate)

[App Service](overview.md)는 각 단계별로 다양한 옵션을 제공합니다.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. 애플리케이션 작동을 관찰 및 감시 
#### <a name="track-service-health"></a>서비스 상태를 추적합니다.
Microsoft Azure는 서비스가 중단되거나 성능이 저하될 때마다 경고를 표시합니다. [Azure Portal](https://portal.azure.com/)에서 서비스의 상태를 추적할 수 있습니다. 자세한 내용은 [서비스 상태 추적](../monitoring-and-diagnostics/insights-service-health.md)을 참조하세요.

#### <a name="monitor-your-app"></a>앱 모니터링
이 옵션은 애플리케이션의 문제를 해결할 수 있게 도와줍니다. 앱의 블레이드에서 **요청 및 오류** 타일을 클릭합니다. **메트릭** 블레이드는 추가 가능한 모든 메트릭을 보여줍니다.

모니터링할 수 있는 앱의 일부 메트릭은 다음과 같습니다.

* 평균 메모리 작업 집합
* 평균 응답 시간
* CPU 시간
* 메모리 작업 집합
* 요청

![앱을 모니터링하여 502 잘못된 게이트웨이 및 503 서비스를 사용할 수 없음 HTTP 오류 해결](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

자세한 내용은 다음을 참조하세요.

* [Azure App Service에서 앱 모니터링](web-sites-monitor.md)
* [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. 데이터 수집
#### <a name="use-the-diagnostics-tool"></a>진단 도구 사용
App Service는 앱 문제를 해결하는 데 도움이 되는 지능적인 대화형 환경으로, 구성이 필요하지 않습니다. 앱에서 문제가 발생하면 진단 도구는 무엇이 문제인지를 표시하여 문제를 더 쉽고 빠르게 확인하고 해결하기 위한 올바른 정보로 안내합니다.

App Service 진단에 액세스하려면 [Azure Portal](https://portal.azure.com)의 App Service 앱 또는 App Service 환경으로 이동합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결**을 클릭합니다.

#### <a name="use-the-kudu-debug-console"></a>Kudu 디버그 콘솔 사용
App Service는 사용자 환경에 대한 정보를 얻을 수 있는 JSON 엔드포인트 및 파일 디버그, 탐색, 업로드에 사용할 수 있는 디버그 콘솔과 함께 제공됩니다. 이 콘솔을 ‘Kudu 콘솔’ 또는 앱에 대한 ‘SCM 대시보드’라고 합니다.

**https://&lt;Your app name>.scm.azurewebsites.net/** 링크로 이동해서 대시보드에 액세스할 수 있습니다.

Kudu가 제공하는 것은 다음과 같습니다.

* 애플리케이션에 대한 환경 설정
* 로그 스트림
* 진단 덤프
* Powershell cmdlet 및 기본 DOS 명령을 실행할 수 있는 콘솔을 디버깅합니다.

Kudu의 또 다른 유용한 기능은 애플리케이션에 첫 번째 예외가 발생할 경우, 메모리 덤프를 만들기 위해 Kudu와 SysInternal 도구 Procdump를 사용할 수 있습니다. 메모리 덤프는 프로세스의 스냅숏이며 앱의 더욱 복잡한 문제를 해결하는 데 많은 도움을 줍니다.

Kudu에서 사용 가능한 기능에 대한 자세한 내용은 [사용자가 꼭 알아야 할 Azure Websites 온라인 도구](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)를 참조하세요.

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. 문제 완화
#### <a name="scale-the-app"></a>앱 크기 조정
Azure App Service에서 성능과 처리량의 증가를 위해 사용자가 실행하는 애플리케이션의 크기를 조정할 수 있습니다. 앱을 확장하려면 두 가지 작업이 필요합니다. App Service 계획을 상위 가격 책정 계층으로 변경해야 하고, 상위 가격 책정 계층으로 전환한 후에 특정 설정을 구성해야 합니다.

크기 조정에 대한 자세한 내용은 [Azure App Service에서 앱 크기 조정](web-sites-scale.md)을 참조하세요.

또한 둘 이상의 인스턴스에서 애플리케이션을 실행할 수 있습니다. 더 많은 처리 기능을 제공할 만 아니라, 어느 정도의 내결함성을 제공합니다. 하나의 인스턴스에서 프로세스가 다운되면, 또 다른 인스턴스에서 계속 요청을 처리합니다.

수동 또는 자동으로 크기를 조정할 수 있습니다.

#### <a name="use-autoheal"></a>AutoHeal를 사용
AutoHeal은 사용자가 선택한 설정(예: 구성 변경, 요청, 메모리 기반 제한 또는 요청을 실행하는데 필요한 시간)에 따라 앱에 대한 작업자 프로세스를 재활용 합니다. 대부분의 경우에는 프로세스를 재활용하는 방법이 문제를 해결하는 가장 빠른 방법입니다. 언제든지 Azure Portal 내에서 직접 앱을 다시 시작할 수도 있지만, AutoHeal에서 자동으로 다시 시작합니다. 앱의 루트 web.config에 일부 트리거를 추가하기만 하면 됩니다. 이 설정은 응용 프로그램 하나는.NET 아닌 경우에 동일한 방식으로 작동 됩니다 note 합니다.

자세한 내용은 [Auto-Healing Azure Websites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)를 참조하세요.

#### <a name="restart-the-app"></a>앱 다시 시작
이 방법은 일회성 문제를 해결하는 가장 간단한 방법입니다. [Azure Portal](https://portal.azure.com/)또는 앱의 블레이드에서 앱을 중지하거나 다시 시작하는 옵션을 사용할 수 있습니다.

 ![앱을 다시 시작하여 502 잘못된 게이트웨이 및 503 서비스를 사용할 수 없음 HTTP 오류 해결](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

또한 Azure Powershell을 사용하여 앱을 관리할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

