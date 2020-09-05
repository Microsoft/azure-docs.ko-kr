---
title: Azure Migrate를 사용하여 평가/마이그레이션을 위해 VMware VM 준비
description: Azure Migrate를 사용하여 VMware VM의 평가/마이그레이션을 준비하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8b812924c0922d460c631baec8b0e13a9f45cd76
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86109579"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>평가 후 Azure로 마이그레이션할 VMware VM 준비

이 문서는 [Azure Migrate](migrate-services-overview.md)를 사용하여 온-프레미스 VMware VM을 평가하고 Azure로 마이그레이션하기 위한 준비 과정을 도와줍니다.

이 자습서는 VMware VM을 평가하고 마이그레이션하는 방법을 보여 주는 시리즈의 두 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate를 사용할 수 있게 Azure를 준비합니다.
> * Azure Migrate:Server Assessment 도구를 사용하여 VMware VM을 평가하기 위한 준비를 합니다.
> * Azure Migrate:Server Migration 도구를 사용하여 VMware VM을 마이그레이션하기 위한 준비를 합니다. 

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 빠르게 개념을 증명하는 데 유용합니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prepare-azure"></a>Azure 준비

다음 표에는 Azure에서 완료해야 하는 작업이 요약되어 있습니다. 각 작업의 지침은 이 표를 따릅니다.

**Task** | **세부 정보** | **권한**
--- | --- | ---
**Azure Migrate 프로젝트 만들기** | Azure Migrate 프로젝트는 Azure Migrate 도구, Microsoft 도구 및 타사 제품을 사용하여 평가 및 마이그레이션을 오케스트레이션하고 관리하기 위한 중앙 위치를 제공합니다. | Azure 계정에는 프로젝트가 있는 리소스 그룹에 Contributor 또는 Owner 권한이 있어야 합니다.
**어플라이언스 등록** | Azure Migrate는 가벼운 Azure Migrate 어플라이언스를 사용하여 VM을 검색하고, Server Assessment 도구를 사용하여 VM을 평가하고, Server Migration 도구에서 에이전트 없는 마이그레이션을 사용하여 VM을 마이그레이션합니다. [등록](migrate-appliance-architecture.md#appliance-registration)에 대해 자세히 알아보세요. | 어플라이언스를 등록하려면 Azure 계정에 Azure 구독에 대한 Contributor 또는 Owner 권한이 있어야 합니다.
**Azure AD 앱 만들기** | 어플라이언스를 등록하면 Azure Migrate는 Azure AD(Azure Active Directory) 앱을 만듭니다. <br/><br/> - 첫 번째 앱은 어플라이언스에서 실행되는 에이전트와 Azure Migrate 간의 통신에 사용됩니다. <br/><br/> - 두 번째 앱은 에이전트 없는 VMware VM 마이그레이션을 위해 사용자의 구독에서 생성된 KeyVault에 액세스하는 데만 사용됩니다.   | Azure 계정에는 Azure AD 앱을 만들 수 있는 권한이 필요합니다.
**Key Vault 만들기** | 에이전트 없는 마이그레이션을 사용하여 VMware VM을 마이그레이션하기 위해, Azure Migrate에서는 구독의 복제 계정에 대한 액세스 키를 관리하는 Key Vault를 만듭니다. | Azure Migrate가 Key Vault를 만들 수 있도록, Azure Migrate 프로젝트가 있는 리소스 그룹에 대한 권한(Owner 또는 Contributor와 User Access Administrator)을 설정합니다.


### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있어야 합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Azure AD 앱을 만들 수 있는 권한 할당

어플라이언스를 등록하려면 Azure AD 앱을 만드는 권한이 Azure 계정에 필요합니다. 다음 방법 중 하나를 사용하여 권한을 할당합니다.

- **방법 1: 계정에 권한 부여**: 테넌트/전역 관리자는 테넌트의 사용자 계정에 Azure AD 앱을 만들고 등록할 수 있는 권한을 부여할 수 있습니다.
- **방법 2: 사용자 계정에 권한 있는 역할 할당**: 테넌트/전역 관리자는 애플리케이션 개발자 역할(필요한 권한을 가진)을 사용자 계정에 할당할 수 있습니다.

> [!NOTE]
> 이러한 권한은 새 어플라이언스를 등록할 때만 필요합니다. 어플라이언스가 설정되면 해당 권한을 제거할 수 있습니다.


#### <a name="method-1-grant-permissions-to-the-account"></a>방법 1: 계정에 권한 부여

다음과 같이 계정에 권한을 부여합니다.

1. 테넌트 또는 전역 관리자인지 확인합니다. 그런 다음, Azure AD에서 **Azure Active Directory** > **사용자** > **사용자 설정**으로 이동합니다.
2. **앱 등록**을 **예**로 설정합니다. 이것이 중요한 내용이 포함되지 않는 기본 설정입니다. [자세히 알아보기](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD 권한](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>방법 2 애플리케이션 개발자 역할 할당

또는 테넌트/글로벌 관리자가 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다. 역할 할당에 대해 [자세히 알아보세요](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

### <a name="assign-permissions-to-create-a-key-vault"></a>Key Vault를 만들 수 있는 권한 할당

Azure Migrate에서 Key Vault를 만들도록 설정하려면 다음과 같이 권한을 할당합니다.

1. Azure Portal의 리소스 그룹에서 **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.

    - 서버 평가를 실행하는 데는 **기여자** 권한으로 충분합니다.
    - 에이전트 없는 서버 마이그레이션을 실행하려면 **소유자**(또는 **기여자** 및 **사용자 액세스 관리자**) 권한이 있어야 합니다.

3. 필요한 권한이 없으면 리소스 그룹 소유자에게 요청합니다.

## <a name="prepare-for-assessment"></a>평가 준비

VMware VM 평가를 준비하려면 다음을 수행해야 합니다.

1. **VMware 설정 확인**. 마이그레이션하려는 vCenter Server 및 VM이 요구 사항을 충족하는지 확인합니다.
2. **평가에 대한 권한을 설정합니다**. Azure Migrate는 vCenter 계정을 사용하여 vCenter Server에 액세스하고, VM을 검색하여 평가합니다.
3. **어플라이언스 요구 사항 확인**. 다음 자습서에서 Azure Migrate 어플라이언스를 배포하기 전에 배포 요구 사항을 확인합니다.

### <a name="verify-vmware-settings"></a>VMware 설정 확인

1. 평가할 [VMware 요구 사항을 확인](migrate-support-matrix-vmware.md#vmware-requirements)합니다.
2. 필요한 포트가 vCenter Server에서 열려 있는지 [확인](migrate-support-matrix-vmware.md#port-access-requirements)합니다.
3. vCenter Server에서 계정에 OVA 파일을 사용하여 VM을 만들 수 있는 권한이 있는지 확인합니다. OVA 파일을 사용하여 Azure Migrate 어플라이언스를 VMware VM으로 배포할 때 필요합니다.
4. VM을 Azure로 마이그레이션하기 전에 VM에서 몇 가지 사항을 변경해야 합니다.

    - 일부 운영 체제의 경우 이러한 변경은 Azure Migrate에서 자동으로 수행합니다. 
    - 이러한 변경은 마이그레이션을 시작하기 전에 수행해야 합니다. 변경하기 전에 VM을 마이그레이션하면 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.
    - 필요한 [Windows](prepare-for-migration.md#windows-machines) 및 [Linux](prepare-for-migration.md#linux-machines) 변경 사항을 검토합니다.


### <a name="set-up-permissions-for-assessment"></a>평가 권한 설정

Azure Migrate 어플라이언스가 평가 및 에이전트 없는 마이그레이션에 사용할 VM을 검색하려면 Azure Migrate에서 vCenter Server에 액세스할 수 있어야 합니다. 다음과 같이 계정을 설정합니다.

1. vSphere 웹 클라이언트에서 **관리** > **액세스** > **SSO 사용자 및 그룹**을 엽니다.
2. **사용자**에서 **새 사용자** 아이콘을 클릭합니다.
3. 새로운 사용자 세부 정보를 입력합니다.
4. 테이블 값에 따라 권한을 선택합니다.

    **기능** | **계정 권한**
    --- | ---
    [VM 액세스](tutorial-assess-vmware.md) | 평가의 경우 계정에 읽기 전용 액세스 권한이 필요합니다.
    [VM 앱, 역할 및 기능 검색](how-to-discover-applications.md) | 앱 검색을 사용하려면 평가에 사용되는 읽기 전용 계정에는 **가상 머신** > **게스트 작업**에 대해 설정된 권한이 있어야 합니다.
    [(에이전트 없는) VM의 종속성 분석](how-to-create-group-machine-dependencies-agentless.md) | 종속성을 분석하려면 평가에 사용되는 읽기 전용 계정에는 **가상 머신** > **게스트 작업**에 대해 설정된 권한이 있어야 합니다.
    
> [!NOTE]
> 평가의 VM 검색을 특정 범위로 제한하려면 [이 문서](set-discovery-scope.md#assign-a-role-for-assessment)를 검토하세요.

### <a name="verify-appliance-settings-for-assessment"></a>평가를 위한 어플라이언스 설정 확인

[다음 자습서](tutorial-assess-vmware.md)에서는 Azure Migrate 어플라이언스를 설정하고 평가를 시작합니다. 그 전에, 다음과 같은 어플라이언스 요구 사항을 검토합니다. 

1. Azure Migrate 어플라이언스 배포 [요구 사항을 검토](migrate-appliance.md#appliance---vmware)합니다.
2. 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 Azure URL을 검토합니다.
3. 어플라이언스에서 사용하는 포트를 [적어 둡니다](migrate-support-matrix-vmware.md#port-access-requirements).
4. 어플라이언스가 검색 및 평가 중에 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.

## <a name="prepare-for-agentless-vmware-migration"></a>에이전트 없는 VMware 마이그레이션 준비

[에이전트 없는 마이그레이션 또는 에이전트 기반 마이그레이션](server-migrate-overview.md)을 사용하여 VMware VM을 마이그레이션할 수 있습니다. 이 섹션에서는 에이전트 없는 마이그레이션의 요구 사항을 요약합니다.

1. 에이전트 없는 마이그레이션을 사용할 것인지 [결정](server-migrate-overview.md#compare-migration-methods)합니다.
2. 마이그레이션하려는 머신의 하이퍼바이저 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)합니다.
3. 에이전트 없는 마이그레이션을 사용하여 마이그레이션하려는 VMware VM의 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)합니다.
4. 에이전트 없는 마이그레이션에 Azure Migrate 어플라이언스를 사용하기 위한 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless)합니다.
5. [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에 필요한 URL 액세스를 확인합니다.
6. [포트 액세스 ](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) 요구 사항을 검토합니다.
7. 다음 절차에 설명된 대로 에이전트 없는 마이그레이션을 위한 권한을 설정합니다.


### <a name="assign-permissions-to-an-account"></a>계정에 권한 할당

Azure Migrate 어플라이언스는 vCenter Server에 연결하여 에이전트 없는 마이그레이션을 사용하는 VM을 검색하고 마이그레이션합니다. 어플라이언스에 필요한 권한을 사용자 계정에 할당하거나, 권한을 가진 역할을 만들고 해당 역할을 사용자 계정에 할당할 수 있습니다.

1. vSphere 웹 클라이언트에서 **관리** > **액세스** > **SSO 사용자 및 그룹**을 엽니다.
2. **사용자**에서 **새 사용자** 아이콘을 클릭합니다.
3. 새로운 사용자 세부 정보를 입력합니다.
4. [다음 권한](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)을 할당합니다.

#### <a name="create-a-role-and-assign-to-an-account"></a>역할을 만들어 계정에 할당

또는 계정을 만들 수 있습니다. 그리고 다음과 같이 역할을 만들어서 계정에 할당합니다.

1. Sphere Web Client에 vCenter Server 관리자로 로그인합니다.
2. vCenter Server 인스턴스 > **역할 만들기**를 선택합니다.
3. 역할 이름(예: <em>Azure_Migrate</em>)을 지정하고, [필요한 권한](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)을 역할에 할당합니다.

    ![vCenter Server 계정 권한](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. 이제 계정을 만들고, 계정에 역할을 할당합니다.

> [!NOTE]
> 에이전트 없는 마이그레이션의 VM 검색을 특정 범위로 제한하려면 [이 문서](set-discovery-scope.md#assign-a-role-for-agentless-migration)를 검토하세요.



## <a name="prepare-for-agent-based-vmware-migration"></a>에이전트 기반 VMware 마이그레이션 준비

[에이전트 없는 마이그레이션 또는 에이전트 기반 마이그레이션](server-migrate-overview.md)을 사용하여 VMware VM을 마이그레이션할 수 있습니다. 이 섹션에서는 에이전트 기반 마이그레이션의 요구 사항을 요약합니다.

1. 에이전트 기반 마이그레이션을 사용할 것인지 [결정](server-migrate-overview.md#compare-migration-methods)합니다.
1. 마이그레이션하려는 머신의 하이퍼바이저 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)합니다.
2. 마이그레이션하려는 각 VM에 대한 모바일 서비스 설치를 포함하여 VMware VM 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)합니다.
3. 에이전트 기반 마이그레이션은 복제 어플라이언스를 사용합니다.
    - 복제 어플라이언스 배포 요구 사항을 [검토](migrate-replication-appliance.md#appliance-requirements)합니다.
    - MySQL을 어플라이언스에 설치하는 [옵션을 검토](migrate-replication-appliance.md#mysql-installation)합니다.
    - [퍼블릭](migrate-replication-appliance.md#url-access) 및 [정부](migrate-replication-appliance.md#azure-government-url-access) 클라우드에 필요한 URL 액세스를 확인합니다.
    - 복제 어플라이언스의 [포트 액세스](migrate-replication-appliance.md#port-access) 요구 사항을 검토합니다.
4. VM을 Azure로 마이그레이션하기 전에 VM에서 몇 가지 사항을 변경해야 합니다. 필요한 [Windows](prepare-for-migration.md#windows-machines) 및 [Linux](prepare-for-migration.md#linux-machines) 변경 사항을 검토합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure 사용 권한 설정
> * VMware 평가 및 마이그레이션 준비


Azure Migrate 프로젝트를 설정하고 Azure로 마이그레이션하기 위해 VMware VM을 평가하려면 두 번째 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [VMware VM 평가](./tutorial-assess-vmware.md)
