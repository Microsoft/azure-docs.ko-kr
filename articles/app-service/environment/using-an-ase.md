---
title: App Service Environment 사용 및 관리
description: App Service Environment에서 앱을 만들고, 게시 하 고, 크기를 조정 하는 방법을 알아봅니다. 이 문서에서 일반적인 작업을 모두 찾습니다.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5e3cb07730aafed7d1c339f543e7fb09fe956cab
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961910"
---
# <a name="use-an-app-service-environment"></a>App Service 환경 사용

ASE (App Service Environment)는 고객의 Azure Virtual Network 인스턴스에서 서브넷에 Azure App Service를 배포 하는 것입니다. ASE는 다음과 같은 요소로 구성 됩니다.

- **프런트 엔드**: HTTP 또는 HTTPS가 App Service Environment에서 종료 되는 위치
- **작업자**: 앱을 호스트 하는 리소스
- **데이터베이스**: 환경을 정의 하는 정보를 저장 합니다.
- **저장소**: 고객이 게시 한 앱을 호스트 하는 데 사용 됩니다.

앱 액세스를 위해 외부 또는 내부 VIP (가상 IP)를 사용 하 여 ASE를 배포할 수 있습니다. 외부 VIP를 사용 하는 배포를 일반적으로 *외부 ASE*라고 합니다. 내부 VIP를 사용 하는 배포는 ILB (내부 부하 분산 장치)를 사용 하기 때문에 *ILB ASE* 라고 합니다. ILB ASE에 대해 자세히 알아보려면 [ILB ASE 만들기 및 사용][MakeILBASE]을 참조하세요.

## <a name="create-an-app-in-an-ase"></a>ASE에서 앱 만들기

ASE에서 앱을 만들려면 일반적으로 앱을 만들 때와 동일한 프로세스를 사용 하지만 몇 가지 작은 차이점이 있습니다. 새 App Service 계획을 만들 때는 다음을 수행합니다.

- 앱을 배포하기 위한 지리적 위치를 선택하는 대신 ASE를 위치로 선택합니다.
- ASE에서 만든 모든 App Service 계획은 격리 된 가격 책정 계층에만 있을 수 있습니다.

ASE가 없는 경우 [App Service Environment 만들기][MakeExternalASE]의 지침에 따라 만들 수 있습니다.

ASE에서 앱을 만들려면

1. **리소스 만들기**  >  **웹 + 모바일**  >  **웹 앱**을 선택 합니다.

1. 앱의 이름을 입력합니다. ASE에서 App Service 계획을 이미 선택한 경우 앱의 도메인 이름에 ASE의 도메인 이름이 반영 됩니다.

    ![앱 이름 선택][1]

1. 구독을 선택합니다.

1. 새 리소스 그룹의 이름을 입력하거나 **기존 항목 사용**을 선택하고 드롭다운 목록에서 이름을 선택합니다.

1. OS를 선택합니다.

1. ASE에서 기존 App Service 계획을 선택하거나 다음 단계를 통해 새 App Service 계획을 만듭니다.

    a. Azure Portal 왼쪽 메뉴에서 **리소스 만들기 > 웹 앱**을 선택 합니다.

    b. 구독을 선택합니다.

    다. 리소스 그룹을 선택 하거나 만듭니다.

    d. 웹 앱의 이름을 입력 합니다.

    e. **코드** 또는 **dockercontainer**를 선택 합니다.

    f. 런타임 스택을 선택 합니다.

    g. **Linux** 또는 **Windows**를 선택합니다. 

    h. **지역** 드롭다운 목록에서 ASE를 선택 합니다. 

    i. 새 App Service 계획을 선택 하거나 만듭니다. 새 App Service 계획을 만드는 경우 적절 한 **격리** 된 SKU 크기를 선택 합니다.

    ![격리 가격 책정 계층][2]

    > [!NOTE]
    > Linux 앱 및 Windows 앱은 동일한 App Service 계획에 있을 수 없지만 동일한 App Service Environment에 있을 수 있습니다.
    >

1. **검토 + 만들기**를 선택 하 고 정보가 올바른지 확인 한 다음 **만들기**를 선택 합니다.

## <a name="how-scale-works"></a>확장이 작동하는 방식

모든 App Service 앱은 App Service 계획에서 실행됩니다. App Service 환경은 App Service 계획을 포함하고 App Service 계획은 앱을 포함합니다. 앱의 크기를 조정 하는 경우 동일한 계획의 App Service 계획과 모든 앱의 크기를 조정 합니다.

App Service 계획의 크기를 조정 하는 경우 필요한 인프라가 자동으로 추가 됩니다. 인프라를 추가 하는 동안 작업 크기를 조정 하는 데 시간이 지연 됩니다. 여러 크기 조정 작업을 순서 대로 수행 하는 경우 첫 번째 인프라 크기 조정 요청이 처리 되 고 나머지는 큐에 대기 됩니다. 첫 번째 크기 조정 작업이 완료 되 면 다른 인프라 요청은 모두 함께 작동 합니다. 그리고 인프라가 추가 될 때 App Service 요금제가 적절 하 게 할당 됩니다. 새 App Service 계획을 만드는 작업은 추가 하드웨어를 요청 하므로 크기를 조정 하는 작업입니다.

다중 테 넌 트 App Service에서는 리소스 풀을 쉽게 지원할 수 있기 때문에 크기 조정이 즉시 실행 됩니다. ASE에는 이러한 버퍼가 없으며 리소스가 필요에 따라 할당 됩니다.

ASE에서 App Service 계획을 최대 100 개의 인스턴스로 확장할 수 있습니다. ASE에는 해당 ASE의 모든 App Service 계획에서 최대 201 개의 인스턴스가 포함 될 수 있습니다.

## <a name="ip-addresses"></a>IP 주소

앱에 전용 IP 주소를 할당할 수 App Service. 이 기능은 [Azure App Service에 기존 사용자 지정 TLS/SSL 인증서 바인딩][ConfigureSSL]에 설명 된 대로 IP 기반 SSL을 구성한 후에 사용할 수 있습니다. ILB ASE에서 IP 기반 SSL에 사용할 IP 주소를 더 추가할 수 없습니다.

외부 ASE를 사용 하 여 다중 테 넌 트 App Service와 동일한 방식으로 앱에 대 한 IP 기반 SSL을 구성할 수 있습니다. ASE에는 항상 최대 30 개의 IP 주소와 하나의 예비 주소가 있습니다. 하나를 사용할 때마다 주소를 항상 사용할 수 있도록 다른가 추가 됩니다. 다른 IP 주소를 할당 하려면 시간 지연이 필요 합니다. 이렇게 지연 하면 IP 주소를 연속으로 추가할 수 없습니다.

## <a name="front-end-scaling"></a>프런트 엔드 확장

App Service 계획을 확장 하는 경우 작업자를 지원 하기 위해 자동으로 추가 됩니다. 모든 ASE는 프런트 엔드 2개를 포함하는 상태로 작성됩니다. 프런트 엔드는 모든 15 App Service 계획 인스턴스 집합에 대해 하나의 프런트 엔드 속도로 자동으로 확장 됩니다. 예를 들어 각각 5 개의 인스턴스를 포함 하는 3 개의 App Service 계획이 있는 경우 총 15 개의 인스턴스와 3 개의 프런트 엔드가 있습니다. 총 30 개의 인스턴스로 크기를 조정 하는 경우 4 개의 프런트 엔드가 있습니다. 이 패턴은 규모 확장 시 계속 됩니다.

기본적으로 할당 되는 프런트 엔드 수는 보통 로드에 적합 합니다. 5 개 인스턴스마다 하나의 프런트 엔드로 비율을 낮출 수 있습니다. 프런트 엔드의 크기를 변경할 수도 있습니다. 기본적으로 단일 코어입니다. Azure Portal에서 해당 크기를 2 개 또는 4 개 코어로 변경할 수 있습니다.

비율 또는 프런트 엔드 크기를 변경 하는 비용이 청구 됩니다. 자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요. ASE의 로드 용량을 개선 하려는 경우 확장 비율을 조정 하기 전에 먼저 2 코어 프런트 엔드로 크기를 조정 하 여 더 향상 된 기능을 얻을 수 있습니다. 프런트 엔드의 핵심 크기를 변경 하면 ASE가 업그레이드 되 고 정기적인 업무 시간 외에 수행 되어야 합니다.

프런트 엔드 리소스는 ASE의 HTTP/HTTPS 엔드포인트입니다. 기본 프런트 엔드 구성에서는 프런트 엔드당 메모리 사용량이 일관되게 약 60%입니다. 프런트 엔드의 크기를 조정 하는 주된 이유는 주로 HTTPS 트래픽으로 구동 되는 CPU 사용입니다.

## <a name="app-access"></a>앱 액세스

외부 ASE에서 앱을 만드는 데 사용 되는 도메인 접미사는 *입니다. &lt; asename &gt; . p.azurewebsites.net*. ASE 이름이 _external ase_ 이 고 해당 ase에서 _contoso_ 라는 앱을 호스트 하는 경우 다음 url에서 해당 앱에 연결 합니다.

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

외부 ASE를 만드는 방법에 대 한 자세한 내용은 [App Service Environment 만들기][MakeExternalASE]를 참조 하세요.

ILB ASE에서 앱을 만드는 데 사용 되는 도메인 접미사는 *입니다. &lt; asename &gt; . appserviceenvironment.net*. ASE가 _ilb-ase_ 로 명명 되 고 해당 ase에서 _contoso_ 라는 앱을 호스트 하는 경우 다음 url에서 해당 앱에 연결 합니다.

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

ILB ASE를 만드는 방법에 대 한 자세한 내용은 [ILB Ase 만들기 및 사용][MakeILBASE]을 참조 하세요.

SCM URL은 Kudu 콘솔에 액세스 하거나 웹 배포를 사용 하 여 앱을 게시 하는 데 사용 됩니다. Kudu 콘솔에 대한 자세한 내용은 [Azure App Service용 Kudu 콘솔][Kudu]을 참조하세요. Kudu 콘솔은 디버깅, 파일 업로드, 파일 편집 등을 위한 웹 UI를 제공합니다.

### <a name="dns-configuration"></a>DNS 구성 

외부 ASE를 사용 하는 경우 ASE에서 만든 앱이 Azure DNS 등록 됩니다. 외부 ASE에는 앱을 공개적으로 사용할 수 있는 추가 단계가 없습니다. ILB ASE를 사용 하 여 사용자 고유의 DNS를 관리 해야 합니다. 사용자의 DNS 서버에서 또는 Azure DNS 개인 영역을 사용 하 여이 작업을 수행할 수 있습니다.

ILB ASE를 사용 하 여 자체 DNS 서버에서 DNS를 구성 하려면:

1. &lt;ASE 이름&gt;.appserviceenvironment.net 영역 만들기
1. 해당 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 ILB IP 주소를 가리키는 A 레코드 만들기
1. &lt;ASE 이름&gt;.appserviceenvironment.net에 scm이라는 영역 만들기
1. scm 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기

Azure DNS 개인 영역에서 DNS를 구성 하려면:

1. ASE 이름이 appserviceenvironment.net 인 Azure DNS 개인 영역을 만듭니다 &lt; &gt; .
1. 해당 영역에 *로 ILB IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에 @로 ILB IP 주소를 가리키는 A 레코드 만들기
1. 해당 영역에서 ILB IP 주소에 * scm을 가리키는 A 레코드를 만듭니다.

ASE 기본 도메인 접미사에 대 한 DNS 설정은 해당 이름 으로만 액세스할 수 있도록 앱을 제한 하지 않습니다. ILB ASE의 앱에 대 한 유효성 검사 없이 사용자 지정 도메인 이름을 설정할 수 있습니다. 그런 다음 *contoso.net*이라는 영역을 만들려는 경우이를 수행 하 고 ILB IP 주소를 가리킬 수 있습니다. 사용자 지정 도메인 이름은 앱 요청에 대해서는 작동 하지만 scm 사이트의 경우에는 작동 하지 않습니다. Scm 사이트는 * &lt; appname &gt; . scm &lt; 에서만 사용할 수 있습니다. asename &gt; . appserviceenvironment.net*. 

이라는 영역 *입니다. &lt; &gt;appserviceenvironment.net* 는 전역적으로 고유 합니다. 2019 년 5 월 이전에는 고객이 ILB ASE의 도메인 접미사를 지정할 수 있었습니다. 도메인 접미사에 대해 *contoso.com* 를 사용 하려는 경우이 작업을 수행할 수 있으며 scm 사이트를 포함 합니다. 이 모델에는 다음을 비롯 한 과제가 있습니다. 기본 SSL 인증서 관리, scm 사이트에 Single Sign-On 부족 및 와일드 카드 인증서를 사용 하기 위한 요구 사항 ILB ASE 기본 인증서 업그레이드 프로세스도 중단 되어 응용 프로그램을 다시 시작 했습니다. 이러한 문제를 해결 하기 위해 ILB ASE 동작은 ASE 이름 및 Microsoft 소유의 접미사에 따라 도메인 접미사를 사용 하도록 변경 되었습니다. ILB ASE 동작은 2019 년 5 월 이후 ILB Ase 적용 됩니다. 기존 ILB Ase는 여전히 ASE의 기본 인증서와 해당 DNS 구성을 관리 해야 합니다.

## <a name="publishing"></a>게시

다중 테 넌 트 App Service와 마찬가지로 ASE에서 다음 메서드를 사용 하 여 게시할 수 있습니다.

- 웹 배포
- FTP
- CI (연속 통합)
- Kudu 콘솔에서 끌어서 놓기
- IDE (예: Visual Studio, Eclipse 또는 IntelliJ 아이디어)

외부 ASE를 사용 하는 경우 이러한 게시 옵션은 모두 동일한 방식으로 작동 합니다. 자세한 내용은 [Azure App Service의 배포][AppDeploy]를 참조하세요.

ILB ASE를 사용 하 여 게시 끝점은 ILB를 통해서만 사용할 수 있습니다. ILB는 Virtual Network의 ASE 서브넷에 있는 프라이빗 IP에 있습니다. ILB에 대 한 네트워크 액세스 권한이 없는 경우 해당 ASE에 앱을 게시할 수 없습니다. [ILB ASE 만들기 및 사용][MakeILBASE]에서 설명한 대로 시스템에서 앱에 대 한 DNS를 구성 해야 합니다. 이 요구 사항에는 SCM 끝점이 포함 됩니다. 끝점이 올바르게 정의 되지 않은 경우에는 게시할 수 없습니다. 또한 사용자의 Ide는 ILB에 대 한 네트워크 액세스 권한이 있어야 해당 사용자에 게 직접 게시할 수 있습니다.

추가 변경 없이, 게시 끝점은 인터넷에 액세스할 수 없기 때문에 GitHub 및 Azure DevOps와 같은 인터넷 기반 CI 시스템은 ILB ASE와 작동 하지 않습니다. ILB ASE가 포함 된 가상 네트워크에 자체 호스트 된 릴리스 에이전트를 설치 하 여 Azure DevOps에서 ILB ASE에 게시를 사용 하도록 설정할 수 있습니다. 또는 Dropbox와 같은 끌어오기 모델을 사용 하는 CI 시스템을 사용할 수도 있습니다.

ILB ASE의 앱에 대한 게시 엔드포인트에서는 ILB ASE가 만들어진 도메인을 사용합니다. 앱의 게시 프로필과 앱의 포털 창 ( **개요**  >  **필수** 및 **속성**)에서 볼 수 있습니다.

## <a name="storage"></a>스토리지

ASE에는 ASE의 모든 앱에 대해 1TB의 저장소가 있습니다. 격리 가격 SKU의 App Service 요금제는 250 GB로 제한 됩니다. ASE에서 250 GB의 저장소는 1TB 제한까지 App Service 계획 마다 추가 됩니다. 4 개 보다 많은 App Service 요금제를 사용할 수 있지만 1TB 제한을 초과 하는 저장소는 더 이상 추가 되지 않습니다.

## <a name="logging"></a>로깅

ASE를 Azure Monitor와 통합 하 여 ASE에 대 한 로그를 Azure Storage, Azure Event Hubs 또는 Log Analytics에 보낼 수 있습니다. 이러한 항목은 오늘 기록 됩니다.

| 상황 | 메시지 |
|---------|----------|
| ASE가 비정상입니다. | 잘못 된 가상 네트워크 구성으로 인해 지정 된 ASE가 비정상 상태입니다. 비정상 상태가 지속 되 면 ASE가 일시 중단 됩니다. 여기에 정의 된 지침을 참조 하세요 https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE 서브넷의 공간이 거의 부족 합니다. | 지정 된 ASE가 공간을 거의 벗어난 서브넷에 있습니다. {0}남은 주소가 있습니다. 이러한 주소가 모두 사용 되 면 ASE는 크기를 조정할 수 없습니다.  |
| ASE가 총 인스턴스 제한에 도달 함 | 지정 된 ASE가 ASE의 총 인스턴스 제한에 도달 했습니다. 현재 {0} 최대 201 인스턴스의 App Service 계획 인스턴스를 포함 합니다. |
| ASE가 종속성에 도달할 수 없습니다. | 지정 된 ASE에 연결할 수 없습니다 {0} .  여기에 정의 된 지침을 참조 하세요 https://docs.microsoft.com/azure/app-service/environment/network-info . |
| ASE가 일시 중지 되었습니다. | 지정한 ASE가 일시 중지 되었습니다. ASE 일시 중단은 계정 shortfall 또는 잘못 된 가상 네트워크 구성으로 인해 발생할 수 있습니다. 근본 원인을 해결 하 고 ASE를 다시 시작 하 여 트래픽을 계속 처리 합니다. |
| ASE 업그레이드가 시작 되었습니다. | 지정 된 ASE로의 플랫폼 업그레이드가 시작 되었습니다. 크기 조정 작업에서 지연이 발생 합니다. |
| ASE 업그레이드가 완료 되었습니다. | 지정 된 ASE로의 플랫폼 업그레이드가 완료 되었습니다. |
| 크기 조정 작업이 시작 되었습니다. | App Service 계획 ( {0} )의 확장이 시작 되었습니다. 원하는 상태: {1} I {2} 작업자.
| 크기 조정 작업이 완료 되었습니다. | App Service 계획 ( {0} )의 확장이 완료 되었습니다. 현재 상태: {1} I {2} 작업자. |
| 크기 조정 작업이 실패 했습니다. | App Service 계획 ( {0} )의 크기를 조정 하지 못했습니다. 현재 상태: {1} I {2} 작업자. |

ASE에서 로깅을 사용 하도록 설정 하려면:

1. 포털에서 **진단 설정**으로 이동 합니다.
1. **진단 설정 추가**를 선택 합니다.
1. 로그 통합에 대 한 이름을 제공 합니다.
1. 원하는 로그 대상을 선택 하 고 구성 합니다.
1. **Appservice환경 Platformlogs**를 선택 합니다.

![ASE 진단 로그 설정][4]

Log Analytics와 통합 하는 경우 ASE 포털에서 **로그** 를 선택 하 고 **Appservice환경 platformlogs**에 대 한 쿼리를 만들어 로그를 볼 수 있습니다. 로그는 ASE에 트리거를 트리거하는 이벤트가 있는 경우에만 내보내집니다. ASE에 이러한 이벤트가 없으면 로그가 없는 것입니다. Log Analytics 작업 영역에서 로그의 예제를 신속 하 게 보려면 ASE의 App Service 계획 중 하나를 사용 하 여 크기 조정 작업을 수행 합니다. 그런 다음 **Appservice환경 Platformlogs** 에 대해 쿼리를 실행 하 여 해당 로그를 볼 수 있습니다. 

**경고 만들기**

로그에 대 한 경고를 만들려면 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리][logalerts]의 지침을 따르세요. 개요:

* ASE 포털에서 경고 페이지를 엽니다.
* **새 경고 규칙** 선택
* Log Analytics 작업 영역으로 사용할 리소스를 선택 합니다.
* "Appservice환경 Platformlogs"와 같은 쿼리를 사용 하도록 사용자 지정 로그 검색을 사용 하 여 조건을 설정 합니다. 여기서 ResultDescription은 "크기 조정 시작" 또는 원하는 모든 항목을 포함 합니다. 임계값을 적절 하 게 설정 합니다. 
* 원하는 대로 작업 그룹을 추가 하거나 만듭니다. 작업 그룹은 전자 메일 또는 SMS 메시지 전송과 같은 경고에 대 한 응답을 정의 하는 위치입니다.
* 경고 이름을로 하 고 저장 합니다.

## <a name="upgrade-preference"></a>업그레이드 기본 설정

Ase 여러 개 있는 경우 일부 Ase을 다른 사용자 보다 먼저 업그레이드 해야 할 수 있습니다. ASE **HostingEnvironment 리소스 관리자** 개체 내에서 **upgradepreference**설정에 대 한 값을 설정할 수 있습니다. **Upgradepreference** 설정은 템플릿, ARMClient 또는를 사용 하 여 구성할 수 있습니다 https://resources.azure.com . 가능한 세 가지 값은 다음과 같습니다.

- **없음**: Azure는 특정 일괄 처리 없이 ASE를 업그레이드 합니다. 이 값은 기본값입니다.
- **초기**: ASE는 App Service 업그레이드의 처음 절반에서 업그레이드 됩니다.
- **후기**: ASE는 App Service 업그레이드의 후반부에서 업그레이드 됩니다.

를 사용 하 https://resources.azure.com 는 경우 다음 단계에 따라 **upgradepreferences** 설정 값을 설정 합니다.

1. Resources.azure.com으로 이동 하 여 Azure 계정으로 로그인 합니다.
1. 구독 \/ \[ 구독 이름 \] \/ resourcegroups \/ \[ 리소스 그룹 이름 \] \/ 공급자 \/ \/ hostingEnvironments \/ \[ ASE 이름 \] 으로 리소스를 이동 합니다.
1. 위쪽에서 **읽기/쓰기** 를 선택 합니다.
1. **편집**을 선택합니다.
1. **Upgradepreference** 를 원하는 세 값 중 하나로 설정 합니다.
1. **패치**를 선택 합니다.

![azure com 표시 리소스][5]

업그레이드 **기본 설정** 기능을 사용 하면 "초기" Ase "Ase" ase 이전에 업그레이드 되기 때문에 여러 개의이 있는 경우에 가장 적합 합니다. 여러 Ase이 있는 경우 개발 및 테스트 Ase를 "초기"로 설정 하 고 프로덕션 Ase "후기"로 설정 해야 합니다.

## <a name="pricing"></a>가격 책정

*격리* 라고 불리는 가격 책정 SKU는 ase 사용 하기 위한 것입니다. ASE에서 호스트 되는 모든 App Service 계획은 격리 가격 책정 SKU에 있습니다. App Service 요금제에 대 한 격리 된 요금은 지역별로 다를 수 있습니다.

App Service 계획의 가격 외에 ASE 자체에 대 한 고정 요금이 있습니다. 플랫 비율은 ASE 크기에 따라 변경 되지 않습니다. 모든 15 App Service 계획 인스턴스에 대해 하나의 추가 프런트 엔드에 대 한 기본 규모 비율의 ASE 인프라를 지불 합니다.

App Service 계획 인스턴스 15 개 마다 하나의 프런트 엔드에 대 한 기본 배율 비율이 충분히 빠르지 않을 경우 프런트 엔드가 추가 되는 비율 또는 프런트 엔드의 크기를 조정할 수 있습니다. 이 비율 또는 크기를 조정할 경우 기본적으로 추가되지 않는 프런트 엔드 코어에 대해 비용을 지불하게 됩니다.

예를 들어 확장 비율을 10으로 조정하면 App Service 계획에서 인스턴스 10개마다 프런트 엔드 하나가 추가됩니다. 고정 비용을 지불하는 경우에는 인스턴스 15개마다 프런트 엔드 하나가 추가되는 확장 비율이 사용됩니다. 확장 비율이 10인 경우에는 App Service 계획 인스턴스 10개에 대해 추가되는 세 번째 프런트 엔드의 요금을 지불하게 됩니다. 인스턴스 수가 15개가 될 때는 요금을 지불하지 않아도 됩니다. 이 경우에는 프런트 엔드가 자동으로 추가되기 때문입니다.

프런트 엔드의 크기를 두 코어로 조정 하지만 비율을 조정 하지 않으면 추가 코어에 대 한 비용을 지불 하 게 됩니다. ASE는 두 개의 프런트 엔드를 사용 하 여 생성 되므로 크기를 2 코어 프런트 엔드로 늘리면 자동 크기 조정 임계값 보다 낮은 경우에도 두 개의 추가 코어에 대해 비용이 청구 됩니다.

자세한 내용은 [Azure App Service 가격][Pricing]을 참조하세요.

## <a name="delete-an-ase"></a>ASE 삭제

ASE를 삭제하려면 다음을 수행합니다.

1. **App Service Environment** 창의 위쪽에서 **삭제** 를 선택 합니다.

1. ASE의 이름을 입력하여 ASE 삭제를 확인합니다. ASE를 삭제 하면 해당 ASE 내의 모든 콘텐츠도 삭제 됩니다.

    ![ASE 삭제][3]

1. **확인**을 선택합니다.

## <a name="ase-cli"></a>ASE CLI

ASE에 대 한 관리를 위한 명령줄 기능이 있습니다.  Az cli 명령은 아래에 나와 있습니다.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md