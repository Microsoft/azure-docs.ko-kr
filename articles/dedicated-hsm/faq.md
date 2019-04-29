---
title: 질문과 대답 - Azure 전용 HSM | Microsoft Docs
description: Azure 전용 HSM의 다양한 주제를 다루는 질문과 대답
services: dedicated-hsm
author: johncdawson
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: barclayn
ms.openlocfilehash: d432dc25a1995a2f0348c7626a051f46ffbf418b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118981"
---
# <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

Microsoft Azure Dedicated HSM과 관련된 일반적인 질문에 대한 대답을 확인하세요.

## <a name="the-basics"></a>기본 사항

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Q: HSM(하드웨어 보안 모듈)이란?

HSM(하드웨어 보안 모듈)은 암호화 키를 보호하고 관리하는 데 사용되는 물리적 컴퓨팅 디바이스입니다. HSM에 저장된 키는 암호화 작업에 사용할 수 있습니다. 키 자료는 변조 방지 및 변조 확인 하드웨어 모듈에서 안전하게 유지됩니다. HSM을 사용하면 인증되고 권한이 부여된 애플리케이션만 키를 사용할 수 있습니다. 키 자료는 HSM 보호 경계를 벗어나지 않습니다.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>Q: Azure 전용 HSM 제품이란?

Azure Dedicated HSM은 고객의 가상 네트워크에 직접 연결된 Azure 데이터 센터에서 호스팅되는 HSM을 제공하는 클라우드 기반 서비스입니다. 이러한 HSM은 전용 네트워크 어플라이언스(Gemalto의 SafeNet Network HSM 7 모델 A790)입니다. 고객의 사설 IP 주소 공간에 직접 배포되며, Microsoft는 HSM의 암호화 기능에 액세스할 수 없습니다. 이러한 디바이스에 대한 완전한 관리 및 암호화 제어 권한은 고객에게만 있습니다. 고객은 디바이스 관리에 대한 책임이 있으며, 디바이스에서 전체 활동 로그를 직접 가져올 수 있습니다. Dedicated HSM을 사용하면 고객이 FIPS 140-2 수준 3, HIPAA, PCI-DSS, eIDAS 및 기타 등등의 규정 준수/규제 요구 사항을 충족할 수 있습니다.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>Q: Dedicated HSM에 사용되는 하드웨어는 어떻게 되나요?

Microsoft는 Azure 전용 HSM 서비스를 제공하기 위해 Gemalto와 파트너 관계를 맺고 있습니다. 사용되는 특정 디바이스는 [SafeNet Luna Network HSM 7 모델 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)입니다. 이 디바이스는 FIPS 140-2 수준 3 검증 펌웨어를 제공할 뿐만 아니라 10개의 파티션을 통해 짧은 대기 시간, 고성능 및 대용량도 제공합니다. 

### <a name="q-what-is-an-hsm-used-for"></a>Q: HSM의 용도는 무엇인가요?

HSM은 SSL(Secure Sockets Layer), 데이터 암호화, PKI(공개 키 인프라), DRM(디지털 권한 관리) 및 문서 서명과 같은 암호화 기능에 사용되는 암호화 키를 저장하는 데 사용됩니다.

### <a name="q-how-does-dedicated-hsm-work"></a>Q: 전용 HSM은 어떻게 작동하나요?

고객은 PowerShell 또는 명령줄 인터페이스를 사용하여 HSM을 특정 지역에 프로비전할 수 있습니다. 고객은 HSM이 연결될 가상 네트워크를 지정하고, 일단 프로비전된 HSM은 고객의 사설 IP 주소 공간에 할당된 IP 주소로 지정된 서브넷에서 사용할 수 있습니다. 다음으로, 고객은 어플라이언스 관리 및 운영을 위해 SSH를 사용하여 HSM에 연결하고, HSM 클라이언트 연결을 설정하고, HSM을 초기화하고, 파티션을 만들고, 파티션 책임자, 암호화 책임자 및 암호화 사용자와 같은 역할을 정의 및 할당할 수 있습니다. 그런 다음, 고객은 Gemalto에서 제공한 HSM 클라이언트 도구/SDK/소프트웨어를 사용하여 애플리케이션에서 암호화 작업을 수행합니다.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Q: 전용 HSM 서비스에는 어떤 소프트웨어가 제공되나요?

일단 Microsoft에서 프로비전하면 Gemalto는 HSM 디바이스용 소프트웨어를 모두 제공합니다. 이 소프트웨어는 [Gemalto 고객 지원 포털](https://supportportal.gemalto.com/csm/)에서 사용할 수 있습니다. Dedicated HSM 서비스를 사용하는 고객은 Gemalto 지원에 등록해야 하며, 관련 소프트웨어에 대한 액세스 및 다운로드를 가능하게 하는 고객 ID를 가지고 있어야 합니다. 지원되는 클라이언트 소프트웨어는 FIPS 140-2 수준 3 검증 펌웨어 버전 7.0.3과 호환되는 버전 7.2입니다. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>Q: Azure 전용 HSM은 암호 기반 및 PED 기반 인증을 제공하나요?

현재, Azure 전용 HSM은 HSM에 암호 기반 인증만 제공합니다.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>Q: Azure 전용 HSM이 내 HSM을 호스트할 수 있나요?

Microsoft는 Dedicated HSM 서비스를 통해 Gemalto SafeNet Luna Network HSM만 제공하며, 고객이 제공하는 디바이스를 호스팅할 수 없습니다.

### <a name="q-does-azure-dedicated-hsm-support-payment-pinetf-features"></a>Q: Azure 전용 HSM 지원 결제 (PIN/ETF) 기능을 하나요?

Azure 전용 HSM 서비스는 SafeNet Luna Network HSM 7(모델 A790) 디바이스를 사용합니다. 이러한 디바이스는 결제 HSM 특정 기능(예: PIN 또는 ETF)이나 인증을 지원하지 않습니다. 나중에 결제 Hsm을 지원 하도록 Azure 전용 HSM 서비스를 사용할 수 있도록 하려는 경우 전달 하세요 피드백에 Microsoft 계정 담당자를 합니다.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>Q: Azure 지역은 전용 HSM을 사용할 수 있습니다?

런타임에 년 3 월 2019 일부 터 전용 HSM은 아래에 나열 된 14 개 지역에서 사용할 수 있습니다. 추가 영역을 계획 및 Microsoft 계정 담당자를 통해 논의 될 수 있습니다.

* 미국 동부
* 미국 동부 2
* 미국 서부
* 미국 중남부
* 동남아시아
* 동아시아
* 유럽 북부
* 서유럽
* 영국 남부
* 영국 서부
* 캐나다 중부
* 캐나다 동부
* 오스트레일리아 동부
* 오스트레일리아 남동부

## <a name="interoperability"></a>상호 운용성

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Q: 내 애플리케이션을 전용 HSM에 연결하려면 어떻게 하나요?

Gemalto에서 제공한 HSM 클라이언트 도구/SDK/소프트웨어를 사용하여 애플리케이션에서 암호화 작업을 수행합니다. 이 소프트웨어는 [Gemalto 고객 지원 포털](https://supportportal.gemalto.com/csm/)에서 사용할 수 있습니다. Dedicated HSM 서비스를 사용하는 고객은 Gemalto 지원에 등록해야 하며, 관련 소프트웨어에 대한 액세스 및 다운로드를 가능하게 하는 고객 ID를 가지고 있어야 합니다.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Q: 애플리케이션에서 지역 내 또는 지역 간의 다른 VNET에 있는 전용 HSM에 연결할 수 있나요?

예, 지역 내에서 [VNET 피어링](../virtual-network/virtual-network-peering-overview.md)을 사용하여 가상 네트워크 간의 연결을 설정합니다. 지역 간 연결의 경우 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 사용해야 합니다.

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Q: 전용 HSM은 온-프레미스 HSM과 동기화할 수 있나요?

예, Dedicated HSM은 온-프레미스 HSM과 동기화할 수 있습니다. [지점 간 VPN 또는 지점 및 사이트 간 연결](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 네트워크와의 연결을 설정할 수 있습니다.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Q: 전용 HSM에 저장된 키를 사용하여 다른 Azure 서비스에서 사용하는 데이터를 암호화할 수 있나요?

아니요. Azure Dedicated HSM은 가상 네트워크 내에서만 액세스할 수 있습니다.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Q: 기존 온-프레미스 HSM에서 전용 HSM으로 키를 가져올 수 있나요?

예, 온-프레미스 Gemalto SafeNet HSM이 있으면 가능합니다. 여러 가지 방법이 있습니다. Gemalto HSM 설명서를 참조하세요.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Q: 전용 HSM 클라이언트 소프트웨어에서 지원하는 운영 체제는 어떻게 되나요?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* 가상: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Q: 여러 HSM의 여러 파티션이 포함된 고가용성 구성을 만들기 위해 클라이언트 애플리케이션을 구성하려면 어떻게 할까요?

고가용성을 갖추려면 각 HSM의 파티션을 사용하도록 HSM 클라이언트 애플리케이션 구성을 설정해야 합니다. Gemalto HSM 클라이언트 소프트웨어 설명서를 참조하세요.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Q: 전용 HSM에서 지원하는 인증 메커니즘은 무엇인가요?

Azure Dedicated HSM은 SafeNet Network HSM 7 어플라이언스(모델 A790)를 사용하고 암호 기반 인증을 지원합니다.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Q: 전용 HSM에서 사용할 수 있는 SDK, API, 클라이언트 소프트웨어는 어떻게 되나요?

PKCS#11, Java(JCA/JCE), Microsoft CAPI 및 CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Q: Luna 5/6 HSM에서 Azure 전용 HSM으로 키를 가져오거나 마이그레이션할 수 있나요?

예. Gemalto 마이그레이션 가이드를 참조하세요. 

## <a name="using-your-hsm"></a>HSM 사용

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Q: Azure Key Vault 또는 Azure 전용 HSM을 사용할지 여부를 결정하려면 어떻게 할까요?

Azure Dedicated HSM은 HSM을 사용하는 온-프레미스 애플리케이션으로 마이그레이션하는 기업에 적합합니다. Dedicated HSM은 최소한의 변경으로 애플리케이션을 마이그레이션하는 옵션을 제공합니다. Azure VM 또는 Web App에서 실행되는 애플리케이션의 코드에서 암호화 작업이 수행되는 경우 Dedicated HSM을 사용할 수 있습니다. 일반적으로 IaaS(Infra as a Service) 모델에서 실행되어 HSM을 키 저장소로 지원하는 수축 포장형 소프트웨어는 Dedicated HSM을 사용할 수 있습니다. 예를 들어 Application Gateway, 키리스 SSL용 애플리케이션 게이트웨이 또는 트래픽 관리자, ADCS(Active Directory 인증서 서비스), 이와 유사한 PKI 도구, 문서 서명에 사용되는 도구/애플리케이션, 코드 서명 또는 EKM(확장 가능 키 관리) 공급자를 사용하여 HSM의 마스터 키가 있는 TDE(투명한 데이터베이스 암호화)로 구성된 SQL Server(IaaS)가 있습니다. Azure Key Vault는 "클라우드 태생"의 애플리케이션 또는 고객 데이터가 PaaS(Platform as a Service) 또는 SaaS(Software as a Service) 시나리오에서 처리되는 저장 데이터 암호화 시나리오(예: Office 365 고객 키, Azure Information Protection, Azure Disk Encryption, 고객 관리 키를 사용하는 Azure Storage 암호화, 고객 관리 키를 사용하는 Azure SQL)에 적합합니다.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Q: Azure 전용 HSM에 가장 적합한 사용 시나리오는 무엇인가요?

Azure Dedicated HSM은 마이그레이션 시나리오에 가장 적합합니다. 즉, 이미 HSM을 사용하고 있는 온-프레미스 애플리케이션을 Azure로 마이그레이션하는 경우입니다. 이 경우 애플리케이션을 최소한으로 변경하면서 Azure로 마이그레이션하는 충돌이 적은 옵션이 제공됩니다. Azure VM 또는 Web App에서 실행되는 애플리케이션의 코드에서 암호화 작업이 수행되는 경우 Dedicated HSM을 사용할 수 있습니다. 일반적으로 IaaS(Infrarastructure as a Service) 모델에서 실행되어 HSM을 키 저장소로 지원하는 수축 포장형 소프트웨어는 Dedicated HSM을 사용할 수 있으며 다음과 같습니다.

* 키리스 SSL용 애플리케이션 게이트웨이 또는 트래픽 관리자
* ADCS(Active Directory 인증서 서비스)
* 유사한 PKI 도구
* 문서 서명에 사용되는 도구/애플리케이션
* 코드 서명
* EKM(확장 가능 키 관리) 공급자를 사용하여 HSM의 마스터 키가 있는 TDE(투명한 데이터베이스 암호화)로 구성된 SQL Server(IaaS)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Q: 전용 HSM은 Office 365 고객 키, Azure Information Protection, Azure Data Lake Store, Disk Encryption, Azure Storage 암호화, Azure SQL TDE에서 사용할 수 있나요?

아니요. Dedicated HSM은 고객의 사설 IP 주소 공간에 직접 프로비전되어 다른 Azure 또는 Microsoft 서비스에서 액세스할 수 없습니다.

## <a name="administration-access-and-control"></a>관리, 액세스 및 제어

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Q: 고객이 전용 HSM을 통해 HSM을 독점적으로 완벽하게 제어하나요?

예. 각 HSM 어플라이언스는 단일 고객만 전적으로 사용하며, 일단 프로비전되고 관리자 암호가 변경되면 아무도 관리 제어 권한을 갖지 못합니다.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Q: Microsoft에서 내 HSM에 부여하는 액세스 권한 수준은 어떻게 되나요?

Microsoft에는 HSM에 대한 관리 또는 암호화 제어 권한이 없습니다. 대신 Microsoft에는 온도 및 구성 요소 상태와 같은 기본 원격 분석 데이터를 검색하기 위해 직렬 포트 연결을 통한 모니터 수준 액세스 권한이 있습니다. Microsoft는 이를 통해 상태 문제에 대한 자동 관리 알림을 제공할 수 있습니다. 필요한 경우 고객은 이 계정을 사용하지 않도록 설정할 수 있습니다.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>Q: 사용 하 여 Microsoft "tenantadmin" 계정 이란 무엇 이며, SafeNet Hsm에서 "관리자" 관리자에 사용 하 던?

HSM 장치는 일반적인 기본 암호를 사용 하 여 관리자의 기본 사용자를 사용 하 여 제공 됩니다. Microsoft 고객이 프로 비전 하려고 대기 하는 풀의 모든 장치는 기본 암호를 사용에서 하지 않았습니다. 이 엄격한 보안 요구 사항을 충족 하지 것입니다. 이러한 이유로 프로 비전 시간에서 삭제 된 강력한 암호를 설정 합니다. 또한 프로 비전 시간에서는 새 사용자를 만듭니다 "tenantadmin"를 호출 하는 관리자 역할에서입니다. 이 사용자의 기본 암호 및 고객에 게가 변경 첫 번째 작업으로 먼저 새로 프로 비전 된 장치에 로그인 할 때입니다. 이 프로세스 높은 수준의 보안을 보장 하 고 고객에 대 한 우리의 약속 유일한 관리 제어를 유지 합니다. "Tenantadmin" 사용자를 해당 계정을 사용 하 길 원하는 고객의 관리자 사용자 암호 재설정을 사용할 수 있도록 확인 해야 합니다. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Q: Microsoft 또는 Microsoft의 모든 사용자가 전용 HSM의 키에 액세스할 수 있나요?

아니요. Microsoft에는 고객에게 할당된 Dedicated HSM에 저장된 키에 대한 액세스 권한이 없습니다.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Q: 내게 할당된 HSM의 소프트웨어/펌웨어를 업그레이드할 수 있나요?

최상의 지원을 받으려면 HSM의 소프트웨어/펌웨어를 업그레이드하지 않는 것이 좋습니다. 그러나 다른 펌웨어 버전에서 특정 기능이 필요한 경우 고객은 소프트웨어 / 펌웨어 업그레이드를 포함한 모든 관리 권한을 갖습니다. 예를 들어 FIPS 검증 상태에 영향을 미칠 수 있으므로 변경하기 전에 이에 대한 의미를 이해해야 합니다. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>Q: 전용 HSM을 관리하려면 어떻게 할까요?

Dedicated HSM은 SSH를 통해 액세스하여 관리할 수 있습니다.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Q: 전용 HSM에서 파티션을 관리하려면 어떻게 할까요?

Gemalto HSM 클라이언트 소프트웨어를 사용하여 HSM 및 파티션을 관리합니다.

### <a name="q-how-do-i-monitor-my-hsm"></a>Q: HSM을 모니터링하려면 어떻게 할까요?

고객은 syslog 및 SNMP를 통해 HSM 활동 로그에 대한 모든 액세스 권한을 갖습니다. 고객이 HSM에서 로그 또는 이벤트를 받으려면 syslog 서버 또는 SNMP 서버를 설정해야 합니다.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Q: 전용 HSM에서 모든 HSM 작업에 대한 전체 액세스 로그를 가져올 수 있나요?

예. 로그는 HSM 어플라이언스에서 syslog 서버로 보낼 수 있습니다.

## <a name="high-availability"></a>고가용성

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Q: 동일한 지역 또는 여러 지역 간에 고가용성을 구성할 수는?

예. 고가용성 구성 및 설정은 Gemalto에서 제공하는 HSM 클라이언트 소프트웨어에서 수행됩니다. 사이트-사이트를 사용 하 여 VNET에 연결 된 동일한 VNET 또는 다른 Vnet을 동일한 지역에 지역 또는 온-프레미스 Hsm에서 Hsm 또는 지점 간 VPN을 동일한 고가용성 구성에 추가할 수 있습니다. 키 자료만 및 역할과 같은 특정 하지 않은 구성 항목 동기화이 점에 유의 해야 합니다.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Q: Q: Azure Dedicated HSM을 사용하여 온-프레미스 네트워크의 HSM을 고가용성 그룹에 추가할 수 있나요?

예. SafeNet Luna Network HSM 7에 대한 HA(고가용성) 요구 사항을 충족해야 합니다.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Q: Q: Azure Dedicated HSM을 사용하여 온-프레미스 네트워크의 Luna 5/6 HSM을 고가용성 그룹에 추가할 수 있나요?

아니요.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Q: 단일 애플리케이션에서 동일한 고가용성 구성에 추가할 수 있는 HSM의 수는 어떻게 되나요?

16 HA 그룹 구성원에 뛰어난 결과 사용 하 여 테스트에서 완료, 전체 제한

## <a name="support"></a>지원

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Q: 전용 HSM 서비스에 대한 SLA는 어떻게 되나요?

보장은 없습니다 특정 가동 시간 전용 HSM 서비스를 제공 합니다. 대신 Microsoft는 디바이스에 대한 네트워크 수준의 액세스를 보장하므로 표준 Azure 네트워킹 SLA가 적용됩니다.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Q: Azure 전용 HSM에 사용되는 HSM은 어떻게 보호되나요?

Azure 데이터 센터에는 광범위한 물리적 및 절차적 보안 제어 기능이 있습니다. 이 외에도 Dedicated HSM은 데이터 센터의 제한된 액세스 영역에서 호스팅됩니다. 이러한 영역에는 보안을 강화하기 위한 추가적인 물리적 액세스 제어 및 비디오 카메라 감시 기능이 있습니다.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Q: 보안 위반 또는 하드웨어 변조 이벤트가 발생하면 어떻게 되나요?

Dedicated HSM 서비스는 SafeNet Network HSM 7 어플라이언스를 사용합니다. 이러한 어플라이언스는 물리적 및 논리적 변조 탐지를 지원합니다. 변조 이벤트가 발생하면 HSM이 자동으로 초기화됩니다.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Q: 오류 또는 악의적인 내부자 공격으로 인해 전용 HSM의 키가 손실되지 않도록 하려면 어떻게 할까요?

온-프레미스 HSM 백업 디바이스를 사용하여 재해 복구를 위해 HSM을 정기적으로 백업하는 것이 좋습니다. HSM 백업 디바이스에 연결된 온-프레미스 워크스테이션에 대한 피어 간 또는 사이트 간 VPN 연결을 사용해야 합니다.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Q: 전용 HSM에 대한 지원을 받으려면 어떻게 할까요?

Microsoft와 Gemalto에서 지원 됩니다.  하드웨어 또는 네트워크 액세스 문제가 있다면 Microsoft와 HSM 구성, 소프트웨어를 사용 하 여 문제가 있는 경우 및 응용 프로그램 개발 Gemalto 사용 하 여 지원 요청을 제기해 주세요 지원 요청을 발생 시킵니다. Microsoft 사용 하 여 지원 요청을 지정 하지 않은 문제가 있는 경우으로 Gemalto 수 참여 하는 다음이 필요 합니다. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>Q: 어떻게 가져오나 요 클라이언트 소프트웨어, 설명서 및 SafeNet Luna 7 HSM에 대 한 통합 지침에 액세스할 수 있습니까?

서비스를 등록 한 후 Gemalto 고객 ID Gemalto 고객 지원 포털에서 등록에 허용 되는 제공 됩니다. 이렇게 하면 모든 소프트웨어 및 설명서 뿐만 아니라 Gemalto와 직접 사용 하도록 설정 하면 지원 요청에 대 한 액세스.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Q: 보안 취약성이 있고 Gemalto에서 패치를 릴리스한 경우 OS/펌웨어 업그레이드/패치 작업은 누가 수행해야 하나요?

Microsoft는 고객에게 할당된 HSM에 연결할 수 없습니다. 따라서 고객이 HSM을 업그레이드하고 패치해야 합니다.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>Q: 내 HSM 다시 부팅 해야 하는 경우에 어떻게 합니까?

 HSM 명령줄 다시 부팅 옵션을 있으며이 "하드 재부팅" 옵션을 사용 해야 함을 유의 해야 합니다. 어떤 이유로 든 실패 하면 microsoft 지원 요청을 발생 시키고 실제로 다시 부팅 장치가 있을 수 있습니다. 

## <a name="cryptography-and-standards"></a>암호화 및 표준

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Q: 가장 중요한 데이터에 대한 암호화 키는 전용 HSM에서 안전하게 저장되나요?

예, Dedicated HSM은 FIPS 140-2 수준 3 검증 HSM을 사용하는 SafeNet Network HSM 7 어플라이언스를 프로비전합니다. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Q: 전용 HSM에서 지원하는 암호화 키와 알고리즘은 어떻게 되나요?

Dedicated HSM 서비스는 SafeNet Network HSM 7 어플라이언스를 프로비전합니다. 이 어플라이언스에서 지원하는 다양한 암호화 키 유형 및 알고리즘은 다음과 같습니다. 전체 Suite B 지원

* 비대칭:
  * RSA
  * DSA
  * Diffie-Hellman
  * 타원 곡선(Elliptic Curve)
  * 암호화(ECDSA, ECDH, Ed25519, ECIES) - KCDSA(명명된 사용자 정의 Brainpool 곡선) 포함
* 대칭:
  * AES-GCM
  * Triple DES
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * 해시/메시지 다이제스트/HMAC: SHA-1, SHA-2, SM3
  * 키 파생: SP800 108 카운터 모드
  * 키 래핑: SP800-38F
  * 난수 생성: FIPS 140-2 승인 DRBG(SP 800-90 CTR 모드) - BSI DRG.4 준수

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Q: 전용 HSM은 FIPS 140-2 수준 3으로 검증되었나요?

예. Dedicated HSM 서비스는 FIPS 140-2 수준 3 검증 HSM을 사용하는 SafeNet Network HSM 7 어플라이언스를 프로비전합니다.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Q: FIPS 140-2 수준 3 검증 모드에서 전용 HSM을 작동하려면 어떻게 해야 하나요?

Dedicated HSM 서비스는 SafeNet Luna Network HSM 7 어플라이언스를 프로비전합니다. 이러한 어플라이언스는 FIPS 140-2 수준 3 검증 HSM을 사용합니다. 배포된 기본 구성, 운영 체제 및 펌웨어도 FIPS에서 검증됩니다. FIPS 140-2 수준 3 규정 준수를 위해 어떠한 조치도 취할 필요가 없습니다.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Q: Q: 고객이 HSM을 프로비전 해제할 때 모든 키 자료가 초기화되도록 하려면 어떻게 해야 하나요?

프로비전 해제를 요청하기 전에 고객이 Gemalto에서 제공한 HSM 클라이언트 도구를 사용하여 HSM을 초기화해야 합니다.

## <a name="performance-and-scale"></a>성능 및 확장

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Q: 전용 HSM에서 지원하는 초당 암호화 작업 수는 어떻게 되나요?

Dedicated HSM은 SafeNet Network HSM 7 어플라이언스(모델 A790)를 프로비전합니다. 일부 작업의 최대 성능에 대한 요약은 다음과 같습니다. 

* RSA-2048: 초당 10,000개 트랜잭션
* ECC P256: 초당 20,000개 트랜잭션
* AES-GCM: 초당 17,000개 트랜잭션

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Q: 전용 HSM에 만들 수 있는 파티션의 수는 어떻게 되나요?

A790 사용 SafeNet Luna HSM 7 모델은 서비스의 비용에 10 개의 파티션에 대 한 라이선스를 포함 합니다. 장치에 파티션 수가 100 개로 제한 하 고 추가 라이선스 비용이 부과 되 고 장치의 새 라이선스 파일을 설치할 필요는 것이 한도까지 파티션을 추가 합니다.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Q: 전용 HSM에서 지원할 수 있는 키의 수는 어떻게 되나요?

키의 최대 수는 사용 가능한 메모리의 함수입니다. 사용 중인 SafeNet Luna 7 모델 A790에 32MB의 메모리만 있습니다. 비대칭 키를 사용 하는 경우에 다음 숫자 키 쌍에 적용할 수 있습니다.

* RSA-2048 - 19,000개
* ECC-P256 - 91,000개

용량은 키 생성 템플릿에 설정된 특정 키 특성 및 파티션 수에 따라 달라집니다.
