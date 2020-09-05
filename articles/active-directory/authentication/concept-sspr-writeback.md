---
title: 셀프 서비스 암호 재설정을 사용하여 온-프레미스 암호 쓰기 저장 - Azure Active Directory
description: Azure Active Directory의 암호 변경 또는 이벤트 재설정을 온-프레미스 디렉터리 환경에 쓰기 저장할 수 있는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3959fc7df78a5c1f255f7551a018eec6b7279eb1
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717441"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Azure Active Directory에서 셀프 서비스 암호 재설정 쓰기 저장의 작동 방식

Azure Active Directory(Azure AD) 셀프 서비스 암호 재설정(SSPR)을 통해 사용자는 클라우드에서 암호를 재설정할 수 있지만, 대부분의 회사에는 사용자가 존재하는 온-프레미스 Active Directory Domain Services(AD DS) 환경도 있습니다. 비밀번호 쓰기 저장은 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)를 통해 활성화되며 클라우드의 암호 변경 내용을 실시간으로 기존 온-프레미스 디렉터리에 다시 쓸 수 있는 기능입니다. 이 구성에서 사용자가 SSPR를 이용하여 클라우드에서 암호를 변경하거나 재설정하면 업데이트된 암호 또한 온-프레미스 AD DS 환경에 쓰기 저장됩니다.

> [!IMPORTANT]
> 이 개념 문서에서는 셀프 서비스 암호 재설정 쓰기 저장의 작동 원리를 관리자에 게 설명 합니다. 셀프 서비스 암호 재설정에 이미 등록된 최종 사용자가 계정으로 다시 이동해야 하는 경우 https://aka.ms/sspr 로 이동합니다.
>
> IT 팀이 자신의 암호를 재설정 하는 기능을 사용하도록 설정하지 않은 경우, 추가 지원이 필요 하면 기술 지원팀에 문의하세요.

암호 쓰기 저장 기능은 다음과 같은 하이브리드 ID 모델을 사용하는 환경에서 지원됩니다.

* [암호 해시 동기화](../hybrid/how-to-connect-password-hash-synchronization.md)
* [통과 인증](../hybrid/how-to-connect-pta.md)
* [ADFS(Active Directory Federation Services)](../hybrid/how-to-connect-fed-management.md)

비밀번호 쓰기 저장에서 제공하는 기능은 다음과 같습니다.

* **온-프레미스 Active Directory Domain Services(AD DS) 암호 정책의 적용**: 사용자가 암호를 재설정하는 경우 해당 디렉터리에 커밋하기 전에 온-프레미스 AD DS 정책을 준수하는지 확인합니다. 이 검토 작업에는 기록, 복잡성, 나이, 암호 필터 및 AD DS에 정의한 기타 암호 제한 사항을 확인하는 일이 포함됩니다.
* **지연 피드백 없음**:  암호 쓰기 저장은 동기식 작업입니다. 사용자의 암호가 정책을 준수하지 않거나 어떠한 이유로든 재설정 또는 변경할 수 없는 경우 즉시 사용자에게 알려줍니다.
* **액세스 패널 및 Office 365에서 암호 변경 지원**: 페더레이션 또는 암호 해시 동기화된 사용자가 만료되었거나 만료되지 않은 암호를 변경하면 해당 암호는 AD DS에 쓰기 저장됩니다.
* **Azure Portal에서 관리자가 암호를 재설정할 때 비밀번호 쓰기 저장 지원**: 사용자가 페더레이션 또는 암호 해시 동기화된 경우 관리자가 [Azure Portal](https://portal.azure.com)에서 해당 사용자의 암호를 재설정하면 암호가 온-프레미스에 쓰기 저장됩니다. 이 기능은 현재 Office 관리자 포털에서 지원되지 않습니다.
* **인바운드 방화벽 규칙이 필요하지 않음**: 비밀번호 쓰기 저장은 Azure Service Bus Relay를 기본 통신 채널로 사용합니다. 모든 통신은 포트 443을 통해 아웃바운드됩니다.

> [!NOTE]
> 온-프레미스 AD의 보호 그룹 내에 있는 관리자 계정은 비밀번호 쓰기 저장에 사용할 수 없습니다. 관리자는 클라우드에서 암호를 변경할 수는 있지만, 암호 재설정을 사용하여 잊어버린 암호를 재설정할 수는 없습니다. 보호 된 그룹에 대 한 자세한 내용은 [AD DS 보호 된 계정 및 그룹](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)을 참조 하세요.

SSPR 쓰기 저장을 시작하려면 다음 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정(SSPR) 쓰기 저장 사용](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>암호 쓰기 저장의 작동 원리

페더레이션 또는 암호 해시 동기화된 사용자가 클라우드에서 자신의 암호를 재설정하거나 변경하려고 시도하면 다음 작업이 수행됩니다.

1. 사용자가 어떤 종류의 암호를 가지고 있는지 확인하기 위한 검사가 수행됩니다. 암호가 온-프레미스에서 관리되는 경우:
   * 쓰기 저장 서비스를 사용 중인지 확인하기 위한 검사가 수행됩니다. 사용 중인 경우 사용자가 계속 진행할 수 있습니다.
   * 쓰기 저장 서비스가 중지된 경우 사용자에게 지금은 암호를 재설정할 수 없다고 알려줍니다.
1. 다음으로, 사용자는 적절한 인증 게이트를 통과하여 **암호 재설정** 페이지로 이동됩니다.
1. 사용자는 새 암호를 선택하고 다시 확인합니다.
1. 사용자가 **제출**을 선택하면 쓰기 저장 설정 프로세스에서 만든 대칭 키로 일반 텍스트 암호가 암호화됩니다.
1. 암호화된 암호는 HTTPS 채널을 통해 테넌트별 Service Bus Relay(쓰기 저장 설정 프로세스에서 자동으로 설정됨)로 전송되는 페이로드에 포함됩니다. 이 릴레이는 온-프레미스 설치만 알고 있는 임의로 생성된 암호에 의해 보호됩니다.
1. 메시지가 서비스 버스에 도달하면 암호 재설정 엔드포인트가 자동으로 절전 모드에서 해제되어 보류 중인 재설정 요청이 있는지 확인합니다.
1. 그런 다음, 서비스에서 클라우드 앵커 특성을 사용하여 해당 사용자를 찾습니다. 이 조회에 성공하려면 다음 조건을 충족해야 합니다.

   * 사용자 개체가 AD DS 커넥터 공간에 있어야 합니다.
   * 사용자 개체가 해당 MV(metaverse) 개체에 연결되어야 합니다.
   * 사용자 개체는 해당 하는 Azure AD 커넥터 개체에 연결 되어야 합니다.
   * AD DS connector 개체에서 MV로의 링크에는 링크에 대 한 동기화 규칙이 있어야 합니다 `Microsoft.InfromADUserAccountEnabled.xxx` .

   클라우드에서 호출이 들어오면 동기화 엔진이 **Cloudanchor** 특성을 사용 하 여 Azure AD 커넥터 공간 개체를 조회 합니다. 그런 다음, MV 개체에 대 한 링크를 팔 로우 한 다음 AD DS 개체로 다시 연결 합니다. 동일한 사용자에 대해 여러 개의 AD DS 개체 (다중 포리스트)가 있을 수 있기 때문에 동기화 엔진은 링크에 의존 하 여 올바른 개체를 `Microsoft.InfromADUserAccountEnabled.xxx` 선택 합니다.

1. 사용자 계정이 검색 되 면 적절 한 AD DS 포리스트에서 직접 암호를 다시 설정 하려고 시도 합니다.
1. 암호 설정 작업이 성공하면 사용자에게 암호가 변경되었음을 알려줍니다.

   > [!NOTE]
   > 사용자의 암호 해시가 암호 해시 동기화를 통해 Azure AD에 동기화되는 경우 온-프레미스 암호 정책이 클라우드 암호 정책보다 약할 가능성이 있습니다. 이 경우 온-프레미스 정책이 적용됩니다. 이 정책을 사용하면 Single Sign-On을 제공하기 위해 암호 해시 동기화를 사용하든 아니면 페더레이션을 사용하든, 온-프레미스 정책이 클라우드에서 적용됩니다.

1. 암호 설정 작업이 실패하면 다시 시도하라는 오류 메시지가 표시됩니다. 작업은 다음과 같은 이유로 실패할 수 있습니다.
    * 서비스가 종료되었습니다.
    * 선택한 암호가 조직 정책을 준수하지 않습니다.
    * 로컬 AD DS 환경에서 사용자를 찾을 수 없습니다.

   오류 메시지는 관리자 개입 없이 문제 해결을 시도할 수 있도록 사용자에게 지침을 제공합니다.

## <a name="password-writeback-security"></a>비밀번호 쓰기 저장 보안

비밀번호 쓰기 저장은 매우 안전한 서비스입니다. 사용자 정보를 보호하기 위해 다음과 같은 4계층 보안 모델이 사용됩니다.

* **테넌트별 Service Bus Relay**
   * 사용자가 이 서비스를 설정하면 임의로 생성된 강력한 암호에 의해 보호되는 테넌트별 Service Bus Relay가 설정되며, Microsoft는 절대로 이 암호에 액세스할 수 없습니다.
* **잠겨 있는 강력한 암호 암호화 키**
   * Service Bus Relay를 만든 후 회선을 통해 도착하는 암호를 암호화하는 데 사용되는 강력한 대칭 키가 만들어집니다. 이 키는 클라우드의 회사의 암호 저장소에만 존재하며 디렉터리의 다른 암호와 마찬가지로 강력하게 잠겨 있고 감사됩니다.
* **업계 표준 TLS(전송 계층 보안)**
   1. 클라우드에서 암호 재설정 또는 변경 작업이 발생하면 공개 키를 사용하여 일반 텍스트 암호가 암호화됩니다.
   1. 암호화된 암호는 Microsoft TLS/SSL 인증서를 사용하여 암호화된 채널을 통해 Service Bus Relay로 전송되는 HTTPS 메시지에 배치됩니다.
   1. Service Bus에 메시지가 도착한 후에 온-프레미스 에이전트가 시작하고 이전에 생성된 강력한 암호를 사용하여 Service Bus에 인증합니다.
   1. 온-프레미스 에이전트는 암호화된 메시지를 선택하고 프라이빗 키를 사용하여 암호를 해독합니다.
   1. 온-프레미스 에이전트는 AD DS SetPassword API를 통해 암호를 설정하려고 합니다. 이 단계에서는 클라우드에서 AD DS 온-프레미스 암호 정책 (예: 복잡성, 연령, 기록, 필터 등)을 적용할 수 있습니다.
* **메시지 만료 정책**
   * 온-프레미스 서비스가 종료되었기 때문에 메시지가 Service Bus에 남아 있는 경우 시간이 초과되고 몇 분 후에 제거됩니다. 메시지의 시간 초과 및 제거를 통해 메시지 보안이 더욱 강화됩니다.

### <a name="password-writeback-encryption-details"></a>비밀번호 쓰기 저장 암호화 세부 정보

사용자가 암호 재설정을 제출한 후 재설정 요청은 여러 암호화 단계를 거친 후 온-프레미스 환경에 도달합니다. 이러한 암호화 단계는 최대의 서비스 안정성과 보안을 보장합니다. 다음과 같이 설명할 수 있습니다.

1. **2048비트 RSA 키를 사용한 암호 암호화**: 사용자가 온-프레미스에 다시 작성된 암호를 제출하면 제출된 암호 자체를 2048비트 RSA 키로 암호화합니다.
1. **AES-GCM을 사용한 패키지 수준 암호화**: AES-GCM을 사용하여 전체 패키지(암호 + 필수 메타데이터)를 암호화합니다. 이 암호화는 기본 Service Bus 채널에 직접 액세스할 수 있는 사람이 콘텐츠를 보거나 변조 하는 것을 방지 합니다.
1. **모든 통신은 TLS/SSL을 통해 발생**합니다. Service Bus와의 모든 통신은 SSL/TLS 채널에서 발생 합니다. 이 암호화는 권한이 없는 제3자의 콘텐츠를 보호합니다.
1. **6 개월 마다 자동 키 롤오버**: 모든 키는 6 개월 마다 또는 비밀 번호 쓰기 저장을 사용 하지 않도록 설정한 후 Azure AD Connect에서 다시 사용 하도록 설정 하 여 최대 서비스 보안 및 안전을 보장 합니다.

### <a name="password-writeback-bandwidth-usage"></a>비밀번호 쓰기 저장 대역폭 사용

비밀번호 쓰기 저장은 다음과 같은 경우에만 온-프레미스 에이전트로 요청을 다시 보내는 낮은 대역폭 서비스입니다.

* Azure AD Connect를 통해 이 기능이 사용되거나 사용되지 않도록 설정될 때 2개의 메시지가 전송됩니다.
* 서비스가 실행되는 동안 한 메시지를 서비스 하트 비트로 5분마다 한 번씩 전송합니다.
* 두 개의 메시지는 새 암호를 전송할 때 각각 전송됩니다.
   * 첫 번째 메시지는 작업을 수행하는 요청입니다.
   * 두 번째 메시지는 작업의 결과를 포함하고 다음과 같은 경우에 전송됩니다.
      * 사용자 셀프 서비스 비밀번호 다시 설정 중에 새 비밀번호가 제출될 때마다
      * 사용자 비밀번호 변경 작업 중에 새 비밀번호가 제출될 때마다
      * 관리자가 시작한 사용자 비밀번호 재설정 중에 새 비밀번호가 제출될 때마다(Azure 관리 포털에서만)

#### <a name="message-size-and-bandwidth-considerations"></a>메시지 크기 및 대역폭 고려 사항

앞에서 설명한 각 메시지의 일반적으로 1KB 미만입니다. 극단적인 부하에서도 비밀번호 쓰기 저장 서비스 자체에서 초당 몇 Kb의 대역폭을 소비합니다. 각 메시지가 비밀번호 업데이트 작업에서 요구할 때만 실시간으로 전송되고 메시지 크기가 너무 작기 때문에 쓰기 저장 기능의 대역폭 사용은 실제 측정 가능한 영향을 미치기에는 너무 작습니다.

## <a name="supported-writeback-operations"></a>지원되는 쓰기 저장 작업

암호는 다음과 같은 경우에 모두 다시 기록됩니다.

* **지원되는 최종 사용자 작업**
   * 모든 최종 사용자 셀프 서비스 자발적 암호 변경 작업
   * 모든 최종 사용자 셀프 서비스 강제 암호 변경 작업(예: 암호 만료)
   * [암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 시작되는 모든 최종 사용자 셀프 서비스 암호 재설정

* **지원되는 관리자 작업**
   * 모든 관리자 셀프 서비스 자발적 암호 변경 작업
   * 모든 관리자 셀프 서비스 강제 암호 변경 작업(예: 암호 만료)
   * [암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 시작되는 모든 관리자 셀프 서비스 암호 재설정
   * [Azure Portal](https://portal.azure.com)에서 관리자 시작 최종 사용자 암호 재설정
   * [Microsoft Graph API 베타](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta)에서 관리자 시작 최종 사용자 암호 재설정

## <a name="unsupported-writeback-operations"></a>지원되지 않는 쓰기 저장 작업

암호는 다음과 같은 경우에 쓰기 저장되지 않습니다.

* **지원되지 않는 최종 사용자 작업**
   * PowerShell 버전 1, 버전 2 또는 Microsoft Graph API를 사용하여 자신의 암호를 재설정하는 최종 사용자
* **지원되지 않는 관리자 작업**
   * PowerShell 버전 1, 버전 2 또는 Microsoft Graph API에서 관리자 시작 최종 사용자 암호 재설정([Microsoft Graph API 베타](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) 지원됨)
   * [Microsoft 365 관리 센터](https://admin.microsoft.com)에서 관리자 시작 최종 사용자 암호 재설정
   * 모든 관리자는 암호 재설정 도구를 사용하여 암호 쓰기 저장에 대한 암호를 재설정할 수 없음

> [!WARNING]
> Active Directory 사용자 및 컴퓨터 또는 Active Directory 관리 센터 같은 온-프레미스 AD DS 관리 도구에 있는 "다음 로그온 시 사용자가 반드시 암호를 변경해야 함" 확인란의 사용은 Azure AD Connect의 미리 보기 기능으로 지원됩니다. 자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](../hybrid/how-to-connect-password-hash-synchronization.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

SSPR 쓰기 저장을 시작하려면 다음 자습서를 완료하세요.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정(SSPR) 쓰기 저장 사용](./tutorial-enable-sspr-writeback.md)