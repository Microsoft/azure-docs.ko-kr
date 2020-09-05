---
title: 보안 권장 사항
description: 공유 책임 모델에 명시 된 대로 보안 의무를 충족 하는 데 도움이 되는 보안 권장 사항을 구현 합니다. 앱의 보안을 향상 시킵니다.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: eba08211355b0f57dd9dd11ec4c18d5912208ec8
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962216"
---
# <a name="security-recommendations-for-app-service"></a>App Service에 대 한 보안 권장 사항

이 문서에는 Azure App Service에 대 한 보안 권장 사항이 포함 되어 있습니다. 이러한 권장 사항을 구현 하면 공유 책임 모델에 설명 된 대로 보안 의무를 달성 하는 데 도움이 되며 웹 앱 솔루션에 대 한 전반적인 보안을 향상 시킬 수 있습니다. Microsoft에서 서비스 공급자 역할을 수행 하는 것에 대 한 자세한 내용은 [Azure 인프라 보안](../security/fundamentals/infrastructure.md)을 참조 하세요.

## <a name="general"></a>일반

| 권장 | 주석 |
|-|-|----|
| 최신 정보 유지 | 지원 되는 플랫폼, 프로그래밍 언어, 프로토콜 및 프레임 워크의 최신 버전을 사용 합니다. |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 |
|-|----|
| 익명 액세스 사용 안 함 | 익명 요청을 지원 해야 하는 경우가 아니면 익명 액세스를 사용 하지 않도록 설정 합니다. Azure App Service 인증 옵션에 대 한 자세한 내용은 [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 참조 하세요.|
| 인증 필요 | 가능 하면 인증 및 권한 부여를 처리 하는 코드를 작성 하는 대신 App Service 인증 모듈을 사용 합니다. [Azure App Service의 인증 및 권한 부여](overview-authentication-authorization.md)를 참조 하세요. |
| 인증 된 액세스를 사용 하 여 백 엔드 리소스 보호 | 사용자의 id를 사용 하거나 응용 프로그램 id를 사용 하 여 백 엔드 리소스에 인증할 수 있습니다. 응용 프로그램 id를 사용 하도록 선택 하는 경우 [관리 id](overview-managed-identity.md)를 사용 합니다.
| 클라이언트 인증서 인증 필요 | 클라이언트 인증서 인증은 사용자가 제공 하는 인증서를 사용 하 여 인증할 수 있는 클라이언트의 연결만 허용 하 여 보안을 향상 시킵니다. |

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 |
|-|-|
| HTTP를 HTTPs로 리디렉션 | 기본적으로 클라이언트는 HTTP 또는 HTTPS를 모두 사용 하 여 웹 앱에 연결할 수 있습니다. HTTPS는 SSL/TLS 프로토콜을 사용 하 여 암호화 되 고 인증 된 보안 연결을 제공 하므로 HTTP를 HTTPs로 리디렉션하는 것이 좋습니다. |
| Azure 리소스에 대 한 통신 암호화 | 앱이 [SQL Database](https://azure.microsoft.com/services/sql-database/) 또는 [Azure Storage](../storage/index.yml)와 같은 azure 리소스에 연결 하는 경우 연결은 azure에 유지 됩니다. Azure의 공유 네트워킹을 통해 연결이 이동 하므로 항상 모든 통신을 암호화 해야 합니다. |
| 최신 TLS 버전을 사용할 수 있어야 합니다. | 2018 새로운 Azure App Service 앱은 TLS 1.2를 사용 합니다. 최신 버전의 TLS에는 이전 프로토콜 버전 보다 향상 된 보안 기능이 포함 되어 있습니다. |
| FTPS 사용 | App Service는 파일을 배포하기 위해 FTP와 FTPS를 모두 지원합니다. 가능 하면 FTP 대신 FTPS를 사용 합니다. 이러한 프로토콜 중 하나 또는 둘 다 사용하지 않는 경우 [해당 프로토콜을 사용하지 않도록 설정](deploy-ftp.md#enforce-ftps)해야 합니다. |
| 애플리케이션 데이터 보호 | 코드 또는 구성 파일에 데이터베이스 자격 증명, API 토큰 또는 개인 키와 같은 응용 프로그램 암호를 저장 하지 마세요. 일반적으로 허용되는 방법은 선택한 언어로 표준 패턴을 사용하여 [환경 변수](https://wikipedia.org/wiki/Environment_variable)로 액세스하는 것입니다. Azure App Service에서 [앱 설정](./configure-common.md) 및 [연결 문자열](./configure-common.md)을 통해 환경 변수를 정의할 수 있습니다. 앱 설정 및 연결 문자열은 Azure에 암호화되어 저장됩니다. 앱 설정은 앱이 시작 될 때 앱 프로세스 메모리에 삽입 되기 전에만 해독 됩니다. 암호화 키는 정기적으로 회전합니다. 또는 고급 암호 관리를 위해 Azure App Service 앱을 [Azure Key Vault](../key-vault/index.yml) 와 통합할 수 있습니다. [관리 ID를 사용하여 Key Vault에 액세스](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)하는 경우 App Service 앱에서 필요한 비밀에 안전하게 액세스할 수 있습니다. |

## <a name="networking"></a>네트워킹

| 권장 | 주석 |
|-|-|
| 고정 IP 제한 사용 | Windows에서 Azure App Service를 사용 하 여 앱에 액세스할 수 있는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에서 정의된 개별 IP 주소 또는 IP 주소 범위가 포함될 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](app-service-ip-restrictions.md)을 참조하세요.  |
| 격리 가격 책정 계층 사용 | 격리 가격 책정 계층을 제외 하 고, 모든 계층은 Azure App Service의 공유 네트워크 인프라에서 앱을 실행 합니다. 격리 계층은 전용 [App Service 환경](environment/intro.md)내에서 앱을 실행 하 여 완벽 한 네트워크 격리를 제공 합니다. App Service 환경은 [Azure Virtual Network](../virtual-network/index.yml)의 사용자 인스턴스에서 실행됩니다.|
| 온-프레미스 리소스에 액세스할 때 보안 연결 사용 | [하이브리드 연결](app-service-hybrid-connections.md), [Virtual Network 통합](web-sites-integrate-with-vnet.md)또는 [App Service 환경](environment/intro.md) 을 사용 하 여 온-프레미스 리소스에 연결할 수 있습니다. |
| 인바운드 네트워크 트래픽에 대 한 노출 제한 | 네트워크 보안 그룹을 사용 하면 네트워크 액세스를 제한 하 고 노출 된 끝점의 수를 제어할 수 있습니다. 자세한 내용은 [App Service Environment에 대 한 인바운드 트래픽을 제어 하는 방법](environment/app-service-app-service-environment-control-inbound-traffic.md)을 참조 하세요. |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 |
|-|-|
|Azure Security Center 표준 계층 사용 | [Azure Security Center](../security-center/security-center-app-services.md) 은 기본적으로 Azure App Service와 통합 됩니다. 평가를 실행 하 고 보안 권장 사항을 제공할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

애플리케이션 공급자에게 추가 보안 요구 사항이 있는지 확인합니다. 보안 애플리케이션 개발에 대한 자세한 내용은 [보안 개발 설명서](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)를 참조하세요.