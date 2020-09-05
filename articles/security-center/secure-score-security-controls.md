---
title: Azure Security Center의 보안 점수
description: Azure Security Center의 보안 점수 및 보안 컨트롤에 대한 설명
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 9594e1ed14b017591ea2c4ddda59ba61feb81b0c
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272283"
---
# <a name="enhanced-secure-score-in-azure-security-center"></a>Azure Security Center의 보안 점수 향상

## <a name="introduction-to-secure-score"></a>보안 점수 소개

Azure Security Center에는 현재 보안 상황을 이해하고 효과적이고 효율적으로 보안을 개선하도록 지원한다는 두 가지 주요 목표가 있습니다. 이러한 목표를 달성할 수 있도록 하는 Security Center의 핵심적인 측면은 보안 점수입니다.

Security Center는 리소스, 구독 및 조직의 보안 이슈를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다.

Security Center의 보안 점수 페이지에는 다음이 포함됩니다.

- **점수** - 보안 점수는 백분율 값으로 표시되지만 기본 값도 명확히 표시됩니다.

    [![명확한 기본 수치를 포함하는 백분율 값으로 표시되는 보안 점수](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **보안 컨트롤** - 각 컨트롤은 관련된 보안 권장 사항의 논리적 그룹으로, 취약한 공격 노출 영역을 반영합니다. 컨트롤은 이러한 권장 사항을 구현하는 데 도움이 되는 지침을 포함하는 보안 권장 사항 세트입니다. 점수는 컨트롤 내의 단일 리소스에 대한 *모든* 권장 사항에 따라 수정해야만 향상됩니다.

    조직이 각 개별 공격 노출 영역을 얼마나 잘 보호하고 있는지 즉시 확인하려면 각 보안 컨트롤의 점수를 검토합니다.

    자세한 내용은 아래의 [보안 점수를 계산 하는 방법](secure-score-security-controls.md#how-your-secure-score-is-calculated) 을 참조 하세요. 


>[!TIP]
> 권장 사항 수준에서 이전 버전의 Security Center 제안 사항: 단일 리소스에 대 한 권장 사항에 따라 수정하면 보안 점수가 향상됩니다. 현재, 점수는 컨트롤 내의 단일 리소스에 대한 *모든* 권장 사항에 따라 수정해야만 향상됩니다. 따라서 리소스의 보안이 향상된 경우에만 점수가 향상됩니다.


## <a name="accessing-your-secure-score"></a>보안 점수 액세스

Azure Portal 또는 Azure Security Center REST API를 사용 하 여 프로그래밍 방식으로 전체 보안 점수 뿐만 아니라 구독 당 점수를 확인할 수 있습니다.

### <a name="getting-your-secure-score-from-the-portal"></a>포털에서 보안 점수 얻기

Security Center는 포털에서 점수를 두드러지게 표시 합니다. 개요 페이지에 표시 되는 첫 번째 항목입니다. 전용 보안 점수 페이지를 클릭하면 구독별로 분석된 점수를 볼 수 있습니다. 단일 구독을 클릭하여 우선 순위가 지정된 권장 사항의 세부 목록과 이러한 권장 사항에 따라 수정할 때 구독의 점수에 미치는 영향을 확인할 수 있습니다.

![포털에 표시 된 전체 보안 점수](media/secure-score-security-controls/single-secure-score-via-ui.png)

### <a name="getting-your-secure-score-from-the-rest-api"></a>REST API에서 보안 점수 얻기

[보안 점수 API](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (현재 미리 보기 상태)를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공 하 고 시간에 따른 보안 점수에 대 한 자체 보고 메커니즘을 작성 합니다. 예를 들어 **보안 점수** API를 사용 하 여 특정 구독에 대 한 점수를 가져올 수 있습니다. 또한 보안 **점수 컨트롤** API를 사용 하 여 보안 제어 및 구독의 현재 점수를 나열할 수 있습니다.

![API를 통해 단일 보안 점수 검색](media/secure-score-security-controls/single-secure-score-via-api.png)

보안 점수 API를 기반으로 하는 도구에 대 한 예제는 [GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)을 참조 하세요. 

## <a name="how-your-secure-score-is-calculated"></a>보안 점수를 계산 하는 방법 

전체적인 보안 점수에 대한 각 보안 컨트롤의 기여도는 권장 사항 페이지에 명확히 표시됩니다.

[![향상된 보안 점수로 보안 컨트롤 도입](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

보안 컨트롤을 위한 가능한 모든 포인트를 구하려면 모든 리소스가 보안 컨트롤 내의 모든 보안 권장 사항을 준수해야 합니다. 예를 들어, Security Center에는 관리 포트를 보호하는 방법에 대한 여러 권장 사항이 있습니다. 과거에는 다른 문제는 해결되지 않은 상태로 두고, 관련되고 상호 종속되는 권장 사항 일부를 수정할 수 있으며 이 경우 보안 점수가 향상되었습니다. 그렇지만 객관적으로 보면 모든 문제가 해결될 때까지 보안이 개선되지 않았다고 주장하는 것이 쉽습니다. 따라서 이제는 보안 점수에 영향을 주려면 모든 문제를 수정해야 합니다.

예를 들어 "시스템 업데이트 적용"이라는 보안 컨트롤의 최대 점수는 6포인트이며, 이 컨트롤의 잠재적 증가 값을 도구 설명에서 볼 수 있습니다.

[![보안 컨트롤 "시스템 업데이트 적용"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

시스템 업데이트 적용 컨트롤의 최대 점수는 항상 6입니다. 이 예제에는 50개의 리소스가 있습니다. 따라서 최대 점수를 50으로 나누면 결과적으로 모든 리소스는 0.12포인트를 기여합니다. 

* **잠재적 증가**(0.12 x 8개의 비정상 리소스 = 0.96) - 컨트롤 내에서 사용할 수 있는 나머지 포인트입니다. 이 컨트롤의 모든 권장 사항을 수정하면 점수가 2%씩 증가합니다(이 경우 0.96포인트가 1포인트로 반올림됨). 
* **현재 점수**(0.12 x 42개의 정상 리소스 = 5.04) - 이 컨트롤의 현재 점수입니다. 각 컨트롤의 점수로 전체 점수를 계산합니다. 이 예제에서 이 컨트롤은 현재 보안 합계에서 5.04포인트를 차지합니다.
* **최대 점수** - 컨트롤 내의 모든 권장 사항을 완료하여 얻을 수 있는 최대 포인트입니다. 컨트롤의 최대 점수는 해당 컨트롤의 상대적 중요도를 나타냅니다. 가장 먼저 작업할 이슈를 심사하려면 최대 점수 값을 사용합니다. 


### <a name="calculations---understanding-your-score"></a>계산 - 점수 이해

|메트릭|수식 및 예제|
|-|-|
|**보안 컨트롤의 현재 점수**|<br>![보안 컨트롤의 현재 점수를 계산하는 수식](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>각 개별 보안 컨트롤은 보안 점수에 기여합니다. 컨트롤 내의 권장 구성에 의해 영향을 받는 각 리소스는 컨트롤의 현재 점수에 기여합니다. 각 컨트롤의 현재 점수는 컨트롤 *내* 리소스의 상태를 측정한 것입니다.<br>![보안 컨트롤의 현재 점수를 계산할 때 사용되는 값을 보여 주는 도구 설명](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>이 예제에서 최대 점수 6은 정상 및 비정상 리소스의 합계인 78로 나뉩니다.<br>6 / 78 = 0.0769<br>이 값에 정상 리소스의 수(4)를 곱하여 현재 점수가 됩니다.<br>0.0769 * 4 = **0.31**<br><br>|
|**보안 점수**<br>단일 구독|<br>![현재 보안 점수를 계산하는 수식](media/secure-score-security-controls/secure-score-equation.png)<br><br>![모든 컨트롤이 사용하도록 설정된 단일 구독 보안 점수](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>이 예제에는 모든 보안 컨트롤을 사용할 수 있는 단일 구독이 있습니다(잠재적 최대 점수 60포인트). 점수로는 가능한 60포인트 중에서 28포인트가 표시되고 나머지 32포인트는 보안 컨트롤의 "잠재적 점수 증가" 수치에 반영됩니다.<br>![컨트롤 목록 및 잠재적 점수 증가](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**보안 점수**<br>여러 구독|<br>모든 구독에서 모든 리소스에 대한 현재 점수가 추가되고 단일 구독의 경우와 동일한 계산이 수행됩니다.<br><br>여러 구독을 볼 때 보안 점수는 설정된 모든 정책 내의 모든 리소스를 평가하고, 각 보안 컨트롤의 최대 점수에 미치는 조합된 영향을 그룹화합니다.<br>![모든 컨트롤이 사용하도록 설정된 여러 구독의 보안 점수](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>조합된 점수는 평균이 **아니며**, 모든 구독에서 모든 리소스의 상태를 평가한 것입니다.<br>여기서도 권장 사항 페이지로 이동하여 사용할 수 있는 잠재적 포인트를 추가하면 현재 점수(24)와 사용 가능한 최대 점수(60) 간에 차이가 있다는 것을 알 수 있습니다.|
||||

## <a name="improving-your-secure-score"></a>보안 점수 향상

보안 점수를 향상시키려면 권장 사항 목록에서 보안 권장 사항을 수정하세요. 각 리소스에 대해 각 권장 사항을 수동으로 수정하거나 **빠른 수정!** 옵션(사용 가능한 경우)을 사용하여 권장 사항의 수정 작업을 리소스 그룹에 빠르게 적용할 수 있습니다. 자세한 내용은 [권장 사항에 따라 수정](security-center-remediate-recommendations.md)을 참조하세요.

>[!IMPORTANT]
> 기본 제공 권장 사항만 보안 점수에 영향을 줍니다.


## <a name="security-controls-and-their-recommendations"></a>보안 제어 및 해당 권장 사항

다음 표에서는 Azure Security Center의 보안 컨트롤을 나열합니다. 각 컨트롤에 대해 *모든* 리소스의 컨트롤에 나열된 *모든* 권장 사항에 따라 수정할 경우 보안 점수에 추가될 수 있는 최대 포인트를 확인할 수 있습니다. 

<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>보안 컨트롤, 제어 및 설명</b><br></th>
    <th class="tg-cly1"><b>권장 사항</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">MFA 사용(최대 점수 10)</p></strong>암호만 사용하여 사용자를 인증하면 공격 노출 영역이 열리게 됩니다. 암호가 약하거나 다른 곳에 노출된 경우 실제로 사용자 이름과 암호를 사용하는 사용자 로그인일까요?<br><a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a>를 사용하도록 설정하면 계정이 더 안전하게 유지되고 사용자는 SSO(Single Sign-On)를 사용하여 거의 모든 애플리케이션에서 인증을 받을 수 있습니다.</td>
    <td class="tg-lboi"; width=55%>- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.<br>- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">보안 관리 포트(최대 점수 8)</p></strong>무차별 암호 대입 공격(brute force attack)은 VM에 액세스하기 위해 관리 포트를 대상으로 합니다. 포트는 항상 열려 있어야 하는 것은 아니므로 Just-In-Time 네트워크 액세스 제어, 네트워크 보안 그룹 및 가상 머신 포트 관리를 사용하여 포트에 대한 노출을 줄이는 것이 한 가지 완화 전략입니다.<br>많은 IT 조직이 네트워크에서 SSH 통신 아웃바운드를 차단하지 않으므로 공격자는 감염된 시스템의 RDP 포트가 공격자 명령과 다시 통신하여 서버를 제어하도록 허용하는 암호화된 터널을 만들 수 있습니다. 공격자는 Windows 원격 관리 하위 시스템을 사용하여 사용자 환경에서 수평으로 이동하고 도난 당한 자격 증명을 사용하여 네트워크의 다른 리소스에 액세스할 수 있습니다.</td>
    <td class="tg-lboi"; width=55%>- 가상 컴퓨터의 관리 포트를 just-in-time 네트워크 액세스 제어로 보호 해야 합니다.<br>- 가상 머신을 네트워크 보안 그룹과 연결해야 합니다.<br>- 가상 머신에서 관리 포트를 닫아야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">시스템 업데이트 적용(최대 점수 6)</p></strong>시스템 업데이트는 조직에 운영 효율성을 유지하고, 보안 취약성을 줄이고, 최종 사용자에게 보다 안정적인 환경을 제공하는 기능을 제공합니다. 업데이트를 적용하지 않으면 패치가 적용되지 않은 취약한 상태가 되고 공격에 취약한 환경이 발생합니다. 이러한 취약성이 악용될 수 있으며 데이터 손실, 데이터 반출, 랜섬웨어 및 리소스 남용으로 이어질 수 있습니다. 시스템 업데이트를 배포하려면 <a href="https://docs.microsoft.com/azure/automation/automation-update-management">업데이트 관리 솔루션을 사용하여 가상 머신에 대한 패치 및 업데이트를 관리</a>할 수 있습니다. 업데이트 관리는 소프트웨어 릴리스의 배포 및 유지 관리를 제어하는 프로세스입니다.</td>
    <td class="tg-lboi"; width=55%>- 컴퓨터에서 모니터링 에이전트 상태 문제를 해결해야 합니다.<br>- 가상 머신 확장 집합에 모니터링 에이전트를 설치해야 합니다.<br>- 컴퓨터에 모니터링 에이전트를 설치해야 합니다.<br>- 클라우드 서비스 역할에 대해 OS 버전을 업데이트해야 합니다.<br>- 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.<br>- 시스템 업데이트를 머신에 설치해야 합니다.<br>- 시스템 업데이트를 적용하려면 머신을 다시 시작해야 합니다.<br>- Kubernetes Services를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 합니다.<br>- 가상 머신에 모니터링 에이전트를 설치해야 합니다.<br>- Log Analytics 에이전트는 Windows 기반 Azure Arc 컴퓨터 (미리 보기)에 설치 되어야 합니다.<br>- Linux 기반 Azure Arc 컴퓨터 (미리 보기)에 Log Analytics 에이전트를 설치 해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">취약성 해결(최대 점수 6)</p></strong>취약성은 리소스의 기밀성, 가용성 또는 무결성을 손상시키기 위해 위협 행위자가 활용할 수 있는 약점입니다. <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">취약성 관리</a>는 조직의 노출을 줄이고, 엔드포인트 노출 영역을 강화하고, 조직의 복원력을 높이고, 리소스의 공격 노출 영역을 줄입니다. 위협 및 취약성 관리는 소프트웨어 및 보안 구성 오류에 대한 가시성을 제공하고 완화에 대한 권장 사항을 제공합니다.</td>
    <td class="tg-lboi"; width=55%>- SQL Database에서 고급 데이터 보안을 사용 하도록 설정 해야 합니다.<br>- Azure Container Registry 이미지의 취약성을 수정해야 합니다.<br>- SQL 데이터베이스의 취약성을 수정해야 합니다.<br>- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.<br>- SQL Managed Instance에서 취약성 평가를 사용 하도록 설정 해야 합니다.<br>- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.<br>- 취약성 평가 솔루션을 가상 머신에 설치해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">미사용 암호화 사용(최대 점수 4)</p></strong><a href="https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest">미사용 데이터 암호화</a>는 저장된 데이터에 대한 데이터 보호를 제공합니다. 미사용 데이터에 대한 공격에는 데이터가 저장된 하드웨어에 대한 물리적 액세스 권한을 얻습니다. Azures는 대칭 암호화를 사용하여 미사용 데이터를 암호화하고 해독합니다. 데이터가 스토리지에 쓰여질 때 대칭 암호화 키를 사용하여 데이터를 암호화합니다. 암호화 키를 사용하여 메모리에서 사용하도록 준비된 데이터의 암호를 해독합니다. 키는 ID 기반 액세스 제어 및 감사 정책으로 안전하게 보호되는 위치에 저장해야 합니다. 이러한 보안 위치 중 하나는 Azure Key Vault입니다. 공격자가 암호화된 데이터를 획득했지만 암호화 키는 획득하지 못한 경우 암호를 해독하지 않으면 데이터에 액세스할 수 없습니다.</td>
    <td class="tg-lboi"; width=55%>- 가상 머신에서 디스크 암호화를 적용해야 합니다.<br>- SQL Database 투명한 데이터 암호화를 사용 하도록 설정 해야 합니다.<br>- Automation 계정 변수를 암호화해야 합니다.<br>- Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 합니다.<br>- SQL 서버 TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">전송 중 데이터 암호화(최대 점수 4)</p></strong>데이터는 구성 요소, 위치 또는 프로그램 간에 전송될 때 "전송 중" 상태입니다. 전송 중인 데이터 보호에 실패하는 조직은 가로채기(man-in-the-middle) 공격, 도청 및 세션 하이재킹에 대해 취약합니다. SSL/TLS 프로토콜을 사용하여 데이터를 교환하고 VPN을 사용하는 것이 좋습니다. 인터넷을 통해 Azure Virtual Machine과 온-프레미스 위치 간에 암호화된 데이터를 전송하는 경우 <a href="https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN Gateway</a>와 같은 가상 네트워크 게이트웨이를 사용하여 암호화된 트래픽을 보낼 수 있습니다.</td>
    <td class="tg-lboi"; width=55%>- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.<br>- 함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.<br>- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.<br>- 스토리지 계정에 보안 전송을 사용하도록 설정해야 합니다.<br>- 웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">액세스 및 사용 권한 관리(최대 점수 4)</p></strong>보안 프로그램의 핵심 부분은 사용자에 게 작업을 수행하는 데 필요한 액세스 권한만 있는지 확인하는 것입니다. 즉, <a href="https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">최소 권한 액세스 모델</a>을 따릅니다.<br><a href="https://docs.microsoft.com/azure/role-based-access-control/overview">RBAC(역할 기반 Access Control)</a>에서 역할 할당을 만들어 리소스에 대한 액세스를 제어합니다. 역할 할당은 다음과 같은 세 가지 요소로 구성됩니다.<br>- <strong>보안 주체</strong>: 사용자가 해당 액세스를 요청하는 개체입니다.<br>- <strong>역할 정의</strong>: 사용 권한<br>- <strong>범위</strong>: 권한이 적용되는 리소스 세트입니다.</td>
    <td class="tg-lboi"; width=55%>- 더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다(미리 보기).<br>- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다(미리 보기).<br>- 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다(미리 보기).<br>- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다(미리 보기).<br>- 구독에 둘 이상의 소유자를 할당해야 합니다.<br>- Kubernetes Service(미리 보기)에서 RBAC(역할 기반 액세스 제어)를 사용해야 합니다.<br>- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.<br>- 관리 인증서 대신 서비스 주체를 사용 하 여 구독을 보호 해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">보안 구성 수정(최대 점수 4)</p></strong>잘못 구성된 IT 자산은 공격 당할 위험이 높습니다. 자산을 배포하고 마감일을 충족해야 할 때는 기본 강화 작업을 잊는 경우가 많습니다. 보안 구성 오류는 운영 체제 및 네트워크 어플라이언스에서 클라우드 리소스에 이르는 인프라의 모든 수준에서 나타날 수 있습니다.<br>Azure Security Center는 리소스 구성을 업계 표준, 규정 및 벤치마크의 요구 사항과 지속적으로 비교합니다. 조직에 중요한 관련 "규정 준수 패키지"(표준 및 기준)를 구성할 때 나타나는 간격을 토대로 CCEID와 잠재적 보안 영향에 대한 설명을 포함 하는 보안 권장 사항이 제공됩니다.<br>일반적으로 사용되는 패키지는 <a href="https://docs.microsoft.com/azure/security/benchmarks/introduction">Azure Security 벤치마크</a> 및 <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure Foundations 벤치마크 버전 1.1.0</a>입니다.</td>
    <td class="tg-lboi"; width=55%>- Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다.<br>- 컨테이너 보안 구성의 취약성을 수정해야 합니다.<br>- 머신 보안 구성의 취약성을 수정해야 합니다.<br>- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.<br>- 가상 머신에 모니터링 에이전트를 설치해야 합니다.<br>- 컴퓨터에 모니터링 에이전트를 설치해야 합니다.<br>- Log Analytics 에이전트는 Windows 기반 Azure Arc 컴퓨터 (미리 보기)에 설치 되어야 합니다.<br>- Linux 기반 Azure Arc 컴퓨터 (미리 보기)에 Log Analytics 에이전트를 설치 해야 합니다.<br>- 가상 머신 확장 집합에 모니터링 에이전트를 설치해야 합니다.<br>- 컴퓨터에서 모니터링 에이전트 상태 문제를 해결해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">무단 네트워크 액세스 제한(최대 점수 4)</p></strong>조직 내의 엔드포인트는 가상 네트워크에서 지원되는 Azure 서비스로의 직접 연결을 제공합니다. 서브넷의 가상 머신은 모든 리소스와 통신할 수 있습니다. 서브넷 내의 리소스 간에 통신을 제한하려면 네트워크 보안 그룹을 만들고, 서브넷에 연결합니다. 조직은 인바운드 및 아웃바운드 규칙을 만들어 권한이 없는 트래픽을 제한하고 보호할 수 있습니다.</td>
    <td class="tg-lboi"; width=55%>- 가상 머신에서 IP 전달을 사용하지 않도록 설정해야 합니다.<br>- Kubernetes Service에 권한 있는 IP 범위를 정의해야 합니다(미리 보기).<br>- (더 이상 사용되지 않음) App Services에 대한 액세스를 제한해야 합니다(미리 보기).<br>- (더 이상 사용되지 않음) IaaS NSG의 웹 애플리케이션에 대한 규칙을 강화해야 합니다.<br>- 가상 머신을 네트워크 보안 그룹과 연결해야 합니다.<br>- CORS에서 모든 리소스가 API 앱에 액세스하도록 허용하지 않아야 합니다.<br>- CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 합니다.<br>- CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하지 않아야 합니다.<br>- API 앱에 대해 원격 디버깅을 해제해야 합니다.<br>- 함수 앱에 대해 원격 디버깅을 해제해야 합니다.<br>- 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.<br>- 인터넷 연결 VM을 포함하는 허용되는 네트워크 보안 그룹에 대한 액세스를 제한해야 합니다.<br>- 인터넷 연결 가상 머신에 대한 네트워크 보안 그룹 규칙을 강화해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">적응형 애플리케이션 제어 적용(최대 점수 3)</p></strong>AAC(적응형 애플리케이션 제어)는 Azure 및 비 Azure 머신에서 실행할 수 있는 애플리케이션을 제어하도록 하는 지능적이고 자동화된 종단 간 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다.<br>Security Center는 machine learning을 사용 하 여 컴퓨터 그룹의 알려진 안전한 응용 프로그램 목록을 만듭니다.<br>승인 된 응용 프로그램 목록에 대 한이 혁신적인 접근 방식은 관리 복잡성 없이 보안 혜택을 제공 합니다.<br>AAC는 특정 애플리케이션 세트를 실행해야 하는 특화된 서버에 특히 적절합니다.</td>
    <td class="tg-lboi"; width=55%>- 가상 머신에서 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.<br>- 가상 머신에 모니터링 에이전트를 설치해야 합니다.<br>- 컴퓨터에 모니터링 에이전트를 설치해야 합니다.<br>- Log Analytics 에이전트는 Windows 기반 Azure Arc 컴퓨터 (미리 보기)에 설치 되어야 합니다.<br>- Linux 기반 Azure Arc 컴퓨터 (미리 보기)에 Log Analytics 에이전트를 설치 해야 합니다.<br>- 컴퓨터에서 모니터링 에이전트 상태 문제를 해결해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">데이터 분류 적용(최대 점수 2)</p></strong>민감도 및 비즈니스 영향을 기준으로 조직의 데이터를 분류하면 데이터에 대한 가치를 확인하고 할당할 수 있으며, 거버넌스에 대 한 전략 및 기본 사항을 제공합니다.<br><a href="https://docs.microsoft.com/azure/information-protection/what-is-information-protection">Azure Information Protection</a>을 데이터 분류를 지원할 수 있습니다. 이 기능은 암호화, ID 및 권한 부여 정책을 사용하여 데이터를 보호하고 데이터 액세스를 제한합니다. Microsoft에서 사용하는 일부 분류는 비즈니스 외, 퍼블릭, 일반, 기밀 및 극비를 사용합니다.</td>
    <td class="tg-lboi"; width=55%>- SQL 데이터베이스에서 중요한 데이터를 분류해야 합니다(미리 보기).</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">DDoS 공격으로부터 애플리케이션 보호(최대 점수 2)</p></strong>DDoS(분산 서비스 거부)는 리소스의 폭발적 사용을 야기하고 애플리케이션을 사용할 수 있게 만듭니다. <a href="https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection 표준</a>을 사용하여 세 가지 주요 유형의 DDoS 공격으로부터 조직을 보호합니다.<br>- <strong>대규모 공격</strong>에서는 합법적인 트래픽이 네트워크에 급증합니다. DDoS Protection Standard는 이러한 공격을 흡수하거나 자동으로 삭제하여 완화합니다.<br>- <strong>프로토콜 공격</strong>은 계층 3 및 계층 4 프로토콜 스택의 취약성을 악용하여 대상을 액세스 불능 상태로 만듭니다. DDoS Protection 표준은 악성 트래픽을 차단하여 이러한 공격을 완화합니다.<br>- <strong>리소스(애플리케이션) 계층 공격</strong>은 웹 애플리케이션 패킷을 대상으로 합니다. 웹 애플리케이션 방화벽 및 DDoS Protection 표준을 사용하여 이 유형의 공격으로부터 방어합니다.</td>
    <td class="tg-lboi"; width=55%>- DDoS Protection 표준을 사용하도록 설정해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Endpoint Protection 사용(최대 점수 2)</p></strong>엔드포인트가 맬웨어로부터 보호되도록 하기 위해 동작 센서는 엔드포인트 운영 체제에서 데이터를 수집 및 처리하고 분석을 위해 이 데이터를 프라이빗 클라우드로 보냅니다. 보안 분석은 빅 데이터, 기계 학습 및 기타 원본을 활용하여 위협에 대한 대응 방안을 권장합니다. 예를 들어 <a href="https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a>는 위협 인텔리전스를 사용하여 공격 방법을 식별하고 보안 경고를 생성합니다.<br>Security Center에서는 Endpoint Protection 솔루션 Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v12.1.1.1100, McAfee v10 for Windows, McAfee v10 for Linux 및 Sophos v9 for Linux를 지원합니다. Security Center가 이러한 솔루션을 검색하는 경우 Endpoint Protection 설치에 대한 권장 사항이 더 이상 표시되지 않습니다.</td>
    <td class="tg-lboi"; width=55%>- V가상 머신 확장 집합에서 Endpoint Protection 상태 실패를 해결해야 합니다.<br>- 컴퓨터에서 Endpoint Protection 상태 문제를 해결해야 합니다.<br>- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.<br>- 가상 머신에 Endpoint Protection 솔루션을 설치해야 합니다.<br>- 컴퓨터에서 모니터링 에이전트 상태 문제를 해결해야 합니다.<br>- 가상 머신 확장 집합에 모니터링 에이전트를 설치해야 합니다.<br>- 컴퓨터에 모니터링 에이전트를 설치해야 합니다.<br>- 가상 머신에 모니터링 에이전트를 설치해야 합니다.<br>- Log Analytics 에이전트는 Windows 기반 Azure Arc 컴퓨터 (미리 보기)에 설치 되어야 합니다.<br>- Linux 기반 Azure Arc 컴퓨터 (미리 보기)에 Log Analytics 에이전트를 설치 해야 합니다.<br>- 머신에 Endpoint Protection 솔루션을 설치해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">감사 및 로깅 사용(최대 점수 1)</p></strong>데이터 로깅은 과거 문제에 대한 인사이트를 제공하고, 잠재적인 문제를 방지하고, 애플리케이션 성능을 향상시킬 수 있으며 수동 작업을 자동화하는 기능을 제공합니다.<br>- <strong>제어/관리 로그</strong>는 <a href="https://docs.microsoft.com/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> 작업에 대한 정보를 제공합니다.<br>- <strong>데이터 평면 로그</strong>는 Azure 리소스 사용의 일부로 발생한 이벤트에 대한 정보를 제공합니다.<br>- <strong>처리된 이벤트</strong>는 처리되어 분석된 이벤트/경고에 대한 정보를 제공합니다.</td>
    <td class="tg-lboi"; width=55%>- SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.<br>- App Services의 진단 로그를 사용하도록 설정해야 합니다.<br>- Azure Data Lake Store의 진단 로그를 사용하도록 설정해야 합니다.<br>- Azure Stream Analytics의 진단 로그를 사용하도록 설정해야 합니다.<br>- Batch 계정의 진단 로그를 사용하도록 설정해야 합니다.<br>- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.<br>- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.<br>- IoT Hub의 진단 로그를 사용하도록 설정해야 합니다.<br>- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.<br>- Logic Apps의 진단 로그를 사용하도록 설정해야 합니다.<br>- Search Service의 진단 로그를 사용하도록 설정해야 합니다.<br>- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.<br>- Virtual Machine Scale Sets의 진단 로그를 사용하도록 설정해야 합니다.<br>- Batch 계정에서 메트릭 경고 규칙을 구성해야 합니다.<br>- SQL 감사 설정에는 중요한 작업을 캡처하도록 구성된 작업 그룹이 있어야 합니다.<br>- 감사 보존 기간(일)을 90일보다 큰 값으로 설정하여 SQL 서버를 구성해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Advanced threat protection 사용 (최대 점수 0)</p></strong>Azure Security Center의 위협 방지는 사용자 환경에 대 한 포괄적인 방어 기능을 제공 합니다. Security Center는 사용자 환경의 모든 영역에서 위협을 탐지하면 경고를 생성합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다.<br>각 위협 방지 번들은이 보안 제어에서 관련 권장 사항을 사용 하 여 설정할 수 있는 별도의 선택적 제공입니다.<br><a href="https://docs.microsoft.com/azure/security-center/threat-protection">Security Center에서 위협 방지에 대해 자세히 알아보세요</a>.</td>
    <td class="tg-lboi"; width=55%>- Azure SQL Database 서버에서 고급 데이터 보안을 사용 하도록 설정 해야 합니다.<br>- 컴퓨터의 SQL server에서 고급 데이터 보안을 사용 하도록 설정 해야 합니다.<br>- Virtual Machines에서 Advanced threat protection을 사용 하도록 설정 해야 합니다.<br>- Azure App Service 요금제에서 Advanced threat protection을 사용 하도록 설정 해야 함<br>- Azure Storage 계정에서 Advanced threat protection을 사용 하도록 설정 해야 함<br>- Azure Kubernetes Service 클러스터에서 Advanced threat protection을 사용 하도록 설정 해야 함<br>- Azure Container Registry 레지스트리에서 Advanced threat protection을 사용 하도록 설정 해야 합니다.<br>- Azure Key Vault 자격 증명 모음에서 Advanced threat protection을 사용 하도록 설정 해야 함</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">보안 모범 사례 구현(최대 점수 0)</p></strong>최신 보안 방침에서는 네트워크 경계의 "침해를 가정"합니다. 이러한 이유로 이 컨트롤의 많은 모범 사례는 ID 관리에 중점을 둡니다.<br>키와 자격 증명을 잃어 버리는 것은 일반적인 문제입니다. <a href="https://docs.microsoft.com/azure/key-vault/key-vault-overview">Azure Key Vault</a>는 키, .pfx 파일 및 암호를 암호화하여 키와 암호를 보호합니다.<br>VPN(가상 사설망)은 가상 머신에 안전하게 액세스할 수 있는 방법입니다. VPN을 사용할 수 없는 경우 <a href="https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks">Azure Multi-Factor Authentication</a>과 같은 복잡한 암호 및 2단계 인증을 사용합니다. 2단계 인증은 사용자 이름 및 암호에만 의존하는 약점을 방지합니다.<br>강력한 인증 및 권한 부여 플랫폼을 사용하는 것이 또 다른 모범 사례입니다. 페더레이션된 ID를 사용하면 조직에서 권한 있는 ID의 관리를 위임할 수 있습니다. 직원의 채용을 끝내고 해당 액세스 권한을 취소해야 하는 경우에도 이러한 과정이 중요합니다.</td>
    <td class="tg-lboi"; width=55%>- 구독에 최대 3명의 소유자를 지정해야 합니다.<br>- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.<br>- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.<br>- 방화벽 및 가상 네트워크 구성을 사용한 스토리지 계정에 대한 액세스를 제한해야 합니다.<br>- RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙을 이벤트 허브 네임스페이스에서 제거해야 합니다.<br>- SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.<br>- 관리형 인스턴스에서 Advanced Data Security를 사용하도록 설정해야 합니다.<br>- 이벤트 허브 인스턴스의 권한 부여 규칙을 정의해야 합니다.<br>- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.<br>- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.<br>- 서브넷을 네트워크 보안 그룹과 연결해야 합니다.<br>- [미리 보기] Windows Exploit Guard를 사용하도록 설정해야 합니다. <br>- [미리 보기] 모드 게스트 구성 에이전트를 설치해야 합니다.<br>- 인터넷에 연결 되지 않은 가상 컴퓨터는 네트워크 보안 그룹을 사용 하 여 보호 해야 합니다.</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>보안 점수 FAQ

### <a name="why-has-my-secure-score-gone-down"></a>보안 점수가 중단된 이유는 무엇인가요?
점수가 계산 되는 방식에 대 한 변경 내용을 포함 하는 향상 된 보안 점수로 전환 된 Security Center. 이제 리소스가 포인트를 받으려면 모든 권장 사항을 해결해야 합니다. 점수도 0-10 크기로 변경되었습니다.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>보안 컨트롤의 4가지 권장 사항 중 3가지를 해결하는 경우 보안 점수가 변경되나요?
아니요. 단일 리소스에 대한 모든 권장 사항을 수정할 때까지 변경되지 않습니다. 컨트롤에 대해 최대 점수를 얻으려면 모든 리소스에 대해 모든 권장 사항을 수정해야 합니다.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>이전 보안 점수 환경을 계속 사용할 수 있나요? 
아니요. 에 대해 나란히 실행 되는 동안에는 쉽게 전환할 수 있습니다. 이전 모델은 이제 사용 되지 않습니다. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>권장 사항이 나에게 적용되지 않는 경우 정책에서 사용하지 않도록 설정해도 보안 컨트롤이 이행되고 보안 점수가 업데이트되나요?
예. 사용자 환경에 적용할 수 없는 경우 권장 사항을 사용하지 않도록 설정하는 것이 좋습니다. 특정 권장 사항을 사용하지 않도록 설정하는 방법에 대한 지침은 [보안 정책 사용 안 함](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies)을 참조하세요.

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>보안 컨트롤에서 보안 점수에 대해 0포인트를 제공하면 무시해도 되나요?
경우에 따라 컨트롤 최대 점수가 0보다 클 수 있지만 영향은 0입니다. 리소스 수정을 위한 증분 점수가 크지 않은 경우 0으로 반올림됩니다. 이러한 권장 사항은 여전히 보안을 향상시키므로 무시하지 마세요. 유일한 예외는 "추가 모범 사례" 컨트롤입니다. 이러한 권장 사항을 수정하면 점수가 증가되지 않지만 전반적인 보안은 향상됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수와 해당 점수가 적용되는 보안 컨트롤에 대해 설명했습니다. 관련 자료는 다음 문서를 참조하세요.

- [권장 사항의 다양한 요소에 대해 알아보기](security-center-recommendations.md)
- [권장 사항을 수정하는 방법 알아보기](security-center-remediate-recommendations.md)
