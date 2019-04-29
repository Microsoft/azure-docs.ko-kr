---
title: .NET 앱에 대 한 azure Application Insights 스냅숏 디버거 업그레이드 | Microsoft Docs
description: Azure App Services 또는 Nuget 패키지를 통해 최신 버전으로 스냅숏 디버거를 업그레이드 하는 방법
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784196"
---
# <a name="upgrading-the-snapshot-debugger"></a>스냅숏 디버거 업그레이드

데이터에 대 한 최상의 보안을 위해 Microsoft는 전문된 공격자에 취약 해질 것으로 나타난 TLS 1.0 및 TLS 1.1 전환 합니다. 이전 버전의 사이트 확장을 사용 하는 경우 작업을 계속 하려면 업그레이드를 해야 합니다. 이 문서에서는 스냅숏 디버거를 최신 버전으로 업그레이드 하는 데 필요한 단계를 설명 합니다. 스냅숏 디버거 사이트 확장을 사용 하도록 설정한 경우 또는 응용 프로그램에 추가 하는 SDK/Nuget을 사용 하는 경우에 따라 두 가지 주 업그레이드 경로가 있습니다. 둘 다 업그레이드 경로 아래 설명 되어 있습니다. 

## <a name="upgrading-the-site-extension"></a>사이트 확장으로 업그레이드

사이트 확장을 사용 하 여 스냅숏 디버거를 사용 하도록 설정한 경우 다음 절차를 사용 하 여 쉽게 업그레이드할 수 있습니다.

1. Azure 포털에 로그인합니다.
2. Application Insights 스냅숏 디버거를 사용 하도록 설정 하 고 있는 리소스를 이동 합니다. 예를 들어, 웹 앱에 대 한 App Service 리소스를 이동 합니다.

   ![개별 App Service 리소스의 스크린 샷 DiagService01 라는](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 개요 블레이드에서 리소스를 이동한 후 Application Insights에서 클릭 합니다.

   ![세 개의 단추 스크린샷입니다. 이름 Application Insights를 사용 하 여 가운데 단추 선택](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 현재 설정을 사용 하 여 새 블레이드가 열립니다. 기회를 설정을 변경 하려는 경우가 아니면 그대로 둘 수 있습니다. 합니다 **적용** 블레이드의 맨 아래에 단추가 기본적으로 활성화 되지 않으면 및 단추를 활성화 하려면 설정 중 하나를 설정/해제 해야 합니다. 실제 설정을 변경 하지 않아도, 설정을 변경 후 즉시 다시 변경 하는 대신 합니다. 설정 하 고 다음을 선택 하 여 Profiler를 설정/해제 하는 것이 좋습니다 **적용**합니다.

   ![빨간색으로 강조 표시 하는 적용 단추를 사용 하 여 스크린 샷의 Application Insights 앱 서비스 구성 페이지](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. 클릭 하면 **적용**, 변경 내용을 확인 해야 합니다.

    > [!NOTE]
    > 업그레이드 프로세스의 일부로 사이트를 다시 시작 됩니다.

   ![스크린 샷의 App Service의 모니터링 프롬프트를 적용 합니다. 텍스트 상자에는 메시지가 표시 됩니다. "이제 앱 설정에 변경 내용을 적용 하 고 웹 앱에 Application Insights 리소스를 연결 하는 도구를 설치 합니다. 사이트 다시 시작 됩니다. "활성 컨트롤러를 다시 시작하면 장치는 수동 컨트롤러에 장애 조치합니다.](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. 클릭 **예** 변경 내용을 적용 합니다. 프로세스 동안 변경 내용이 적용 되었는지 보여 주는 알림이 표시 됩니다.

   ![오른쪽 위 모서리에 표시 되는 확장 메시지 업데이트 적용 변경 내용-스크린샷](./media/snapshot-debugger-upgrade/updating-extensions.png)

완료 되 면을 **"변경 내용이 적용 됩니다."** 알림이 표시 됩니다.

   ![적용 된 변경 내용의 메시지의 스크린 샷](./media/snapshot-debugger-upgrade/changes-are-applied.png)

사이트 이제 업그레이드 하 고 사용할 준비가 되었습니다.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>SDK/Nuget을 사용 하 여 스냅숏 디버거 업그레이드

응용 프로그램의 버전을 사용 하는 경우 `Microsoft.ApplicationInsights.SnapshotCollector` 버전 1.3.1 아래에 업그레이드할 필요는 [최신 버전](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) 작업을 계속할 수 있습니다.
