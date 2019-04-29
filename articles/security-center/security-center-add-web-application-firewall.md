---
title: Azure Security Center에서 웹 애플리케이션 방화벽 추가 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **웹 애플리케이션 방화벽 추가** 및 **애플리케이션 보호 완료**를 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 63852ccab842f11f30bcbe695206fedf72931911
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706240"
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Azure Security Center에서 웹 애플리케이션 방화벽 추가
Azure Security Center에서는 웹 애플리케이션을 보호하기 위해 Microsoft 파트너의 WAF(웹 애플리케이션 방화벽)를 추가하도록 권장할 수 있습니다. 이 문서에서는 이 권장 사항을 적용하는 방법의 예를 보여 줍니다.

공개 인바운드 웹 포트(80,443)으로 연결된 네트워크 보안 그룹에 있는 모든 공용 연결 IP(인스턴스 수준 IP 또는 부하 분산된 IP)에 대해 WAF 권장 사항이 표시됩니다.

Security Center에서는 가상 머신과 [격리된](https://azure.microsoft.com/pricing/details/app-service/windows/) 서비스 계획 아래에 배포된 외부 ASE(App Service 환경)에 있는 웹 애플리케이션을 대상으로 한 공격을 방어할 수 있도록 WAF를 프로비전할 것을 권장합니다. 앱은 격리된 전용 Azure 환경에 호스팅되기 때문에 온-프레미스 네트워크와 안전한 연결이 필요하거나 추가적인 성능 및 규모가 필요한 앱에 이상적입니다. 앱은 격리된 환경에 있는 것 외에도 앱에는 외부 IP 주소 부하 분산 장치가 필요합니다. ASE에 대한 자세한 내용을 보려면 [App Service Environment 설명서](../app-service/environment/intro.md)를 참조하세요.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  이 문서는 단계별 가이드가 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항**에서 **웹 애플리케이션 방화벽을 사용하여 웹 애플리케이션 보호**를 선택합니다.
   ![웹 애플리케이션 보안][1]
2. **웹 애플리케이션 방화벽을 사용하여 웹 애플리케이션을 보호**에서 웹 애플리케이션을 선택합니다. **웹 애플리케이션 방화벽 추가**가 열립니다.
   ![웹 애플리케이션 방화벽 추가][2]
3. 기존 웹 애플리케이션 방화벽(사용 가능한 경우)을 사용하도록 선택하거나, 새로 만들 수 있습니다. 이 예제에서는 기존 WAF가 없으므로 WAF를 만들어 보겠습니다.
4. WAF를 만들려면 통합 파트너 목록에서 솔루션을 선택합니다. 이 예제에서는 **Barracuda Web Application Firewall**을 선택합니다.
5. 파트너 솔루션에 대한 정보를 제공하는 **Barracuda Web Application Firewall**이 열립니다. **만들기**를 선택합니다.

   ![방화벽 정보 블레이드][3]

6. **새 웹 애플리케이션 방화벽**이 열립니다. 이 블레이드에서 **VM 구성** 단계를 수행하고 **WAF 정보**를 입력할 수 있습니다. **VM 구성**을 선택합니다.
7. **VM 구성**에서 WAF를 실행할 가상 머신을 스핀업하는 데 필요한 정보를 입력합니다.

   ![VM 구성][4]
   
8. **새 웹 애플리케이션 방화벽**으로 돌아가서 **WAF 정보**를 선택합니다. **WAF 정보**에서 WAF 자체를 구성합니다. 7단계에서는 WAF가 실행되는 가상 머신을 구성하고 8단계에서는 WAF 자체를 프로비전할 수 있습니다.

## <a name="finalize-application-protection"></a>애플리케이션 보호 완료
1. **권장 사항**으로 돌아갑니다. WAF를 만든 후에 **애플리케이션 보호 완료**라고 하는 새 항목이 생성되었습니다. 새 항목을 사용하면 Azure Virtual Network 내에서 WAF를 연결하는 프로세스를 완료하여 애플리케이션을 보호할 수 있다는 점을 알 수 있습니다.

   ![애플리케이션 보호 완료][5]

2. **애플리케이션 보호 완료**를 선택합니다. 새 블레이드가 열립니다. 해당 트래픽 경로를 전환해야 하는 웹 애플리케이션을 확인할 수 있습니다.
3. 웹 애플리케이션을 선택합니다. 웹 애플리케이션 방화벽 설정을 완료하는 단계를 알려주는 블레이드가 열립니다. 단계를 완료한 다음 **트래픽 제한**을 선택합니다. 그려면 Security Center가 연결을 수행합니다.

   ![트래픽 제한][6]

> [!NOTE]
> 기존 WAF 배포에 이러한 애플리케이션을 추가하여 보안 센터에서 여러 웹 애플리케이션을 보호할 수 있습니다.
>
>

이제 해당 WAF의 로그가 완전히 통합되었습니다. Security Center에서 자동으로 로그를 수집 및 분석하기 시작하여 중요한 보안 경고를 사용자에게 드러낼 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터 권장 사항 "웹 애플리케이션 추가"를 구현하는 방법을 보여 주었습니다. 웹 애플리케이션 방화벽 구성에 대한 자세한 내용은 다음을 참조하세요.

* [App Service Environment에 대한 웹 애플리케이션 방화벽(WAF) 구성](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) -- Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
