---
title: App Service의 운영 체제 기능 - Azure
description: Azure App Service에서 웹앱, 모바일 앱 백 엔드, API 앱에 사용할 수 있는 OS 기능에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e5ab6651503766844b2aeef1849bffff9cf4d7bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835510"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Azure App Service의 운영 체제 기능
이 문서에서는 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에서 실행되는 모든 Windows 앱에서 사용할 수 있는 일반적인 기준 운영 체제 기능을 설명합니다. 이 기능에는 파일, 네트워크, 레지스트리 액세스, 진단 로그 및 이벤트가 포함됩니다. 

> [!NOTE] 
> App Service의 [Linux 앱](containers/app-service-linux-intro.md)은 자체 컨테이너에서 실행됩니다. 호스트 운영 체제에 대한 액세스는 허용되지 않으며, 컨테이너에 대한 루트 액세스 권한이 제공되지 않습니다. 마찬가지로, [Windows 컨테이너에서 실행되는 앱](app-service-web-get-started-windows-container.md)의 경우 컨테이너에 대한 관리자 액세스 권한은 있지만 호스트 운영 체제에 대한 액세스 권한은 없습니다. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service 계획 계층
App Service는 다중 테넌트 호스팅 환경에서 고객 앱을 실행합니다. **무료** 및 **공유** 계층에서 배포된 앱은 공유 가상 머신에서 작업자 프로세스로 실행되고, **Standard** 및 **Premium** 계층으로 배포된 앱은 단일 고객과 연관 있는 앱 전용 가상 머신에서 실행됩니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

App Service는 서로 다른 계층 간의 원활한 크기 조정 환경을 지원하기 때문에, App Service 앱에 적용되는 보안 구성이 동일하게 유지됩니다. 따라서 App Service가 서로 다른 계층 간에 전환될 때 앱이 갑자기 다르게 작동하여 예기치 못한 방식으로 장애가 발생하는 일이 없습니다.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>개발 프레임워크
App Service 가격 책정 계층은 앱에서 사용할 수 있는 계산 리소스(CPU, 디스크 스토리지, 메모리, 네트워크 송신)의 양을 제어합니다. 하지만 앱에서 사용할 수 있는 프레임워크 기능의 범위는 크기 조정 계층에 관계없이 동일하게 유지됩니다.

App Service는 ASP.NET, 클래식 ASP, node.js, PHP, Python을 포함하여 다양한 개발 프레임워크를 지원합니다. 이 프레임워크는 모두 IIS 내에서 확장으로 실행됩니다. 보안 구성을 간소화하고 일반화하기 위해 App Service 앱은 일반적으로 다양한 개발 프레임워크를 기본 설정으로 실행합니다. 개별 개발 프레임워크별로 API 노출 영역 및 기능을 사용자 지정하여 앱을 구성하는 접근 방식을 사용할 수도 있습니다. 대신 App Service는 앱의 개발 프레임워크에 관계없이 일반적인 기준 운영 체제 기능을 사용하여 보다 일반적인 접근 방식을 취합니다.

다음 섹션에서는 App Service 앱에 사용할 수 있는 일반적인 종류의 운영 체제 기능이 요약되어 있습니다.

<a id="FileAccess"></a>

## <a name="file-access"></a>파일 액세스
App Service에는 로컬 드라이브와 네트워크 드라이브를 포함한 다양한 드라이브가 있습니다.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>로컬 드라이브
근본적으로 App Service는 Azure PaaS(Platform as a Service) 인프라에서 실행되는 서비스입니다. 따라서 가상 머신에 "부착된" 로컬 드라이브는 Azure에서 실행되는 모든 작업자 역할에서 사용할 수 있는 것과 동일한 드라이브 종류입니다. 다음 내용이 포함됩니다.

- 운영 체제 드라이브(D:\ 드라이브)
- App Service에서 단독으로 사용하며 고객이 액세스할 수 없는 Azure 패키지 cspkg 파일을 포함하는 애플리케이션 드라이브
- VM의 크기에 따라 크기가 달라지는 "user" 드라이브(C:\ 드라이브) 

애플리케이션이 커질수록 디스크 사용률을 모니터링하는 것이 중요합니다. 디스크 할당량에 도달하면 애플리케이션에 부정적인 영향을 줄 수 있습니다. 예를 들면 다음과 같습니다. 

- 앱이 디스크 공간 부족을 나타내는 오류를 throw할 수 있습니다.
- Kudu 콘솔로 이동하면 디스크 오류가 표시될 수 있습니다.
- VSTS 또는 Visual Studio의 배포가 `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)`로 인해 실패할 수 있습니다.
- 앱 성능이 저하될 수 있습니다.

<a id="NetworkDrives"></a>

### <a name="network-drives-aka-unc-shares"></a>네트워크 드라이브(일명 UNC 공유)
앱 배포 및 유지 관리를 간단하게 만드는 App Service의 고유한 측면 중 하나는 모든 사용자 콘텐츠가 UNC 공유 집합에 저장된다는 점입니다. 이 모델은 부하가 분산된 여러 개의 서버가 있는 온-프레미스 웹 호스팅 환경에서 사용되는 일반적인 콘텐츠 저장 패턴에 잘 매핑됩니다. 

App Service 내에는 각 데이터 센터에서 만들어진 다수의 UNC 공유가 있습니다. 각 데이터 센터에서 모든 고객의 사용자 콘텐츠가 차지하는 비율이 각 UNC 공유에 할당됩니다. 뿐만 아니라 단일 고객의 구독에 대한 모든 파일 콘텐츠는 항상 동일한 UNC 공유에 보관됩니다. 

Azure 서비스가 작동하는 방식으로 인해 UNC 공유를 호스트하는 특정 가상 머신은 시간이 지남에 따라 변경됩니다. 가상 머신은 일반적인 Azure 작동 과정에서 채택되거나 채택되지 않으면서, UNC 공유는 다양한 가상 머신에 탑재될 것이 확실합니다. 이 때문에 앱은 UNC 파일 경로의 컴퓨터 정보가 시간이 지남에 따라 안정성을 유지할 것으로 강력하게 가정해서는 안 됩니다. 대신, App Service가 제공하는 간편한 *faux* 절대 경로인 **D:\home\site**를 사용해야 합니다. 이 가짜 절대 경로를 사용하면 앱 및 사용자를 알 수 없는 이동식 방법으로 고유 앱을 참조할 수 있습니다. **D:\home\site**를 사용하여 전송별로 새로운 절대 경로를 구성하지 않고 앱 간에 공유 파일을 전송할 수 있습니다.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>앱에 부여되는 파일 액세스 형식
각 고객의 구독에는 데이터 센터 내의 특정 UNC 공유에 예약된 디렉터리 구조가 있습니다. 고객에게는 특정 데이터 센터 내에서 만든 여러 개의 앱이 있을 수 있으므로, 단일 고객 구독에 속한 모든 디렉터리는 동일한 UNC 공유에 만들어집니다. 이 공유는 콘텐츠, 오류 및 진단 로그, 소스 제어에서 만들어진 이전 버전 앱 등에 해당하는 디렉터리를 포함할 수 있습니다. 예상대로 고객의 앱 디렉터리는 앱의 애플리케이션 코드가 런타임에 읽고 쓸 수 있습니다.

앱을 실행하는 가상 머신에 설치된 로컬 드라이브에서 App Service는 C:\ 드라이브의 일정 공간을 앱 특정 임시 로컬 저장소로 예약합니다. 앱은 고유 임시 로컬 저장소를 완전히 읽고 쓸 수 있지만, 이 저장소는 사실상 애플리케이션 코드에서 직접적으로 사용되도록 만들어지지 않았습니다. IIS 및 웹 애플리케이션 프레임워크를 위한 임시 파일 저장소를 제공하기 위한 것입니다. 또한 App Service는 각 앱이 사용할 수 있는 임시 로컬 저장소의 양을 제한하여 개별 앱이 과도한 양의 로컬 파일 저장소를 소비하지 못하도록 합니다.

App Service가 임시 로컬 저장소를 사용하는 방법을 두 가지 예로 들면 임시 ASP.NET 파일용 디렉터리와 IIS 압축 파일용 디렉터리 등입니다. ASP.NET 컴파일 시스템은 임시 컴파일 캐시 위치로 "Temporary ASP.NET Files" 디렉터리를 사용합니다. IIS는 압축된 응답 출력을 저장하는 데 "IIS Temporary Compressed Files" 디렉터리를 사용합니다. 이 두 가지 파일 사용법은(다른 사용법도 포함) 모두 App Service에서 앱별 임시 로컬 저장소로 다시 매핑됩니다. 이렇게 다시 매핑되면 해당 기능이 예상대로 지속됩니다.

App Service의 각 앱은 "애플리케이션 풀 ID"라는 권한이 낮은 임의의 고유 작업자 프로세스 ID로 실행됩니다. 이 ID에 대한 자세한 내용은 [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities)를 참조하세요. 애플리케이션 코드는 운영 체제 드라이브(D:\ 드라이브)에 대한 기본적인 읽기 전용 액세스에 이 ID를 사용합니다. 따라서 애플리케이션 코드는 일반적인 디렉터리 구조를 나열하고 운영 체제 드라이브에 있는 일반 파일을 읽을 수 있습니다. 이는 다소 광범위한 수준의 액세스 권한으로 보일 수도 있지만, Azure 호스팅 서비스에서 작업자 역할을 프로비전하고 드라이브 콘텐츠를 읽을 때 동일한 디렉터리 및 파일에 액세스할 수 있습니다. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>여러 인스턴스의 파일 액세스
홈 디렉터리에는 앱의 콘텐츠가 포함되어 있고, 애플리케이션 코드는 홈 디렉터리에 쓸 수 있습니다. 앱이 여러 인스턴스에서 실행되는 경우 모든 인스턴스에 동일한 디렉터리가 표시되도록 홈 디렉터리가 모든 인스턴스 간에 공유됩니다. 예를 들어 업로드된 파일을 홈 디렉터리에 저장하는 앱의 경우, 모든 인스턴스에서 해당 파일을 즉시 사용할 수 있습니다. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>네트워크 액세스
애플리케이션 코드는 외부 서비스를 노출하는 인터넷 액세스 엔드포인트에 아웃바운드 네트워크를 연결하는 데 TCP/IP 및 UDP 기반 프로토콜을 사용할 수 있습니다. 앱은 이 동일한 프로토콜을 사용하여 Azure 내의 서비스에 연결할 수 있습니다.&#151;예를 들어 SQL Database에 대한 HTTPS 연결을 설정하면 됩니다.

앱이 하나의 로컬 루프백 연결을 설정하고 앱이 해당 로컬 루프백 소켓을 수신 대기하도록 만드는 제한된 기능도 있습니다. 이 기능은 주로 기능의 일부로 로컬 루프백 소켓을 수신 대기하는 앱을 사용하도록 설정하기 위한 것입니다. 각 앱에서 "비공개" 루프백 연결을 볼 수 있습니다 앱 "A"는 앱 "B"가 설정한 로컬 루프백 소켓을 수신할 수 없습니다.

전체적으로 앱을 실행하는 다양한 프로세스 사이의 IPC(프로세스 간 통신) 메커니즘으로 명명된 파이프도 지원됩니다. 예를 들어 IIS FastCGI 모듈은 PHP 페이지를 실행하는 개별 프로세스를 조정하는 데 명명된 파이프를 사용합니다.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>코드 실행, 프로세스 및 메모리
앞에서 설명했듯이, 앱은 임의의 애플리케이션 풀 ID를 사용하여 권한이 낮은 작업자 프로세스 내부에서 실행됩니다. 애플리케이션 코드는 작업자 프로세스 및 CGI 프로세스나 다른 애플리케이션에 의해 생성될 수 있는 하위 프로세스와 연결된 메모리 공간에 액세스할 수 있습니다. 하지만 하나의 앱이 동일한 가상 컴퓨터에 있더라도 다른 앱의 메모리나 데이터에 액세스할 수는 없습니다.

앱은 지원되는 웹 개발 프레임워크에서 작성된 스크립트나 페이지를 실행할 수 있습니다. App Service는 웹 프레임워크 설정을 더 제한적인 모드로 구성하지 않습니다. 예를 들어 App Service에서 실행되는 ASP.NET 앱은 더 제한적인 신뢰 모드와 달리 "완전" 신뢰 모드로 실행됩니다. 클래식 ASP 및 ASP.NET 모두를 포함한 웹 프레임워크는 Windows 운영 체제에 기본적으로 등록된 ADO(ActiveX Data Object)와 같은 In-Process COM 구성 요소(Out of Process COM 구성 요소 아님)를 호출할 수 있습니다.

앱은 임의의 코드를 생성하고 실행할 수 있습니다. 앱이 명령 셸 생성이나 PowerShell 스크립트 실행과 같은 작업을 수행하는 것도 가능합니다. 하지만 앱에서 임의의 코드 및 프로세스를 생성할 수 있지만 실행 프로그램 및 스크립트는 여전히 상위 애플리케이션 풀에 부여된 권한으로 제한됩니다. 예를 들어 앱은 아웃바운드 HTTP 호출을 수행하는 실행 파일을 생성할 수 있지만, 이 실행 파일은 가상 컴퓨터 IP 주소를 NIC에서 바인딩 해제할 수 없습니다. 아웃바운드 네트워크 호출은 권한이 낮은 코드에 허용되지만 가상 머신에서 네트워크 설정을 다시 구성하려면 관리자 권한이 필요합니다.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>진단 로그 및 이벤트
로그 정보는 일부 앱이 액세스하는 또 하나의 데이터 집합입니다. App Service에서 실행되는 코드에 사용할 수 있는 로그 정보의 종류에는 앱에서 생성된 진단 및 로그 정보(앱에서 쉽게 액세스할 수 있음)가 포함됩니다. 

예를 들어 활성 앱에서 생성된 W3C HTTP 로그는 앱에 대해 만들어진 네트워크 공유 위치에 있는 로그 디렉터리에서 사용할 수 있거나 고객이 스토리지에 W3C를 기록하도록 설정한 경우 Blob Storage에서 사용할 수 있습니다. 두 번째 옵션을 사용하면 네트워크 공유와 관련된 파일 스토리지 제한을 초과할 위험 없이 대량의 로그를 수집할 수 있습니다.

이와 비슷하게 .NET 추적 및 진단 인프라를 사용하여 .NET 앱의 실시간 진단 정보를 기록할 수 있으며, 여기서도 추적 정보를 앱의 네트워크 공유에 쓰거나 Blob Storage 위치에 쓰는 옵션이 사용됩니다.

앱에서 사용할 수 없는 진단 로깅 및 추적의 영역은 Windows ETW 이벤트 및 일반적인 Windows 이벤트 로그(예: 시스템, 애플리케이션 및 보안 이벤트 로그)입니다. ETW 추적 정보는 잠재적으로 시스템 전체에서 볼 수 있으므로(올바른 ACL 사용) ETW 이벤트에 대한 읽기 및 쓰기 액세스는 차단됩니다. 개발자는 읽기/쓰기 ETW 이벤트 및 일반적인 Windows 이벤트 로그에 대한 API 호출이 작동하는 것처럼 보이지만 이는 App Service가 성공한 것처럼 보이도록 호출을 "가장"하기 때문이라는 것을 알아챌 수 있습니다. 실제로 애플리케이션 코드는 이 이벤트 데이터에 액세스할 수 없습니다.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>레지스트리 액세스
앱은 실행 가상 머신의 레지스트리의 상당 부분(전체는 아님)에 읽기 전용으로 액세스할 수 있습니다. 즉, 앱이 로컬 사용자 그룹에 대한 읽기 전용 액세스를 허용하는 레지스트리 키에 액세스할 수 없음을 의미합니다. 현재 읽기 또는 쓰기 액세스가 지원되지 않는 레지스트리의 하나의 영역은 HKEY\_CURRENT\_USER Hive입니다.

사용자별 레지스트리 키 액세스를 포함하여 레지스트리에 대한 쓰기 액세스는 차단됩니다. 앱의 관점에서 보면, Azure 환경에서는 여러 가상 머신에서 앱을 마이그레이션할 수 있고 마이그레이션하므로 레지스트리에 대한 쓰기 액세스에 의존해서는 안 됩니다. 앱이 사용할 수 있는 유일한 쓰기 가능한 영구 저장소는 App Service UNC 공유에 저장된 앱별 콘텐츠 디렉터리 구조입니다. 

## <a name="remote-desktop-access"></a>원격 데스크톱 액세스

App Service는 VM 인스턴스에 대한 원격 데스크톱 액세스를 제공하지 않습니다.

## <a name="more-information"></a>자세한 정보

[Azure App Service 샌드박스](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) - App Service의 실행 환경에 대한 최신 정보입니다. 이 페이지는 App Service 개발 팀에서 직접 유지 관리합니다.

