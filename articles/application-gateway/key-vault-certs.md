---
title: Azure Key Vault 인증서를 사용 하 여 SSL 종료
description: HTTPS 사용 수신기에 연결 된 서버 인증서에 대 한 Key Vault Azure 애플리케이션 Gateway를 통합 하는 방법에 대해 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 5633dd7b72f4de22cd34b7d093e8ec4d9cb411f1
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137694"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Key Vault 인증서를 사용 하 여 SSL 종료

[Azure Key Vault](../key-vault/key-vault-overview.md) 는 암호, 키 및 SSL 인증서를 보호 하는 데 사용할 수 있는 플랫폼 관리 암호 저장소입니다. Azure 애플리케이션 Gateway는 HTTPS 사용 수신기에 연결 된 서버 인증서에 대 한 Key Vault와의 통합을 지원 합니다. 이 지원은 Application Gateway의 v2 SKU로 제한 됩니다.

Key Vault 통합은 SSL 종료를 위한 두 가지 모델을 제공 합니다.

- 수신기에 연결 된 SSL 인증서를 명시적으로 제공할 수 있습니다. 이 모델은 ssl 인증서를 SSL 종료를 위해 Application Gateway에 전달 하는 일반적인 방법입니다.
- HTTPS 사용 수신기를 만들 때 필요에 따라 기존 Key Vault 인증서 또는 암호에 대 한 참조를 제공할 수 있습니다.

Key Vault와 Application Gateway 통합 하면 다음과 같은 여러 이점이 제공 됩니다.

- SSL 인증서가 응용 프로그램 개발 팀에서 직접 처리 되지 않으므로 보안이 강화 됩니다. 통합을 통해 별도의 보안 팀이 다음을 수행할 수 있습니다.
  * 응용 프로그램 게이트웨이를 설정 합니다.
  * Application gateway 수명 주기를 제어 합니다.
  * 키 자격 증명 모음에 저장 된 인증서에 액세스할 수 있도록 선택한 응용 프로그램 게이트웨이에 대 한 사용 권한을 부여 합니다.
- 주요 자격 증명 모음으로 기존 인증서 가져오기 지원 또는 Key Vault Api를 사용 하 여 신뢰할 수 있는 Key Vault 파트너와 함께 새 인증서를 만들고 관리할 수 있습니다.
- 키 자격 증명 모음에 저장 된 인증서의 자동 갱신을 지원 합니다.

Application Gateway는 현재 소프트웨어 유효성 검사 인증서만 지원 합니다. HSM (하드웨어 보안 모듈)-유효성이 검사 된 인증서는 지원 되지 않습니다. Application Gateway Key Vault 인증서를 사용 하도록 구성 된 경우 해당 인스턴스는 Key Vault에서 인증서를 검색 하 여 SSL 종료를 위해 로컬로 설치 합니다. 또한 인스턴스는 갱신 된 버전의 인증서 (있는 경우)를 검색 하기 위해 24 시간 간격으로 Key Vault를 폴링합니다. 업데이트 된 인증서가 있는 경우 현재 HTTPS 수신기와 연결 된 SSL 인증서가 자동으로 순환 됩니다.

> [!NOTE]
> Azure Portal는 비밀이 아닌 KeyVault 인증서만 지원 합니다. Application Gateway는 여전히 KeyVault의 비밀을 참조할 수 있지만 PowerShell, CLI, API, ARM 템플릿 등과 같은 비 포털 리소스를 통해서만 지원 됩니다. 

## <a name="how-integration-works"></a>통합 작동 방법

Key Vault와 통합 Application Gateway 하려면 3 단계 구성 프로세스가 필요 합니다.

1. **사용자 할당 관리 id 만들기**

   사용자를 대신 하 여 Key Vault에서 인증서를 검색 하는 데 사용 Application Gateway 하는 기존 사용자 할당 관리 id를 만들거나 다시 사용 합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요. 이 단계에서는 Azure Active Directory 테 넌 트에 새 id를 만듭니다. Id를 만드는 데 사용 되는 구독에서 id를 신뢰 합니다.

1. **주요 자격 증명 모음 구성**

   그런 다음 기존 인증서를 가져오거나 키 자격 증명 모음에 새 인증서를 만듭니다. 인증서는 응용 프로그램 게이트웨이를 통해 실행 되는 응용 프로그램에서 사용 됩니다. 이 단계에서는 암호로 사용 되지 않는 기본 64 인코딩 PFX 파일로 저장 된 key vault 암호를 사용할 수도 있습니다. 키 자격 증명 모음에서 인증서 유형 개체와 함께 사용할 수 있는 자동 갱신 기능으로 인해 인증서 유형을 사용 하는 것이 좋습니다. 인증서 또는 암호를 만든 후에는 키 자격 증명 모음에서 액세스 정책을 정의 하 여 id에 암호에 대 한 액세스 *권한을 부여할 수* 있도록 합니다.

1. **응용 프로그램 게이트웨이 구성**

   위의 두 단계를 완료 한 후에는 사용자 할당 관리 id를 사용 하도록 기존 응용 프로그램 게이트웨이를 설정 하거나 수정할 수 있습니다. Key Vault 인증서 또는 암호 ID의 전체 URI를 가리키도록 HTTP 수신기의 SSL 인증서를 구성할 수도 있습니다.

   ![주요 자격 증명 모음 인증서](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>다음 단계

[Azure PowerShell를 사용 하 여 Key Vault 인증서로 SSL 종료 구성](configure-keyvault-ps.md)
