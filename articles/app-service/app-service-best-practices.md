---
title: 모범 사례
description: Azure App Service에서 실행 되는 앱에 대 한 모범 사례 및 일반적인 문제 해결 시나리오를 알아봅니다.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: 0a25ae41a5f4ed73148f629799ca4865d756a769
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962454"
---
# <a name="best-practices-for-azure-app-service"></a>Azure App Service에 대한 모범 사례
이 문서는 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)를 사용하는 모범 사례를 요약합니다. 

## <a name="colocation"></a><a name="colocation"></a>공동 배치
웹앱과 같은 솔루션을 작성하는 Azure 리소스와 데이터베이스가 다른 지역에 있는 경우 다음과 같은 영향을 미칠 수 있습니다.

* 리소스 간 통신에 대한 대기 시간 증가
* [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-transfers)에서 설명한 것과 같이 지역 간 아웃바운드 데이터 전송에 대한 금전적인 청구

동일한 지역의 공동 배치는 웹앱과 같은 솔루션과 콘텐츠 또는 데이터를 저장하는 데 사용되는 데이터베이스 또는 스토리지 계정을 작성하는 Azure 리소스에 가장 적합합니다. 리소스를 만들 때 동일한 Azure 지역에 있어서는 안 되는 특정 비즈니스 또는 디자인 사유가 없는 한 동일한 Azure 지역에 있어야 합니다. 프리미엄 App Service 계획 앱에서 현재 사용할 수 있는 [App Service 복제 기능](app-service-web-app-cloning.md)을 사용하여 App Service 앱을 데이터베이스와 동일한 지역으로 이동할 수 있습니다.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>앱에서 예상보다 더 많은 메모리를 사용하는 경우
앱에서 모니터링 또는 서비스 권장 사항을 통해 표시 되는 것 보다 더 많은 메모리를 사용 하는 경우 [App Service 자동 복구 기능](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)을 고려 합니다. 자동 복구 기능에 대한 옵션 중 하나는 메모리 임계값을 기반으로 하는 사용자 지정 작업을 수행하는 것입니다. 작업은 작업자 프로세스를 재활용하여 자리 완화에 대한 메모리 덤프를 통해 전자 메일 알림에서 조사로 범위를 확장합니다. 자동 복구는 [App Service 지원 사이트 확장](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)에 대한 이 블로그 게시물에 설명된 대로 web.config 및 친숙한 사용자 인터페이스를 통해 구성될 수 있습니다.   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>앱에서 예상보다 더 많은 CPU를 사용하는 경우
앱에서 모니터링 또는 서비스 권장 사항을 통해 표시된 대로 예상보다 더 많은 CPU를 사용하거나 반복되는 CPU 스파이크를 경험하는 경우 App Service 계획 강화 또는 확장을 고려합니다. 애플리케이션이 상태 저장인 경우 강화가 유일한 옵션인 반면 애플리케이션이 상태 비저장인 경우 확장을 통해 더 많은 유연성 및 더 높은 확장 가능성을 줍니다. 

"상태 저장" 및 "상태 비저장" 응용 프로그램에 대 한 자세한 내용은 [Azure App Service에서 확장 가능한 종단 간 다중 계층 응용 프로그램 계획](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid)비디오를 시청할 수 있습니다. App Service 크기 조정 및 자동 크기 조정 옵션에 대한 자세한 내용은 [Azure App Service에서 웹앱 크기 조정](manage-scale-up.md)을 참조하세요.  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>소켓 리소스를 모두 사용한 경우
아웃바운드 TCP 연결을 소모하는 일반적인 이유는 TCP 연결을 다시 사용하도록 구현되지 않는 클라이언트 라이브러리의 사용되지 않는 HTTP - Keep-Alive와 같은 높은 수준의 프로토콜의 경우입니다. 효율적인 아웃바운드 재사용에 대한 코드에서 구성 또는 액세스할 수 있도록 App Service 계획의 앱에서 참조하는 각 라이브러리에 대한 설명서를 검토하세요. 또한 연결 누수를 방지하도록 올바른 생성 및 릴리스 또는 정리에 대한 라이브러리 설명서 지침을 따릅니다. 이러한 클라이언트 라이브러리 조사 진행 중 여러 인스턴스로 확장하여 영향을 완화할 수 있습니다.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js 및 발신 HTTP 요청
Node.js 및 다수의 발신 HTTP 요청을 수행하는 경우, HTTP - Keep-Alive를 처리하는 것이 중요합니다. [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` 패키지를 사용하면 코드로 더 쉽게 처리할 수 있습니다.

처리기에서는 아무 작업도 수행하지 않더라도 `http` 응답을 항상 처리해야 합니다. 응답을 제대로 처리하지 않으면 사용할 수 있는 소켓이 없어지기 때문에 애플리케이션이 결국 멈추게 됩니다.

예를 들어 `http` 또는 `https` 패키지를 사용하여 작업하는 경우:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

다중 코어가 있는 컴퓨터에 Linux의 App Service에서 실행 중인 경우 또 다른 좋은 방법은 PM2를 사용하여 여러 Node.js 프로세스를 시작하여 애플리케이션을 실행하는 것입니다. 이 작업은 컨테이너에 시작 명령을 지정하여 수행할 수 있습니다.

예를 들어 4개의 인스턴스를 시작하려면:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>앱 백업이 실패하는 경우
앱 백업이 실패하는 가장 일반적인 이유는 잘못된 스토리지 설정 및 잘못된 데이터베이스 구성 등 두 가지가 있습니다. 이러한 오류는 일반적으로 스토리지 또는 데이터베이스 리소스를 변경하거나 이러한 리소스에 액세스하는 방법을 변경하는 경우에 발생합니다(예: 백업 설정에서 선택한 데이터베이스가 업데이트된 자격 증명). 백업은 일반적으로 일정에 따라 실행되며, 스토리지(백업된 파일을 출력하는 경우) 및 데이터베이스(백업에 포함될 콘텐츠를 복사하고 읽는 경우)에 대한 액세스 권한이 필요합니다. 이러한 리소스 중 하나에 액세스하는 데 실패하면 일관된 백업 실패가 발생합니다. 

백업 실패가 발생하는 경우 가장 최근의 결과를 검토하여 어떤 유형의 오류가 발생하는지를 이해해야 합니다. 스토리지 액세스 실패의 경우 백업 구성에 사용된 스토리지 설정을 검토하고 업데이트하세요. 데이터베이스 액세스에 실패한 경우 연결 문자열을 앱 설정의 일부로 검토하고 업데이트합니다. 백업 구성을 업데이트하도록 진행하여 필요한 데이터베이스를 적절히 포함합니다. 앱 백업에 대한 자세한 내용은 [Azure App Service에서 웹앱 백업](manage-backup.md)을 참조하세요.

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>새 Node.js 앱이 Azure App Service에 배포되는 경우
Node.js 앱에 대한 Azure App Service 기본 구성은 가장 일반적인 앱 요구에 가장 적합하게 지정되었습니다. Node.js 앱 구성이 개별화된 조정부터 성능 향상, CPU/메모리/네트워크 리소스에 대한 리소스 사용 최적화 등의 다양한 이점을 얻으려면 [Azure App Service의 Node 애플리케이션에 대한 모범 사례 및 문제 해결 가이드](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)를 참조하세요. 이 문서에서는 Node.js 앱에 대해 구성해야 하는 iisnode 설정에 대해 설명하고, 앱이 직면할 수 있는 다양한 시나리오 또는 문제에 대해 설명하고, 이러한 문제를 해결하는 방법을 보여 줍니다.


## <a name="next-steps"></a>다음 단계
모범 사례에 대 한 자세한 내용은 [App Service 진단을](./overview-diagnostics.md) 방문 하 여 리소스와 관련 하 여 조치 가능한 모범 사례를 확인 하세요.

- [Azure Portal](https://portal.azure.com)에서 웹 앱으로 이동 합니다.
- 왼쪽 탐색에서 **문제 진단 및 해결** 을 클릭 하면 App Service 진단이 열립니다.
- **모범 사례** 홈 페이지 타일을 선택 합니다.
- 모범 사례에 **대 한 모범 사례** 를 클릭 하 여 모범 사례에 대 한 응용 프로그램의 현재 상태를 확인 하는 **최적의 구성을 위한** 모범 사례를 & 합니다.

또한이 링크를 사용 하 여 리소스에 대 한 App Service 진단을 직접 열 수 있습니다 `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .