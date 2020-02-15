---
title: Azure AD Domain Services에 대 한 일반적인 배포 시나리오 Microsoft Docs
description: 가치를 제공 하 고 비즈니스 요구를 충족 하는 Azure Active Directory Domain Services에 대 한 일반적인 시나리오 및 사용 사례에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 5d0035e7c87806012d13117ae5335b7de5f3c99d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132294"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대 한 일반적인 사용 사례 및 시나리오

Azure Active Directory Domain Services (Azure AD DS)는 도메인 가입, 그룹 정책, LDAP (lightweight directory access protocol), Kerberos/NTLM 인증 등의 관리 되는 도메인 서비스를 제공 합니다. Azure AD DS는 기존 Azure AD 테넌트와 통합되므로 사용자가 기존 자격 증명을 사용하여 로그인할 수 있습니다. 클라우드에서 도메인 컨트롤러를 배포, 관리 및 패치 하지 않고도 이러한 도메인 서비스를 사용할 수 있습니다 .이는 온-프레미스 리소스를 Azure로 원활 하 게 리프트 앤 시프트 하는 기능을 제공 합니다.

이 문서에서는 Azure AD DS 가치를 제공 하 고 이러한 요구를 충족 하는 일반적인 비즈니스 시나리오를 간략하게 설명 합니다.

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure virtual machines의 보안 관리

단일 AD 자격 증명 집합을 사용할 수 있도록 Azure Vm (가상 머신)을 Azure AD DS 관리 되는 도메인에 조인할 수 있습니다. 이 방법은 각 VM에서 로컬 관리자 계정 유지 관리, 환경 간의 별도 계정 및 암호와 같은 자격 증명 관리 문제를 줄입니다.

Azure AD DS 관리 되는 도메인에 가입 된 Vm은 그룹 정책을 사용 하 여 관리 하 고 보호할 수도 있습니다. 필요한 보안 기준을 Vm에 적용 하 여 회사 보안 지침에 따라 잠글 수 있습니다. 예를 들어 그룹 정책 관리 기능을 사용 하 여 VM에서 실행할 수 있는 응용 프로그램의 유형을 제한할 수 있습니다.

![Azure 가상 머신의 간소화된 관리](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

일반적인 예제 시나리오를 살펴보겠습니다. 서버 및 기타 인프라가 수명 종료에 도달 하면 Contoso는 현재 온-프레미스에서 호스트 되는 응용 프로그램을 클라우드로 이동 하려고 합니다. 현재 IT 표준에 따라 회사 응용 프로그램을 호스트 하는 서버는 그룹 정책을 사용 하 여 도메인에 가입 되 고 관리 되어야 합니다. Contoso의 IT 관리자는 사용자가 회사 자격 증명을 사용 하 여 로그인 할 수 있으므로 관리를 용이 하 게 하기 위해 Azure에 배포 된 Vm에 도메인 가입을 선호 합니다. 도메인에 가입 하는 경우 Gpo (그룹 정책 개체)를 사용 하 여 필요한 보안 기준을 준수 하도록 Vm을 구성할 수도 있습니다. Contoso는 Azure에서 자체 도메인 컨트롤러를 배포, 모니터링 및 관리 하는 것을 선호 합니다.

Azure AD DS는이 사용 사례에 적합 합니다. Azure AD DS 관리 되는 도메인을 사용 하 여 Vm을 도메인에 가입 시키고, 단일 자격 증명 집합을 사용 하 고, 그룹 정책을 적용할 수 있습니다. 관리 되는 도메인은 도메인 컨트롤러를 직접 구성 하 고 유지 관리할 필요가 없습니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용 되는 배포 고려 사항은 다음과 같습니다.

* Azure AD DS 관리 되는 도메인은 기본적으로 단일 플랫 OU (조직 구성 단위) 구조를 사용 합니다. 모든 도메인에 가입 된 Vm은 단일 OU에 있습니다. 원하는 경우 사용자 지정 Ou를 만들 수 있습니다.
* Azure AD DS는 각각 사용자 및 컴퓨터 컨테이너에 대해 기본 제공 GPO를 사용 합니다. 추가 제어를 위해 사용자 지정 Gpo를 만들고 사용자 지정 Ou를 대상으로 지정할 수 있습니다.
* Azure AD DS는 기본 AD 컴퓨터 개체 스키마를 지원 합니다. 컴퓨터 개체의 스키마를 확장할 수 없습니다.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP 바인딩 인증을 사용 하는 리프트 앤 시프트 온-프레미스 응용 프로그램

예를 들어 Contoso는 ISV가 지난 몇 년 전에 구매한 온-프레미스 응용 프로그램을 보유 하 고 있습니다. 응용 프로그램은 현재 ISV에 의해 유지 관리 모드에 있으며 응용 프로그램에 대 한 변경 요청은 매우 비용이 많이 듭니다. 이 응용 프로그램에는 웹 폼을 사용 하 여 사용자 자격 증명을 수집 하 고 온-프레미스 AD DS 환경에 대 한 LDAP 바인딩을 수행 하 여 사용자를 인증 하는 웹 기반 프런트 엔드가 있습니다.

![LDAP 바인딩](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso는이 응용 프로그램을 Azure로 마이그레이션해야 합니다. 응용 프로그램은 필요한 변경 없이 계속 해 서 그대로 작동 해야 합니다. 또한 사용자는 기존 회사 자격 증명을 사용 하 여 또는 추가 교육 없이 인증할 수 있습니다. 응용 프로그램이 실행 되는 최종 사용자에 게는 투명 해야 합니다.

이 시나리오에서 Azure AD DS는 응용 프로그램이 인증 프로세스의 일부로 LDAP 바인딩을 수행할 수 있도록 합니다. 레거시 온-프레미스 응용 프로그램은 Azure로 전환 하 여 구성 또는 사용자 환경을 변경 하지 않고 계속 해 서 사용자를 인증할 수 있습니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용 되는 배포 고려 사항은 다음과 같습니다.

* 응용 프로그램에서 디렉터리에 대 한 수정/쓰기가 필요 하지 않은지 확인 합니다. Azure AD DS 관리 되는 도메인에 대 한 LDAP 쓰기 액세스는 지원 되지 않습니다.
* Azure AD DS 관리 되는 도메인에 대해 직접 암호를 변경할 수 없습니다. 최종 사용자가 Azure AD의 셀프 서비스 암호 변경 메커니즘을 사용하거나 온-프레미스 디렉터리에 대해 암호를 바꿀 수 있습니다. 이러한 변경 내용은 자동으로 동기화 되며 Azure AD DS 관리 되는 도메인에서 사용할 수 있습니다.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>LDAP read를 사용 하 여 디렉터리에 액세스 하는 리프트 앤 시프트 온-프레미스 응용 프로그램

이전 예제 시나리오와 마찬가지로 Contoso에 거의 10 년 전에 개발 된 온-프레미스 LOB (기간 업무) 응용 프로그램이 있다고 가정해 보겠습니다. 이 응용 프로그램은 디렉터리를 인식 하며 LDAP를 사용 하 여 AD DS에서 사용자에 대 한 정보/특성을 읽도록 설계 되었습니다. 응용 프로그램은 특성을 수정 하거나 디렉터리에 쓰지 않습니다.

Contoso는이 응용 프로그램을 Azure로 마이그레이션하고 현재이 응용 프로그램을 호스트 하는 에이징 온-프레미스 하드웨어를 사용 중지 하려고 합니다. REST 기반 Microsoft Graph API와 같은 최신 디렉터리 Api를 사용 하도록 응용 프로그램을 다시 작성할 수 없습니다. 리프트 앤 시프트 옵션은 코드를 수정 하거나 응용 프로그램을 다시 작성 하지 않고 클라우드에서 실행 되도록 응용 프로그램을 마이그레이션할 수 있는 경우에 필요 합니다.

이 시나리오를 지원 하기 위해 Azure AD DS를 사용 하면 응용 프로그램에서 관리 되는 도메인에 대해 LDAP 읽기를 수행 하 여 필요한 특성 정보를 얻을 수 있습니다. 응용 프로그램은 다시 작성 하지 않아도 되므로 Azure로 리프트 앤 시프트를 사용 하면 사용자가 실행 위치에 변화가 없어도 앱을 계속 사용할 수 있습니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용 되는 배포 고려 사항은 다음과 같습니다.

* 응용 프로그램에서 디렉터리에 대 한 수정/쓰기가 필요 하지 않은지 확인 합니다. Azure AD DS 관리 되는 도메인에 대 한 LDAP 쓰기 액세스는 지원 되지 않습니다.
* 응용 프로그램에 사용자 지정/확장 Active Directory 스키마가 필요 하지 않은지 확인 합니다. Azure AD DS에서 스키마 확장이 지원 되지 않습니다.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>온-프레미스 서비스 또는 디먼 응용 프로그램을 Azure로 마이그레이션

일부 응용 프로그램에는 계층 중 하나가 데이터베이스와 같은 백 엔드 계층에 대해 인증 된 호출을 수행 해야 하는 여러 계층이 포함 되어 있습니다. AD 서비스 계정은 이러한 시나리오에서 일반적으로 사용 됩니다. 응용 프로그램을 Azure로 리프트 앤 시프트 하는 경우 Azure AD DS를 사용 하면 동일한 방식으로 서비스 계정을 계속 사용할 수 있습니다. 온-프레미스 디렉터리에서 Azure AD로 동기화 되는 동일한 서비스 계정을 사용 하도록 선택 하거나 사용자 지정 OU를 만든 다음 해당 OU에 별도의 서비스 계정을 만들 수 있습니다. 어느 방법을 사용 하 든 응용 프로그램은 다른 계층 및 서비스에 대해 인증 된 호출을 수행 하기 위해 계속 해 서 동일한 방식으로 작동 합니다.

![WIA를 사용하는 서비스 계정](./media/active-directory-domain-services-scenarios/wia-service-account.png)

이 예제 시나리오에서 Contoso에는 웹 프런트 엔드, SQL server 및 백 엔드 FTP 서버를 포함 하는 사용자가 작성 한 소프트웨어 자격 증명 모음 응용 프로그램이 있습니다. 서비스 계정을 사용 하는 Windows 통합 인증 FTP 서버에 대 한 웹 프런트 엔드를 인증 합니다. 웹 프런트 엔드는 서비스 계정으로 실행되도록 설정됩니다. 백 엔드 서버는 웹 프런트 엔드에 대한 서비스 계정에서 액세스 권한을 부여하도록 구성됩니다. Contoso는이 응용 프로그램을 Azure로 이동 하기 위해 클라우드에서 자신의 도메인 컨트롤러 Vm을 배포 하 고 관리 하지 않으려고 합니다.

이 시나리오에서는 웹 프런트 엔드, SQL server 및 FTP 서버를 호스트 하는 서버를 Azure Vm으로 마이그레이션하고 Azure AD DS 관리 되는 도메인에 조인할 수 있습니다. 그런 다음 Vm은 앱의 인증 목적으로 온-프레미스 디렉터리에 동일한 서비스 계정을 사용할 수 있습니다 .이는 Azure AD Connect을 사용 하 여 Azure AD를 통해 동기화 됩니다.

### <a name="deployment-notes"></a>배포 참고 사항

이 예제 사용 사례에 적용 되는 배포 고려 사항은 다음과 같습니다.

* 응용 프로그램에서 인증을 위해 사용자 이름과 암호를 사용 하는지 확인 합니다. Azure AD DS에서 인증서 또는 스마트 카드 기반 인증을 지원 하지 않습니다.
* Azure AD DS 관리 되는 도메인에 대해 직접 암호를 변경할 수 없습니다. 최종 사용자가 Azure AD의 셀프 서비스 암호 변경 메커니즘을 사용하거나 온-프레미스 디렉터리에 대해 암호를 바꿀 수 있습니다. 이러한 변경 내용은 자동으로 동기화 되며 Azure AD DS 관리 되는 도메인에서 사용할 수 있습니다.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure의 Windows Server 원격 데스크톱 서비스 배포

Azure AD DS를 사용 하 여 Azure에 배포 된 원격 데스크톱 서버에 관리 되는 도메인 서비스를 제공할 수 있습니다. 이 배포 시나리오에 대 한 자세한 내용은 [Azure AD Domain Services을 RDS 배포와 통합 하는 방법][windows-rds]을 참조 하세요.

## <a name="domain-joined-hdinsight-clusters-preview"></a>도메인에 가입 된 HDInsight 클러스터 (미리 보기)

Apache 레인저를 사용 하도록 설정 하 여 Azure AD DS 관리 되는 도메인에 가입 된 Azure HDInsight 클러스터를 설정할 수 있습니다. Apache 레인저를 통해 Hive 정책을 만들고 적용할 수 있으며, Excel 또는 Tableau와 같은 ODBC 기반 도구를 사용 하 여 데이터 과학자 같은 사용자가 Hive에 연결할 수 있습니다. 도메인에 가입 된 HDInsight에 HBase, Spark 및 스톰과 같은 다른 워크 로드를 추가 하는 작업을 계속 합니다.

이 배포 시나리오에 대 한 자세한 내용은 [도메인에 가입 된 HDInsight 클러스터를 구성 하는 방법][hdinsight] 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

시작 하려면 [Azure Active Directory Domain Services 인스턴스를 만들고 구성][tutorial-create-instance] 하세요.

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
