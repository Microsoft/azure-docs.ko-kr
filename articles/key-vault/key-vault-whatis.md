---
title: Azure Key Vault란? | Microsoft Docs
description: Azure Key Vault는 암호화 키를 보호 하 고 암호는 클라우드 응용 프로그램 및 서비스 사용 방법에 대해 알아봅니다.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 48ac0c3efe74723099e87a77871aa1a78834efbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640562"
---
# <a name="what-is-azure-key-vault"></a>Azure Key Vault란?

암호화 키를 사용 하 여 클라우드 응용 프로그램 및 서비스 하 고 정보를 유지 하는 데 암호 보호. Azure Key Vault는 이러한 키와 비밀을 보호 합니다. Key Vault를 사용 하면 하드웨어 보안 모듈 (Hsm)로 보호 된 키를 사용 하 여 인증 키, 저장소 계정 키, 데이터 암호화 키, pfx 파일 및 암호를 암호화할 수 있습니다.

Key Vault는 다음 문제를 해결 하는 데 도움이 됩니다.

- **비밀 관리**: 안전 하 게 저장 하 고 밀접 하 게 토큰, 암호, 인증서, API 키 및 기타 비밀에 대 한 액세스를 제어 합니다.
- **키 관리**: 페이지를 만들고 데이터를 암호화 하는 암호화 키를 제어 합니다. 
- **인증서 관리**: 프로 비전, 관리 및 사용 하 여 Azure와 연결 된 내부 리소스에 대 한 공용 및 개인 Secure Sockets Layer/Transport Layer Security (SSL/TLS) 인증서를 배포 합니다. 
- **Hsm에서 지 원하는 암호를 저장**: 소프트웨어 또는 FIPS 140-2 Level 2 Hsm 키와 암호 정보를 보호 하기 위해 유효성 검사를 사용 합니다.

## <a name="basic-concepts"></a>기본 개념

Azure Key Vault는 비밀을 안전하게 저장하고 액세스하기 위한 도구입니다. 비밀은 API 키, 암호 또는 인증서 등에 대한 액세스를 엄격하게 제어하려는 항목입니다. 자격 증명 모음을은 비밀의 논리적 그룹입니다.

다른 중요 한 용어는 다음과 같습니다.

- **테넌트**: 테넌트는 Microsoft 클라우드 서비스의 특정 인스턴스를 소유하고 관리하는 조직입니다. 대부분의 조직에 대 한 Azure 및 Office 365 서비스의 집합을 참조 하는 것이 됩니다.

- **자격 증명 모음 소유자**: 자격 증명 모음 소유자는 키 자격 증명 모음을 만들고 전체 자격 증명 모음을 액세스하고 제어할 수 있습니다. 자격 증명 모음 소유자는 비밀과 키에 액세스하는 사용자를 기록하도록 감사를 설정할 수도 있습니다. 관리자는 키 수명 주기를 제어할 수 있습니다. 새 버전의 키로 롤링하고, 백업하고, 관련 작업을 수행할 수 있습니다.

- **자격 증명 모음 소비자**: 자격 증명 모음 소유자가 소비자 액세스 권한을 부여하면 자격 증명 모음 소비자가 키 자격 증명 모음 내의 자산에 대한 작업을 수행할 수 있습니다. 사용 가능한 작업은 부여된 사용 권한에 따라 달라집니다.

- **리소스**: 리소스는 Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 일반적인 예로 가상 머신, 저장소 계정, 웹 앱, 데이터베이스 및 가상 네트워크. 다양 합니다.

- **리소스 그룹**: 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다.

- **서비스 주체**: Azure 서비스 주체는 사용자가 만든 앱, 서비스 및 자동화 도구를 사용 하 여 특정 Azure 리소스에 액세스 하는 보안 id입니다. "사용자 id"으로 생각해 (사용자 이름 및 암호 또는 인증서) 특정 역할 및 엄격 하 게 제어를 사용 하 여 합니다. 일반 사용자 ID와 달리 서비스 주체는 특정 작업만 수행하면 됩니다. 관리 태스크를 수행 하는 데 필요한 최소 권한 수준만 부여 하는 경우 보안이 향상 됩니다.

- [Azure AD(Azure Active Directory)](../active-directory/active-directory-whatis.md): Azure AD는 테넌트에 대한 Active Directory 서비스입니다. 각 디렉터리에는 하나 이상의 도메인이 있습니다. 디렉터리는 연결된 여러 구독을 가질 수 있지만 오직 하나의 테넌트만을 가질 수 있습니다.

- **Azure 테넌트 ID**: 테넌트 ID는 Azure 구독 내에서 Azure AD 인스턴스를 식별하는 고유한 방법입니다.

- **Identities 관리**: Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. 관리 ID를 사용하면 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 Key Vault 또는 모든 서비스에 인증할 수 있으므로 코드에 어떤 자격 증명도 포함할 필요가 없습니다. 자세한 내용은 다음 이미지를 참조 하며 [Azure 리소스에 대 한 관리 되는 id 개요](../active-directory/managed-identities-azure-resources/overview.md)합니다.

    ![Azure 리소스 작업에 대 한 관리 되는 id의 다이어그램](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Key Vault를 사용 하 여 모든 작업을 위해 처음에 인증 해야 합니다. Key Vault에 인증 하는 방법은 세 가지가 있습니다.

- [Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview): Azure의 가상 머신에서 앱을 배포할 때에 Key Vault에 액세스할 수 있는 가상 컴퓨터에 id를 할당할 수 있습니다. Id를 할당할 수도 있습니다 [다른 Azure 리소스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)합니다. 이 방식의 장점은 앱 또는 서비스에 첫 번째 암호 순환을 관리 되지 않습니다. ID가 Azure에서 자동으로 회전됩니다. 모범 사례로이 방법을 권장 합니다. 
- **서비스 주체 및 인증서**: 서비스 주체 및 Key Vault에 액세스할 수 있는 연결된 된 인증서를 사용할 수 있습니다. 응용 프로그램 소유자나 개발자 인증서를 회전 해야 하기 때문에이 방법은 권장 하지 않습니다.
- **서비스 주체 및 비밀**: 서비스 주체 및 암호를 사용 하 여 Key Vault에 인증할 수 있습니다, 있지만 권장 하지 않습니다. Key Vault에 인증 하는 데 사용 되는 부트스트랩 암호를 자동으로 회전 하는 것이 어렵습니다.


## <a name="key-vault-roles"></a>Key Vault 역할

다음 표에서 키 자격 증명 모음이 개발자와 보안 관리자의 요구를 충족하도록 도와주는 방법을 더 잘 이해할 수 있습니다.

| 역할 | 문제 설명 | Azure Key Vault로 해결됨 |
| --- | --- | --- |
| Azure 애플리케이션용 개발자 |"하려고 서명 및 암호화 키를 사용 하는 Azure 용 응용 프로그램을 작성 합니다. 하지만 이러한 키를 되도록 내 응용 프로그램의 외부 솔루션은 지리적으로 분산 되는 응용 프로그램에 적합 합니다. <br/><br/>직접 코드를 작성하지 않고도 이러한 키와 비밀을 보호하려고 합니다. 또한 이러한 키와 비밀을 내 애플리케이션에서 최적의 성능으로 사용할 수 있게 하려고 합니다." |√ 키를 자격 증명 모음에 저장하고 필요할 때 URI로 호출합니다.<br/><br/> √ Azure에서는 업계 표준 알고리즘, 키 길이 및 하드웨어 보안 모듈을 사용하여 키를 보호합니다.<br/><br/> √ 애플리케이션과 동일한 Azure 데이터 센터에 상주하는 HSM에서 키를 처리합니다. 이 방법은 온-프레미스와 같이 별도의 위치에 있는 키보다 안정성이 뛰어나 대기 시간이 줄어 듭니다. |
| SaaS(Software as a Service) 개발자 |"내 고객의 테넌트 키와 암호에 대한 책임 또는 잠재적인 법적 책임을 원하지 않습니다. <br/><br/>핵심 소프트웨어 기능을 제공하는 일에 최선을 다해 집중할 수 있도록 고객이 키를 소유하고 관리하기를 원합니다." |√ 고객은 Azure에서 고유한 키를 가져오고 관리할 수 있습니다. SaaS 응용 프로그램을 고객의 키를 사용 하 여 암호화 작업을 수행 하면 Key Vault는 응용 프로그램을 대신 하 여 이러한 작업을 수행 합니다. 애플리케이션에서 고객의 키를 표시하지 않습니다. |
| 수석 보안 책임자(CSO) |"보안 키 관리를 위해 애플리케이션이 FIPS 140-2 Level 2 HSM을 준수하는지 알고 싶습니다. <br/><br/>내 조직이 키 수명 주기를 제어하고 키 사용을 모니터링할 수 있는지 확인하고 싶습니다. <br/><br/>여러 Azure 서비스 및 리소스를 사용하더라도, Azure의 단일 위치에서 키를 관리 하고 싶습니다." |√ HSM은 FIPS 140-2 Level 2 검증되었습니다.<br/><br/>√ Key Vault는 Microsoft에서 키를 보거나 추출할 수 없게 설계되어 있습니다.<br/><br/>√ 키 사용은 거의 실시간으로 기록됩니다.<br/><br/>√ Azure에 얼마나 많은 자격 증명 모음이 있는지, 지원되는 영역 및 사용하는 애플리케이션과 관계 없이 자격 증명 모음은 단일 인터페이스를 제공합니다. |

Azure를 구독하는 사용자는 주요 자격 증명 모음을 만들고 사용할 수 있습니다. Key Vault 혜택이 있지만 개발자와 보안 관리자, 구현 하 고 다른 Azure 서비스를 관리 하는 조직의 관리자에 의해 관리 될 수 있습니다. 예를 들어,이 관리자는 Azure 구독에 로그인 할 수 키를 저장 한 다음 다음과 같은 운영 작업을 담당 하는 조직에 대 한 자격 증명 모음을 만듭니다.

- 키 또는 비밀 만들기 또는 가져오기
- 키 또는 비밀 취소 또는 삭제
- 사용자 또는 애플리케이션이 키 자격 증명 모음에 액세스하도록 권한을 부여하므로 해당 키 및 비밀을 관리하거나 사용할 수 있습니다.
- 구성 키 용도(예: 서명 또는 암호화)
- 키 사용량 모니터링

그런 다음이 관리자가 개발자가 응용 프로그램에서 호출 하는 Uri를 제공 합니다. 또한이 관리자는 보안 관리자에 게 키 사용 현황 로깅 정보를 제공합니다. 

![Azure Key Vault 작동 원리 개요][1]

개발자는 API를 사용하여 직접 키를 관리할 수도 있습니다. 자세한 내용은 [키 자격 증명 모음 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

설명 하는 방법 [자격 증명 모음 보안](key-vault-secure-your-key-vault.md)합니다.

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault는 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.
