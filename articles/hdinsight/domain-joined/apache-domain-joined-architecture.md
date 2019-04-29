---
title: Enterprise Security Package를 사용하는 Azure HDInsight 아키텍처
description: Enterprise Security Package를 사용하여 HDInsight 보안을 계획하는 방법을 알아봅니다.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f81aa1e493014f192dc11ca698e9793075ac26ce
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128333"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>HDInsight에서 Enterprise Security Package 사용

표준 Azure HDInsight 클러스터는 단일 사용자 클러스터입니다. 대량 데이터 워크로드를 구축하는 작은 애플리케이션 팀이 있는 대부분의 회사에 적합합니다. 각 사용자는 요청 시 전용 클러스터를 만들고 더 이상 필요하지 않은 경우, 삭제할 수 있습니다. 

대부분의 기업이 클러스터를 IT 팀에서 관리하고 여러 애플리케이션 팀이 클러스터를 공유하는 모델로 이동했습니다. 이러한 대기업은 Azure HDInsight의 각 클러스터에 대한 다중 사용자 액세스 권한이 필요합니다.

HDInsight는 널리 사용되는 ID 공급자인 Active Directory를 관리되는 방식으로 사용합니다. [Azure AD DS(Azure Active Directory Domain Services)](../../active-directory-domain-services/active-directory-ds-overview.md)와 HDInsight를 통합하고 도메인 자격 증명을 사용하여 클러스터에 액세스할 수 있습니다. 

HDInsight의 VM(가상 머신)은 제공된 도메인에 가입된 도메인입니다. 따라서 HDInsight에서 실행 중인 모든 서비스(Apache Ambari, Apache Hive 서버, Apache Ranger, Apache Spark thrift 서버 및 기타)가 인증된 사용자에 대해 원활하게 작동합니다. 관리자는 Apache Ranger를 사용하여 클러스터의 리소스에 대한 역할 기반 액세스 제어를 제공하는 강력한 권한 부여 정책을 만들 수 있습니다.

## <a name="integrate-hdinsight-with-active-directory"></a>Active Directory와 HDInsight 통합

오픈 소스 Apache Hadoop은 인증 및 보안에 Kerberos 프로토콜을 사용합니다. 따라서 ESP(Enterprise Security Package)가 포함된 HDInsight 클러스터 노드는 Azure AD DS에서 관리하는 도메인에 조인됩니다. Kerberos 보안은 클러스터의 Hadoop 구성 요소에 대해 구성됩니다. 

다음 작업은 자동으로 만들어집니다.

- 각 Hadoop 구성 요소에 대한 서비스 주체
- 또한 도메인에 가입된 각 머신에 대한 머신 보안 주체
- 이러한 서비스 및 머신 보안 주체를 저장하는 각 클러스터에 대한 OU(조직 구성 단위)

요약하면 다음으로 환경을 설정해야 합니다.

- Active Directory 도메인(Azure AD DS에서 관리함)
- Azure AD DS에서 사용하도록 설정된 보안 LDAP(LDAPS)
- 가상 네트워크를 별도로 선택하는 경우, HDInsight 가상 네트워크에서 Azure AD DS 가상 네트워크로 적절한 네트워킹 연결 HDInsight 가상 네트워크 내의 VM은 가상 네트워크 피어링을 통해 Azure AD DS에 연결되어 있어야 합니다. HDInsight 및 Azure AD DS가 동일한 가상 네트워크에 배포된 경우, 연결이 자동으로 제공되며 추가 작업이 필요하지 않습니다.

## <a name="set-up-different-domain-controllers"></a>다른 도메인 컨트롤러 설정
HDInsight는 현재 클러스터가 Kerberos 통신에 사용하는 주 도메인 컨트롤러로 Azure AD DS만 지원합니다. 그러나 이러한 설정을 통해 HDInsight 액세스에 Azure AD DS를 사용하도록 설정하면 다른 복잡한 Active Directory 설정이 가능합니다.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md)는 관리되는 도메인을 제공합니다. 이 도메인은 Windows Server Active Directory와 완벽하게 호환됩니다. Microsoft는 HA(고가용성) 설정에서 도메인을 관리하고, 패치하고, 모니터링하는 작업에 주의를 기울입니다. 도메인 컨트롤러를 유지 관리할 걱정 없이 클러스터를 배포할 수 있습니다. 

사용자, 그룹 및 암호는 Azure AD에서 동기화됩니다. Azure AD 인스턴스에서 Azure AD DS로 단방향 동기화를 사용하면 사용자가 동일한 회사 자격 증명을 통해 클러스터에 로그인할 수 있습니다. 

자세한 내용은 [Azure AD DS를 사용하여 ESP가 포함된 HDInsight 클러스터 구성](./apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>온-프레미스 Active Directory 또는 IaaS VM의 Active Directory

도메인에 대한 온-프레미스 Active Directory 인스턴스 또는 더 복잡한 Active Directory 설정이 있는 경우, Azure AD Connect를 사용하여 해당 ID를 Azure AD에 동기화할 수 있습니다. 그런 다음, 해당 Active Directory 테넌트에서 Azure AD DS를 사용하도록 설정할 수 있습니다. 

Kerberos가 암호 해시를 사용하므로 [Azure AD DS에서 암호 해시 동기화를 사용하도록 설정](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)해야 합니다. 

AD FS(Active Directory Federation Services)를 통한 페더레이션을 사용하는 경우 암호 해시 동기화를 사용하도록 설정해야 합니다. (권장되는 설정은 [이 비디오](https://youtu.be/qQruArbu2Ew)를 참조하세요.) 암호 해시 동기화는 AD FS 인프라에 실패한 경우 재해 복구에 유용하며 유출된 자격 증명 보호 기능도 제공합니다. 자세한 내용은 [Azure AD Connect에서 암호 해시 동기화 사용](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)을 참조하세요. 

Azure AD 및 Azure AD DS 없이 온-프레미스 Active Directory 또는 IaaS VM의 Active Directory만 사용하는 방식은 ESP가 포함된 HDInsight 클러스터에 대해 지원되는 구성이 아닙니다.

페더레이션을 사용 중이고 암호 해시가 올바르게 동기화되었지만 인증 오류가 발생하는 경우에는 PowerShell 서비스 주체에 대해 클라우드 암호 인증이 활성화되어 있는지 확인합니다. 그렇지 않은 경우 Azure AD 테넌트에 대해 [HRD(홈 영역 검색) 정책](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)을 설정해야 합니다. HRD 정책을 확인하고 설정하려면:

1. Azure AD PowerShell 모듈을 설치합니다.

   ```
   Install-Module AzureAD
   ```

2. 글로벌 관리자(테넌트 관리자) 자격 증명을 사용하여 `Connect-AzureAD`를 입력합니다.

3. Microsoft Azure PowerShell 서비스 주체가 이미 만들어졌는지 확인합니다.

   ```
   $powershellSPN = Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. 존재하지 않는 경우(즉, `($powershellSPN -eq $null)`인 경우) 서비스 주체를 만듭니다.

   ```
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. 정책을 만들고 이 서비스 주체에 연결합니다.

   ```
   $policy = New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuth -Type HomeRealmDiscoveryPolicy

   Add-AzureADServicePrincipalPolicy -Id $powershellSPN.ObjectId -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>다음 단계

* [ESP가 포함된 HDInsight 클러스터 구성](apache-domain-joined-configure-using-azure-adds.md)
* [ESP가 포함된 HDInsight 클러스터용 Apache Hive 정책 구성](apache-domain-joined-run-hive.md)
* [ESP가 포함된 HDInsight 클러스터 관리](apache-domain-joined-manage.md) 
