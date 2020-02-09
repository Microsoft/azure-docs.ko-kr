---
title: Log Analytics 게이트웨이를 사용 하 여 컴퓨터 연결 | Microsoft Docs
description: Log Analytics 게이트웨이를 사용 하 여 인터넷에 액세스할 수 없는 경우 Azure Automation 및 Log Analytics 서비스에 데이터를 전송 하 여 장치와 Operations Manager 모니터링 컴퓨터를 연결 합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: 30854382b5a6dfd0faabfc2f59340dc21518d6f2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773284"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>에서 Log Analytics 게이트웨이를 사용 하 여 인터넷에 액세스 하지 않고 컴퓨터 연결 Azure Monitor

>[!NOTE]
>OMS (Microsoft Operations Management Suite)를 Microsoft Azure 모니터로 전환 하면 용어는 변경 됩니다. 이 문서는 OMS 게이트웨이를 Azure Log Analytics 게이트웨이로 나타냅니다. 
>

이 문서에서는 직접 연결 된 컴퓨터 또는 Operations Manager에서 모니터링 하는 컴퓨터에서 인터넷에 액세스할 수 없는 경우 Log Analytics 게이트웨이를 사용 하 여 Azure Automation 및 Azure Monitor와의 통신을 구성 하는 방법을 설명 합니다. 

Log Analytics 게이트웨이는 http CONNECT 명령을 사용 하 여 HTTP 터널링을 지 원하는 HTTP 전달 프록시입니다. 이 게이트웨이는 인터넷에 직접 연결할 수 없는 컴퓨터를 대신 하 여 Azure Automation 및 Log Analytics 작업 Azure Monitor 영역에 데이터를 보냅니다. 

Log Analytics 게이트웨이는 다음을 지원합니다.

* 각 에이전트에 구성 된 동일한 Log Analytics 작업 영역에 보고 하 고 Hybrid Runbook Worker를 사용 하 Azure Automation 여 구성 된 작업 영역을 보고 합니다.  
* Microsoft Monitoring Agent Azure Monitor의 Log Analytics 작업 영역에 직접 연결 된 Windows 컴퓨터입니다.
* Linux 용 Log Analytics 에이전트가 Azure Monitor의 Log Analytics 작업 영역에 직접 연결 된 linux 컴퓨터입니다.  
* System Center Operations Manager 2012 s p 1에서 UR7, Operations Manager 2012 R2 UR3 이상을 또는 Log Analytics와 통합 된 Operations Manager 2016 이상에서 관리 그룹을 사용 합니다.  

일부 IT 보안 정책은 네트워크 컴퓨터에 대 한 인터넷 연결을 허용 하지 않습니다. 이러한 연결 되지 않은 컴퓨터는 POS (point of sale) 장치 또는 IT 서비스를 지 원하는 서버 일 수 있습니다 (예:). 이러한 장치를 관리 하 고 모니터링할 수 있도록 Azure Automation 또는 Log Analytics 작업 영역에 연결 하려면 Log Analytics 게이트웨이와 직접 통신 하도록 구성 합니다. Log Analytics 게이트웨이는 구성 정보를 수신 하 고 데이터를 전달할 수 있습니다. Log Analytics 에이전트를 사용 하 여 컴퓨터를 구성 하 여 Log Analytics 작업 영역에 직접 연결 하는 경우에는 컴퓨터가 Log Analytics 게이트웨이와 대신 통신 합니다.  

Log Analytics 게이트웨이는 에이전트에서 서비스로 직접 데이터를 전송 합니다. 전송 중인 데이터를 분석 하지 않으며 게이트웨이는 서비스와의 연결이 끊어질 때 데이터를 캐시 하지 않습니다. 게이트웨이가 서비스와 통신할 수 없는 경우 에이전트는 계속 실행 되어 모니터링 되는 컴퓨터의 디스크에 수집 된 데이터를 큐에 대기 시킵니다. 연결이 복원 되 면 에이전트는 Azure Monitor으로 수집 된 캐시 된 데이터를 보냅니다.

Operations Manager 관리 그룹이 Log Analytics와 통합 된 경우 사용 하도록 설정한 솔루션에 따라 구성 정보를 받고 수집 된 데이터를 보내도록 Log Analytics 게이트웨이에 연결 하도록 관리 서버를 구성할 수 있습니다. .  Operations Manager 에이전트는 일부 데이터를 관리 서버로 보냅니다. 예를 들어 에이전트는 Operations Manager 경고, 구성 평가 데이터, 인스턴스 공간 데이터 및 용량 데이터를 보낼 수 있습니다. 인터넷 정보 서비스 (IIS) 로그, 성능 데이터 및 보안 이벤트와 같은 기타 대용량 데이터는 Log Analytics 게이트웨이로 직접 전송 됩니다. 

하나 이상의 Operations Manager 게이트웨이 서버를 배포 하 여 경계 네트워크 또는 격리 된 네트워크의 신뢰할 수 없는 시스템을 모니터링 하는 경우 해당 서버는 Log Analytics 게이트웨이와 통신할 수 없습니다.  Operations Manager 게이트웨이 서버는 관리 서버에만 보고할 수 있습니다.  Operations Manager 관리 그룹이 Log Analytics 게이트웨이와 통신 하도록 구성 된 경우 프록시 구성 정보는 Azure Monitor에 대 한 로그 데이터를 수집 하도록 구성 된 모든 에이전트 관리 컴퓨터에 자동으로 배포 됩니다. 설정이 비어 있는 경우에도 마찬가지입니다.

게이트웨이를 통해 Log Analytics 작업 영역과 통신 하는 직접 연결 또는 Operations Management 그룹에 고가용성을 제공 하려면 NLB (네트워크 부하 분산)를 사용 하 여 여러 게이트웨이 서버 간에 트래픽을 리디렉션하고 분산 합니다. 이렇게 하면 한 게이트웨이 서버가 다운 되 면 트래픽이 사용 가능한 다른 노드로 리디렉션됩니다.  

Log Analytics 게이트웨이를 실행 하는 컴퓨터에는 Log Analytics Windows 에이전트가 게이트웨이에서 통신 해야 하는 서비스 엔드포인트을 식별 해야 합니다. 또한 에이전트는 게이트웨이 뒤에 있는 에이전트 또는 Operations Manager 관리 그룹이로 구성 된 동일한 작업 영역에 대 한 보고서를 보내도록 게이트웨이를 지시 해야 합니다. 이 구성을 통해 게이트웨이 및 에이전트가 할당 된 작업 영역과 통신할 수 있습니다.

게이트웨이는 최대 4 개의 작업 영역으로 멀티홈 될 수 있습니다. Windows 에이전트가 지 원하는 총 작업 영역 수입니다.  

각 에이전트는 게이트웨이에서 데이터를 자동으로 전송 하거나 게이트웨이로 전송할 수 있도록 게이트웨이에 네트워크를 연결 해야 합니다. 도메인 컨트롤러에 게이트웨이를 설치 하지 마세요. 게이트웨이 서버 뒤에 있는 linux 컴퓨터는 [래퍼 스크립트 설치](agent-linux.md#install-the-agent-using-wrapper-script) 방법을 사용 하 여 linux 용 Log Analytics 에이전트를 설치할 수 없습니다. 게이트웨이는 앞에서 언급 한 Azure 서비스와의 통신만 지원 하기 때문에 에이전트를 수동으로 다운로드 하 여 컴퓨터에 복사 하 고 수동으로 설치 해야 합니다.

다음 다이어그램에서는 게이트웨이를 통해 직접 에이전트에서 Azure Automation 하 고 Log Analytics 하는 데이터 흐름을 보여 줍니다. 에이전트 프록시 구성은 Log Analytics 게이트웨이에서 구성 된 포트와 일치 해야 합니다.  

![서비스와의 직접 에이전트 통신 다이어그램](./media/gateway/oms-omsgateway-agentdirectconnect.png)

다음 다이어그램에서는 Operations Manager 관리 그룹에서 Log Analytics로의 데이터 흐름을 보여 줍니다.   

![Log Analytics와 Operations Manager 통신 다이어그램](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>시스템 설정

Log Analytics 게이트웨이를 실행 하도록 지정 된 컴퓨터에는 다음 구성이 있어야 합니다.

* Windows 10, Windows 8.1 또는 Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 또는 Windows Server 2008
* Microsoft .NET Framework 4.5도 필요합니다
* 최소 4 코어 프로세서 및 8gb의 메모리 
* 게이트웨이를 통해 통신 하는 에이전트와 동일한 작업 영역에 보고 하도록 구성 된 [Windows 용 Log Analytics 에이전트](agent-windows.md)

### <a name="language-availability"></a>사용 가능한 언어

Log Analytics 게이트웨이는 다음 언어로 제공 됩니다.

- 중국어 (간체)
- 중국어 (번체)
- 체코어
- 네덜란드어
- 한국어
- 프랑스어
- 독일어
- 헝가리어
- 이탈리아어
- 일본어
- 한국어
- 폴란드어
- 포르투갈어 (브라질)
- 포르투갈어(포르투갈)
- 러시아어
- 스페인어 (국제)

### <a name="supported-encryption-protocols"></a>지원되는 암호화 프로토콜

Log Analytics 게이트웨이는 TLS (전송 계층 보안) 1.0, 1.1 및 1.2만 지원 합니다.  SSL(Secure Sockets Layer) (SSL)를 지원 하지 않습니다.  Log Analytics 전송 중인 데이터의 보안을 보장 하려면 TLS 1.2 이상을 사용 하도록 게이트웨이를 구성 합니다. 이전 버전의 TLS 또는 SSL은 취약 합니다. 현재 이전 버전과의 호환성을 허용 하지만 사용 하지 마세요.  

자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)를 검토하세요. 

### <a name="supported-number-of-agent-connections"></a>지원되는 에이전트 연결 수

다음 표에서는 게이트웨이 서버와 통신할 수 있는 에이전트 수를 대략적으로 보여 줍니다. 지원은 6 초 마다 약 200 KB의 데이터를 업로드 하는 에이전트를 기반으로 합니다. 테스트 된 각 에이전트의 경우 데이터 볼륨은 하루에 약 2.7 GB입니다.

|게이트웨이 |지원 되는 에이전트 (근사치)|  
|--------|----------------------------------|  
|CPU: Intel Xeon Processor E5-2660 v3 \@ 2.6 g h z 2 코어<br> 메모리: 4gb<br> 네트워크 대역폭: 1Gbps| 600|  
|CPU: Intel Xeon Processor E5-2660 v3 \@ 2.6 g h z 4 코어<br> 메모리: 8gb<br> 네트워크 대역폭: 1Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Log Analytics 게이트웨이 다운로드

Microsoft 다운로드 센터 ([다운로드 링크](https://go.microsoft.com/fwlink/?linkid=837444)) 또는 Azure Portal에서 Log Analytics gateway 설치 파일의 최신 버전을 가져옵니다.

Azure Portal에서 Log Analytics 게이트웨이를 가져오려면 다음 단계를 수행 합니다.

1. 서비스 목록을 찾아본 다음 **Log Analytics**를 선택합니다. 
1. 작업 영역을 선택합니다.
1. **일반**의 작업 영역 블레이드에서 **빠른 시작**을 선택합니다. 
1. **작업 영역에 연결하는 데이터 원본 선택**에서 **컴퓨터**를 선택합니다.
1. **직접 에이전트** 블레이드에서 **다운로드 Log Analytics 게이트웨이**를 선택 합니다.
 
   ![Log Analytics 게이트웨이를 다운로드 하는 단계 스크린샷](./media/gateway/download-gateway.png)

또는 

1. **설정** 아래 작업 영역 블레이드에서 **고급 설정**을 선택합니다.
1. **Windows 서버** > **연결 된 원본** 으로 이동 하 여 **Log Analytics 게이트웨이 다운로드**를 선택 합니다.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>설치 마법사를 사용 하 여 Log Analytics 게이트웨이 설치

설치 마법사를 사용 하 여 게이트웨이를 설치 하려면 다음 단계를 수행 합니다. 

1. 대상 폴더에서 **Log Analytics gateway.msi**를 두 번 클릭합니다.
1. **Welcome** 페이지에서 **다음**을 선택합니다.

   ![게이트웨이 설치 마법사의 시작 페이지 스크린샷](./media/gateway/gateway-wizard01.png)

1. **사용권 계약** 페이지에서 동의 함을 **선택 하 여 Microsoft** 소프트웨어 사용 조건에 동의 하 **고 다음을 선택 합니다.**
1. **포트 및 프록시 주소** 페이지에서 다음 단계를 수행합니다.

   a. 게이트웨이에 사용할 TCP 포트 번호를 입력 합니다. 설치 프로그램은이 포트 번호를 사용 하 여 Windows 방화벽에 대 한 인바운드 규칙을 구성 합니다.  기본값은 8080입니다.
      유효한 포트 번호 범위는 1 ~ 65535입니다. 입력한 내용이 이 범위를 벗어나면 오류 메시지가 표시됩니다.

   b. 게이트웨이가 설치 되어 있는 서버에서 프록시를 통해 통신 해야 하는 경우 게이트웨이를 연결 해야 하는 프록시 주소를 입력 합니다. 예를 들어 다음과 같이 입력합니다. `http://myorgname.corp.contoso.com:80`  이 필드를 비워 두면 게이트웨이가 인터넷에 직접 연결을 시도 합니다.  프록시 서버에 인증이 필요한 경우 사용자 이름과 암호를 입력합니다.

   다. **다음**을 선택합니다.

   ![게이트웨이 프록시에 대 한 구성 스크린샷](./media/gateway/gateway-wizard02.png)

1. Microsoft 업데이트 사용 하도록 설정 되지 않은 경우 Microsoft 업데이트 페이지가 표시 되 고 사용 하도록 선택할 수 있습니다. 항목을 선택 하 고 **다음**을 선택 합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.
1. **대상 폴더** 페이지에서 기본 폴더인 C:\Program Files\OMS gateway를 그대로 두거나 게이트웨이를 설치 하려는 위치를 입력 합니다. 그런 후 **다음**을 선택합니다.
1. **설치 준비 완료** 페이지에서 **설치**를 선택합니다. 사용자 계정 컨트롤에서 설치 권한을 요청 하는 경우 **예**를 선택 합니다.
1. 설치가 완료 되 면 **마침**을 선택 합니다. 서비스가 실행 되 고 있는지 확인 하려면 services.msc 스냅인을 열고 서비스 목록에 **OMS 게이트웨이가** 표시 되 고 해당 상태가 **실행**중인지 확인 합니다.

   ![OMS 게이트웨이가 실행 중임을 보여 주는 로컬 서비스의 스크린샷](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>명령줄을 사용 하 여 Log Analytics 게이트웨이 설치

게이트웨이의 다운로드 된 파일은 명령줄 또는 다른 자동화 된 메서드에서 자동 설치를 지 원하는 Windows Installer 패키지입니다. Windows Installer에 대 한 표준 명령줄 옵션에 익숙하지 않은 경우 [명령줄 옵션](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)을 참조 하세요.
 
다음 표에서는 설치 프로그램에서 지 원하는 매개 변수를 보여 줍니다.

|매개 변수| 메모|
|----------|------| 
|PORTNUMBER | 게이트웨이에서 수신 대기 하는 TCP 포트 번호 |
|프록시 | 프록시 서버의 IP 주소 |
|INSTALLDIR | 게이트웨이 소프트웨어 파일의 설치 디렉터리를 지정 하는 정규화 된 경로 |
|USERNAME | 프록시 서버를 사용 하 여 인증할 사용자 ID |
|PASSWORD | 프록시를 사용 하 여 인증할 사용자 ID의 암호 |
|LicenseAccepted | 사용권 계약에 동의 하는지 확인 하려면 **1** 값을 지정 합니다. |
|HASAUTH | 사용자 이름/암호 매개 변수가 지정 된 경우 값 **1** 을 지정 합니다. |
|HASPROXY | **프록시** 매개 변수에 대 한 IP 주소를 지정할 때 값 **1** 을 지정 합니다. |

게이트웨이를 자동으로 설치 하 고 특정 프록시 주소, 포트 번호로 구성 하려면 다음을 입력 합니다.

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

/Qn 명령줄 옵션을 사용 하 여 설치 프로그램을 숨깁니다./qb는 자동 설치 중에 설치 프로그램을 표시 합니다.  

프록시를 사용 하 여 인증할 자격 증명을 제공 해야 하는 경우 다음을 입력 합니다.

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

설치 후 다음 PowerShell cmdlet을 사용 하 여 설정이 허용 되는지 확인할 수 있습니다 (사용자 이름 및 암호 제외).

- **Get OMSGatewayConfig** – 게이트웨이가 수신 대기 하도록 구성 된 TCP 포트를 반환 합니다.
- **OMSGatewayRelayProxy** – 통신 하도록 구성한 프록시 서버의 IP 주소를 반환 합니다.

## <a name="configure-network-load-balancing"></a>네트워크 부하 분산 구성

Microsoft [nlb (네트워크 부하 분산)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)또는 하드웨어 기반 부하 분산 장치를 사용 하 여 nlb (네트워크 부하 분산)를 사용 하 여 고가용성을 위해 게이트웨이를 구성할 수 있습니다. 부하 분산 장치는 노드 전반에 걸쳐 Log Analytics 에이전트 또는 Operations Manager 관리 서버에서 요청된 연결을 리디렉션하여 트래픽을 관리합니다. 게이트웨이 서버가 하나 다운되면 트래픽은 다른 노드로 리디렉션됩니다.

### <a name="microsoft-network-load-balancing"></a>Microsoft 네트워크 부하 분산

Windows Server 2016 네트워크 부하 분산 클러스터를 설계하고 배포하는 방법을 알아보려면 [네트워크 부하 분산](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)을 참조하세요. 다음 단계에서는 Microsoft 네트워크 부하 분산 클러스터를 구성하는 방법에 대해 설명합니다.  

1. NLB 클러스터의 구성원인 Windows 서버에 관리 계정으로 로그인합니다.  
2. [서버 관리자]에서 [네트워크 부하 분산 관리자]를 열고, **도구**를 클릭한 다음 **네트워크 부하 분산 관리자**를 클릭합니다.
3. Log Analytics 게이트웨이 서버를 설치되어 있는 Microsoft Monitoring Agent와 연결하려면 클러스터의 IP 주소를 마우스 오른쪽 단추로 클릭한 다음, **클러스터에 호스트 추가**를 클릭합니다. 

    ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가](./media/gateway/nlb02.png)
 
4. 연결하려는 게이트웨이 서버의 IP 주소를 입력합니다. 

    ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가: 연결](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure Load Balancer를 디자인 하 고 배포 하는 방법을 알아보려면 [Azure Load Balancer 무엇 인가요?](../../load-balancer/load-balancer-overview.md)를 참조 하세요. 기본 부하 분산 장치를 배포 하려면 **백 엔드 서버 만들기**섹션에 설명 된 단계를 제외 하 고이 [빠른](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) 시작에 설명 된 단계를 수행 합니다.   

> [!NOTE]
> **기본 SKU**를 사용 하 여 Azure Load Balancer를 구성 하려면 Azure virtual Machines가 가용성 집합에 속해야 합니다. 가용성 집합에 대해 자세히 알아보려면 [Azure에서 Windows 가상 머신의 가용성 관리](../../virtual-machines/windows/manage-availability.md)를 참조 하세요. 기존 가상 컴퓨터를 가용성 집합에 추가 하려면 [VM 가용성 집합 Azure Resource Manager 설정](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)을 참조 하세요.
> 

부하 분산 장치를 만든 후에는 하나 이상의 게이트웨이 서버에 트래픽을 분산 하는 백 엔드 풀을 만들어야 합니다. 빠른 시작 문서 섹션 [부하 분산 장치에 대 한 리소스 만들기](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)에 설명 된 단계를 따릅니다.  

>[!NOTE]
>상태 프로브를 구성 하는 경우 게이트웨이 서버의 TCP 포트를 사용 하도록 구성 해야 합니다. 상태 프로브는 상태 검사에 대 한 응답에 따라 부하 분산 장치 순환에서 게이트웨이 서버를 동적으로 추가 하거나 제거 합니다. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Log Analytics 에이전트 및 Operations Manager 관리 그룹 구성

이 섹션에서는 Azure Automation 또는 Log Analytics와 통신 하기 위해 Log Analytics 게이트웨이로 직접 연결 된 Log Analytics 에이전트, Operations Manager 관리 그룹 Azure Automation 또는 하이브리드 Runbook Worker를 구성 하는 방법에 대해 알아봅니다.  

### <a name="configure-a-standalone-log-analytics-agent"></a>독립 실행형 Log Analytics 에이전트 구성

Log Analytics 에이전트를 구성 하는 경우 프록시 서버 값을 Log Analytics 게이트웨이 서버의 IP 주소 및 포트 번호로 바꿉니다. 부하 분산 장치 뒤에 여러 게이트웨이 서버를 배포한 경우 Log Analytics 에이전트 프록시 구성은 부하 분산 장치의 가상 IP 주소입니다.  

>[!NOTE]
>Log Analytics에 직접 연결 하는 게이트웨이 및 Windows 컴퓨터에 Log Analytics 에이전트를 설치 하려면 [Azure에서 Log Analytics 서비스에 Windows 컴퓨터 연결](agent-windows.md)을 참조 하세요. Linux 컴퓨터를 연결 하려면 [Azure Monitor에 linux 컴퓨터 연결](agent-linux.md)을 참조 하세요. 
>

게이트웨이 서버에 에이전트를 설치한 후에는 게이트웨이와 통신 하는 작업 영역 또는 작업 영역 에이전트에 보고 하도록 구성 합니다. Log Analytics Windows 에이전트가 게이트웨이에 설치 되어 있지 않으면 OMS 게이트웨이 이벤트 로그에 이벤트 300이 기록 되어 에이전트가 설치 되어야 함을 나타냅니다. 에이전트가 설치 되어 있지만이를 통해 통신 하는 에이전트와 동일한 작업 영역에 보고 하도록 구성 되지 않은 경우 이벤트 105는 동일한 로그에 기록 됩니다 .이는 게이트웨이의 에이전트가 co 에이전트와 동일한 작업 영역에 보고 하도록 구성 해야 함을 나타냅니다. 게이트웨이와 mmunicate.

구성을 완료 한 후 **OMS 게이트웨이** 서비스를 다시 시작 하 여 변경 내용을 적용 합니다. 그렇지 않으면 게이트웨이는 Log Analytics 통신 하려고 시도 하는 에이전트를 거부 하 고 OMS 게이트웨이 이벤트 로그에 이벤트 105을 보고 합니다. 이는 게이트웨이 서버의 에이전트 구성에서 작업 영역을 추가 하거나 제거 하는 경우에도 발생 합니다.

Automation Hybrid Runbook Worker와 관련 된 정보는 Hybrid Runbook Worker를 [사용 하 여 데이터 센터 또는 클라우드에서 리소스 자동화](../../automation/automation-hybrid-runbook-worker.md)를 참조 하세요.

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>모든 에이전트가 동일한 프록시 서버를 사용 하는 Operations Manager 구성

설정이 비어 있더라도 Operations Manager 프록시 구성은 Operations Manager에 보고하는 모든 에이전트에 자동으로 적용됩니다.  

OMS 게이트웨이를 사용 하 여 Operations Manager을 지원 하려면 다음이 있어야 합니다.

* OMS 게이트웨이 서버에 설치 되 고 관리 그룹에 보고 하도록 구성 된 것과 동일한 Log Analytics 작업 영역을 사용 하 여 구성 된 Microsoft Monitoring Agent (버전 8.0.10900.0 이상)
* 인터넷 연결. 또는 OMS 게이트웨이가 인터넷에 연결 된 프록시 서버에 연결 되어 있어야 합니다.

> [!NOTE]
> 게이트웨이의 값을 지정 하지 않으면 빈 값이 모든 에이전트에 푸시됩니다.
>

Operations Manager 관리 그룹이 Log Analytics 작업 영역에 처음으로 등록 하는 경우 운영 콘솔에서 관리 그룹에 대 한 프록시 구성을 지정 하는 옵션이 표시 되지 않습니다. 이 옵션은 관리 그룹이 서비스에 등록 된 경우에만 사용할 수 있습니다.  

통합을 구성 하려면 운영 콘솔과 관리 그룹의 모든 관리 서버를 실행 하는 시스템에서 Netsh를 사용 하 여 시스템 프록시 구성을 업데이트 합니다. 다음 단계를 수행하세요.

1. 관리자 권한 명령 프롬프트를 엽니다.

   a. **시작** 을 선택 하 고 **cmd**를 입력 합니다.  

   b. **명령 프롬프트** 를 마우스 오른쪽 단추로 클릭 하 고 **관리자 권한으로 실행**을 선택 합니다.  

1. 다음 명령을 입력합니다.

   `netsh winhttp set proxy <proxy>:<port>`

Log Analytics와의 통합을 완료 한 후 `netsh winhttp reset proxy`를 실행 하 여 변경 내용을 제거 합니다. 그런 다음 운영 콘솔에서 **프록시 서버 구성** 옵션을 사용 하 여 Log Analytics 게이트웨이 서버를 지정 합니다. 

1. Operations Manager 콘솔의 **Operations Management Suite**에서 **연결**을 선택한 다음 **프록시 서버 구성**을 선택 합니다.

   ![선택 항목을 보여 주는 Operations Manager의 스크린샷 구성 프록시 서버](./media/gateway/scom01.png)

1. **프록시 서버를 사용 하 여 Operations Management Suite에 액세스를** 선택한 다음 Log Analytics 게이트웨이 서버의 IP 주소 또는 부하 분산 장치의 가상 ip 주소를 입력 합니다. `http://`접두사를 사용 하 여 시작 해야 합니다.

   ![프록시 서버 주소를 표시 하는 Operations Manager의 스크린샷](./media/gateway/scom02.png)

1. **마침**을 선택합니다. 이제 Operations Manager 관리 그룹이 게이트웨이 서버를 통해 Log Analytics 서비스와 통신하도록 구성되었습니다.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>특정 에이전트에서 프록시 서버를 사용 하는 Operations Manager 구성

크거나 복잡 한 환경의 경우 특정 서버 (또는 그룹)만 Log Analytics 게이트웨이 서버를 사용 하도록 할 수 있습니다.  이러한 서버에서는 관리 그룹에 대 한 전역 값으로이 값을 덮어쓰기 때문에 Operations Manager 에이전트를 직접 업데이트할 수 없습니다.  대신 이러한 값을 푸시하는 데 사용 되는 규칙을 재정의 합니다.  

> [!NOTE] 
> 사용자 환경에서 여러 Log Analytics 게이트웨이 서버를 허용 하려는 경우이 구성 기법을 사용 합니다.  예를 들어 특정 Log Analytics 게이트웨이 서버를 지역별로 지정 하도록 요구할 수 있습니다.
>

Log Analytics 게이트웨이 서버를 사용 하도록 특정 서버 또는 그룹을 구성 하려면: 

1. Operations Manager 콘솔을 열고 **작성** 작업 영역을 선택합니다.  
1. 제작 작업 영역에서 **규칙**을 선택합니다. 
1. Operations Manager 도구 모음에서 **범위** 단추를 선택 합니다. 이 단추를 사용할 수 없는 경우에는 **모니터링** 창에서 폴더가 아니라 개체를 선택 했는지 확인 합니다. **관리 팩 개체 범위 지정** 대화 상자에서 일반적인 대상 클래스, 그룹 또는 개체의 목록을 표시합니다. 
1. **찾을 대상** 필드에 **상태 관리 서비스** 를 입력 하 고 목록에서 선택 합니다. **확인**을 선택합니다.  
1. **Advisor 프록시 설정 규칙**을 검색 합니다. 
1. Operations Manager 도구 모음에서 **재정의** 를 선택 하 고 다음 **클래스의 특정 개체에 대 한 rule\for: 상태 관리 서비스** 를 가리킨 다음 목록에서 개체를 선택 합니다.  또는이 재정의를 적용 하려는 서버의 상태 관리 서비스 개체를 포함 하는 사용자 지정 그룹을 만듭니다. 그런 다음 사용자 지정 그룹에 재정의를 적용 합니다.
1. **재정의 속성** 대화 상자의 **재정의** 열에서 **webproxyaddress** 매개 변수 옆에 있는 확인 표시를 추가 합니다.  **재정의 값** 필드에 Log Analytics 게이트웨이 서버의 URL을 입력 합니다. `http://`접두사를 사용 하 여 시작 해야 합니다.  

    >[!NOTE]
    > 규칙을 사용하도록 설정하지 않아도 됩니다. Microsoft System center advisor 모니터링 서버 그룹을 대상으로 하는 Microsoft System Center Advisor 보안 참조 재정의 관리 팩의 재정의를 사용 하 여 이미 자동으로 관리 됩니다.
    > 

1. **대상 관리 팩 선택** 목록에서 관리 팩을 선택 하거나 **새로**만들기를 선택 하 여 봉인 되지 않은 새 관리 팩을 만듭니다. 
1. 완료하면 **확인**을 선택합니다. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Automation Hybrid Runbook Worker에 대 한 구성

사용자 환경에 Automation Hybrid Runbook Worker가 있는 경우 다음 단계에 따라 작업자를 지원 하도록 게이트웨이를 구성 합니다.

각 지역의 URL을 찾으려면 Automation 설명서의 [네트워크 구성](../../automation/automation-hybrid-runbook-worker.md#network-planning) 섹션을 참조 하세요.

컴퓨터가 Hybrid Runbook Worker으로 자동으로 등록 된 경우, 예를 들어 하나 이상의 Vm에 대해 업데이트 관리 솔루션을 사용 하도록 설정한 경우 다음 단계를 수행 합니다.

1. 작업 런타임 데이터 서비스 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. `Restart-Service OMSGatewayService` PowerShell cmdlet을 사용하여 Log Analytics 게이트웨이 서비스를 다시 시작합니다.

Hybrid Runbook Worker 등록 cmdlet을 사용 하 여 Azure Automation에 컴퓨터를 연결 하는 경우 다음 단계를 수행 합니다.

1. 에이전트 서비스 등록 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 작업 런타임 데이터 서비스 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Log Analytics 게이트웨이 서비스를 다시 시작합니다.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet

Cmdlet을 사용 하 여 Log Analytics 게이트웨이의 구성 설정을 업데이트 하는 작업을 완료할 수 있습니다. Cmdlet을 사용 하기 전에 다음을 수행 해야 합니다.

1. Microsoft Windows Installer (Log Analytics gateway)를 설치 합니다.
1. PowerShell 콘솔 창을 엽니다.
1. 다음 명령을 입력 하 여 모듈을 가져옵니다. `Import-Module OMSGateway`
1. 이전 단계에서 오류가 발생하지 않은 경우 모듈을 성공적으로 가져왔으며 cmdlet을 사용할 수 있습니다. `Get-Module OMSGateway`을 입력합니다.
1. Cmdlet을 사용 하 여 변경을 수행한 후 OMS 게이트웨이 서비스를 다시 시작 합니다.

3 단계의 오류는 모듈을 가져오지 못했음을 의미 합니다. PowerShell이 모듈을 찾을 수 없는 경우 오류가 발생할 수 있습니다. OMS 게이트웨이 설치 경로: *C:\Program FILES\MICROSOFT oms Gateway\PowerShell\OmsGateway*에서 모듈을 찾을 수 있습니다.

| **Cmdlet** | **매개 변수** | **설명** | **예제** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |키 |서비스 구성 가져오기 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |키(필수) <br> 값 |서비스 구성 변경 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |릴레이(업스트림) 프록시 주소 가져오기 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |주소<br> 사용자 이름<br> 암호 |릴레이(업스트림) 프록시 주소(및 자격 증명) 설정 |1. 릴레이 프록시와 자격 증명을 설정 합니다.<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. 인증이 필요 없는 릴레이 프록시 설정: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. 릴레이 프록시 설정 지우기:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |현재 허용 된 호스트 가져오기 (로컬로 구성 된 허용 된 호스트만, 허용 된 호스트를 자동으로 다운로드 하지 않음) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에 추가 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에서 제거 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에 추가 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에서 제거 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |현재 허용 된 클라이언트 인증서 주체 가져오기 (로컬로 구성 된 허용 된 제목만, 허용 된 주체를 자동으로 다운로드 하지 않음) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>문제 해결

게이트웨이에서 기록한 이벤트를 수집 하려면 Log Analytics 에이전트가 설치 되어 있어야 합니다.

![Log Analytics 게이트웨이 로그의 이벤트 뷰어 목록 스크린샷](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics 게이트웨이 이벤트 Id 및 설명

다음 표에서는 Log Analytics 게이트웨이 로그 이벤트에 대 한 이벤트 Id 및 설명을 보여 줍니다.

| **ID** | **설명** |
| --- | --- |
| 400 |특정 ID가 없는 모든 응용 프로그램 오류입니다. |
| 401 |잘못된 구성. 예를 들어, listenPort = 정수가 아닌 "text"입니다. |
| 402 |TLS 핸드셰이크 메시지 구문 분석 중 예외 |
| 403 |네트워킹 오류. 예를 들어, 대상 서버에 연결할 수 없습니다. |
| 100 |일반 정보 |
| 101 |서비스가 시작됨 |
| 102 |서비스가 중지됨 |
| 103 |클라이언트에서 HTTP CONNECT 명령을 받았습니다. |
| 104 |HTTP CONNECT 명령이 아님 |
| 105 |대상 서버가 허용 목록에 없거나 대상 포트가 안전 하지 않습니다 (443). <br> <br> OMS 게이트웨이 서버의 MMA 에이전트와 OMS 게이트웨이와 통신 하는 에이전트가 동일한 Log Analytics 작업 영역에 연결 되어 있는지 확인 합니다. |
| 105 |오류 TcpConnection – 클라이언트 인증서가 잘못 되었습니다: CN = Gateway. <br><br> OMS 게이트웨이 버전 1.0.395.0 이상인 이상을 사용 하 고 있는지 확인 합니다. 또한 OMS 게이트웨이 서버의 MMA 에이전트와 OMS 게이트웨이와 통신 하는 에이전트가 동일한 Log Analytics 작업 영역에 연결 되어 있는지 확인 합니다. |
| 106 |지원 되지 않는 TLS/SSL 프로토콜 버전입니다.<br><br> Log Analytics 게이트웨이는 TLS 1.0, TLS 1.1 및 1.2만 지원 합니다. SSL은 지원하지 않습니다.|
| 107 |TLS 세션이 확인되었습니다. |

### <a name="performance-counters-to-collect"></a>수집할 성능 카운터

다음 테이블은 Log Analytics 게이트웨이에 사용할 수 있는 성능 카운터를 보여줍니다. 성능 모니터를 사용 하 여 카운터를 추가 합니다.

| **이름** | **설명** |
| --- | --- |
| Log Analytics 게이트웨이/활성 클라이언트 연결 |활성 클라이언트 네트워크(TCP) 연결의 수 |
| Log Analytics 게이트웨이/오류 수 |오류 수 |
| Log Analytics 게이트웨이/연결된 클라이언트 |연결된 클라이언트 수 |
| Log Analytics 게이트웨이/오류 수 |TLS 유효성 검사 오류로 인한 거부 횟수 |

![성능 카운터를 보여 주는 Log Analytics 게이트웨이 인터페이스 스크린샷](./media/gateway/counters.png)

## <a name="assistance"></a>지원

Azure Portal에 로그인 하면 Log Analytics 게이트웨이 또는 다른 Azure 서비스나 기능을 통해 도움을 받을 수 있습니다.
도움말을 보려면 포털의 오른쪽 위 모퉁이에 있는 물음표 아이콘을 선택 하 고 **새 지원 요청**을 선택 합니다. 그런 다음 새 지원 요청 양식을 작성 합니다.

![새 지원 요청 스크린샷](./media/gateway/support.png)

## <a name="next-steps"></a>다음 단계

데이터 [원본을 추가](../../azure-monitor/platform/agent-data-sources.md) 하 여 연결 된 원본에서 데이터를 수집 하 고 Log Analytics 작업 영역에 데이터를 저장 합니다.
