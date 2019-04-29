---
title: Azure Security Center에서 보안 솔루션 통합 | Microsoft Docs
description: Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: e756a0a7af9ad89e3aad8b0dbe27a870a3f855c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60907489"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center에서 보안 솔루션 통합
이 문서를 통해 이미 Azure Security Center에 연결된 보안 솔루션을 관리하고 새로 추가할 수 있습니다.

## <a name="integrated-azure-security-solutions"></a>통합된 Azure 보안 솔루션
Security Center를 사용하면 Azure에서 통합된 보안 솔루션을 쉽게 사용할 수 있습니다. 이점은 다음과 같습니다.

- **간소화된 배포**: Security Center에서는 통합된 파트너 솔루션의 간결한 프로비전을 제공합니다. 맬웨어 방지 및 취약성 평가와 같은 솔루션의 경우 Security Center는 가상 머신에 필요한 에이전트를 프로비전할 수 있습니다. 또한 방화벽 어플라이언스의 경우 Security Center는 필요한 네트워크 구성을 충분히 고려할 수 있습니다.
- **통합된 감지**: 파트너 솔루션의 보안 이벤트는 자동으로 수집, 집계되며 Security Center 알림 및 사고의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합 상태 모니터링 및 관리**: 고객은 한 눈에 모든 파트너 솔루션을 모니터링하기 위해 통합 상태 이벤트를 사용할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.

현재 통합 보안 솔루션에는 다음이 포함됩니다.

- 웹 애플리케이션 방화벽([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) 및 [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- 차세대 방화벽([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](https://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](https://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) 및 [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- 취약점 평가([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) 및 [Rapid7](https://www.rapid7.com/products/insightvm/))

> [!NOTE]
> 대부분의 보안 공급업체가 자신들의 어플라이언스에서 외부 에이전트를 실행하는 것을 금지하고 있기 때문에 Security Center는 파트너 가상 어플라이언스에 Microsoft Monitoring Agent를 설치하지 않습니다.
>
>

## <a name="how-security-solutions-are-integrated"></a>보안 솔루션을 통합하는 방법
Security Center에서 배포된 Azure 보안 솔루션은 자동으로 연결됩니다. 또한 다음을 비롯한 다른 보안 데이터 원본에 연결할 수 있습니다.

- Azure AD Identity Protection
- 온-프레미스 또는 기타 클라우드에서 실행되는 컴퓨터
- CEF(공통 이벤트 형식)을 지원하는 보안 솔루션
- Microsoft Advanced Threat Analytics

![파트너 솔루션 통합](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>통합된 Azure 보안 솔루션 및 다른 데이터 원본 관리

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

2. **Microsoft Azure 메뉴**에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

3. Security Center 메뉴에서 **보안 솔루션**을 선택합니다.

   ![Security Center 개요](./media/security-center-partner-integration/overview.png)

**보안 솔루션**에서 통합된 Azure 보안 솔루션의 상태에 대한 정보를 보고 기본 관리 작업을 수행할 수 있습니다. 또한 CEF(공통 이벤트 형식)에서 Azure Active Directory Identity Protection 알림 및 방화벽 로그와 같은 다른 형식의 보안 데이터 원본을 연결할 수 있습니다.

### <a name="connected-solutions"></a>연결된 솔루션

**연결된 솔루션** 섹션은 Security Center에 현재 연결된 보안 솔루션 및 각 솔루션의 상태에 대한 정보를 포함합니다.  

![연결된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

파트너 솔루션의 상태는 다음과 같을 수 있습니다.

* 정상(녹색) - 상태 문제가 없습니다.
* 비정상(빨강) - 즉각적인 주의가 필요한 상태 문제가 있습니다.
* 상태 문제(주황색) - 솔루션이 상태의 보고를 중지했습니다.
* 보고되지 않음(회색) - 솔루션이 아무 것도 보고하지 않았습니다. 솔루션이 최근에 연결되었고 여전히 배포 중이거나 상태 데이터를 사용할 수 없는 경우 솔루션의 상태를 보고하지 않을 수 있습니다.

> [!NOTE]
> 상태 데이터를 사용할 수 없는 경우 Security Center에 마지막으로 수신된 이벤트의 날짜와 시간을 통해 솔루션이 보고 중인지 여부가 표시됩니다. 상태 데이터를 사용할 수 있으며 지난 14일 이내에 경고가 수신되지 않은 경우 Security Center는 솔루션이 비정상 상태이거나 보고하지 않음을 나타냅니다.
>
>

1. 다음을 비롯한 추가 정보 및 옵션에 대해서는 **보기**를 참조하세요.

   - **솔루션 콘솔**. 이 솔루션에 대한 관리 환경을 엽니다.
   - **VM 연결**. 애플리케이션 연결 블레이드를 엽니다. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
   - **솔루션 삭제**
   - **구성**

   ![파트너 솔루션 세부 정보](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>검색된 솔루션

Security Center는 Azure에서 실행되는 보안 솔루션을 자동으로 검색하지만 Security Center에 연결되지 않고 **검색된 솔루션** 섹션에 솔루션을 표시합니다. 여기에는 [Azure AD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)와 같은 Azure 솔루션뿐만 아니라 파트너 솔루션도 포함됩니다.

> [!NOTE]
> Security Center의 표준 계층은 검색된 솔루션 기능의 구독 수준에서 필요합니다. Security Center의 가격 책정 계층에 대한 자세한 내용은 [가격 책정](security-center-pricing.md)을 참조하세요.
>
>

솔루션 아래의 **연결**을 선택하여 Security Center와 통합하고 보안 경고에서 알림을 받습니다.

![검색된 솔루션](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center는 CEF(일반 이벤트 형식) 로그를 전달할 수 있는 구독에 배포되는 솔루션도 검색합니다. CEF 로그를 사용하여 Security Center에 [보안 솔루션을 연결](quick-security-solutions.md)하는 방법을 알아봅니다.

### <a name="add-data-sources"></a>데이터 원본 추가

**데이터 원본 추가** 섹션에는 연결할 수 있는 다른 사용 가능한 데이터 원본이 포함됩니다. 이러한 원본의 데이터를 추가하는 방법에 대한 지침은 **추가**를 클릭합니다.

![데이터 원본](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

### <a name="connect-external-solutions"></a>외부 솔루션 연결

컴퓨터에서 보안 데이터를 수집하는 것 외에도 CEF(일반적인 이벤트 형식)를 지원하는 솔루션을 포함하여 다양한 다른 보안 솔루션의 보안 데이터를 통합할 수 있습니다. CEF는 Syslog 메시지를 기반으로 하는 업계 표준 형식으로, 많은 보안 공급업체에서 다양한 플랫폼 간에 이벤트를 통합하는 데 사용합니다.

이 빠른 시작에서는 다음을 수행하는 방법을 보여 줍니다.
- CEF 로그를 사용하여 보안 솔루션을 Security Center에 연결
- 보안 솔루션과의 연결 유효성 검사

#### <a name="prerequisites"></a>필수 조건
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)으로 등록할 수 있습니다.

이 빠른 시작을 단계별로 실행하려면 Security Center의 표준 가격 책정 계층에 있어야 합니다. 비용 없이 Security Center 표준을 사용해 볼 수 있습니다. [Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md) 빠른 시작을 통해 표준 계층으로 업그레이드하는 방법을 안내합니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

또한 Security Center에 이미 연결된 Syslog 서비스가 있는 [Linux 컴퓨터](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux)가 필요합니다.

#### <a name="connect-solution-using-cef"></a>CEF를 사용하여 솔루션 연결

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.
2. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

    ![Security Center 선택](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. Security Center 주 메뉴 아래에서 **보안 솔루션**을 선택합니다.
4. Security Solutions 페이지에 있는 **데이터 원본(3) 추가**의 **일반적인 이벤트 형식** 아래에서 **추가**를 클릭합니다.

    ![데이터 원본 추가](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. [일반적인 이벤트 형식 로그] 페이지에서 두 번째 단계, **필요한 로그를 UDP 포트 25226을 통해 에이전트로 보내도록 Syslog 전달 구성**을 펼치고, Linux 컴퓨터에서 아래 지침을 따릅니다.

    ![Syslog 구성](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. 세 번째 단계, **에이전트 컴퓨터에 에이전트 구성 파일 배치**를 펼치고, Linux 컴퓨터에서 아래 지침을 따릅니다.

    ![에이전트 구성](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. 네 번째 단계, **Syslog 디먼 및 에이전트 다시 시작**을 펼치고, Linux 컴퓨터에서 아래 지침을 따릅니다.

    ![syslog 다시 시작](./media/quick-security-solutions/quick-security-solutions-fig5.png)


#### <a name="validate-the-connection"></a>연결 유효성 검사

아래 단계를 진행하기 전에 syslog에서 Security Center에 보고하기 시작할 때까지 기다려야 합니다. 시간이 걸릴 수 있으며 환경의 크기에 따라 다릅니다.

1.  Security Center 대시보드의 왼쪽 창에서 **검색**을 클릭합니다.
2.  Syslog(Linux 컴퓨터)가 연결된 작업 영역을 선택합니다.
3.  *CommonSecurityLog*를 입력하고 **검색** 단추를 클릭합니다.

다음 예제는 이러한 단계의 결과를 보여 줍니다. ![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

#### <a name="clean-up-resources"></a>리소스 정리
이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 이후의 빠른 시작과 자습서를 계속 사용하려면 표준 계층을 계속 실행하고 자동 프로비저닝을 설정된 상태로 유지합니다. 계속하지 않거나 체험 계층으로 되돌리려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **보안 정책**을 선택합니다.
2. 체험 계층으로 되돌리려는 구독 또는 정책을 선택합니다. **보안 정책**이 열립니다.
3. **정책 구성 요소** 아래에서 **가격 책정 계층**을 선택합니다.
4. **체험**을 선택하여 표준 계층에서 체험 계층으로 구독을 변경합니다.
5. **저장**을 선택합니다.

자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. Security Center 주 메뉴로 돌아가서 **보안 정책**을 선택합니다.
2. 자동 프로비저닝을 사용하지 않도록 설정할 구독을 선택합니다.
3. **보안 정책 - 데이터 수집**에서 **온보딩** 아래의 **해제**를 선택하여 자동 프로비저닝을 사용하지 않도록 설정합니다.
4. **저장**을 선택합니다.

>[!NOTE]
> 자동 프로비저닝을 해제해도 Microsoft Monitoring Agent가 프로비전된 Azure VM에서 Agent가 제거되지는 않습니다. 자동 프로비저닝을 사용하지 않도록 설정하면 리소스에 대한 보안 모니터링이 제한됩니다.
>

## <a name="exporting-data-to-a-siem"></a>SIEM으로 데이터 내보내기

Azure Security Center에서 생성하는 처리된 이벤트는 Azure Monitor를 통해 사용할 수 있는 로그 형식 중 하나인 Azure [활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)에 게시됩니다. Azure Monitor는 모니터링 데이터를 SIEM 도구에 라우팅하는 것에 대한 통합된 파이프라인을 제공합니다. 이렇게 하려면 해당 데이터를 Event Hubs에 스트리밍합니다. 그런 후 스트리밍한 데이터는 파트너 도구에 끌어올 수 있습니다.

이 파이프는 Azure 환경에서 모니터링 데이터에 대한 액세스 권한을 갖기 위해 [단일 파이프라인 Azure 모니터링](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)을 사용합니다. 그러면 데이터를 사용할 SIEM 및 모니터링 도구를 쉽게 설정할 수 있습니다.

다음 섹션에서는 데이터를 이벤트 허브로 스트리밍하도록 구성하는 방법에 대해 설명합니다. 다음 단계에서는 Azure Security Center가 Azure 구독에 이미 구성되어 있다고 가정합니다.

대략적인 개요

![대략적인 개요](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>SIEM에 공개된 Azure 보안 데이터는 무엇입니까?

이 버전에서는 [보안 경고](../security-center/security-center-managing-and-responding-alerts.md)가 공개됩니다. 이후 릴리스에는 보안 권장 사항이 포함된 데이터 집합을 보강합니다.

### <a name="how-to-setup-the-pipeline"></a>파이프라인을 설정하는 방법

#### <a name="create-an-event-hub"></a>이벤트 허브 만들기

시작하기 전에 먼저 [Event Hubs 네임스페이스](../event-hubs/event-hubs-create.md)를 만들어야 합니다. 이 네임스페이스 및 이벤트 허브는 모든 모니터링 데이터에 대한 대상입니다.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Azure 활동 로그를 Event Hubs로 스트림

다음 아티클 [Event Hubs에 활동 로그 스트림하기](../azure-monitor/platform/activity-logs-stream-event-hubs.md)를 참조하세요.

#### <a name="install-a-partner-siem-connector"></a>파트너 SIEM 커넥터 설치 

Azure Monitor를 사용하여 모니터링 데이터를 이벤트 허브로 라우팅하면 파트너 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다.

[지원되는 SIEM](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub) 목록을 보려면 다음 링크를 참조하세요.

### <a name="example-for-querying-data"></a>데이터 쿼리 예제 

경고 데이터를 끌어오기 위해 사용할 수 있는 몇 가지 Splunk 쿼리는 다음과 같습니다.

| **쿼리 설명** | **쿼리** |
|----|----|
| All Alerts| index=main Microsoft.Security/locations/alerts|
| 이름별로 작업 횟수를 요약합니다.| operationName별 index=main sourcetype="amal:security" \| table operationName \| 통계치|
| 경고 정보 얻기: 시간, 이름, 상태, ID 및 구독 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 파트너 솔루션을 통합하는 방법을 살펴보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Security Center에서 보안 상태 모니터링](security-center-monitoring.md) Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) Security Center 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
