---
title: 클라우드 서비스 수명 주기 이벤트 처리 | Microsoft Docs
description: 수명 주기 이벤트에 응답 하는 메서드를 제공 하는 RoleEntryPoint를 비롯 하 여 .NET에서 클라우드 서비스 역할의 수명 주기 메서드를 사용 하는 방법에 대해 알아봅니다.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: d64414abfbc62e52b172a2c42796ec8d89d1719f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930063"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>.NET에서 웹 또는 작업자 역할의 수명 주기 사용자 지정
작업자 역할을 만들 때 수명 주기 이벤트에 응답할 수 있게 재정의하도록 메서드를 제공하는 [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) 클래스를 확장합니다. 웹 역할의 경우, 이 클래스는 선택적이므로 수명 주기 이벤트에 응답하는 데 사용합니다.

## <a name="extend-the-roleentrypoint-class"></a>RoleEntryPoint 클래스 확장
[RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) 클래스는 웹 또는 작업자 역할을 **시작**, **실행** 또는 **중지**할 때 Azure에서 호출하는 메서드를 포함합니다. 필요에 따라 역할 초기화, 역할 종료 시퀀스 또는 역할의 실행 스레드를 관리하는 이 메서드를 재정의할 수 있습니다. 

**RoleEntryPoint**를 확장하는 경우 메서드의 다음 동작을 알고 있어야 합니다.

* [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) 메서드는 부울 값을 반환 하므로이 메서드에서 **false** 를 반환할 수 있습니다.
  
   코드가 **false**를 반환하는 경우, 준비된 모든 종료 시퀀스를 실행하지 않고 역할 프로세스가 갑자기 종료됩니다. 일반적으로, **OnStart** 메서드에서 **false**를 반환하지 않아야 합니다.
* **RoleEntryPoint** 메서드의 오버로드 내에서 catch되지 않은 모든 예외는 처리되지 않은 예외로 취급됩니다.
  
   수명 주기 메서드 중 하나 내에서 예외가 발생하는 경우, [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) 이벤트가 발생한 다음 프로세스가 종료됩니다. 역할이 오프라인 상태가 되었거나, Azure에서 다시 시작할 수 있습니다. 처리되지 않은 예외가 발생하면, [중지](/previous-versions/azure/reference/ee758136(v=azure.100)) 이벤트가 발생하지 않으며 **OnStop** 메서드가 호출되지 않습니다.

사용자의 역할이 시작되지 않거나 초기화, 사용 중 및 중지 상태 사이에서 재활용되는 경우, 코드는 역할이 다시 시작될때마다 수명 주기 이벤트 중 하나에서 처리되지 않은 예외를 발생시킬 수 있습니다. 이 경우에 [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) 이벤트를 사용하여 예외의 원인을 확인하고 적절하게 처리해야 합니다. 사용자의 역할은 [실행](/previous-versions/azure/reference/ee772746(v=azure.100)) 메서드에서 반환될 수도 있으며 역할을 다시 시작하게 합니다. 배포 상태에 대한 자세한 내용은 [역할을 재활용하게 하는 일반적인 문제](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)를 참조하세요.

> [!NOTE]
> **Azure Tools for Microsoft Visual Studio**을 사용하여 애플리케이션을 개발하려면 역할 프로젝트 템플릿은 자동으로 *WebRole.cs* 및 *WorkerRole.cs* 파일에서 **RoleEntryPoint** 클래스를 확장합니다.
> 
> 

## <a name="onstart-method"></a>Onstart 메서드
**OnStart** 메서드는 역할 인스턴스가 Azure에서 온라인 상태로 전환될 때 호출됩니다. OnStart 코드가 실행되는 동안 역할 인스턴스는 **Busy** 로 표시되며 부하 분산 장치에서 전송된 외부 트래픽은 없습니다. 이벤트 처리기 구현 및 [Azure Diagnostics](cloud-services-how-to-monitor.md) 시작과 같은 초기화 작업을 수행하도록 이 메서드를 재정의할 수 있습니다.

**OnStart**가 **true**를 반환하면, 인스턴스가 성공적으로 초기화되고 Azure는 **RoleEntryPoint.Run** 메서드를 호출합니다. **OnStart**가 **false**를 반환하면, 계획된 종료 시퀀스를 실행하지 않고 역할이 즉시 종료됩니다.

다음 코드 예제에서는 **OnStart** 메서드를 재정의하는 방법을 보여 줍니다. 역할 인스턴스가 시작되고 스토리지 계정에 로깅 데이터의 전송을 설정하면 이 메서드가 구성되어 진단 모니터를 시작합니다.

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop 메서드
**OnStop** 메서드는 역할 인스턴스가 Azure에서 오프라인 상태로 전환된 후 프로세스가 종료되기 전에 호출됩니다. 역할 인스턴스가 올바르게 종료하는 데 필요한 코드를 호출하도록 이 메서드를 재정의할 수 있습니다.

> [!IMPORTANT]
> **OnStop** 메서드에서 실행되는 코드는 사용자가 시작한 종료 이외의 이유로 호출될 때 완료되는 시간이 제한되어 있습니다. 이 시간이 지난 후에는 프로세스가 종료되므로 **OnStop** 메서드의 코드가 신속하게 실행될 수 있도록 하거나 완료될 때까지 실행되지 않는 것을 허용해야 합니다. **OnStop** 메서드는 **Stopping** 이벤트가 발생한 후 호출됩니다.
> 
> 

## <a name="run-method"></a>Run 메서드
**Run** 메서드를 재정의하여 역할 인스턴스에 대한 장기 실행 스레드를 구현할 수 있습니다.

**Run** 메서드 재정의는 필요하지 않습니다. 기본 구현은 영원히 중지되는 스레드를 시작합니다. **Run** 메서드를 재정의하는 경우 코드가 무기한으로 차단됩니다. **Run** 메서드가 반환되는 경우 역할이 자동으로 정상적으로 재활용됩니다. 즉, Azure에서는 **Stopping** 이벤트를 발생시키고 **OnStop** 메서드를 호출하여 역할이 오프라인 상태로 전환되기 전에 종료 시퀀스가 실행될 수 있도록 합니다.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>웹 역할에 대한 ASP.NET 수명 주기 메서드 구현
**RoleEntryPoint** 클래스에서 제공하는 메서드 외에도 ASP.NET 수명 주기 메서드를 사용하여 웹 역할에 대한 초기화 및 종료 시퀀스를 관리할 수 있습니다. 이 기존 ASP.NET 애플리케이션을 Azure에 이식하는 경우 호환성을 위해 유용할 수 있습니다. ASP.NET 수명 주기 메서드는 **RoleEntryPoint** 메서드 내에서 호출됩니다. **Application\_Start** 메서드는 **RoleEntryPoint.OnStart** 메서드를 완료한 후 호출됩니다. **Application\_End** 메서드는 **RoleEntryPoint.OnStop** 메서드를 호출하기 전에 호출됩니다.

## <a name="next-steps"></a>다음 단계
[클라우드 서비스 패키지를 만드는](cloud-services-model-and-package.md)방법에 대해 알아봅니다.




