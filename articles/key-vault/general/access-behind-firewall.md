---
title: 방화벽 뒤에 있는 Key Vault에 액세스 - Azure Key Vault | Microsoft Docs
description: 방화벽으로 보호된 키 자격 증명 모음 클라이언트 애플리케이션이 키 자격 증명 모음에 액세스할 수 있게 하려면 어떤 포트, 호스트 또는 IP 주소를 열어야 하는지 알아봅니다.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 1ab5ae7bf9f1d13458e3bbeeec564fe642eb3303
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588731"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Key Vault 액세스

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>키 자격 증명 모음에 액세스하기 위해 방화벽으로 보호된 내 키 자격 증명 모음 클라이언트 애플리케이션을 사용하려면 어떤 포트, 호스트 또는 IP 주소를 열어야 하나요?

주요 자격 증명 모음에 액세스하려면 주요 자격 증명 모음 클라이언트 애플리케이션은 다양한 기능에 대한 여러 엔드포인트에 액세스해야 합니다.

* Azure AD(Azure Active Directory)를 통한 인증.
* Azure Key Vault의 관리. Azure Resource Manager를 통한 액세스 정책 만들기, 읽기, 업데이트, 삭제 및 설정을 포함합니다.
* Key Vault 자체에 저장된 개체(키와 비밀)를 액세스하고 관리하는 작업은 Key Vault 특정 엔드포인트를 통과합니다(예: `https://yourvaultname.vault.azure.net`).  

구성 및 환경에 따라 일부의 변형이 있습니다.

## <a name="ports"></a>포트

모든 3가지 함수(인증, 관리 및 데이터 평면 액세스)의 주요 자격 증명 모음에 대한 모든 트래픽은 HTTPS: 포트 443을 통해 이동합니다. 그러나 CRL의 경우 가끔 HTTP(포트 80) 트래픽이 있습니다. OCSP를 지원하는 클라이언트는 CRL에 도달하지 않아야 하지만 경우에 따라 [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)에 도달할 수 있습니다.  

## <a name="authentication"></a>인증

주요 자격 증명 모음 클라이언트 애플리케이션은 인증을 위해 Azure Active Directory 엔드포인트에 액세스해야 합니다. 사용되는 엔드포인트는 Azure AD 테넌트 구성 및 주체의 형식(사용자 계정, 서비스 주체) 및 계정의 형식, 즉, Microsoft 계정 또는 회사 또는 학교 계정에 따라 달라집니다.  

| 주체 유형 | 엔드포인트:포트 |
| --- | --- |
| Microsoft 계정을 사용하는 사용자<br> (예: user@hotmail.com) |**전역:**<br> login.microsoftonline.com:443<br><br> **Azure 중국:**<br> login.chinacloudapi.cn:443<br><br>**Azure 미국 정부:**<br> login.microsoftonline.us:443<br><br>**Azure 독일:**<br> login.microsoftonline.de:443<br><br> and <br>login.live.com:443 |
| Azure AD로 회사 또는 학교 계정을 사용하는 사용자 또는 서비스 주체(예: user@contoso.com) |**전역:**<br> login.microsoftonline.com:443<br><br> **Azure 중국:**<br> login.chinacloudapi.cn:443<br><br>**Azure 미국 정부:**<br> login.microsoftonline.us:443<br><br>**Azure 독일:**<br> login.microsoftonline.de:443 |
| 회사 또는 학교 계정을 사용하는 사용자 또는 서비스 주체 및 AD FS(Active Directory Federation Services) 또는 다른 페더레이션된 엔드포인트(예: user@contoso.com) |회사 또는 학교 계정에 대한 모든 엔드포인트 및 AD FS 또는 다른 페더레이션된 엔드포인트 |

다른 복잡한 시나리오도 가능합니다. 추가 정보는 [Azure Active Directory 인증 흐름](../../active-directory/develop/authentication-scenarios.md), [Azure Active Directory와 애플리케이션 통합](../../active-directory/develop/active-directory-how-to-integrate.md) 및 [Active Directory 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn151124.aspx)을 참조하세요.  

## <a name="key-vault-management"></a>Key Vault 관리

Key Vault 관리(CRUD 및 액세스 정책 설정)의 경우 주요 자격 증명 모음 클라이언트 애플리케이션은 Azure Resource Manager 엔드포인트에 액세스해야 합니다.  

| 연산 유형 | 엔드포인트:포트 |
| --- | --- |
| Key Vault 제어 평면 작업<br> \- Azure Resource Manager 사용 |**전역:**<br> management.azure.com:443<br><br> **Azure 중국:**<br> management.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> management.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> management.microsoftazure.de:443 |
| Microsoft Graph API |**전역:**<br> graph.microsoft.com:443<br><br> **Azure 중국:**<br> graph.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> graph.microsoft.com:443<br><br> **Azure 독일:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Key Vault 작업

모든 주요 자격 증명 모음 개체(키와 암호) 관리 및 암호화 작업의 경우 주요 자격 증명 모음 클라이언트는 주요 자격 증명 모음 엔드포인트에 액세스해야 합니다. 엔드포인트 DNS 접미사는 주요 자격 증명 모음의 위치에 따라 다릅니다. 주요 자격 증명 모음 엔드포인트는 다음 테이블에 설명된 대로 *vault-name*.*region-specific-dns-suffix* 형식입니다.  

| 연산 유형 | 엔드포인트:포트 |
| --- | --- |
| 키에 대한 암호화 작업을 포함하는 작업, 키 및 암호 만들기, 읽기, 업데이트 및 삭제, 키 자격 증명 모음 개체(키 또는 암호)의 태그 및 기타 특성 설정 또는 가져오기 |**전역:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure 중국:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure 미국 정부:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP 주소 범위

Key Vault 서비스는 PaaS 인프라와 같은 다른 Azure 리소스를 사용합니다. 따라서 Key Vault 서비스 엔드포인트는 특정 시간에 가지므로 특정 범위의 IP 주소를 제공할 수 없습니다. 방화벽이 IP 주소 범위만을 지원하는 경우 다음에서 사용할 수 있는 Microsoft Azure 데이터 센터 IP 범위 문서를 참조하세요.
* [공용](https://www.microsoft.com/en-us/download/details.aspx?id=56519)
* [US Gov](https://www.microsoft.com/en-us/download/details.aspx?id=57063)
* [독일](https://www.microsoft.com/en-us/download/details.aspx?id=57064)
* [중국](https://www.microsoft.com/en-us/download/details.aspx?id=57062)

인증 및 ID(Azure Active Directory)는 글로벌 서비스이며 다른 지역으로 장애 조치(failover)하거나 고지 없이 트래픽을 이동할 수 있습니다. 이 시나리오에서는 [인증 및 ID IP 주소](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip)에 나열된 모든 IP 범위를 방화벽에 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

Key Vault에 대한 질문이 있으면 [Azure Key Vault에 대한 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-key-vault.html)를 방문하세요.
