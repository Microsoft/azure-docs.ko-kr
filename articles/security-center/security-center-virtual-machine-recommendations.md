---
title: Azure Security Center의 가상 머신 관리 | Microsoft Docs
description: 이 문서에서는 가상 머신, 컴퓨터, 웹 앱 및 App Service 환경을 보호하는 방법에 대한 Azure Security Center 권장 사항을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: a4aaf440856746895a31914aeee2bddec2ce23f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544993"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Azure Security Center 리소스 권장 사항 이해하기


## <a name="recommendations"></a>권장 사항
아래의 표를 참조하면 사용 가능한 Compute 및 App 서비스 권장 사항을 이해하고, 이것을 적용할 경우 수행되는 사항을 이해하는 데 도움이 됩니다.

### <a name="computers"></a>컴퓨터
| 권장 사항 | 설명 |
| --- | --- |
| [구독에 대해 데이터 수집 활성화](security-center-enable-data-collection.md) |구독 또는 구독의 VM(가상 머신) 각각에 대해 보안 정책에서 데이터 수집을 켜는 것이 좋습니다. |
| [Azure Storage 계정에 암호화 사용](security-center-enable-encryption-for-storage-account.md) | 미사용 데이터에 대한 Azure Storage 서비스 암호화를 사용하도록 권장합니다. SSE(Storage 서비스 암호화)는 Azure Storage에 기록되는 데이터를 암호화하고 검색 전 해독하는 방식으로 작동합니다. SSE는 현재 Azure Blob service에만 사용할 수 있으며 블록 blob, 페이지 blob 및 추가 blob에 사용할 수 있습니다. 자세한 내용을 알아보려면 [미사용 데이터에 대한 Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.</br>SSE는 Resource Manager 저장소 계정에만 지원됩니다. 클래식 저장소 계정은 현재 지원되지 않습니다. 클래식 및 Resource Manager 배포 모델을 이해하려면 [Azure 배포 모델](../azure-classic-rm.md)을 참조하세요. |
| [보안 구성 수정](security-center-remediate-os-vulnerabilities.md) |OS 구성을 권장 보안 구성 규칙에 맞추도록 권장합니다. 예를 들어 암호 저장을 허용하지 않습니다. |
| [시스템 업데이트 적용](security-center-apply-system-updates.md) |누락된 시스템 보안 및 중요 업데이트를 VM에 배포하는 것이 좋습니다. |
| [Just-In-Time 네트워크 액세스 제어 적용](security-center-just-in-time.md) | Just-In-Time VM 액세스만 적용해야 합니다. Just-In-Time 기능은 미리 보기로 제공되며 Security Center의 표준 계층에서 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요. |
| [시스템 업데이트 후 다시 부팅](security-center-apply-system-updates.md#reboot-after-system-updates) |시스템 업데이트 적용 프로세스를 완료하려면 VM을 다시 부팅하는 것이 좋습니다. |
| [Endpoint Protection 설치](security-center-install-endpoint-protection.md) |VM(Windows VM만 해당)에 맬웨어 방지 프로그램을 프로비전하는 것이 좋습니다. |
| [VM 에이전트 사용](security-center-enable-vm-agent.md) |VM 에이전트가 필요한 VM을 확인할 수 있습니다. 패치 검색, 기준 검색 및 맬웨어 방지 프로그램을 프로비전하려면 VM에 VM 에이전트가 설치되어 있어야 합니다. Azure Marketplace에서 배포된 VM에 VM 에이전트가 기본적으로 설치됩니다. [VM 에이전트 및 확장 - 2부](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) 문서에 VM 에이전트 설치 방법이 설명되어 있습니다. |
| [디스크 암호화 적용](security-center-apply-disk-encryption.md) |Azure 디스크 암호화(Windows 및 Linux VM)를 사용하여 VM 디스크를 암호화하는 것이 좋습니다. VM에서 OS 및 데이터 볼륨에 암호화를 사용하는 것이 좋습니다. |
| [OS 버전 업데이트](security-center-update-os-version.md) |클라우드 서비스의 OS(운영 체제) 버전을 OS 제품군에 대해 사용할 수 있는 가장 최신 버전으로 업데이트하는 것이 좋습니다.  Cloud Services에 대한 자세한 내용은 [Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요. |
| [취약점 평가 설치되지 않음](security-center-vulnerability-assessment-recommendations.md) |VM에 취약점 평가 솔루션을 설치하는 것이 좋습니다. |
| [취약점 해결](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |VM에 설치된 취약점 평가 솔루션에 감지되는 시스템 및 애플리케이션 취약점을 볼 수 있습니다. |

### App Services <a name="app-services"></a>
| 권장 사항 | 설명 |
| --- | --- |
| App Service에 HTTPS를 통해서만 액세스 가능 | HTTPS만을 통해 App Service의 액세스를 제한하는 것이 좋습니다. |
| 웹 애플리케이션에 대해 웹 소켓이 사용되지 않도록 설정됨| 웹 애플리케이션 내에서 웹 소켓 사용을 주의 깊게 검토하는 것이 좋습니다.  웹 소켓 프로토콜은 다양한 유형의 보안 위협에 취약합니다. |
| 웹 애플리케이션에 대한 사용자 지정 도메인 사용 | 사용자 지정 도메인을 사용하여 피싱 및 기타 DNS 관련 공격 같은 일반적인 공격에서 웹 애플리케이션을 보호하는 것이 좋습니다. |
| 웹 애플리케이션에 대한 IP 제한 구성 | 애플리케이션에 액세스할 수 있는 IP 주소 목록을 정의하는 것이 좋습니다.  IP 제한 사항을 사용하여 일반적인 공격으로부터 웹 애플리케이션을 보호합니다. |
| 애플리케이션에 액세스하는 모든('*') 리소스를 허용하지 마십시오. | WEBSITE_LOAD_CERTIFICATES 매개 변수를 ‘*’로 설정하지 않는 것이 좋습니다. 매개 변수를 '*'로 설정하는 것은 모든 인증서가 웹 애플리케이션 개인 인증서 저장소로 로드되는 것을 의미합니다.  사이트는 런타임 시 모든 인증서에 대한 액세스가 필요하지 않으므로 최소 권한의 원칙이 악용될 수 있습니다. |
| CORS에서 모든 리소스가 애플리케이션에 액세스하도록 허용하지 않아야 함 | 필요한 도메인만 웹 애플리케이션과 상호 작용할 수 있도록 하는 것이 좋습니다. CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. |
| 웹 애플리케이션에 대해 지원되는 최신 .NET Framework 사용 | 최신 보안 클래스에 대해 최신 .NET Framework 버전을 사용하는 것이 좋습니다. 오래된 클래스 및 형식을 사용하면 애플리케이션이 취약해질 수 있습니다. |
| 웹 애플리케이션에 대해 지원되는 최신 Java 버전 사용 | 최신 보안 클래스에 대해 최신 Java 버전을 사용하는 것이 좋습니다. 오래된 클래스 및 형식을 사용하면 애플리케이션이 취약해질 수 있습니다. |
| 웹 애플리케이션에 대해 지원되는 최신 PHP 버전 사용 | 최신 보안 클래스에 대해 최신 PHP 버전을 사용하는 것이 좋습니다. 오래된 클래스 및 형식을 사용하면 애플리케이션이 취약해질 수 있습니다. |
| [웹 애플리케이션 방화벽 추가](security-center-add-web-application-firewall.md) |웹 엔드포인트에 WAF(웹 애플리케이션 방화벽)를 배포하는 것이 좋습니다. 공개 인바운드 웹 포트(80,443)으로 연결된 네트워크 보안 그룹에 있는 모든 공용 연결 IP(인스턴스 수준 IP 또는 부하 분산된 IP)에 대해 WAF 권장 사항이 표시됩니다.</br></br>Security Center에서는 가상 머신과 App Service 환경에 있는 웹 애플리케이션을 대상으로 한 공격을 방어할 수 있도록 WAF를 프로비전할 것을 권장합니다. ASE(App Service 환경)는 Azure App Service의 [프리미엄](https://azure.microsoft.com/pricing/details/app-service/) 서비스 계획 옵션으로, Azure App Service 앱의 안전한 실행을 위해 완전히 격리된 전용 환경을 제공합니다. ASE에 대한 자세한 내용을 보려면 [App Service Environment 설명서](../app-service/environment/intro.md)를 참조하세요.</br></br>기존 WAF 배포에 이러한 애플리케이션을 추가하여 보안 센터에서 여러 웹 애플리케이션을 보호할 수 있습니다. |
| [애플리케이션 보호 완료](security-center-add-web-application-firewall.md#finalize-application-protection) |WAF 구성을 완료하려면 트래픽 경로가 WAF 어플라이언스로 전환되어야 합니다. 이 권장 사항을 따르면 필요한 설정 변경이 완료됩니다. |
| 웹 애플리케이션에 대해 지원되는 최신 Node.js 버전 사용 | 최신 보안 클래스에 대해 최신 Node.js 버전을 사용하는 것이 좋습니다. 오래된 클래스 및 형식을 사용하면 애플리케이션이 취약해질 수 있습니다. |
| CORS에서 모든 리소스가 Function App에 액세스하도록 허용하지 않아야 함 | 필요한 도메인만 웹 애플리케이션과 상호 작용할 수 있도록 하는 것이 좋습니다. CORS(교차 원본 리소스 공유)는 함수 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. |
| Function App에 대해 사용자 지정 도메인 사용 | 사용자 지정 도메인을 사용하여 피싱 및 기타 DNS 관련 공격 같은 일반적인 공격에서 함수 앱을 보호하는 것이 좋습니다. |
| Function App에 대한 IP 제한 구성 | 애플리케이션에 액세스할 수 있는 IP 주소 목록을 정의하는 것이 좋습니다. IP 제한 사항을 사용하여 일반적인 공격으로부터 함수 앱을 보호합니다. |
| Function App에 HTTPS를 통해서만 액세스 가능 | HTTPS만을 통해 함수 앱의 액세스를 제한하는 것이 좋습니다. |
| Function App에 대해 원격 디버깅을 해제해야 함 | 더 이상 사용할 필요가 없는 경우 Function App에 대한 디버깅을 해제하는 것이 좋습니다. 원격 디버깅에 Function App에서 열리는 인바운드 포트가 필요합니다. |
| Function App에 대해 웹 소켓이 사용되지 않도록 설정됨 | Function App 내에서 웹 소켓 사용을 주의 깊게 검토하는 것이 좋습니다. 웹 소켓 프로토콜은 다양한 유형의 보안 위협에 취약합니다. |


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Security Center에서 ID 및 액세스 모니터링](security-center-identity-access.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
* [Azure Security Center에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
