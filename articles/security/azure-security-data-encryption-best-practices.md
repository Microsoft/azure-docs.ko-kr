---
title: 데이터 보안 및 암호화 모범 사례 | Microsoft Docs
description: 이 문서에서는 기본 제공 Azure 기능을 사용한 데이터 보안 및 암호화 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: barclayn
ms.openlocfilehash: 686d4a8ac5239af12206b57072cc00aa10114d79
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125122"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Azure 데이터 보안 및 암호화 모범 사례

클라우드에서 데이터를 보호하려면 데이터에서 발생 가능한 상태 및 해당 상태에 사용 가능한 컨트롤을 고려해야 합니다. Azure 데이터 보안 및 암호화의 모범 사례는 다음과 같은 데이터 상태와 관련이 있습니다.

- 미사용: 물리적 미디어(자기 또는 광 디스크)에 정적으로 존재하는 모든 정보 스토리지 개체, 컨테이너 및 형식이 포함됩니다.
- 전송 중: 구성 요소, 위치 또는 프로그램 간에 데이터를 전송하고 있는 경우 전송 중 상태가 됩니다. 네트워크를 통하거나, 서비스 버스 간에(ExpressRoute와 같은 하이브리드 연결을 비롯한 온-프레미스와 클라우드 간) 또는 입출력 프로세스 중에 전송을 예로 들 수 있습니다.

이 문서에서는 Azure 데이터 보안 및 암호화 모범 사례 컬렉션에 대해 설명합니다. 이러한 모범 사례는 Azure 데이터 보안 및 암호화에 대한 Microsoft의 경험 그리고 여러분 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음 사항을 설명하겠습니다.

* 각 모범 사례
* 해당 모범 사례를 사용해야 하는 이유
* 해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
* 해당 모범 사례를 대체할 수 있는 대안
* 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure 데이터 보안 및 암호화 모범 사례 문서는 이 문서가 작성될 당시의 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

## <a name="choose-a-key-management-solution"></a>키 관리 솔루션 선택

키를 보호하는 것은 클라우드에서 데이터를 보호하는 데 필수적입니다.

[Azure Key Vault](../key-vault/key-vault-overview.md)를 사용하면 클라우드 애플리케이션과 서비스에서 사용하는 암호화 키 및 비밀을 보호할 수 있습니다. 키 자격 증명 모음은 키 관리 프로세스를 간소화하고 데이터를 액세스하고 암호화 하는 키의 제어를 유지할 수 있습니다. 개발자는 개발 및 테스트(분)을 위한 키를 만든 다음, 프로덕션 키로 마이그레이션할 수 있습니다. 보안 관리자는 필요한 경우 권한을 키로 부여(및 해지)할 수 있습니다.

Key Vault를 사용하여 자격 증명 모음이라는 보안 컨테이너를 여러 개 만들 수 있습니다. 이러한 자격 증명 모음은 HSM에서 지원됩니다. 자격 증명 모음은 애플리케이션 비밀을 중앙 집중식으로 저장하여 보안 정보의 우발적인 손실 가능성을 줄이는 데 도움이 됩니다. 또한 키 자격 증명 모음은 저장된 모든 항목에 대한 액세스를 제어하고 기록합니다. Azure Key Vault는 TLS(전송 계층 보안) 인증서를 요청하고 갱신하는 작업을 처리할 수 있습니다. 인증서 수명 주기 관리를 위한 강력한 솔루션 기능을 제공합니다.

Azure Key Vault는 애플리케이션 키와 비밀을 지원하도록 설계되었습니다. Key Vault는 사용자 암호 저장소로 사용할 수 없습니다.

Key Vault를 사용하는 보안 모범 사례는 다음과 같습니다.

**모범 사례**: 특정 범위의 사용자, 그룹 및 애플리케이션에 액세스 권한 부여   
**세부 정보**: RBAC의 미리 정의된 역할을 사용합니다. 예를 들어 키 자격 증명 모음을 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 해당 사용자에게 미리 정의된 [Key Vault 기여자](../role-based-access-control/built-in-roles.md) 역할을 할당합니다. 이 경우의 범위는 구독, 리소스 그룹 또는 특정 키 자격 증명 모음입니다. 미리 정의된 역할이 필요에 맞지 않는 경우 [고유한 역할을 정의](../role-based-access-control/custom-roles.md)할 수 있습니다.

**모범 사례**: 액세스 권한이 있는 사용자를 제어합니다.   
**세부 정보**: 키 자격 증명 모음에 대한 액세스는 별도의 두 인터페이스, 즉 관리 평면 및 데이터 평면을 통해 제어됩니다. 관리 평면과 데이터 평면 액세스 제어는 독립적으로 작동합니다.

RBAC를 사용하여 액세스 권한이 있는 사용자를 제어합니다. 예를 들어 키 자격 증명 모음의 키를 사용하도록 액세스 권한을 애플리케이션에 부여하려면 키 자격 증명 모음 액세스 정책을 사용하여 데이터 평면 액세스 권한만 부여하면 되고, 관리 평면 액세스 권한은 해당 애플리케이션에 필요하지 않습니다. 반대로 사용자가 자격 증명 모음 속성과 태그를 읽을 수 있지만 키, 비밀 또는 인증서에 대한 액세스 권한이 없는 경우 RBAC를 사용하여 이 사용자에게 읽기 권한을 부여하면 되고, 데이터 평면에 대한 액세스 권한은 필요하지 않습니다.

**모범 사례**: Key Vault에 인증서를 저장합니다. 인증서는 가치가 높습니다. 잘못 사용되면 애플리케이션의 보안 또는 데이터의 보안이 손상될 수 있습니다.   
**세부 정보**: Azure Resource Manager는 VM을 배포할 때 Azure Key Vault에 저장된 인증서를 Azure VM에 안전하게 배포할 수 있습니다. 키 자격 증명 모음에 대한 적절한 액세스 정책을 설정하여 인증서에 대한 액세스 권한이 있는 사용자도 제어합니다. 또 다른 혜택은 Azure Key Vault의 한 곳에서 모든 인증서를 관리할 수 있다는 점입니다. 자세한 내용은 [고객 관리 Key Vault에서 VM에 인증서 배포](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)를 참조하세요.

**모범 사례**: 삭제된 Key Vault 또는 Key Vault 개체를 복구할 수 있는지 확인합니다.   
**세부 정보**: Key Vault 또는 Key Vault 개체를 우발적이나 악의적으로 삭제할 수 있습니다. 특히 미사용 데이터를 암호화하는 데 사용되는 키의 경우 Key Vault의 일시 삭제 및 제거 보호 기능을 사용하도록 설정합니다. 이러한 키를 삭제하는 작업은 데이터 손실과 동일합니다. 따라서 필요한 경우 삭제된 자격 증명 모음 및 자격 증명 모음 개체를 복구할 수 있습니다. 정기적으로 Key Vault 복구 작업을 연습합니다.

> [!NOTE]
> 사용자에게 키 자격 증명 모음 관리 평면에 대한 기여자 사용 권한(RBAC)이 있는 경우 이 사용자는 키 자격 증명 모음 액세스 정책을 설정하여 스스로 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. 권한이 있는 사용자만 키 자격 증명 모음, 키, 비밀 및 인증서에 액세스하고 관리할 수 있도록 하려면 키 자격 증명 모음에 대한 기여자 액세스 권한을 갖는 사용자를 엄격하게 제어하는 것이 좋습니다.
>
>

## <a name="manage-with-secure-workstations"></a>보안 워크스테이션을 사용하여 관리

> [!NOTE]
> 구독 관리자 또는 소유자는 보안 액세스 워크스테이션 또는 권한 있는 액세스 워크스테이션을 사용해야 합니다.
>
>

대부분의 공격이 최종 사용자를 대상으로 하기 때문에 엔드포인트는 주요 공격 지점 중 하나입니다. 엔드포인트를 손상시킨 공격자는 사용자의 자격 증명을 활용하여 조직의 데이터에 액세스할 수 있게 됩니다. 대부분의 엔드포인트 공격은 사용자가 로컬 워크스테이션의 관리자라는 사실을 활용합니다.

**모범 사례**: 보안 관리 워크스테이션을 사용하여 중요한 계정, 작업 및 데이터를 보호합니다.   
**세부 정보**: [권한 있는 액세스 워크스테이션](https://technet.microsoft.com/library/mt634654.aspx)을 사용하여 워크스테이션의 공격 노출 영역을 감소시킵니다. 보안 관리 워크스테이션은 이러한 공격 위험 중 일부를 완화하고 데이터를 안전하게 보호하는 데 도움이 될 수 있습니다.

**모범 사례**: 엔드포인트 보호를 보장합니다.   
**세부 정보**: 데이터 위치(클라우드 또는 온-프레미스)에 관계없이 데이터를 사용하는 모든 디바이스에 보안 정책을 적용합니다.

## <a name="protect-data-at-rest"></a>휴지 상태의 데이터 보호

[미사용 데이터 암호화](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)는 데이터 프라이버시, 규정 준수 및 데이터 주권을 위한 필수 단계입니다.

**모범 사례**: 데이터를 보호할 수 있도록 디스크 암호화를 적용합니다.   
**세부 정보**: [Azure Disk Encryption](azure-security-disk-encryption.md)을 사용합니다. 그러면 IT 관리자가 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다. 디스크 암호화는 업계 표준인 Windows BitLocker 기능과 Linux dm-crypt 기능을 결합하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다.

기본적으로 미사용 Azure Storage 및 Azure SQL Database 데이터 및 많은 서비스는 암호화를 옵션으로 제공합니다. Azure Key Vault를 사용하여 데이터에 액세스하고 데이터를 암호화하는 키의 제어를 유지 관리할 수 있습니다. [자세히 알아보려면 Azure 리소스 공급자 암호화 모델 지원](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support)을 참조하세요.

**모범 사례**: 암호화를 사용하여 권한 없는 데이터 액세스와 관련된 위험을 완화합니다.
**세부 정보**: 중요한 데이터를 작성하기 전에 드라이브를 암호화합니다.

데이터 암호화를 적용하지 않는 조직은 데이터 기밀성 문제에 더 많이 노출됩니다. 예를 들어 권한이 없거나 악의적인 사용자가 손상된 계정의 데이터를 훔치거나 명확한 서식으로 코딩된 데이터에 권한 없이 액세스할 수도 있습니다. 또한 회사는 산업 규정을 준수하기 위해 해당 데이터 보안을 강화하도록 성실하게 올바른 보안 컨트롤을 사용하고 있음을 증명해야 합니다.

## <a name="protect-data-in-transit"></a>전송 중인 데이터 보호

전송 중인 데이터 보호는 데이터 보호 전략의 핵심입니다. 데이터가 여러 위치를 이동하기 때문에 항상 SSL/TLS 프로토콜을 사용하여 여러 위치 간에 데이터를 교환하는 것이 일반적으로 좋습니다. VPN을 사용하여 온-프레미스와 클라우드 인프라 간에 전체 통신 채널을 격리하려는 경우도 있습니다.

온-프레미스 인프라와 Azure 간을 이동하는 데이터의 경우 HTTPS 또는 VPN처럼 적절한 안전 장치를 고려합니다. 공용 인터넷을 통해 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 전송하는 경우 [Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/)를 사용합니다.

Azure VPN Gateway, SSL/TLS 및 HTTPS를 사용하는 데 관련된 모범 사례는 다음과 같습니다.

**모범 사례**: 온-프레미스에 있는 여러 워크스테이션에서 Azure Virtual Network로의 액세스를 보호합니다.   
**세부 정보**: [사이트 간 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)을 사용합니다.

**모범 사례**: 온-프레미스에 있는 사설 워크스테이션에서 Azure Virtual Network로의 액세스를 보호합니다.   
**세부 정보**: [지점 및 사이트 간 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)을 사용합니다.

**모범 사례**: 전용 고속 WAN 링크를 통해 대량 데이터 세트를 이동합니다.   
**세부 정보**: [ExpressRoute](../expressroute/expressroute-introduction.md)를 사용합니다. ExpressRoute를 사용하기로 선택하는 경우 [SSL/TLS](https://support.microsoft.com/kb/257591) 또는 기타 프로토콜을 사용하여 애플리케이션 수준에서 데이터를 암호화하면 보안 수준을 더욱 높일 수 있습니다.

**모범 사례**: Azure Portal을 통해 Azure Storage와 상호 작용합니다.   
**세부 정보**: 모든 트랜잭션은 HTTPS를 통해 발생합니다. HTTPS를 통해 [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)를 사용하여 [Azure Storage](https://azure.microsoft.com/services/storage/) 및 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)와 상호 작용할 수도 있습니다.

전송 중인 데이터 보호에 실패하는 조직은 [가로채기(man-in-the-middle) 공격](https://technet.microsoft.com/library/gg195821.aspx), [도청](https://technet.microsoft.com/library/gg195641.aspx) 및 세션 하이재킹에 대해 더 취약합니다. 이러한 공격은 기밀 데이터에 대한 액세스 권한을 획득하기 위한 첫 번째 단계일 수 있습니다.

## <a name="secure-email-documents-and-sensitive-data"></a>이메일, 문서 및 중요한 데이터 보호

회사 외부에서 공유한 이메일, 문서 및 중요한 데이터를 제어하고 보호하려고 합니다. [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/)은 조직이 문서와 이메일을 분류하고, 레이블을 지정하고, 보호할 수 있게 해주는 클라우드 기반 솔루션입니다. 이러한 작업은 규칙 및 조건을 정의하는 관리자에 의해 자동으로, 사용자에 의해 수동으로 또는 사용자가 권장 사항을 가져오는 조합에 의해 수행될 수 있습니다.

데이터가 저장되는 위치 또는 공유 대상에 관계 없이 항상 분류를 식별할 수 있습니다. 레이블에는 헤더, 바닥글 또는 워터마크와 같은 시각적 표시가 포함됩니다. 메타데이터는 일반 텍스트로 파일 및 이메일 헤더에 추가됩니다. 일반 텍스트를 사용하면 데이터 손실을 방지하는 솔루션 등 다른 서비스가 분류를 식별하고 적절한 조치를 취할 수 있습니다.

보호 기술은 Azure RMS(Azure Rights Management)를 사용합니다. 이 기술은 Office 365 및 Azure Active Directory와 같은 다른 Microsoft 클라우드 서비스 및 애플리케이션과 통합됩니다. 이 보호 기술은 암호화, ID 및 권한 부여 정책을 사용합니다. Azure RMS를 통해 적용되는 보호는 조직, 네트워크, 파일 서버 및 애플리케이션의 내부 또는 외부에 있든 그 위치와 상관없이 문서 및 이메일과 함께 유지됩니다.

이 정보 보호 솔루션을 통해 데이터를 다른 사용자와 공유하는 경우에도 제어를 유지합니다. 이러한 애플리케이션 및 솔루션이 온-프레미스 또는 클라우드에 위치하는지와 상관 없이 고유한 기간 업무 애플리케이션 및 소프트웨어 공급 업체의 정보 보호 솔루션에서 Azure RMS를 사용할 수도 있습니다.

다음을 수행하는 것이 좋습니다.

- 조직에 대해 [Azure Information Protection 배포](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)
- 비즈니스 요구 사항을 반영하는 레이블 적용 예를 들면 다음과 같습니다. 이 데이터를 분류하고 보호하기 위해 일급 비밀 데이터가 포함된 모든 문서 및 이메일에 이름이 "극비"로 지정된 레이블을 적용합니다. 그런 다음, 지정한 제한 사항으로 인해 권한이 있는 사용자만 이 데이터를 액세스할 수 있습니다.
- 조직이 보호 서비스를 사용하는 방법을 모니터링할 수 있도록 [Azure RMS에 대한 사용 현황 로깅](https://docs.microsoft.com/azure/information-protection/log-analyze-usage)을 구성합니다.

[데이터 분류](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) 및 파일 보호에 약한 조직은 데이터 유출 및 데이터 남용에 더 취약할 수 있습니다. 적절한 파일 보호를 사용하여 비즈니스에 대한 인사이트를 얻고, 위험한 동작을 검색하고, 수정 조치를 수행하고, 문서에 대한 액세스를 추적하는 등 데이터 흐름을 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 보안 모범 사례 및 패턴](security-best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.

Azure 보안 및 관련 Microsoft 서비스에 대한 보다 일반적인 정보를 제공하는 다음 리소스도 확인할 수 있습니다.
* [Azure 보안 팀 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure Security 관련 최신 정보를 확인할 수 있습니다.
* [Microsoft 보안 응답 센터](https://technet.microsoft.com/library/dn440717.aspx) - Azure와 관련된 문제를 비롯한 Microsoft 보안 취약점을 보고하거나 secure@microsoft.com으로 이메일을 보낼 수 있습니다.
