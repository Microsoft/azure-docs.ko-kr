---
title: Node.js에 대한 모범 사례 및 문제 해결 - Azure App Service
description: Azure App Service의 노드 애플리케이션에 대한 모범 사례 및 문제 해결 단계에 대해 알아봅니다.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.custom: seodec18
ms.openlocfilehash: 321dbf891c77007952f01b32bb509a15c2ac3e6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853063"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Azure App Service Windows의 노드 애플리케이션에 대한 모범 사례 및 문제 해결 가이드

이 문서에서는 Azure App Service에서 실행되는 [노드 애플리케이션](app-service-web-get-started-nodejs.md)([iisnode](https://github.com/azure/iisnode) 사용)에 대한 모범 사례 및 문제 해결 단계를 알아봅니다.

> [!WARNING]
> 프로덕션 사이트에서 문제 해결 단계를 사용할 때는 주의하세요. 비프로덕션 설정(예: 스테이징 슬롯)에서 앱의 문제를 해결하는 것이 좋으며 문제가 해결되면 스테이징 슬롯을 프로덕션 슬롯으로 교환하는 것이 좋습니다.
>

## <a name="iisnode-configuration"></a>IISNODE 구성

이 [스키마 파일](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) 에서는 iisnode에 대해 구성할 수 있는 모든 설정을 보여 줍니다. 애플리케이션에 유용한 일부 설정은 다음과 같습니다.

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

이 설정은 IIS 애플리케이션마다 실행되는 노드 프로세스 수를 제어합니다. 기본값은 1입니다. 값을 0으로 변경하면 VM vCPU 개수만큼 node.exe를 시작할 수 있습니다. 컴퓨터에서 모든 vCPU를 사용할 수 있도록 권장되는 값은 대부분의 애플리케이션에서 0입니다. Node.exe는 단일 스레드이므로 node.exe 하나는 vCPU를 최대 1개 사용합니다. 노드 애플리케이션에서 최대한의 성능을 얻으려면 모든 vCPU를 사용하는 것이 좋습니다.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

이 설정은 node.exe에 대한 경로를 제어합니다. node.exe 버전을 가리키도록 이 값을 설정할 수 있습니다.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

이 설정은 iisnode에서 각 node.exe로 보내는 최대 동시 요청 수를 제어합니다. Azure App Service에서 기본값은 무제한입니다. 애플리케이션이 수신하는 요청 수와 애플리케이션이 각 요청을 처리하는 속도에 따라 값을 구성할 수 있습니다.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

이 설정은 요청을 node.exe에 전송하기 위해 iisnode에서 명명된 파이프에 연결을 재시도하는 최대 횟수를 제어합니다. 이 설정과 namedPipeConnectionRetryDelay를 조합하면 iisnode 내에서 각 요청의 총 시간 제한이 결정됩니다. Azure App Service에서 기본값은 200입니다. 총 시간 제한(초) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

이 설정은 명명된 파이프를 통해 node.exe로 요청을 전송하기 위해 iisnode가 각 재시도 간에 대기하는 시간(ms)을 제어합니다. 기본값은 250ms입니다.
총 시간 제한(초) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

기본적으로 Azure App Service의 iisnode에서 총 시간 제한은 200 \* 250ms = 50초입니다.

### <a name="logdirectory"></a>logDirectory

이 설정은 iisnode에서 stdout/stderr를 로깅할 디렉터리를 제어합니다. 기본값은 주 스크립트 디렉터리(기본 server.js가 있는 디렉터리)를 기준으로 iisnode입니다.

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

이 설정은 노드 애플리케이션을 디버깅할 때 node-inspector iisnode가 사용할 버전을 제어합니다. 현재 iisnode-inspector-0.7.3.dll 및 iisnode-inspector.dll은 이 설정에 대해 2가지 값만 유효합니다. 기본값은 iisnode-inspector-0.7.3.dll입니다. iisnode-inspector-0.7.3.dll 버전은 node-inspector-0.7.3을 사용하고 웹 소켓을 사용합니다. 이 버전을 사용하려면 Azure 웹앱에서 웹 소켓을 사용하도록 설정합니다. 새 node-inspector를 사용하도록 iisnode를 구성하는 방법에 대한 자세한 내용은 <https://ranjithblogs.azurewebsites.net/?p=98>을 참조하세요.

### <a name="flushresponse"></a>flushResponse

IIS는 기본적으로 플러시하기 전에 또는 응답이 끝날 때까지(둘 중 빠른 시점 적용) 응답 데이터를 4MB까지 버퍼링합니다. iisnode는 이 동작을 재정의하는 구성 설정을 제공합니다. iisnode가 node.exe에서 수신하는 즉시 응답 엔터티 본문의 일부를 플러시하려면 web.config에서 iisnode/@flushResponse 특성을 'true'로 설정해야 합니다.

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

응답 엔터티 본문의 모든 조각을 플러시하도록 설정하면 최대 5%까지 시스템의 처리량이 감소되는 성능 오버 헤드가 추가됩니다(현재 v0.1.13). 응답 스트리밍이 필요한 엔드포인트로만 이 설정의 범위를 지정하는 것이 가장 좋습니다(예: web.config에서 `<location>` 요소 사용).

이 외에도 스트리밍 애플리케이션의 경우 iisnode 처리기의 responseBufferLimit를 0으로 설정해야 합니다.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

변경 내용을 감시하는 세미콜론으로 구분된 파일 목록입니다. 파일에 대한 변경 내용으로 인해 애플리케이션 재활용이 발생합니다. 각 항목은 주 애플리케이션 진입점이 있는 디렉터리를 기준으로 선택적 디렉터리 이름 및 필수 파일 이름으로 구성됩니다. 파일 이름 부분에만 와일드 카드가 허용됩니다. 기본값은 `*.js;iisnode.yml`입니다.

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

기본값은 False입니다. 설정된 경우 노드 애플리케이션에서 명명된 파이프(환경 변수 IISNODE\_CONTROL\_PIPE)에 연결할 수 있으며 "재활용" 메시지를 보낼 수 있습니다. 그러면 w3wp가 정상적으로 재활용됩니다.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

기본값은 이 기능을 사용하지 않는다는 의미의 0입니다. 0보다 큰 값으로 설정되면 iisnode에서 모든 하위 프로세스를 'idlePageOutTimePeriod' 밀리초마다 페이지 아웃합니다. 페이지 아웃에 대해 이해하려면 [설명서](/windows/desktop/api/psapi/nf-psapi-emptyworkingset)를 참조하세요. 이 설정은 대량의 메모리를 사용하고 간혹 메모리를 디스크로 페이지 아웃하여 RAM을 확보하려는 애플리케이션의 경우 유용합니다.

> [!WARNING]
> 프로덕션 애플리케이션에서 다음 구성 설정을 사용하도록 설정할 대는 주의하세요. 라이브 프로덕션 애플리케이션에서 사용하지 않도록 설정하는 것이 좋습니다.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

기본값은 False입니다. true로 설정되면 iisnode에서 HTTP 응답 헤더 `iisnode-debug`를 보내는 모든 HTTP 응답에 추가하고 `iisnode-debug` 헤더 값은 URL입니다. 개별 진단 정보 조각은 URL 조각을 확인하여 얻을 수 있지만 브라우저에서 URL을 열면 시각화를 달성할 수 있습니다.

### <a name="loggingenabled"></a>loggingEnabled

이 설정은 iisnode에 의한 stdout 및 stderr의 로깅을 제어합니다. Iisnode는 시작된 노드 프로세스에서 stdout/stderr을 캡처하여 'logDirectory' 설정에 지정된 디렉터리에 씁니다. 사용하도록 설정하면 애플리케이션에서 로그를 파일 시스템에 기록하고 애플리케이션에서 완료한 로깅 양에 따라 성능 저하가 발생할 수 있습니다.

### <a name="deverrorsenabled"></a>devErrorsEnabled

기본값은 False입니다. true로 설정하면 iisnode에서 HTTP 상태 코드 및 Win32 오류 코드가 브라우저에 표시됩니다. win32 코드는 특정 유형의 문제를 디버깅하는 데 유용합니다.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled(라이브 프로덕션 사이트에서 사용 안 함)

이 설정은 디버깅 기능을 제어합니다. Iisnode는 node-inspector에 통합됩니다. 이 설정을 사용하여 노드 애플리케이션의 디버깅을 사용하도록 설정합니다. 이 설정을 사용하도록 설정하면 iisnode가 필요한 ‘debuggerVirtualDir’ 디렉터리의 node-inspector 파일을 노드 애플리케이션에 대한 첫 번째 디버그 요청에 만듭니다. 요청을 `http://yoursite/server.js/debug`에 전송하여 node-inspector를 로드할 수 있습니다. ‘debuggerPathSegment’ 설정으로 디버그 URL 세그먼트를 제어할 수 있습니다. 기본적으로 debuggerPathSegment='debug'입니다. 예를 들어 `debuggerPathSegment`를 GUID로 설정할 수 있으므로 다른 사람이 검색하기 더 어렵습니다.

디버깅에 대한 자세한 내용은 [Windows에서 node.js 애플리케이션 디버그](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html)를 읽어 보세요.

## <a name="scenarios-and-recommendationstroubleshooting"></a>시나리오 및 권장 사항/문제 해결

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>내 노드 애플리케이션에서 너무 많은 아웃바운드 호출을 생성합니다.

많은 애플리케이션에서 일반 작업의 일부로 아웃바운드 연결을 생성하려고 합니다. 예를 들어 요청이 들어오면 노드 앱은 다른 위치에서 REST API에 연결하고 요청을 처리할 일부 정보를 얻습니다. http 또는 https 호출을 수행할 때 연결 유지 에이전트를 사용하려고 합니다. 이러한 아웃바운드 호출을 수행할 때 연결 유지 에이전트로 agentkeepalive 모듈을 사용할 수 있습니다.

agentkeepalive 모듈은 소켓이 Azure 웹앱 VM에서 다시 사용되도록 합니다. 각 아웃바운드 요청에서 새 소켓을 만들면 애플리케이션에 오버 헤드가 추가됩니다. 애플리케이션이 아웃바운드 요청에서 소켓을 재사용하면 애플리케이션이 VM당 할당된 maxSockets를 초과하지 않도록 할 수 있습니다. Azure App Service에 대한 권장 사항은 agentKeepAlive maxSockets 값을 VM당 총 160개 소켓(node.exe의 인스턴스 4 \* 40 maxSockets/인스턴스)으로 설정하는 것입니다.

[agentKeepALive](https://www.npmjs.com/package/agentkeepalive) 구성 예제:

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> 이 예에서는 VM에서 4개의 node.exe를 실행 중이라고 가정합니다. VM에서 다른 수의 node.exe가 실행 중인 경우 maxSockets 설정을 적절하게 수정해야 합니다.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>내 노드 애플리케이션이 너무 많은 CPU를 사용하고 있습니다.

포털의 Azure App Service에서 높은 CPU 사용량에 대한 권장 사항을 받게 됩니다. 또한 특정 [메트릭](web-sites-monitor.md)을 감시하도록 모니터를 설정할 수도 있습니다. [Azure Portal 대시보드](../azure-monitor/app/web-monitor-performance.md)에서 CPU 사용량을 확인할 때는 최고값을 놓치지 않도록 CPU에 대한 MAX 값을 확인하세요.
애플리케이션에서 CPU를 너무 많이 사용한다고 생각되고 그 이유를 설명할 수 없는 경우 노드 애플리케이션을 프로파일링해서 찾을 수 있습니다.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Azure App Service에서 V8-Profiler로 노드 애플리케이션 프로파일링

예를 들어 아래와 같이 프로파일링할 hello world 앱이 있다고 가정해 보겠습니다.

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

디버그 콘솔 사이트 `https://yoursite.scm.azurewebsites.net/DebugConsole`로 이동합니다.

site/wwwroot 디렉터리로 이동합니다. 다음 예제와 같이 명령 프롬프트가 표시됩니다.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

`npm install v8-profiler`명령을 실행합니다.

이 명령은 node\_modules 디렉터리에 v8-profiler와 모든 종속성을 설치합니다.
이제 server.js를 편집하여 애플리케이션을 프로파일링합니다.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

위의 코드 프로필은 WriteConsoleLog 함수를 프로파일링한 후 프로파일 출력을 사이트의 wwwroot 아래 'profile.cpuprofile' 파일에 기록합니다. 애플리케이션에 요청 보내기 사이트의 wwwroot 아래에 'profile.cpuprofile' 파일이 생성된 것을 확인할 수 있습니다.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

이 파일을 다운로드하여 Chrome F12 Tools로 엽니다. Chrome에서 F12 키를 누른 다음 **프로필** 탭을 선택합니다. **로드** 단추를 선택합니다. 다운로드한 profile.cpuprofile 파일을 선택합니다. 방금 로드한 프로파일을 클릭합니다.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

95%의 시간이 WriteConsoleLog 함수에 의해 소요되었다는 것을 볼 수 있습니다. 또한 이 문제를 발생시킨 정확한 줄 번호와 원본 파일도 표시됩니다.

### <a name="my-node-application-is-consuming-too-much-memory"></a>내 노드 애플리케이션이 메모리를 너무 많이 사용합니다.

애플리케이션에서 메모리를 너무 많이 사용하면 포털의 Azure App Service에서 높은 메모리 사용량에 대한 알림이 표시됩니다. 특정 [메트릭](web-sites-monitor.md)을 감시하도록 모니터를 설정할 수 있습니다. [Azure Portal 대시보드](../azure-monitor/app/web-monitor-performance.md)에서 메모리 사용량을 확인할 때는 최고값을 놓치지 않도록 메모리에 대한 MAX 값을 확인해야 합니다.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>node.js에 대한 누수 감지 및 힙 Diff

[node-memwatch](https://github.com/lloyd/node-memwatch) 를 사용하여 메모리 누수를 확인할 수 있습니다.
v8-profiler처럼 `memwatch`를 설치하고 힙을 캡처 및 diff하는 코드를 편집하여 애플리케이션에서 메모리 누수를 확인할 수 있습니다.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>내 node.exe가 임의로 중지됨

node.exe가 임의로 종료된 이유 몇 가지는 다음과 같습니다.

1. 애플리케이션에서 catch되지 않는 예외를 throw합니다. throw된 예외에 대한 자세한 내용은 d:\\home\\LogFiles\\Application\\logging-errors.txt 파일을 확인하세요. 이 파일에는 스택 추적이 있어 애플리케이션을 디버그하고 수정하는 데 유용합니다.
2. 애플리케이션에서 너무 많은 메모리를 사용하여 다른 프로세스가 시작되는 데 영향을 줍니다. 총 VM 메모리가 100%에 근접한 경우 프로세스 관리자가 node.exe를 중지할 수 있습니다. 프로세스 관리자는 다른 프로세스가 작업을 수행할 수 있도록 일부 프로세스를 중지합니다. 이 문제를 해결하려면 메모리 누수에 대한 애플리케이션을 프로파일링합니다. 애플리케이션에 많은 양의 메모리가 필요한 경우 더 큰 VM으로 스케일 아웃합니다(이로 인해 VM에 사용할 수 있는 RAM이 증가됨).

### <a name="my-node-application-does-not-start"></a>내 노드 애플리케이션이 시작되지 않음

애플리케이션 시작 시 500 오류를 반환하는 경우 몇 가지 원인이 있을 수 있습니다.

1. Node.exe가 올바른 위치에 없습니다. nodeProcessCommandLine 설정을 확인합니다.
2. 기본 스크립트 파일이 올바른 위치에 없습니다. web.config를 확인하고 처리기 섹션에서 기본 스크립트 파일의 이름이 기본 스크립트 파일과 일치하는지 확인합니다.
3. Web.config 구성이 잘못되었습니다. 설정 이름/값을 확인하세요.
4. 콜드 부팅 – 애플리케이션을 시작하는 데 너무 오래 걸립니다. 애플리케이션이 (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000초보다 오래 걸리면 iisnode에서 500 오류를 반환합니다. 애플리케이션 시작 시간과 일치하도록 이러한 설정 값을 늘리면 iisnode의 제한 시간 초과와 500 오류 반환을 방지할 수 있습니다.

### <a name="my-node-application-crashed"></a>내 노드 애플리케이션에 충돌 발생

애플리케이션에서 catch되지 않는 예외를 throw합니다. throw된 예외에 대한 자세한 내용은 `d:\\home\\LogFiles\\Application\\logging-errors.txt` 파일을 확인하세요. 이 파일에는 스택 추적이 있어 애플리케이션을 진단하고 수정하는 데 유용합니다.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>내 노드 애플리케이션을 시작하는 데 시간이 너무 오래 걸립니다(콜드 부팅).

애플리케이션 시작 시간이 긴 일반적인 원인은 node\_modules에 많은 수의 노드가 있기 때문입니다. 애플리케이션은 시작할 때 이러한 파일의 대부분을 로드하려고 합니다. 기본적으로 파일은 Azure App Service의 네트워크 공유에 저장되므로 많은 파일을 로드하면 시간이 소요될 수 있습니다.
이 프로세스 속도를 높이는 몇 가지 솔루션은 다음과 같습니다.

1. npm3을 사용하여 플랫 종속성 구조가 있고 중복 종속성이 없는지 확인하고 모듈을 설치합니다.
2. node\_modules의 지연 로드를 시도하고 애플리케이션 시작 시 모든 모듈을 로드하지 않도록 합니다. 모듈을 지연 로드하려면 모듈 코드의 첫 번째 실행 전에 함수 내에서 모듈이 실제로 필요할 때 요청에 대한 호출(‘모듈’)을 수행해야 합니다.
3. Azure App Service는 로컬 캐시라는 기능을 제공합니다. 이 기능은 네트워크 공유에서 VM의 로컬 디스크로 콘텐츠를 복사합니다. 파일이 로컬 파일이므로 node\_modules의 로드 시간이 훨씬 더 빠릅니다.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http 상태 및 하위 상태

이 `cnodeconstants`[원본 파일](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h)에는 오류로 인해 iisnode에서 반환할 수 있는 가능한 상태/하위 상태 조합이 모두 나열되어 있습니다.

win32 오류 코드를 표시하도록 애플리케이션에 대해 FREB를 사용하도록 설정합니다(성능상의 이유로 비프로덕션 사이트에서만 FREB를 사용하도록 설정하세요).

| HTTP 상태 | HTTP 하위 상태 | 가능한 원인 |
| --- | --- | --- |
| 500 |1000 |요청을 IISNODE로 디스패치하는 몇 가지 문제가 있습니다. node.exe가 시작되었는지 확인하세요. Node.exe가 시작 시 충돌되었을 수 있습니다. web.config 구성에 오류가 있는지 확인하세요. |
| 500 |1001 |- Win32Error 0x2 - 앱이 URL에 응답하지 않습니다. URL 다시 쓰기 규칙을 확인하거나 Express 앱에 올바른 경로가 정의되어 있는지 확인하세요. - Win32Error 0x6d – 명명된 파이프를 사용 중 – 파이프를 사용 중이므로 Node.exe에서 요청을 수락하지 않습니다. 높은 cpu 사용량을 확인하세요. - 기타 오류 – node.exe가 충돌되었는지 확인하세요. |
| 500 |1002 |Node.exe가 충돌됨 – d:\\home\\LogFiles\\logging-errors.txt에서 스택 추적을 확인하세요. |
| 500 |1003 |파이프 구성 문제 - 명명된 파이프 구성이 잘못되었습니다. |
| 500 |1004-1018 |node.exe 간에 요청을 보내거나 응답을 처리하는 동안 일부 오류가 발생했습니다. Node.exe가 충돌되었는지 확인하세요. d:\\home\\LogFiles\\logging-errors.txt에서 스택 추적을 확인하세요. |
| 503 |1000 |메모리가 부족하여 추가 명명된 파이프 연결을 할당할 수 없습니다. 앱에서 많은 메모리를 사용하는 이유를 확인하세요. maxConcurrentRequestsPerProcess 설정 값을 확인하세요. 무한하지 않고 요청이 많은 경우 이 값을 늘려야 이 오류를 피할 수 잇습니다. |
| 503 |1001 |애플리케이션을 재활용 중이므로 요청을 node.exe로 디스패치할 수 없습니다. 애플리케이션이 재활용된 후에야 요청이 정상적으로 처리됩니다. |
| 503 |1002 |win32 오류 코드로 실제 원인을 확인하세요. – 요청을 node.exe로 디스패치할 수 없습니다. |
| 503 |1003 |명명된 파이프 사용량이 너무 많음 – node.exe에서 CPU를 과도하게 사용 중인지 확인하세요. |

NODE.exe에 `NODE_PENDING_PIPE_INSTANCES`라는 설정이 있습니다. Azure App Service에서 이 값은5000으로 설정됩니다. 따라서 node.exe는 명명된 파이프에서 한 번에 5000개의 요청만 받아들일 수 있습니다. 이 값은 Azure App Service에서 실행 중인 대부분의 노드 애플리케이션에서 충분합니다. `NODE_PENDING_PIPE_INSTANCES`에 대한 값이 높으므로 Azure App Service에 503.1003이 표시되지 않아야 합니다.

## <a name="more-resources"></a>추가 리소스

Azure App Service에서 다음 링크를 따라 node.js 애플리케이션에 대해 자세히 알아보세요.

* [Azure App Service에서 Node.js 웹앱 시작](app-service-web-get-started-nodejs.md)
* [Azure App Service에서 Node.js 웹앱을 디버그하는 방법](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Azure 애플리케이션에 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js 개발자 센터](../nodejs-use-node-modules-azure-apps.md)
* [Super 암호 Kudu 디버그 콘솔 탐색](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
