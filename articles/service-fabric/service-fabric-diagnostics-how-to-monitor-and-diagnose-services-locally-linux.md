---
title: Linux에서 Azure Service Fabric 앱 디버그
description: 로컬 Linux 개발 컴퓨터에서 Service Fabric 서비스를 모니터링하고 진단하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 523cb0d1a8e8f322c1936f1fe52a954399b2acc5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999770"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>로컬 Linux 컴퓨터 개발 설치에서 서비스 모니터링 및 진단


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

모니터링, 감지, 진단 및 문제 해결을 통해 사용자 환경에 미치는 영향을 최소화하면서 서비스를 계속할 수 있습니다. 모니터링 및 진단은 실제 배포된 프로덕션 환경에 중요합니다. 서비스를 개발하는 동안 유사한 모델을 채택하면 프로덕션 환경으로 이동 시 진단 파이프라인이 작동합니다. 서비스 패브릭을 사용하면 서비스 개발자가 단일 컴퓨터 로컬 개발 설정과 실제 사용 프로덕션 클러스터 설정 양쪽에서 매끄럽게 작동하는 진단 기능을 간편하게 구현할 수 있습니다.


## <a name="debugging-service-fabric-java-applications"></a>Service Fabric Java 애플리케이션 디버깅

Java 애플리케이션에 대해 [다중 로깅 프레임워크](https://en.wikipedia.org/wiki/Java_logging_framework)를 사용할 수 있습니다. `java.util.logging`는 JRE의 기본 옵션 이므로 [GitHub의 코드 예제](https://github.com/Azure-Samples/service-fabric-java-getting-started)에도 사용 됩니다. 다음 논의에서는 `java.util.logging` 프레임워크를 구성하는 방법을 설명합니다.

java.util.logging을 사용하면 애플리케이션 로그를 메모리, 출력 스트림, 콘솔 파일 또는 소켓으로 리디렉션할 수 있습니다. 이들 옵션 각각에 대해 프레임워크에 이미 제공되어 있는 기본 핸들러가 있습니다. `app.properties` 파일을 만들어서 애플리케이션에 대한 파일 핸들러가 모든 로그를 로컬 파일로 리디렉션하도록 구성할 수 있습니다.

다음 코드 조각은 예제 구성을 포함합니다.

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

`app.properties` 파일이 가리키는 폴더가 반드시 존재해야 합니다. `app.properties` 파일이 생성된 후에는 `<applicationfolder>/<servicePkg>/Code/` 폴더의 진입점 스크립트 `entrypoint.sh`가 속성 `java.util.logging.config.file`을 `app.properties` 파일로 설정하도록 수정도 해야 합니다. 항목은 다음 코드 조각과 같습니다.

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


이 구성으로 인해 로그가 순환 방식으로 `/tmp/servicefabric/logs/`에 수집됩니다. 이 경우의 로그 파일 이름은 mysfapp%u.%g.log입니다. 여기서,
* **%u**는 동시 Java 프로세스 간에 충돌을 확인하기 위한 고유 번호입니다.
* **%g**는 회전 로그를 구분하기 위한 세대 번호입니다.

기본적으로 핸들러가 명시적으로 구성되지 않으면 콘솔 핸들러가 등록됩니다. /var/log/syslog 아래 syslog에서 로그를 볼 수 있습니다.

자세한 내용은 [GitHub의 코드 예제](https://github.com/Azure-Samples/service-fabric-java-getting-started)를 참조 하세요.


## <a name="debugging-service-fabric-c-applications"></a>Service Fabric C# 애플리케이션 디버깅


다수의 프레임워크는 Linux의 CoreCLR 애플리케이션 추적에 사용할 수 있습니다. 자세한 내용은 [로깅을 위한 .Net Extensions](https://github.com/dotnet/extensions/tree/master/src/Logging)를 참조 하세요.  EventSource는 C# 개발자에게 친숙하기 때문에 이 문서에서는 Linux의 CoreCLR 샘플에서 추적용으로 EventSource를 사용합니다.

첫 번째 단계는 메모리, 출력 스트림 또는 콘솔 파일 로그를 쓸 수 있도록 System.Diagnostics.Tracing를 포함하는 것입니다.  EventSource를 사용하여 기록하려면 project.json에 다음 프로젝트를 추가합니다.

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

서비스 이벤트를 수신 대기하기 위해 사용자 지정 EventListener를 사용한 다음 적절하게 리디렉션하여 파일을 추적할 수 있습니다. 다음 코드 조각은 EventSource 및 사용자 지정 EventListener를 사용하여 로깅의 샘플 구현을 보여줍니다.


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


앞의 코드 조각은 로그를 `/tmp/MyServiceLog.txt`의 파일에 출력합니다. 이 파일 이름을 적절하게 업데이트해야 합니다. 콘솔로 기록을 리디렉션하려는 경우 사용자 지정된 EventListener 클래스에 다음 코드 조각을 사용합니다.

```csharp
public static TextWriter Out = Console.Out;
```

[C# 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)의 샘플은 EventSource 및 사용자 지정 EventListener를 사용하여 이벤트를 파일에 기록합니다.



## <a name="next-steps"></a>다음 단계
애플리케이션에 추가된 동일한 추적 코드 역시 Azure 클러스터에서 애플리케이션의 진단과 함께 작동합니다. 도구에 대한 다양한 옵션과 도구를 설정하는 방법에 대해 설명하는 이러한 문서를 확인합니다.
* [Azure Diagnostics를 사용하여 로그를 수집하는 방법](./service-fabric-diagnostics-event-aggregation-lad.md)
