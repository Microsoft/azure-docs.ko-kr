---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c44b39effdc6d8fcdc144915ec7b51489e3798cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405433"
---
실행하는 애플리케이션에 대해 VM을 안전하게 유지하는 것은 중요합니다. VM 보안은 VM에 대한 보안 액세스 및 데이터의 보안 저장소를 포함하는 하나 이상의 Azure 서비스 및 기능을 포함할 수 있습니다. 이 문서에서는 VM 및 애플리케이션을 안전하게 유지할 수 있도록 하는 정보를 제공합니다.

## <a name="antimalware"></a>맬웨어 방지

클라우드 환경에 대한 최신 위협 환경은 동적이며 규정 준수 및 보안 요구 사항을 충족하기 위해 압력을 늘려서 효과적으로 보호를 유지합니다. [Azure를 위한 Microsoft 맬웨어 방지 프로그램](../articles/security/azure-security-antimalware.md)은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 무료 실시간 보호 기능입니다. 알려진 악성 또는 원치 않는 소프트웨어가 VM에서 스스로의 설치나 실행을 시도하는 경우 알리도록 경고를 구성할 수 있습니다.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md)를 통해 VM에 대한 위협을 예방하고 감지하며 대응할 수 있습니다. Security Center는 Azure 구독을 통해 통합된 보안 모니터링 및 정책 관리를 제공하고, 달리 발견되지 않을 수도 있는 위협을 검색하는 데 도움이 되며, 보안 솔루션의 광범위한 에코시스템에서 작동합니다.

Security Center의 Just-In-Time 액세스를 VM 배포에 적용하면 Azure VM으로의 인바운드 트래픽을 잠글 수 있으므로 공격에 대한 노출은 줄이면서 필요할 때는 VM 연결을 위한 손쉬운 액세스 기능을 제공할 수 있습니다. Just-In-Time을 사용하도록 설정한 상태에서 사용자가 VM 액세스를 요청하면 Security Center에서는 VM에 대한 사용자 권한을 확인합니다. 사용자에게 올바른 권한이 있으면 요청이 승인되며, Security Center는 제한된 시간 동안 선택한 포트로의 인바운드 트래픽을 허용하도록 NSG(네트워크 보안 그룹)를 자동으로 구성합니다. 시간이 만료되면 Security Center에서 NSG를 이전 상태로 복원합니다. 

## <a name="encryption"></a>암호화

[Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) 및 [Linux VM](../articles/virtual-machines/linux/encrypt-disks.md)의 보안과 규정 준수 상태를 향상시키기 위해 Azure에서 가상 디스크를 암호화할 수 있습니다. Windows VM의 가상 디스크는 미사용 시 BitLocker를 사용하여 암호화됩니다. Linux VM의 가상 디스크는 미사용 시 dm-crypt를 사용하여 암호화됩니다. 

Azure에서 가상 디스크 암호화는 무료입니다. 암호화 키는 소프트웨어 보호를 사용하여 Azure Key Vault에 저장되거나 FIPS 140-2 레벨 2 표준 인증 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이러한 암호화 키는 VM에 연결된 가상 디스크를 암호화하고 암호를 해독하는 데 사용됩니다. 이러한 암호화 키에 대한 제어를 유지하고 그 사용을 감사할 수 있습니다. Azure Active Directory 서비스 사용자는 VM이 켜지고 꺼지는 경우 이러한 암호화 키 발급을 위한 보안 메커니즘을 제공합니다.

## <a name="key-vault-and-ssh-keys"></a>Key Vault 및 SSH 키

비밀 및 인증서는 리소스로 모델링되고 [Key Vault](../articles/key-vault/key-vault-whatis.md)로 제공될 수 있습니다. Azure PowerShell을 사용하여 [Windows VM](../articles/virtual-machines/windows/key-vault-setup.md)에 대한 키 자격 증명 모음 및 [Linux VM](../articles/virtual-machines/linux/key-vault-setup.md)에 대한 Azure CLI를 만들 수 있습니다. 암호화에 대한 키를 만들 수도 있습니다.

키 자격 증명 모음 액세스 정책은 키, 비밀 및 인증서에 대한 권한을 별도로 부여합니다. 예를 들어 사용자에게 키에 대한 액세스 권한만 부여하고 암호에 대해서는 권한을 부여하지 않을 수 있습니다. 그러나 키, 암호 또는 인증서에 대한 액세스 권한은 자격 증명 모음 수준에 있습니다. 즉 [키 자격 증명 모음 액세스 정책](../articles/key-vault/key-vault-secure-your-key-vault.md)에서는 개체 수준 권한을 지원하지 않습니다.

VM에 연결할 때 public-key cryptography를 사용하면 더욱 안전한 방법으로 VM에 로그인할 수 있습니다. 이 프로세스는 사용자를 인증하는 데 있어 사용자 이름과 암호가 아니라 SSH(보안 셸) 명령을 사용하는 공용 및 개인 키의 교환과 관계됩니다. 암호는 무차별 암호 대입 공격, 특히 웹 서버와 같은 인터넷 연결 VM에 대한 공격에 취약합니다. SSH(보안 셸) 키 쌍을 사용하면 인증을 위해 SSH 키를 사용하는 [Linux VM](../articles/virtual-machines/linux/mac-create-ssh-keys.md)을 만들 수 있으므로 로그인할 때 암호를 사용할 필요가 없습니다. SSH 키를 사용하여 [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md)에서 Linux VM에 연결할 수도 있습니다.

## <a name="managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID

클라우드 애플리케이션을 빌드할 때 일반적으로 직면하는 난관 중 하나는 코드에서 클라우드 서비스에 인증하는 데 사용되는 자격 증명을 관리하는 방법입니다. 자격 증명을 안전하게 보호하는 것이 중요합니다. 자격 증명이 개발자 워크스테이션에 표시되지 않고 소스 제거에 체크 인되지 않는 것이 가장 좋습니다. Azure Key Vault를 사용하면 자격 증명, 비밀 및 기타 키를 안전하게 저장할 수 있지만, 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. 

Azure AD(Azure Active Directory)의 Azure 리소스에 대한 관리 ID 기능은 이 문제를 해결합니다. 이 기능은 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다.  VM에서 실행되는 코드는 VM 내에서만 액세스할 수 있는 두 엔드포인트에서 토큰을 요청할 수 있습니다. 이 서비스에 대한 자세한 내용은 [Azure 리소스의 관리 ID](../articles/active-directory/managed-identities-azure-resources/overview.md) 개요 페이지를 참조하세요.   

## <a name="policies"></a>정책

[Azure 정책](../articles/azure-policy/azure-policy-introduction.md)을 사용하여 조직의 [Windows VM](../articles/virtual-machines/windows/policy.md) 및 [Linux VM](../articles/virtual-machines/linux/policy.md)에 대해 원하는 동작을 정의할 수 있습니다. 조직은 정책을 사용하여 엔터프라이즈 전체에 다양한 규칙을 적용할 수 있습니다. 원하는 동작을 적용하여 조직의 성공에 기여함과 동시에 위험을 완화할 수 있습니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

[RBAC(역할 기반 액세스 제어)](../articles/role-based-access-control/overview.md)를 사용하면 팀 내에서 업무를 분리하고 VM에서 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. VM에서 모든 사람에게 무제한 권한을 제공하는 대신 특정 작업만 허용할 수 있습니다. [Azure CLI](https://docs.microsoft.com/cli/azure/role) 또는[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md)을 사용하여 [Azure Portal](../articles/role-based-access-control/role-assignments-portal.md)에서 VM에 대한 액세스 제어를 구성할 수 있습니다.


## <a name="next-steps"></a>다음 단계
- [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) 또는 [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md)용 Azure Security Center를 사용하여 가상 컴퓨터 보안을 모니터링하는 단계를 안내합니다.