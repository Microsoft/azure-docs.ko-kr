---
title: Virtual WAN에 대한 Azure 보안 기준
description: Virtual WAN 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e4bbc1565cec2c356e916f813a6a334648d82754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726736"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Virtual WAN에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 2.0](../security/benchmarks/overview.md)의 지침을 Microsoft Azure Virtual WAN에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Virtual WAN에 적용되는 관련 지침에서 정의된 **보안 컨트롤** 에 따라 그룹화됩니다. Virtual WAN에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

Virtual WAN이 완전히 Azure Security Benchmark에 매핑되는 방법을 보려면 [전체 Virtual WAN 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)을 참조하세요.

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 내부 트래픽에 대한 보안 구현

**지침**: Microsoft Azure Virtual WAN은 사용자 지정 라우팅 기능을 제공하고 ExpressRoute 트래픽에 대한 암호화를 제공합니다. 모든 경로 관리는 가상 허브 라우터에서 제공하며, 이렇게 하면 가상 네트워크 간에 전송 연결이 가능합니다. Virtual WAN을 사용하여 ExpressRoute 트래픽을 암호화하면 공용 인터넷을 통하거나 공용 IP 주소를 사용하지 않고 ExpressRoute를 통해 온-프레미스 네트워크와 Azure 가상 네트워크 간에 암호화된 전송을 제공합니다. 

- [ExpressRoute 트래픽 암호화](virtual-wan-about.md#encryption)

- [Virtual WAN에서 프라이빗 링크 사용](howto-private-link.md)

- [사용자 지정 라우팅 시나리오](scenario-any-to-any.md)

- [사용자 지정 경로 테이블 설명서](how-to-virtual-hub-routing.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="ns-2-connect-private-networks-together"></a>NS-2: 프라이빗 네트워크를 함께 연결 

**지침**: Microsoft Azure ExpressRoute는 Azure Virtual WAN에 대한 프라이빗 연결을 제공합니다. ExpressRoute 연결은 공용 인터넷을 사용하지 않으므로 ExpressRoute는 일반적인 인터넷 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧습니다. 가상 사설망을 사용하여 S2S(사이트 간) VPN 또는 P2S(지점 및 사이트 간) VPN을 통해 Azure에 연결할 수도 있습니다.

- [가상 WAN의 ExpressRoute](virtual-wan-expressroute-portal.md)

- [사이트 간 VPN 개요](virtual-wan-site-to-site-portal.md)

- [지점 및 사이트 간 VPN 개요](virtual-wan-point-to-site-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 외부 네트워크 공격으로부터 애플리케이션 및 서비스 보호

**지침**: Virtual WAN은 기존 네트워크 보호로 보호해야 하는 외부 네트워크에 엔드포인트를 노출하지 않습니다. 가상 네트워크 보호 서비스를 사용하여 Spoke 가상 네트워크(가상 허브에 연결된 모든 가상 네트워크)의 리소스를 자유롭게 보호할 수 있습니다. 

Azure Firewall을 사용하여 인터넷 및 기타 외부 위치에서 잠재적 악성 트래픽에 대한 애플리케이션 및 서비스를 보호합니다. 

Azure에서 제공하는 DDoS Protection을 선택하여 Azure Virtual Network에 대한 공격으로부터 자산을 보호합니다. Azure Security Center를 사용하여 네트워크 관련 리소스와 관련하여 잘못된 구성을 할 위험을 검색합니다.

- [Azure Firewall 설명서](../firewall/index.yml)

- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](../ddos-protection/manage-ddos-protection.md) 

- [Azure Security Center 권장 사항](../security-center/recommendations-reference.md#recs-networking)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: IDS/IPS(침입 탐지 시스템/침입 방지 시스템) 배포

**지침**: Virtual WAN은 Microsoft에서 관리하는 서비스입니다. 기본 침입 감지 또는 침입 방지 기능을 제공하지 않습니다. 그러나 Azure Firewall을 통해 Virtual WAN에 제공되는 보안 기능이 있어 통합된 정책 제어 지점을 사용할 수 있습니다. Azure Firewall 정책을 만들고 정책을 Virtual WAN 허브에 연결하여 필요한 Azure Firewall 리소스가 배포된 상태에서 기존 Virtual WAN 허브가 보안 가상 허브로 작동하도록 허용할 수 있습니다.

- [Virtual WAN 허브에 Azure Firewall 구성](howto-firewall.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: 네트워크 보안 규칙 단순화

**지침**: Virtual Network 서비스 태그를 활용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의하여 네트워크 보안 규칙을 단순화합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 서비스 태그 이름을 규칙의 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

- [애플리케이션 보안 그룹 이해 및 사용](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Azure Firewall 설명서](../firewall/index.yml)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: 보안 DNS(Domain Name Service)

**지침**: Azure Firewall을 사용하여 Virtual WAN에 보안 DNS 기능이 제공됩니다. 클라이언트 가상 머신에서 DNS 서버로의 DNS 요청에 대한 중개자가 되는 DNS 프록시로 작동하도록 Azure Firewall을 구성합니다. 사용자 지정 DNS 서버 구성의 경우 DNS 프록시를 활성화하여 DNS 확인 불일치를 방지하고 네트워크 규칙에서 정규화된 도메인 이름 필터링을 사용합니다. 

- [Azure Firewall 설명서](../firewall/index.yml)

- [Azure Firewall DNS 설정](../firewall/dns-settings.md)

- [Private Link를 사용하여 Azure 방화벽을 DNS 전달자로 사용](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory를 중앙 ID 및 인증 시스템으로 표준화

**지침**: Azure AD(Azure Active Directory)는 Azure 서비스에 대한 기본 ID 및 액세스 관리 서비스입니다. Virtual WAN을 포함합니다. Azure AD를 표준화하여 다음에서 조직의 ID 및 액세스 관리를 관리합니다.

- Microsoft 클라우드 리소스(예: Azure Portal, Azure Storage, Azure Virtual Machines(Linux 및 Windows), Azure Key Vault, PaaS(Platform as a Service) 및 SaaS(Software as a Service) 애플리케이션).
- 조직의 리소스(예: Azure의 애플리케이션 또는 회사 네트워크 리소스)

조직의 클라우드 보안 관행에서 Azure AD를 최우선 순위로 보호하세요. Security Center의 보안 점수 기능으로 ID 및 보안 상태를 평가하여 구성이 Microsoft의 모범 사례 권장 사항과 얼마나 일치하는지 측정합니다. 필요에 따라 보안 상태를 개선하기 위해 Microsoft의 모범 사례 권장 사항을 구현합니다.

Azure AD는 또한 Microsoft 계정이 없는 사용자가 애플리케이션 및 리소스에 로그인할 수 있게 하는 외부 ID를 지원합니다. 

참조 링크에서 지점 및 사이트 간 VPN 시나리오에서 Azure AD 사용에 대한 정보를 검토합니다.

- [P2S OpenVPN 프로토콜 연결을 위한 Azure AD(Active Directory) 테넌트 만들기](openvpn-azure-ad-tenant-multi-app.md)

- [사용자 VPN에 대한 Azure Active Directory 인증 구성](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory의 테넌시](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용

**지침**: 현재 Azure AD(Azure Active Directory) 인증은 Virtual WAN 지점 및 사이트 간 VPN과의 통합을 통해 제공됩니다.

Azure AD(Azure Active Directory)는 Azure 서비스에 대한 기본 ID 및 액세스 관리 서비스입니다. Azure AD는 다단계 인증을 통한 강력한 인증 컨트롤 및 암호 없는 메서드를 지원합니다.

Azure AD는 강력한 인증 제어를 위해 다음을 권장합니다.

- 다단계 인증 - Azure AD 다단계 인증을 사용하고 보안 모범 사례에 대한 Azure Security Center의 ID 및 액세스 관리 권장 사항을 따릅니다. 로그인 조건 및 위험 요소에 따라 모든 사용자, 선택한 사용자 또는 사용자별 수준에서 다단계 인증을 적용합니다.

- 암호 없는 인증 – 세 가지 암호 없는 인증 옵션을 사용할 수 있습니다. 여기에는 비즈니스용 Windows Hello, Microsoft Authenticator 앱 및 스마트 카드와 같은 온-프레미스 인증 방법이 포함됩니다.

관리자 및 권한이 있는 사용자에게 가장 높은 수준의 강력한 인증 방법이 사용되는지 확인한 다음, 다른 사용자에게 강력한 인증 정책을 적용합니다.

- [Virtual WAN용 P2S VPN에서 MFA를 사용하는 방법](openvpn-azure-ad-mfa.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: Azure AD 인증을 사용하여 VPN 사용자(지점 및 사이트 간)에 대해 Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정합니다. 사용자별로 다단계 인증을 구성하거나 조건부 액세스로 다단계 인증을 활용합니다. 조건부 액세스를 사용하면 두 번째 요소의 승격 방법을 보다 세밀하게 제어 할 수 있습니다. VPN에만 다단계 인증을 할당하고 Azure AD 테넌트에 연결된 다른 애플리케이션을 제외할 수 있습니다. 

Azure AD 인증은 OpenVPN 프로토콜을 사용하는 게이트웨이와 Windows를 실행하는 클라이언트에서만 사용할 수 있습니다.

- [조건부 액세스란?](../active-directory/conditional-access/overview.md)

- [사용자 VPN에 대한 Azure Active Directory 인증 구성](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: 의도하지 않은 자격 증명 노출 제거

**지침**: Virtual WAN의 사이트 간 VPN은 고객이 Azure Key Vault에서 검색, 생성 및 관리하는 PSK(사전 공유 키)를 사용합니다. 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

GitHub의 경우 네이티브 암호 검색 기능을 사용하여 코드 내에서 자격 증명 또는 다른 형태의 암호를 식별할 수 있습니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub 암호 검색](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침**: Azure Virtual WAN은 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 구독 및 관리 그룹에 대한 권한 있는 액세스 권한이 부여된 계정을 제한함으로써 중요 비즈니스용 시스템에 대한 액세스를 격리합니다.

또한 중요 비즈니스용 시스템에 설치된 에이전트를 사용하여 Active Directory 도메인 컨트롤러, 보안 도구 및 시스템 관리 도구 등의 중요 비즈니스용 액세스에 대한 관리 액세스가 있는 관리, ID 및 보안 시스템에 대한 액세스를 제한해야 합니다. 관련 관리 및 보안 시스템을 손상시키는 공격자는 중요 비즈니스용 자산을 손상시키기 위해 관련 시스템을 즉시 무기화할 수 있습니다.

모든 유형의 액세스 제어는 일관된 액세스 제어를 보장하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [관리 그룹 액세스](../governance/management-groups/overview.md#management-group-access) 

- [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)를 참조하세요.

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: 전송 중인 중요한 정보 암호화

**지침**: 연결 요구 사항에 대해 Virtual WAN과 함께 지점 및 사이트 간 VPN, 사이트 간 VPN 및 암호화된 Express 경로를 사용합니다. VPN 암호화는 전송 중인 데이터를 '대역 외' 공격(예: 트래픽 캡처)으로부터 보호하여 공격자가 데이터를 읽거나 수정할 수 없도록 합니다. 

- [지점 및 사이트 간 VPN](virtual-wan-point-to-site-portal.md)

- [사이트 간 VPN](virtual-wan-site-to-site-portal.md)

- [암호화된 ExpressRoute](vpn-over-expressroute.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 이를 통해, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

참고: 워크로드 및 서비스에 대한 가시성을 얻으려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: 보안 팀에 자산 인벤토리 및 메타데이터에 대한 액세스 권한이 있는지 확인

**지침**: Azure 리소스, 리소스 그룹 및 구독에 태그를 적용하여 논리적인 분류법으로 구성합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다. Azure Virtual WAN은 자산 템플릿을 내보낼 수 있는 Azure Resource Manager 기반 리소스 배포도 지원합니다. 

- [리소스 명명 및 태그 지정 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Azure Security Center 자산 인벤토리 관리](../security-center/asset-inventory.md)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: 승인된 Azure 서비스만 사용

**지침**: Azure Monitor를 사용하여 승인되지 않은 서비스가 검색되면 경고를 트리거하는 규칙을 만듭니다. Virtual WAN은 많은 네트워킹, 보안 및 라우팅 기능을 결합하여 단일 운영 인터페이스를 제공합니다. Virtual WAN VPN 게이트웨이, ExpressRoute 게이트웨이 및 Azure Firewall에는 Azure Monitor를 통해 사용할 수 있는 로깅 및 메트릭이 있습니다. 
 

- [Virtual WAN 로그 및 메트릭](logs-metrics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: Azure Conditional Access를 사용하여 "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)를 참조하세요.

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 대한 위협 탐지 사용

**지침**: Virtual WAN을 사용하는 지점 및 사이트 간 VPN은 Azure AD(Azure Active Directory)와 통합됩니다. Azure AD는 Azure AD 보고에서 볼 수 있거나 더 정교한 위협 모니터링 및 분석 사용 사례를 위해 Azure Monitor, Azure Sentinel, SIEM 또는 모니터링 도구와 통합할 수 있는 다음과 같은 사용자 로그를 제공합니다. 이러한 항목은 다음과 같습니다.

- 로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.
- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예로는 사용자, 앱, 그룹, 역할, 정책의 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.
- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

Azure Security Center를 사용하여 구독에서 더 이상 사용되지 않는 계정을 포함하여 과도한 인증 시도 실패와 같은 특정 의심스러운 활동에 대한 경고를 만듭니다. 기본 보안 예방 조치 모니터링 외에도 Security Center의 위협 방지 모듈을 사용하여 개별 Azure 컴퓨팅 리소스(가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(SQL DB 및 스토리지) 및 Azure 서비스 계층에서 더 심층적인 보안 경고를 수집합니다. 이 기능을 사용하면 개별 리소스 내에서 비정상 계정 활동을 볼 수 있습니다.

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure ID 보호 사용](../active-directory/identity-protection/overview-identity-protection.md)

- [Virtual WAN 허브에 Azure Firewall 구성](howto-firewall.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure ID 및 액세스 관리에 위협 탐지 사용

**지침**: Virtual WAN을 사용하는 지점 및 사이트 간 VPN은 Azure AD(Azure Active Directory)와 통합됩니다. Azure AD는 Azure AD 보고에서 볼 수 있거나 더 정교한 위협 모니터링 및 분석 사용 사례를 위해 Azure Monitor, Azure Sentinel, SIEM 또는 모니터링 도구와 통합할 수 있는 다음과 같은 사용자 로그를 제공합니다. 이러한 항목은 다음과 같습니다.

- 로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.
- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예로는 사용자, 앱, 그룹, 역할, 정책의 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.
- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

Azure Security Center를 사용하여 구독에서 더 이상 사용되지 않는 계정을 포함하여 과도한 인증 시도 실패와 같은 특정 의심스러운 활동에 대한 경고를 만듭니다. 기본 보안 예방 조치 모니터링 외에도 Security Center의 위협 방지 모듈을 사용하여 개별 Azure 컴퓨팅 리소스(가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(SQL DB 및 스토리지) 및 Azure 서비스 계층에서 더 심층적인 보안 경고를 수집합니다. 이 기능을 사용하면 개별 리소스 내에서 비정상 계정 활동을 볼 수 있습니다.

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure ID 보호 사용](../active-directory/identity-protection/overview-identity-protection.md)

- [Virtual WAN 허브에 Azure Firewall 구성](howto-firewall.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 활동에 대한 로깅 사용

**지침**: Azure Monitor를 사용하여 Azure Virtual WAN을 모니터링합니다. Virtual WAN은 많은 네트워킹, 보안 및 라우팅 기능을 결합하여 단일 운영 인터페이스를 제공합니다. Virtual WAN VPN 게이트웨이, ExpressRoute 게이트웨이 및 Azure Firewall에는 Azure Monitor를 통해 사용할 수 있는 로깅 및 메트릭이 있습니다. 활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다. Azure 활동 로그(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다.

Virtual WAN에 대한 다양한 진단 로그도 사용할 수 있으며 Azure Portal을 사용하여 Virtual WAN 리소스에 대해 구성할 수 있습니다.  Log Analytics로 보내거나 이벤트 허브로 스트리밍하거나 단순히 스토리지 계정에 보관하도록 선택할 수 있습니다. 
 

- [Virtual WAN 로그 및 메트릭](logs-metrics.md)

- [Azure Firewall 로그 및 메트릭](../firewall/logs-and-metrics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**지침**: 자동으로 사용되는 Azure 활동 로그에는 읽기(GET) 작업을 제외한 Azure Virtual WAN 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 활동 로그는 문제 해결 중 오류를 찾거나 조직의 사용자가 리소스를 수정 한 방법을 모니터링하는 데 사용할 수 있습니다.

Virtual WAN에 대해 Azure 리소스 로그를 사용하도록 설정합니다. Azure Security Center 및 Azure Policy를 사용하여 리소스 로그 및 로그 데이터 수집을 실행할 수 있습니다. 이러한 로그는 나중에 보안 인시던트를 조사하고 법정 연습을 수행하는 데 중요한 역할을 할 수 있습니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure의 로깅 및 기타 로그 형식 이해](../azure-monitor/essentials/platform-logs-overview.md) 

- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: 보안 로그 관리 및 분석 중앙 집중화

**지침**: Azure Monitor를 사용하여 Virtual WAN에 대한 보안 로깅을 사용하도록 설정합니다. Virtual WAN은 많은 네트워킹, 보안 및 라우팅 기능을 결합하여 단일 운영 인터페이스를 제공합니다. Virtual WAN VPN 게이트웨이, ExpressRoute 게이트웨이 및 Azure Firewall에는 Azure Monitor를 통해 사용할 수 있는 로깅 및 메트릭이 있습니다. 활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다. Azure 활동 로그(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업을 확인할 수 있습니다. 

Virtual WAN에 대한 다양한 진단 로그도 사용할 수 있으며 Azure Portal을 사용하여 Virtual WAN 리소스에 대해 구성할 수 있습니다. Log Analytics로 보내거나 이벤트 허브로 스트리밍하거나 단순히 스토리지 계정에 보관합니다. 또한 데이터를 사용하도록 설정하고 Azure Sentinel 또는 타사 보안 정보 및 이벤트 관리 솔루션에 온보딩할 수 있습니다. 
 

- [Virtual WAN 로그 및 메트릭](logs-metrics.md)

- [Azure Firewall 로그 및 메트릭](../firewall/logs-and-metrics.md)

Azure Virtual WAN 보안은 Azure 방화벽을 통해 제공됩니다. 

- [Azure Firewall 설명서](../firewall/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: 로그 스토리지 보존 구성

**지침**: 규정 준수, 규정 및 비즈니스 요구 사항에 따라 로그 보존을 구성합니다. Azure Monitor에서 조직의 준수 규정에 따라 Log Analytics 작업 영역의 보존 기간을 설정할 수 있습니다. 장기 및 보관 스토리지에 대한 Azure Storage, Data Lake 또는 Log Analytics 작업 영역 계정을 사용합니다.

- [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage 계정 로그에 대한 보존 정책을 구성하는 방법](../storage/common/manage-storage-analytics-logs.md#configure-logging)

- [Azure Security Center 경고 및 권장 사항 내보내기](../security-center/continuous-export.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 검색 및 분석 – 고품질 경고에 기반한 인시던트 만들기

**지침**: 고품질 경고를 만들고 경고 품질을 측정하는 프로세스가 있는지 확인합니다. 이를 통해 과거 인시던트로부터 교훈을 습득하고 분석가에 대한 경고의 우선 순위를 지정할 수 있으므로 가양성에 시간을 낭비하지 않습니다. 

고품질 경고는 과거 인시던트로부터의 경험, 유효성이 검사된 커뮤니티 원본 및 다양한 신호 원본을 융합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구를 기반으로 하여 작성될 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인시던트를 조사할 때 다양한 데이터 원본을 쿼리하고 사용하여 발생한 상황에 대한 전체 보기를 작성할 수 있는지 확인합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다.  또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.  

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용하여 실행 중인 시스템의 메모리에 대한 스냅샷을 만듭니다.

    - Azure 서비스의 스냅샷 기능 또는 소프트웨어의 자체 기능을 사용하여 실행 중인 시스템의 스냅샷을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

*자세한 내용은 [Azure Security Benchmark: 보안 상태 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)를 참조하세요.*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="endpoint-security"></a>엔드포인트 보안

*자세한 내용은 [Azure Security Benchmark: 엔드포인트 보안](../security/benchmarks/security-controls-v2-endpoint-security.md)을 참조하세요.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: EDR(엔드포인트 검색 및 응답) 사용

**지침**: 고객은 엔드포인트 감지 및 응답 설정을 명시적으로 구성할 수 없습니다. 그러나 Azure Virtual WAN 제품에 사용되는 Virtual Machines는 이러한 기능을 사용합니다. 참조 링크에서 이러한 일반 기능에 대해 자세히 알아보세요. 

- [Microsoft Defender Advanced Threat Protection 개요](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [비 Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따른 필수 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 저장 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 수신/송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - 권한 있는 액세스](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 로그 및 위협 대응 전략을 수립하여 규정 준수 요구 사항을 충족하면서 위협을 신속하게 탐지하고 수정합니다. 분석가가 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고와 원활한 환경을 제공하는 데 적용되는 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 고객

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.