---
title: Azure Application Insights OpenCensus 분산 추적 로컬 전달자 (미리 보기) | Microsoft docs
description: 분산된 전달 OpenCensus는 Python 및 Go와 같은 언어부터 Azure Application Insights까지 추적하고 포괄합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: a7efe663a75fa29a31e7157c5eab24c2973a3758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699344"
---
# <a name="local-forwarder-preview"></a>로컬 전달자 (미리 보기)

로컬 전달자는 다양한 SDK에서 [OpenCensus](https://opencensus.io/) 원격 분석을 수집하고 Application Insights로 경로를 지정하는 에이전트입니다. Windows 및 Linux에서 실행할 수 있습니다. macOS에서 실행할 수도 있지만, 이번에 공식적으로 지원되지는 않습니다.

## <a name="running-local-forwarder"></a>로컬 전달자 실행

로컬 전달자는 [GitHub의 오픈 소스 프로젝트](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases)입니다. 여러 플랫폼에서 로컬 전달자를 실행하는 방법에는 여러 가지가 있습니다.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Windows 서비스

Windows에서 로컬 전달자를 실행하는 가장 쉬운 방법은 Windows 서비스로 설치하는 것입니다. 릴리스에는 운영 체제를 사용하여 쉽게 등록할 수 있는 Windows 서비스 실행 파일이 함께 제공됩니다(*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*).

> [!NOTE]
> 로컬 전달자 서비스는 최소한의 .NET Framework 4.7이 필요합니다. .NET Framework 4.7이 없는 경우 서비스가 설치되기는 하지만 시작되지 않습니다. .NET Framework의 최신 버전에 액세스하려면 **[.NET Framework 다운로드 페이지를 방문](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** 하세요.

1. LF.WindowsServiceHost.zip 파일을 GitHub의 [로컬 전달자 릴리스 페이지](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases)에서 다운로드합니다.

    ![로컬 전달자 릴리스 다운로드 페이지의 스크린샷](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. 데모의 편의 위해 이 예제에서는 .zip 파일을 `C:\LF-WindowsServiceHost` 경로에 추출합니다.

    서비스를 등록하고 시스템 부팅 시 시작되도록 하려면 명령줄에서 다음을 관리자 권한으로 실행합니다.

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    다음과 같은 응답이 표시됩니다.
    
    `[SC] CreateService SUCCESS`
    
    Services GUI 형식 ``services.msc``를 통해 새 서비스를 검사하려면
        
     ![로컬 전달자 서비스의 스크린샷](./media/opencensus-local-forwarder/002-services.png)

3. 새 로컬 전달자를 **마우스 오른쪽 단추로 클릭**하고 **시작**을 선택합니다. 이제 서비스가 실행 중인 상태입니다.

4. 기본적으로 서비스는 어떠한 복구 작업 없이 만들어집니다. **마우스 오른쪽 단추로 클릭**하고 **속성** > **복구**를 선택하여 서비스 오류에 대한 자동 응답을 구성할 수 있습니다.

    또는 오류가 발생할 때 프로그래밍 방식으로 자동 복구 옵션을 설정하려면 다음을 사용할 수 있습니다.

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. 사용자 ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` 파일과 동일한 위치에(이 예제의 경우 ``C:\LF-WindowsServiceHost``) ``LocalForwarder.config``라는 파일이 있습니다. 이것이 사용자의 localforwader 구성을 조정하고, 분산 추적 데이터를 전달할 Application Insights 리소스의 계측 키를 조정할 수 있는 xml 기반 파일입니다. 

    계측 키를 추가하도록 ``LocalForwarder.config`` 파일을 편집한 후, 변경 내용이 적용되도록 **로컬 전달자 서비스**를 다시 시작해야 합니다.
    
6. 원하는 설정이 지정되어 있고 로컬 전달자가 예상대로 추적 데이터를 수신 대기하는지 확인하려면 ``LocalForwarder.log`` 파일을 확인합니다. 결과는 파일의 맨 아래 이미지와 비슷하게 표시됩니다.

    ![LocalForwarder.log 파일의 스크린샷](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>콘솔 애플리케이션

특정 사용 사례의 경우 로컬 전달자를 콘솔 애플리케이션으로 실행하는 것이 유익할 수 있습니다. 릴리스는 콘솔 호스트의 다음 실행 버전으로 제공됩니다.
* 프레임워크 종속 .NET Core 이진 */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. 이 이진을 실행하려면 .NET Core 런타임을 설치해야 합니다. 자세한 내용은 이 다운로드 [페이지](https://www.microsoft.com/net/download/dotnet-core/2.1)를 참조하세요.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* x86 및 x64 플랫폼에 대한 자체 포함된 .NET Core 이진 집합. 이는 .NET Core 런타임을 실행하는 데 필요하지 않습니다. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Windows와 동일하게 릴리스는 콘솔 호스트의 다음 실행 버전으로 제공됩니다.
* 프레임워크 종속 .NET Core 이진 */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. 이 이진을 실행하려면 .NET Core 런타임을 설치해야 합니다. 자세한 내용은 이 다운로드 [페이지](https://www.microsoft.com/net/download/dotnet-core/2.1)를 참조하세요.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* linux-64에 대한 자체 포함된 .NET Core 이진 집합. 이는 .NET Core 런타임을 실행하는 데 필요하지 않습니다. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

많은 Linux 사용자는 로컬 전달자를 디먼으로 실행하려고 합니다. Linux 시스템은 Upstart, sysv 또는 systemd와 같이 서비스 관리를 위한 다양한 솔루션과 함께 제공됩니다. 사용자의 특정 버전이 무엇이든 사용자의 시나리오에 가장 적합한 방식으로 로컬 전달자를 실행할 수 있습니다.

예를 들어 systemd를 사용하는 디먼 서비스를 만들어 보겠습니다. 프레임워크 종속 버전을 사용하지만 자체 포함된 버전에도 동일하게 적용될 수 있습니다.

* *localforwarder.service*라는 이름의 다음과 같은 서비스 파일을 만들고 */lib/systemd/system*에 배치합니다.
이 샘플에서는 사용자 이름이 SAMPLE_USER이고, */ConsoleHost/publish*에서 */home/SAMPLE_USER/LOCALFORWARDER_DIR*로 로컬 전달자 프레임워크 종속 이진 파일을 복사했다고 가정합니다.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* systemd가 모든 부팅 시 로컬 전달자를 시작하도록 지시하려면 다음 명령을 실행합니다.

```
systemctl enable localforwarder
```

* systemd가 즉시 로컬 전달자를 시작하도록 지시하려면 다음 명령을 실행합니다.

```
systemctl start localforwarder
```

* /home/SAMPLE_USER/LOCALFORWARDER_DIR 디렉터리에서 **.log* 파일을 검사하여 서비스를 모니터링합니다.

### <a name="mac"></a>Mac
macOS에서는 로컬 전달자가 작동할 수는 있지만 현재 공식적으로 지원되는 것은 아닙니다.

### <a name="self-hosting"></a>자체 호스팅
또한 로컬 전달자는 .NET 애플리케이션 자체 내에서 호스트할 수 있도록 .NET Standard NuGet 패키지로 분산됩니다.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>로컬 전달자 구성

* 로컬 전달자의 자체 호스트 중 하나를 실행하는 경우(콘솔 호스트 또는 Windows 서비스 호스트) **LocalForwarder.config**가 이진 옆에 배치됩니다.
* 로컬 전달자 NuGet을 자체 호스팅하는 경우 동일한 형식의 구성을 코드에 제공해야 합니다(자체 호스팅 섹션 참조). 구성 구문의 경우 GitHub 리포지토리에서 [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config)를 확인합니다. 

> [!NOTE]
> 구성은 릴리스 간에 변경될 수 있으므로 사용 중인 버전에 주의해야 합니다.

## <a name="monitoring-local-forwarder"></a>로컬 전달자 모니터링

추적은 로컬 전달자를 실행하는 실행 파일 옆에 있는 파일 시스템에 기록됩니다(**.log* 파일 찾기). *NLog.config*란 이름의 파일을 실행 파일 옆에 배치하여 기본값 대신 고유한 구성을 제공할 수 있습니다. 형식의 설명은 [설명서](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format)를 참조하세요.

구성 파일이 제공되지 않은 경우(즉, 기본값) 로컬 전달자는 기본 구성([여기](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config)에서 찾을 수 있음)을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Open Census](https://opencensus.io/)
