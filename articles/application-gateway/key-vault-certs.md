---
title: Key Vault 인증서를 사용 하 여 SSL 종료
description: 사용 하도록 설정 하는 HTTPS 수신기에 연결 된 서버 인증서에 대 한 Key Vault를 사용 하 여 Azure 응용 프로그램 게이트웨이 통합 하는 방법도 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 9ab1a42578081d4e6537f221e3f3a8f4c7babde0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759629"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Key Vault 인증서를 사용 하 여 SSL 종료

[Azure Key Vault](../key-vault/key-vault-whatis.md) 플랫폼에서 관리 되는 암호 저장소가 비밀, 키 및 SSL 인증서를 보호 하는 데 사용할 수 있습니다. Application Gateway는 사용 하도록 설정 하는 HTTPS 수신기에 연결 된 서버 인증서 (공개 미리 보기)에서 Key Vault를 사용 하 여 통합을 지원 합니다. 이 지원은 Application Gateway의 v2 SKU로 제한 됩니다.

> [!IMPORTANT]
> 응용 프로그램 게이트웨이 Key Vault 통합은 현재 공개 미리 보기로 제공 합니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 공개 미리 보기를 사용 하 여 SSL 종료를 위한 두 가지 모델 가지가 있습니다.

- 수신기에 연결 된 SSL 인증서를 명시적으로 제공할 수 있습니다. SSL 종료에 대 한 Application Gateway에 SSL 인증서를 전달 하는 기존 모델입니다.
- 필요에 따라 기존 키 자격 증명 모음 인증서에 대 한 참조를 제공할 수 있습니다 또는 HTTPS 중 암호 수신기 생성 하도록 설정 합니다.

Key Vault 통합을 사용 하 여 많은 혜택이 포함:

- SSL 인증서는 응용 프로그램 개발 팀에서 직접 처리 하지 않습니다 하므로 보안을 강화 합니다. Key Vault와의 통합, 프로 비전 하 고, 수명 주기를 제어 하 고, Key Vault에 저장 된 액세스 인증서를 Application Gateway를 선택 하려면 적절 한 권한을 부여 하려면 별도 보안 팀을 허용 합니다.
- Key Vault로 기존 인증서를 가져오는 지원 하거나 키 자격 증명 모음 Api를 사용 하 여 만들고 신뢰할 수 있는 Key Vault 파트너 중 하나를 사용 하 여 새 인증서를 관리 합니다.
- 자동으로 갱신 하려면 Key Vault에 저장 된 인증서를 지원 합니다.

현재 application Gateway의 유효성을 검사 하는 소프트웨어 인증서만을 지원합니다. 하드웨어 보안 모듈 (HSM)의 유효성을 검사 인증서는 지원 되지 않습니다. Application Gateway Key Vault 인증서를 사용 하도록 구성 되 면 해당 인스턴스 Key Vault에서 인증서를 검색 및 SSL 종료에 대해 로컬로 설치 합니다. 인스턴스는 정기적으로 폴링합니다 Key Vault 인증서의 갱신 된 버전을 검색 하려면 24 시간 간격으로 존재 하는 경우. 업데이트 된 인증서가 없으면 현재 HTTPS 수신기와 연결 된 SSL 인증서를 자동으로 회전 합니다.

## <a name="how-it-works"></a>작동 방법

Key Vault와 통합 된 세 단계로 구성 프로세스를 필요합니다.

1. **관리 되는 id를 할당 하는 사용자 만들기**

   만들기 또는 사용자를 대신해 Key Vault에서 인증서를 검색 하도록 응용 프로그램 게이트웨이 사용 하는 관리 되는 id를 할당 합니다. 기존 사용자를 재사용 해야 합니다. 자세한 내용은 참조 하세요. [Azure 리소스에 대 한 관리 되는 id 란?](../active-directory/managed-identities-azure-resources/overview.md) 이 단계에서 Azure AD 테 넌 트 id를 만드는 데 사용 된 구독에서 신뢰할 수 있는 새 id를 만듭니다.
1. **키 자격 증명 모음 구성**

   응용 프로그램 게이트웨이 통해 실행 중인 응용 프로그램에서 사용 하는 Key Vault에서 새 인증서를 만들어 하거나 가져오거나 다음 해야 합니다. Key Vault 비밀 암호 없는 base 64로 인코딩된 PFX 파일을 사용 하 여이 단계에서는 수로 저장 합니다. 인증서 종류를 사용 하 여 Key Vault에 인증서 형식 개체를 사용 하 여 사용할 수 있는 자동 갱신 기능으로 인해 선호 됩니다. Id를 부여할 수 있도록 Key Vault에 액세스 정책을 정의 해야 합니다 인증서 또는 암호를 만들어지면 *가져올* 인출 비밀에 대 한 액세스.

1. **응용 프로그램 게이트웨이 구성**

   이전 두 단계를 완료 되 면 프로 비전 할 수도 있고 사용자 할당 관리 되는 id를 사용 하도록 기존 Application Gateway를 수정할 수 있습니다. 전체 URI의 키 자격 증명 모음의 인증서 또는 암호 ID를 가리키도록 HTTP 수신기의 SSL 인증서를 구성할 수도

![Key Vault 인증서](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>다음 단계

[Azure PowerShell을 사용 하 여 Key Vault 인증서를 사용 하 여 SSL 종료를 구성](configure-keyvault-ps.md)합니다.