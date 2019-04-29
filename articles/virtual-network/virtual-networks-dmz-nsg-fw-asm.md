---
title: 경계 네트워크 예제 – Nsg 및 방화벽을 사용 하 여 응용 프로그램을 보호 하는 경계 네트워크 빌드 | Microsoft Docs
description: 방화벽을 사용 하 여 경계 네트워크 및 네트워크 보안 그룹 (Nsg) 빌드
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868351"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>예 2: 방화벽 및 Nsg로 응용 프로그램을 보호 하는 경계 네트워크 빌드
[Microsoft cloud services 및 네트워크 보안 페이지를 반환 합니다.][HOME]

이 예제 경계 네트워크를 만드는 방법을 보여 줍니다 (라고도 *DMZ*를 *완충 영역*, 및 *스크린 된 서브넷*)은 방화벽, 4 대의 Windows Server 컴퓨터를 사용 하 여 및 네트워크 보안 그룹 (Nsg). 각 단계가 심도 깊은 이해를 제공 하기 위해 관련 명령에 대 한 세부 정보를 포함 합니다. "트래픽 시나리오" 섹션을 경계 네트워크에서 방어 계층을 통해 트래픽을 진행 하는 방법의 단계별 설명은 제공 합니다. 마지막으로 "참조" 섹션에는 전체 코드 및 테스트 하 고 다양 한 시나리오를 사용 하 여 실험 하기 위한이 환경을 구축 하는 방법에 대 한 지침을 제공 합니다.

![NVA 및 Nsg와 인바운드 경계 네트워크][1]

## <a name="environment"></a>Environment 
이 예제에서는 이러한 항목을 포함 하는 Azure 구독을 사용 하는 시나리오를 기반으로 합니다.

* 두 개의 클라우드 서비스: FrontEnd001 및 BackEnd001 합니다.
* 두 서브넷이 있는 CorpNetwork 이라는 가상 네트워크: 프런트 엔드 및 백 엔드를 제공 합니다.
* 서브넷 모두에 적용 되는 하나의 NSG.
* 네트워크 가상 어플라이언스: Barracuda NextGen 방화벽 프런트 엔드 서브넷에 연결 합니다.
* 응용 프로그램 웹 서버를 나타내는 Windows Server 컴퓨터: IIS01.
* 응용 프로그램 백 엔드 서버를 나타내는 두 Windows Server 컴퓨터: AppVM01 및 AppVM02 합니다.
* DNS 서버를 나타내는 Windows Server 컴퓨터: DNS01.

> [!NOTE]
> 이 예제에서는 Barracuda NextGen Firewall을 사용 하지만 많은 네트워크 가상 어플라이언스를 사용할 수 있습니다.
> 
> 

이 문서의 "참조" 섹션의 PowerShell 스크립트를 사용 하는 대부분의 여기에 설명 된 환경 빌드합니다. Vm 및 가상 네트워크를 스크립트로 작성 된 있지만 이러한 프로세스는이 문서에서 자세히 설명 되지 않습니다.

환경을 구축하려면

1. "참조" 섹션에 포함 된 네트워크 구성 XML 파일을 저장 (업데이트 이름, 위치 및 IP 주소를 해당 시나리오에 맞게).
2. (구독, 서비스 이름 및 등)에 대 한 스크립트를 실행할 환경에 맞게 PowerShell 스크립트에서 사용자 정의 변수를 업데이트 합니다.
3. PowerShell에서 스크립트를 실행 합니다.

> [!NOTE]
> PowerShell 스크립트에서 지정 된 지역에 네트워크 구성 XML 파일에 지정 된 지역을 일치 해야 합니다.
>
>

스크립트를 성공적으로 실행 된 후에 다음이 단계를 완료할 수 있습니다.

1. 방화벽 규칙을 설정 합니다. 이 문서의 "방화벽 규칙" 섹션을 참조 하세요.
2. 원하는 경우 웹 서버 및 경계 네트워크 구성을 사용 하 여 테스트할 수 있도록 간단한 웹 응용 프로그램을 사용 하 여 응용 프로그램 서버를 설정할 수 있습니다. "참조" 섹션에서 제공 하는 두 응용 프로그램 스크립트를 사용할 수 있습니다.

다음 섹션에는 대부분의 Nsg와 관련 된 스크립트의 문 설명 합니다.

## <a name="nsgs"></a>NSG
이 예제에서는 NSG 그룹을 빌드한 후 6개의 규칙을 로드합니다.

> [!TIP]
> 일반적으로 특정 "허용" 규칙을 먼저 만들어야 하 고 보다 일반적인 "거부" 규칙입니다. 첫 번째를 평가 하는 할당 된 우선 순위 컨트롤 규칙이 결정 됩니다. 특정 규칙에 적용 되는 트래픽이 발견 되 면 추가 규칙 평가 하지 않습니다. NSG 규칙은 인바운드 또는 아웃 바운드 방향 (서브넷 관점)에서 적용할 수 있습니다.
> 
> 

선언적으로 인바운드 트래픽에 대해 이러한 규칙이 빌드됩니다.

1. 내부 DNS 트래픽(포트 53)이 허용됩니다.
2. RDP 트래픽 (포트 3389 인터넷에서 VM에) 허용 됩니다.
3. HTTP 트래픽 (포트 80 인터넷에서 NVA (방화벽) 로) 허용 됩니다.
4. 모든 트래픽 (모든 포트 IIS01에서 AppVM01에) 허용 됩니다.
5. 모든 트래픽 (모든 포트) 인터넷에서 전체 virtual network (두 서브넷)에 거부 됩니다.
6. 모든 트래픽 (모든 포트) 프런트 엔드 서브넷에서 백 엔드 서브넷에 거부 됩니다.

바인딩된 이러한 규칙을 사용 하 여 각 서브넷에 HTTP 요청을 웹 서버로, 인터넷에서 바인딩된 경우 모두 규칙 3 (허용) 하 고 규칙 5 (거부)은 적용할 보이지만 규칙 3이 우선순위가 높기, 때문에 적용 됩니다. 규칙 5는 3만 진행 되지 않습니다. 따라서 HTTP 요청 하려면 방화벽에서 허용 됩니다.

동일한 트래픽이 DNS01 서버에 도달 하려고, 하는 경우 규칙 5 (거부) 트래픽을 서버로 전달 하도록 허용 되지 않습니다 있도록 가장 먼저 적용 됩니다. 규칙 6 (거부) 프런트 엔드 서브넷 (규칙 1 및 4에서에서 허용 되는 트래픽) 제외 하 고 백 엔드 서브넷과 통신 하지 못하도록 차단 합니다. 이 공격자가 프런트 엔드에서 웹 응용 프로그램을 손상 시키는 경우 백 엔드 네트워크를 보호 합니다. 이 경우 공격자가 제한 되므로 백 엔드 "보호 된" 네트워크에 액세스 합니다. 공격자는 일 AppVM01 서버에서 노출 되는 리소스에만 액세스할 수 있습니다.

인터넷에 트래픽을 허용 하는 기본 아웃 바운드 규칙이 있습니다. 이 예제는 아웃 바운드 트래픽을 허용 하 고 아웃 바운드 규칙을 수정 하지 않습니다. 양방향에서 트래픽을 잠글, 사용자 정의 라우팅이 필요 합니다. 다양 한 예에서이 기술을 배울 수 있습니다 합니다 [기본 보안 경계 문서][HOME]합니다.

여기에 설명 된 NSG 규칙의 NSG 규칙과 비슷합니다 [예제 1-Nsg 사용 하 여 간단한 DMZ 빌드][Example1]합니다. 자세히 살펴보고 각 NSG 규칙 및 해당 특성에 대 한 해당 문서의 NSG 설명을 검토 합니다.

## <a name="firewall-rules"></a>방화벽 규칙
방화벽을 관리 하 고 필요한 구성을 생성 하는 컴퓨터에 관리 클라이언트를 설치 해야 합니다. 장치를 관리 하는 방법에 대 한 설명서 방화벽 (또는 다른 NVA) 공급 업체를 참조 하세요. 이 단원의 나머지 부분에서는 공급 업체의 관리 클라이언트 (없습니다: Azure portal 또는 PowerShell)를 통한 방화벽 자체의 구성을 설명합니다.

참조 [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) 클라이언트 다운로드 및이 예제에 사용 된 Barracuda 방화벽 연결에 대 한 지침입니다.

방화벽에서 전달 규칙을 만들고 해야 합니다. 이 예제 시나리오에만 인터넷 트래픽을 라우팅합니다 때문에 방화벽에 인바운드 및 다음 웹 서버에만 하면 하나의 전달 NAT 규칙만. 이 예제에 사용 된 Barracuda 방화벽에서 규칙 대상 NAT 규칙 (Dst NAT)이이 트래픽을 전달 하는 것입니다.

다음 규칙을 만듭니다 (또는 기존 기본 규칙을 확인), 다음이 단계를 완료 합니다.
1. Barracuda NG Admin 클라이언트 대시보드에서 구성 탭에 **작동 구성** 섹션에서 **Ruleset**합니다. 

   표 호출 **Main Rules** 기존 활성 표시 및 방화벽 규칙을 비활성화 합니다.

2. 새 규칙을 만들려면 선택 작은 녹색 **+** 이 그리드의 오른쪽 위 모서리에 있는 단추입니다. (방화벽 잠글 수 있습니다. 표시 된 단추가 표시 되 면 **잠금** 작성 또는 규칙을 편집 하 고, 규칙 집합의 잠금을 해제 하 고 편집을 허용 하려면 단추를 선택 하는 일을 할 수 없는 합니다.)
  
3. 기존 규칙을 편집 하려면 선택 규칙을 마우스 오른쪽 단추로 클릭 및 선택 **규칙 편집**합니다.

같은 새 규칙을 만들어 **WebTraffic 합니다.** 

대상 NAT 규칙 아이콘은 다음과 같습니다. ![대상 NAT 아이콘][2]

규칙 자체는 다음과 같이 표시 됩니다.

![방화벽 규칙][3]

HTTP (포트 80 또는 HTTPS의 경우 443)에 도달 하려고 하는 방화벽에 도달 하는 모든 인바운드 주소는 방화벽의 DHCP1 로컬 IP 인터페이스에서 전송 되며 IP 주소가 10.0.1.5 인 웹 서버로 리디렉션됩니다. 포트 80에서 들어오는 트래픽에 포트 80에서 웹 서버로 이동 때문에 포트 변경할 필요가 없습니다. 하지만 대상 목록 했을 수 10.0.1.5:8080 웹 서버의 인바운드 포트 80을 방화벽의 웹 서버에서 인바운드 포트 8080으로 변환 하는 포트 8080에서 수신 대기 하는 경우.

연결 방법을 지정 해야 합니다. 인터넷에서 대상 규칙에 대 한 동적 SNAT은 가장 적합 한 방법입니다.

만 하나의 규칙을 만든 경우에 해당 우선 순위를 올바르게 설정 하는 것이 반드시 합니다. 방화벽에서 모든 규칙의 표에서이 새 규칙이 BLOCKALL 규칙) (아래 맨 아래에 있는 경우이 되지 이루어지는입니다. 웹 트래픽에 대 한 새 규칙이 BLOCKALL 규칙 위에 있는지 확인 합니다.

규칙을 만든 후 방화벽으로 푸시하고 하 고 다음 활성화 해야 합니다. 다음이 단계를 취하지 않을 경우 규칙 변경 내용이 적용 되지 않습니다. 다음 섹션에는 푸시 및 활성화 프로세스를 설명합니다.

## <a name="rule-activation"></a>규칙 활성화
규칙 규칙 집합에 추가 되는 이제 방화벽 규칙 집합을 업로드 하 고 활성화 해야 합니다.

![방화벽 규칙 활성화][4]

관리 클라이언트의 오른쪽 위 모퉁이의 단추 그룹을 볼 수 있습니다. 선택 **변경 내용 보내기** 방화벽으로 수정 된 ruleset을 보내고 선택 **활성화**합니다.

방화벽 규칙 집합을 활성화 했으므로 환경을 완료 되었습니다. 원하는 경우 환경에 응용 프로그램을 추가 하려면 "참조" 섹션에서 샘플 응용 프로그램 스크립트를 실행할 수 있습니다. 응용 프로그램을 추가 하는 경우에 다음 섹션에 설명 된 트래픽 시나리오를 테스트할 수 있습니다.

> [!IMPORTANT]
> 웹 서버를 직접 적중지 않습니다 있음을 알아야 합니다. FrontEnd001.CloudApp.Net에서 HTTP 페이지를 요청 하는 브라우저, 웹 서버가 아닌 방화벽에 HTTP 끝점 (포트 80) 트래픽이 전달 됩니다. 방화벽, 이전에 만든 규칙으로 인해를 사용 하 여 NAT 웹 서버에 요청을 매핑합니다.
> 
> 

## <a name="traffic-scenarios"></a>트래픽 시나리오
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(허용) 방화벽을 통해 웹 서버에 웹
1. 인터넷 사용자 (인터넷 연결 클라우드 서비스) FrontEnd001.CloudApp.Net에서 HTTP 페이지를 요청 합니다.
2. 클라우드 서비스는 방화벽의 로컬 인터페이스 10.0.1.4:80에 포트 80에서 열린 끝점을 통해 트래픽을 전달합니다.
3. 프런트 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1 (DNS) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   2. NSG 규칙 2(rp) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   3. NSG 규칙 3 (인터넷에서 방화벽으로)에 적용 됩니다. 트래픽을 허용 합니다. 규칙 처리를 중지 합니다.
4. 트래픽 (10.0.1.4) 방화벽의 내부 IP 주소에 도달 합니다.
5. 방화벽 전달 규칙에는이 포트 80 트래픽 및 웹 서버 iis01로 리디렉션합니다 결정 합니다.
6. IIS01이 웹 트래픽을 수신 대기 하 고, 요청을 받으면, 요청을 처리를 시작 합니다.
7. IIS01은 AppVM01에서 SQL Server 인스턴스에서 정보를 요청합니다.
8. 가지 아웃 바운드 규칙이 없으므로 프런트 엔드 서브넷에서 트래픽이 허용 됩니다.
9. 백 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1 (DNS) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   2. NSG 규칙 2(rp) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   3. NSG 규칙 3 (인터넷에서 방화벽으로)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   4. NSG 규칙 4 (IIS01 AppVM01에)에 적용 합니다. 트래픽을 허용 합니다. 규칙 처리를 중지 합니다.
10. AppVM01에서 SQL Server 인스턴스에 요청을 받고 응답 합니다.
11. 백 엔드 서브넷에 아웃 바운드 규칙이 없으므로 이기 때문에 응답이 허용 됩니다.
12. 프런트 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
    1. 에 적용 되는 NSG 규칙이 없는 인바운드 NSG 규칙을 하나도 적용 되므로 프런트 엔드 서브넷에 백 엔드 서브넷에서 트래픽을 합니다.
    2. 트래픽을 허용 하므로이 트래픽을 허용 하는 기본 시스템 규칙이 서브넷 간의 트래픽을 허용 합니다.
13. IIS01은 AppVM01에서 응답을 수신 하 고 HTTP 응답을 완료 및 요청자에 게 보냅니다.
14. 방화벽 으로부터 NAT 세션 이므로 방화벽에 대 한 응답 대상은 처음입니다.
15. 방화벽 웹 서버에서 응답을 받아 인터넷 사용자에 게 다시 전달 합니다.
16. 프런트 엔드 서브넷에 아웃 바운드 규칙이 없으므로 이기 때문에 응답이 허용 됩니다. 및 인터넷 사용자는 웹 페이지를 수신 합니다.

#### <a name="allowed-rdp-to-backend"></a>(허용) RDP-백 엔드
1. 인터넷에서 서버 관리자 BackEnd001.CloudApp.Net에서 AppVM01에 대 한 RDP 세션을 요청:*xxxxx*, 여기서 *xxxxx* AppVM01에 RDP에 임의로 할당 된 포트 번호입니다. (Azure portal 또는 PowerShell을 사용 하 여 할당 된 포트를 찾을 수 있습니다.)
2. 방화벽은 FrontEnd001.CloudApp.Net 주소만 에서만 수신 하므로이 트래픽 흐름에 포함 되지 않습니다.
3. 백 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1 (DNS) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   2. NSG 규칙 2(rp) 적용지 않습니다. 트래픽을 허용 합니다. 규칙 처리를 중지 합니다.
4. 아웃 바운드 규칙이 없으므로 이기 때문에 기본 규칙을 적용 하 고 트래픽이 허용 됩니다. 반환 합니다.
5. RDP 세션이 사용 됩니다.
6. AppVM01은 사용자 이름 및 암호를 요구합니다.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(허용) DNS 서버에서 웹 서버 DNS 조회
1. 데이터 피드에서 www.data.gov 하지만 주소를 확인 해야 하는 경우 웹 서버 IIS01 해야 합니다.
2. 네트워크 구성은 가상 네트워크와 DNS01 (백 엔드 서브넷에서 10.0.2.4) 주 DNS 서버로입니다. IIS01은 DNS01로 DNS 요청을 보냅니다.
3. 프런트 엔드 서브넷에 아웃 바운드 규칙이 없으므로 이기 때문에 트래픽이 허용 됩니다.
4. 백 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1 (DNS)에 다음이 적용 됩니다. 트래픽을 허용 합니다. 규칙 처리를 중지 합니다.
5. DNS 서버는 요청을 받습니다.
6. DNS 서버 캐시 된 주소가 없고 인터넷에서 루트 DNS 서버를 쿼리 합니다.
7. 백 엔드 서브넷에 아웃 바운드 규칙이 없으므로 이기 때문에 트래픽이 허용 됩니다.
8. 인터넷 DNS 서버가 응답 합니다. 세션은 내부적으로 시작 되었으므로, 응답이 허용 됩니다.
9. DNS 서버는 응답을 캐시 하 고 IIS01에서 요청에 응답 합니다.
10. 백 엔드 서브넷에 아웃 바운드 규칙이 없으므로 이기 때문에 트래픽이 허용 됩니다.
11. 프런트 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
    1. 에 적용 되는 NSG 규칙이 없는 인바운드 NSG 규칙을 하나도 적용 되므로 프런트 엔드 서브넷에 백 엔드 서브넷에서 트래픽을 합니다.
    2. 하므로 트래픽이 허용은이 트래픽을 허용 하는 서브넷 간의 트래픽을 허용 되는 시스템 규칙입니다.
12. IIS01은 DNS01에서 응답을 받습니다.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(허용) AppVM01에서 웹 서버가 파일 액세스
1. IIS01은 AppVM01에서 파일에를 요청합니다.
2. 프런트 엔드 서브넷에 아웃 바운드 규칙이 없으므로 이기 때문에 트래픽이 허용 됩니다.
3. 백 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
   1. NSG 규칙 1 (DNS) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   2. NSG 규칙 2(rp) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   3. NSG 규칙 3 (인터넷에서 방화벽으로)이 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   4. NSG 규칙 4 (IIS01 AppVM01에)에 적용 합니다. 트래픽을 허용 합니다. 규칙 처리를 중지 합니다.
4. AppVM01 요청을 수신 하 고 (액세스 권한이 부여 된 것으로 가정) 파일을 사용 하 여 응답 합니다.
5. 백 엔드 서브넷에 아웃 바운드 규칙이 없으므로 이기 때문에 응답이 허용 됩니다.
6. 프런트 엔드 서브넷의 인바운드 규칙 처리를 시작합니다.
   1. 에 적용 되는 NSG 규칙이 없는 인바운드 NSG 규칙을 하나도 적용 되므로 프런트 엔드 서브넷에 백 엔드 서브넷에서 트래픽을 합니다.
   2. 하므로 트래픽이 허용은이 트래픽을 허용 하는 서브넷 간의 트래픽을 허용 되는 시스템 규칙입니다.
7. IIS01 파일을 수신 합니다.

#### <a name="denied-web-direct-to-web-server"></a>(거부) 웹 웹 서버로 직접
웹 서버 IIS01 및 방화벽에서 동일한 클라우드 서비스 이기 때문에 동일한 공용 IP 주소를 공유 합니다. 따라서 모든 HTTP 트래픽은 방화벽에 전달 됩니다. 요청은 성공적으로 처리를 하는 동안 웹 서버로 직접 이동할 수 없습니다. 전달, 설계 된 대로, 방화벽을 통해 먼저 합니다. 트래픽 흐름은이 단원의 첫 번째 시나리오를 참조 하세요.

#### <a name="denied-web-to-backend-server"></a>(거부) 웹-백 엔드 서버
1. 인터넷 사용자 BackEnd001.CloudApp.Net 서비스를 통해 AppVM01에서 파일에 액세스 하려고 합니다.
2. 파일 공유를 위해 열린 끝점이 있기 때문에 클라우드 서비스를 전달 하지 않습니다이 서버에 도달 하지 않습니다.
3. 어떤 이유로 끝점이 열려 있는 경우 NSG 규칙 5 (인터넷에서 가상 네트워크) 트래픽을 차단 합니다.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(거부) DNS 서버에서 웹 DNS 조회
1. 인터넷 사용자 BackEnd001.CloudApp.Net 서비스를 통해 DNS01에서 내부 DNS 레코드를 조회 하려고 합니다.
2. DNS를 위해 열린 끝점이 있기 때문에 클라우드 서비스를 전달 하지 않습니다이 서버에 도달 하지 않습니다.
3. 어떤 이유로 끝점이 열려 있는 경우 NSG 규칙 5 (인터넷에서 가상 네트워크) 트래픽을 차단 합니다. (규칙 1 [DNS]는 두 가지 이유로 적용 되지 않습니다. 먼저 원본 주소가 인터넷 이며이 규칙은 로컬 가상 네트워크는 원본 경우에 적용 됩니다. 둘째,이 규칙은 허용 규칙 이므로 트래픽을 거부 하지 않습니다.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(거부) 웹에서 방화벽을 통해 SQL 액세스
1. 인터넷 사용자 (인터넷 연결 클라우드 서비스) FrontEnd001.CloudApp.Net에서 SQL 데이터를 요청 합니다.
2. SQL 위해 열린 끝점이 있기 때문에 클라우드 서비스를 전달 하지 않습니다이 고 방화벽에 도달 하지 않습니다.
3. 어떤 이유로 끝점이 열려 있는 경우 프런트 엔드 서브넷의 인바운드 규칙 처리를 시작 합니다.
   1. NSG 규칙 1 (DNS) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   2. NSG 규칙 2(rp) 적용 되지 않습니다. 다음 규칙으로 이동 합니다.
   3. NSG 규칙 3 (인터넷에서 방화벽으로)에 적용 됩니다. 트래픽을 허용 합니다. 규칙 처리를 중지 합니다.
4. 트래픽 (10.0.1.4) 방화벽의 내부 IP 주소에 도달 합니다.
5. 방화벽 전달 규칙이 없고 sql 있으며 트래픽을 삭제 합니다.

## <a name="conclusion"></a>결론
이 예제에서는 방화벽이 있는 응용 프로그램을 보호 하 고 백 엔드 서브넷을 인바운드 트래픽과 격리 하는 비교적 간단한 방법을 보여 줍니다.

찾을 수 있습니다 네트워크의 개요 및 더 많은 예제는 보안 경계 [같습니다][HOME]합니다.

## <a name="references"></a>참조
### <a name="full-script-and-network-config"></a>전체 스크립트 및 네트워크 구성
PowerShell 스크립트 파일에 전체 스크립트를 저장 합니다. 네트워크 구성 스크립트 Networkconf2.xml"이라는 파일에 저장 합니다.
필요에 따라 사용자 정의 변수를 변경 합니다. 스크립트를 실행 하 고이 문서의 "방화벽 규칙" 단원의 지침을 따릅니다.

#### <a name="full-script"></a>전체 스크립트
사용자 정의 변수를 기반으로이 스크립트는 다음 단계를 완료 됩니다.

1. Azure 구독에 연결 합니다.
2. 저장소 계정을 만듭니다.
3. 네트워크 구성 파일에 정의 된 대로 가상 네트워크 및 두 개의 서브넷을 만듭니다.
4. 4 개의 Windows Server Vm을 빌드하십시오.
5. NSG를 구성 합니다. 구성이 단계를 완료합니다.
   * NSG를 만듭니다.
   * 규칙을 사용 하 여 NSG를 채웁니다.
   * 적절 한 서브넷에 NSG를 바인딩합니다.

이 PowerShell 스크립트는 인터넷에 연결 된 컴퓨터나 서버에서 로컬로 실행 해야 합니다.

> [!IMPORTANT]
> 이 스크립트를 실행 하면 PowerShell에서 경고 및 기타 정보 메시지가 나타날 수 있습니다. 빨간색으로 표시 되는 오류 메시지에 주의 해야 합니다.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>네트워크 구성 파일
업데이트 된 위치를 사용 하 여이 XML 파일을 저장 하 고 $NetworkConfigFile 변수 앞의 스크립트에서이 파일에 대 한 링크를 추가 합니다.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>샘플 애플리케이션 스크립트
이 내용과 기타 경계 네트워크에 대 한 샘플 응용 프로그램을 설치 하려는 경우 참조를 [샘플 응용 프로그램 스크립트][SampleApp]합니다.

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "NSG와 인바운드 DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "대상 NAT 아이콘"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "방화벽 규칙"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "방화벽 규칙 활성화"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
