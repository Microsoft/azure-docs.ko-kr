---
title: Azure Migrate를 사용하여 평가/마이그레이션을 위해 Hyper-V VM 준비
description: Azure Migrate를 사용하여 Hyper-V VM의 평가/마이그레이션을 준비하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86109623"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>평가하고 Azure로 마이그레이션할 Hyper-V VM 준비

이 문서에서는 [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) 및 [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool)을 사용하여 온-프레미스 Hyper-V VM의 평가 및 Azure로 마이그레이션을 준비하는 데 도움이 됩니다.


이 자습서는 Hyper-V VM을 평가하고 Azure로 마이그레이션하는 방법을 보여 주는 시리즈의 첫 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate를 사용할 수 있게 Azure를 준비합니다.
> * Hyper-V VM을 평가하도록 준비합니다.
> * Hyper-V VM을 마이그레이션하도록 준비합니다. 

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prepare-azure"></a>Azure 준비

이 표에는 Azure에서 완료해야 하는 작업이 요약되어 있습니다. 지침은 표 아래에 제공됩니다.

**Task** | **세부 정보** | **권한**
--- | --- | ---
**Azure Migrate 프로젝트 만들기** | Azure Migrate 프로젝트는 Azure Migrate 도구, Microsoft 도구 및 타사 제품을 사용하여 평가 및 마이그레이션을 오케스트레이션하고 관리하기 위한 중앙 위치를 제공합니다. | Azure 계정에는 프로젝트가 있는 리소스 그룹에 Contributor 또는 Owner 권한이 있어야 합니다.
**어플라이언스 등록** | Azure Migrate는 경량 Azure Migrate 어플라이언스를 사용하여 Hyper-V VM을 검색하고 평가합니다. [자세히 알아보기](migrate-appliance-architecture.md#appliance-registration). | 어플라이언스를 등록하려면 Azure 계정에 Azure 구독에 대한 Contributor 또는 Owner 권한이 있어야 합니다.
**Azure AD 앱 만들기** | 어플라이언스를 등록할 때 Azure Migrate는 어플라이언스에서 실행되는 에이전트와 Azure Migrate 간의 통신에 사용되는 Azure AD(Azure Active Directory) 앱을 만듭니다. | Azure 계정에는 Azure AD 앱을 만들 수 있는 권한이 필요합니다.
**VM 만들기** | 리소스 그룹 및 가상 네트워크에서 VM을 만들고 Azure 관리 디스크에 쓸 수 있는 권한이 필요합니다. | Azure 계정에 Virtual Machine Contributor 역할이 필요합니다.


### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당

Azure Migrate 프로젝트를 만들 수 있는 권한이 있는지 확인합니다.

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있어야 합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Azure AD 앱을 만들 수 있는 권한 할당

다음 방법 중 하나를 사용하여 어플라이언스 등록 중에 Azure AD 앱을 만들 수 있는 Azure Migrate에 대한 사용 권한을 할당할 수 있습니다.

- 테넌트/글로벌 관리자는 Azure AD 앱을 만들고 등록할 수 있는 권한을 테넌트의 사용자에게 부여할 수 있습니다.
- 테넌트/글로벌 관리자는 권한이 있는 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다.

> [!NOTE]
> - 앱에는 위에서 설명한 구독에 대한 액세스 권한 이외의 다른 권한이 없습니다.
> - 이러한 권한은 새 어플라이언스를 등록할 때만 필요합니다. 어플라이언스가 설정되면 해당 권한을 제거할 수 있습니다.


#### <a name="grant-account-permissions"></a>계정 권한 부여

테넌트/글로벌 관리자는 다음과 같이 권한을 부여할 수 있습니다.

1. Azure AD에서 테넌트/글로벌 관리자는 **Azure Active Directory** > **사용자** > **사용자 설정**으로 이동해야 합니다.
2. 관리자는 **앱 등록**을 **예**로 설정해야 합니다.

    ![Azure AD 권한](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 이것이 중요한 내용이 포함되지 않는 기본 설정입니다. [자세히 알아보기](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>애플리케이션 개발자 역할 할당

테넌트/글로벌 관리자는 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다. [자세히 알아보기](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

### <a name="assign-azure-account-permissions"></a>Azure 계정 권한 할당

계정에 다음과 같은 권한을 갖도록 Virtual Machine Contributor 역할을 할당합니다.

- 선택한 리소스 그룹에 VM 만들기
- 선택한 가상 네트워크에 VM 만들기
- Azure 관리 디스크에 씁니다. 


### <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

[Azure 네트워크를 설정합니다](../virtual-network/manage-virtual-network.md#create-a-virtual-network). 온-프레미스 머신이 Azure 관리 디스크에 복제됩니다. 마이그레이션을 위해 Azure로 장애 조치하면 Azure VM이 이러한 관리 디스크에서 만들어지고, 설정한 Azure 네트워크에 조인됩니다.


## <a name="prepare-for-assessment"></a>평가 준비

VM 평가를 위해 Hyper-V를 수동으로 또는 구성 스크립트를 사용하여 준비할 수 있습니다. 준비 단계는 다음과 같습니다. 스크립트를 사용하여 준비하는 경우 자동으로 구성됩니다.

**Step** | **스크립트** | **수동**
--- | --- | ---
**Hyper-V 호스트 요구 사항 확인** | 스크립트가 호스트에서 지원되는 버전의 Hyper-V 및 Hyper-V 역할을 실행하는지 확인합니다.<br/><br/> WinRM 서비스를 사용하도록 설정하고, 호스트에서 5985(HTTP) 및 5986(HTTPS) 포트를 엽니다(메타데이터 수집에 필요함). | 서버 평가에 대한 [Hyper-V 호스트 요구 사항](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)을 확인합니다.<br/><br/> [필요한 포트](migrate-support-matrix-hyper-v.md#port-access)가 Hyper-V 호스트에서 열려 있는지 확인합니다.
**PowerShell 버전 확인** | 지원되는 PowerShell 버전에서 스크립트를 실행 중인지 확인합니다. | Hyper-V 호스트에서 PowerShell 버전 4.0 이상을 실행하고 있는지 확인합니다.
**계정 만들기** | 사용자(스크립트를 실행하는 사용자)에게 Hyper-V 호스트에 대한 관리자 권한이 있는지 확인합니다.<br/><br/>  Azure Migrate 서비스에서 Hyper-V 호스트와 통신하는 데 사용되는 로컬 사용자 계정(관리자 아님)을 만들 수 있습니다. 이 사용자 계정은 호스트의 다음 그룹에 추가됩니다.<br/><br/> - 원격 관리 사용자<br/><br/> - Hyper-V 관리자<br/><br/>- 성능 모니터 사용자 | Hyper-V 호스트/클러스터에 대한 관리자 권한이 있는 도메인 또는 로컬 사용자 계정을 설정합니다.<br/><br/> - 검색에 포함하려는 모든 호스트 및 클러스터에 대해 단일 계정이 필요합니다.<br/><br/> - 계정은 로컬 또는 도메인 계정일 수 있습니다. Hyper-V 호스트 또는 클러스터에 대한 관리자 권한이 있는 것이 좋습니다.<br/><br/> 또는 관리자 권한을 할당하지 않으려면 다음 권한이 필요합니다. 원격 관리 사용자, Hyper-V 관리자, 성능 모니터 사용자
**PowerShell 원격 사용** | Azure Migrate 어플라이언스에서 WinRM 연결을 통해 호스트에서 PowerShell 명령을 실행할 수 있도록 호스트에서 PowerShell 원격을 사용하도록 설정합니다.| 설정하려면 각 호스트에서 관리자 권한으로 PowerShell 콘솔을 열고, 다음 명령을 실행합니다.<br/><br/>``` Enable-PSRemoting -force ```
**Hyper-V 통합 서비스 설정** | 호스트에서 관리하는 모든 VM에 Hyper-V Integration Services를 사용하도록 설정되어 있는지 확인합니다. |  각 VM에서 [Hyper-V 통합 서비스를 사용하도록 설정](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)합니다.<br/><br/> Windows Server 2003을 실행하는 경우 [다음 지침을 따릅니다](prepare-windows-server-2003-migration.md).
**VM 디스크가 원격 SMB 공유에 있는 경우 자격 증명을 위임합니다.** | 스크립트는 자격 증명을 위임합니다. | [CredSSP를 사용하도록 설정](#enable-credssp-to-delegate-credentials)하여 자격 증명을 위임합니다.

### <a name="run-the-script"></a>스크립트 실행

스크립트를 다음과 같이 실행합니다.

1. PowerShell 버전 4.0 이상이 Hyper-V 호스트에 설치되어 있는지 확인합니다.
2. [Microsoft 다운로드 센터](https://aka.ms/migrate/script/hyperv)에서 스크립트를 다운로드합니다. 이 스크립트는 Microsoft에서 암호화 방식으로 서명합니다.
3. MD5 또는 SHA256 해시 파일을 사용하여 스크립트 무결성의 유효성을 검사합니다. 해시태그 값은 아래와 같습니다. 다음 명령을 실행하여 스크립트에 대한 해시를 생성합니다.
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    사용 예제:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. 스크립트 무결성의 유효성이 검사되면 다음 PowerShell 명령을 사용하여 각 Hyper-V 호스트에서 스크립트를 실행합니다.
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>해시태그 값

해시 값은 다음과 같습니다.

| **해시** | **값** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>CredSSP를 사용하도록 설정하여 자격 증명 위임

디스크가 SMB 공유에 있는 VM이 호스트에 있는 경우 호스트에서 이 단계를 완료합니다.

- 이 명령은 모든 Hyper-V 호스트에서 원격으로 실행할 수 있습니다.
- 새 호스트 노드를 클러스터에 추가하면 검색을 위해 자동으로 추가되지만, 필요한 경우 새 노드에서 CredSSP를 사용하도록 수동으로 설정해야 합니다.

다음과 같이 사용하도록 설정합니다.

1. SMB 공유의 디스크를 사용하여 Hyper-V VM을 실행하는 Hyper-V 호스트를 식별합니다.
2. 식별된 각 Hyper-V 호스트에서 다음 명령을 실행합니다.

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

어플라이언스가 설정되면 [어플라이언스에서 CredSSP를 사용하도록 설정](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds)하여 해당 설정을 완료합니다. 이에 대해서는 이 시리즈의 다음 자습서에서 설명합니다.


## <a name="prepare-for-appliance-deployment"></a>어플라이언스 배포를 위한 준비

Azure Migrate 어플라이언스를 설정하고 다음 자습서에서 평가를 시작하기 전에 어플라이언스 배포를 준비합니다.

1. 어플라이언스 요구 사항을 [확인](migrate-appliance.md#appliance---hyper-v)합니다.
2. 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 Azure URL을 검토합니다. URL 기반 방화벽 또는 프록시를 사용하는 경우 필요한 URL에 대한 액세스를 허용하는지 확인합니다.
3. 검색 및 평가 중에 어플라이언스가 수집할 데이터를 검토합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항을 [검토](migrate-appliance.md#collected-data---hyper-v)합니다.


## <a name="prepare-for-hyper-v-migration"></a>Hyper-V 마이그레이션 준비

1. 마이그레이션을 위한 Hyper-V 호스트 요구 사항 및 VM 마이그레이션을 위해 Hyper-V 호스트 및 클러스터에서 액세스해야 하는 Azure URL을 [검토](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)합니다.
2. Azure로 마이그레이션하려는 Hyper-V VM에 대한 요구 사항을 [검토](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)합니다.
3. VM을 Azure로 마이그레이션하기 전에 VM에서 몇 가지 사항을 변경해야 합니다.
    - 이러한 변경은 마이그레이션을 시작하기 전에 수행해야 합니다. 변경하기 전에 VM을 마이그레이션하면 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.
    - 필요한 [Windows](prepare-for-migration.md#windows-machines) 및 [Linux](prepare-for-migration.md#linux-machines) 변경 사항을 검토해야 합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure 계정 권한을 설정합니다.
> * 평가 및 마이그레이션을 위해 Hyper-V 호스트 및 VM을 준비합니다.
> * Azure Migrate 어플라이언스의 배포를 준비합니다.

다음 자습서를 계속 진행하여 Azure Migrate 프로젝트를 만들고 Hyper-V VM을 검색 및 평가하여 Azure로 마이그레이션합니다.

> [!div class="nextstepaction"]
> [Hyper-V VM 평가](./tutorial-assess-hyper-v.md)
