---
title: Azure Migrate 어플라이언스 아키텍처
description: 서버 평가 및 마이그레이션에 사용되는 Azure Migrate 어플라이언스에 대해 간략히 설명합니다.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: a83e044acc329572a5f3bfd4856f90379319ba1d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919746"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 어플라이언스 아키텍처

이 문서에서는 Azure Migrate 어플라이언스 아키텍처와 프로세스에 대해 설명 합니다. Azure Migrate 어플라이언스는 Azure로 마이그레이션하기 위한 Vm 및 물리적 서버를 검색 하기 위해 온-프레미스에 배포 되는 경량 어플라이언스입니다. 

## <a name="deployment-scenarios"></a>배포 시나리오

Azure Migrate 어플라이언스가 사용되는 시나리오는 다음과 같습니다.

**시나리오** | **도구** | **용도** 
--- | --- | ---
**VMware VM 평가** | Azure Migrate: 서버 평가 | VMware Vm을 검색 합니다.<br/><br/> 컴퓨터 앱 및 종속성을 검색 합니다.<br/><br/> 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 하 고 Azure에 보냅니다.
**VMware VM 마이그레이션 (에이전트 없는)** | Azure Migrate: 서버 마이그레이션 | VMware VM을 검색합니다.<br/><br/>  [에이전트 없는 마이그레이션을](server-migrate-overview.md)사용 하 여 VMware vm을 복제 합니다.
**Hyper-V VM 평가** | Azure Migrate: 서버 평가 | Hyper-v Vm을 검색 합니다.<br/><br/> 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 하 고 Azure에 보냅니다.
**물리적 머신** |  Azure Migrate: 서버 평가 |  물리적 서버를 검색 합니다.<br/><br/> 컴퓨터 메타 데이터 및 성능 메타 데이터를 수집 하 고 Azure에 보냅니다.

## <a name="appliance-components"></a>어플라이언스 구성 요소

어플라이언스에는 많은 구성 요소가 있습니다.

- **관리 앱**: 어플라이언스를 배포하는 동안 사용자 입력을 위한 웹앱입니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.
- **검색 에이전트**: 에이전트가 컴퓨터 구성 데이터를 수집합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다. 
- **수집기 에이전트**: 에이전트는 성능 데이터를 수집 합니다. Azure로 마이그레이션하기 위한 컴퓨터를 평가할 때 사용됩니다.
- **DRA 에이전트**: VM 복제를 오케스트레이션하고, 복제된 컴퓨터와 Azure 간의 통신을 조정합니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure에 복제할 때만 사용됩니다.
- **게이트웨이**: 복제된 데이터를 Azure에 보냅니다. 에이전트 없는 마이그레이션을 사용하여 VMware VM을 Azure에 복제할 때만 사용됩니다.
- **자동 업데이트 서비스**: 어플라이언스 구성 요소를 업데이트합니다(24시간마다 실행).



## <a name="appliance-deployment"></a>어플라이언스 배포

- [Hyper-v](how-to-set-up-appliance-hyper-v.md) 또는 [vmware](how-to-set-up-appliance-vmware.md) 용 템플릿을 사용 하거나 [vmware/hyper-v](deploy-appliance-script.md)에 대 한 PowerShell 스크립트 설치 관리자를 사용 하거나 [물리적 서버](how-to-set-up-appliance-physical.md)에 대해 Azure Migrate 어플라이언스를 설정할 수 있습니다. 
- 어플라이언스 지원 요구 사항 및 배포 필수 구성 요소는 [어플라이언스 지원 매트릭스](migrate-appliance.md)에 요약 되어 있습니다.


## <a name="appliance-registration"></a>어플라이언스 등록

어플라이언스를 설정 하는 동안 Azure Migrate를 사용 하 여 어플라이언스를 등록 하면 테이블에 요약 된 작업이 발생 합니다.

**동작** | **세부 정보** | **권한**
--- | --- | ---
**소스 공급자 등록** | 이러한 리소스 공급자는 어플라이언스 설정 중에 선택 하는 구독에 등록 됩니다. 예: Microsoft. OffAzure, microsoft. 마이그레이션 및 Microsoft.<br/><br/> 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다. | 리소스 공급자를 등록하려면 구독에 대한 기여자 또는 소유자 역할이 필요합니다.
**Azure AD 앱 만들기-통신** | Azure Migrate는 어플라이언스에서 실행 되는 에이전트와 Azure에서 실행 되는 각 서비스 간의 통신 (인증 및 권한 부여)을 위한 Azure Active Directory (Azure AD) 앱을 만듭니다.<br/><br/> 이 앱에는 모든 리소스에 대 한 Azure Resource Manager 호출 또는 RBAC 액세스를 수행할 수 있는 권한이 없습니다. | 앱을 만들려면 Azure Migrate에 대해 [이러한 권한이](tutorial-prepare-vmware.md#assign-permissions-to-create-azure-ad-apps) 필요 합니다.
**Azure AD 앱 만들기-주요 자격 증명 모음** | 이 앱은 VMware Vm을 Azure로 마이그레이션할 때만 생성 됩니다.<br/><br/> 에이전트 없는 마이그레이션을 위해 사용자의 구독에서 생성 된 키 자격 증명 모음에 액세스 하는 데만 사용 됩니다.<br/><br/> 검색을 어플라이언스에서 시작 하는 경우 Azure key vault (고객의 테 넌 트에서 만들어짐)에 대 한 RBAC 액세스 권한이 있습니다. | 앱을 만들려면 Azure Migrate에 대해 [이러한 권한이](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault) 필요 합니다.



## <a name="collected-data"></a>수집된 데이터

모든 배포 시나리오에 대해 클라이언트에서 수집 하는 데이터는 [어플라이언스 지원 매트릭스](migrate-appliance.md)에 요약 되어 있습니다.

## <a name="discovery-and-collection-process"></a>검색 및 수집 프로세스

![아키텍처](./media/migrate-appliance-architecture/architecture.png)

어플라이언스는 다음 프로세스를 사용 하 여 vCenter 서버 및 Hyper-v 호스트/클러스터와 통신 합니다.

1. **검색 시작**:
    - Hyper-v 어플라이언스에서 검색을 시작 하면 WinRM 포트 5985 (HTTP) 및 5986 (HTTPS)에서 Hyper-v 호스트와 통신 합니다.
    - VMware 어플라이언스에서 검색을 시작 하면 기본적으로 TCP 포트 443의 vCenter server와 통신 합니다. VCenter server가 다른 포트에서 수신 대기 하는 경우 어플라이언스 웹 앱에서 구성할 수 있습니다.
2. **메타 데이터 및 성능 데이터를 수집**합니다.
    - 어플라이언스는 CIM(Common Information Model) (CIM) 세션을 사용 하 여 hyper-v 호스트에서 5985 및 5986 포트의 hyper-v VM 데이터를 수집 합니다.
    - 어플라이언스는 기본적으로 포트 443와 통신 하 여 vCenter Server에서 VMware VM 데이터를 수집 합니다.
3. **데이터 보내기**: 어플라이언스는 Azure Migrate 서버 평가를 위해 수집 된 데이터를 보내고 SSL 포트 443을 통해 서버 마이그레이션을 Azure Migrate 합니다. 어플라이언스는 인터넷을 통해 Azure에 연결 하거나 공용/Microsoft 피어 링과 함께 Express 경로를 사용할 수 있습니다.
    - 성능 데이터의 경우 어플라이언스는 실시간 사용률 데이터를 수집 합니다.
        - 성능 데이터는 각 성능 메트릭에 대해 VMware에 대해 20 초 마다 수집 되 고 Hyper-v의 경우 30 초 마다 수집 됩니다.
        - 수집 된 데이터는 10 분 동안 단일 데이터 요소를 만들기 위해 롤업 됩니다.
        - 최고 사용률 값은 모든 20/30 초 데이터 요소에서 선택 되 고 평가 계산을 위해 Azure로 전송 됩니다.
        - 평가 속성에 지정 된 백분위 수 값 (50 번째/90 번째/95 번째/99 초)에 따라 10 분 지점은 오름차순으로 정렬 되 고 적절 한 백분위 수 값은 평가를 계산 하는 데 사용 됩니다.
    - 서버 마이그레이션의 경우 어플라이언스는 VM 데이터 수집을 시작 하 여 Azure에 복제 합니다.
4. **평가 및 마이그레이션**: 이제 Azure Migrate Server 평가를 사용 하 여 어플라이언스에서 수집한 메타 데이터에서 평가를 만들 수 있습니다. 또한 Azure Migrate 서버 마이그레이션을 사용 하 여 VMware Vm 마이그레이션을 시작 하 여 에이전트 없는 VM 복제를 오케스트레이션 할 수도 있습니다.





## <a name="appliance-upgrades"></a>어플라이언스 업그레이드

어플라이언스에서 실행되는 Azure Migrate 에이전트가 업데이트되면 어플라이언스가 업그레이드됩니다. 이는 기본적으로 어플라이언스에서 자동 업데이트를 사용 하기 때문에 자동으로 발생 합니다. 이 기본 설정을 변경 하 여 에이전트를 수동으로 업데이트할 수 있습니다.

HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance "자동 업데이트" 키를 0 (DWORD)로 설정 하 여 레지스트리에서 자동 업데이트를 해제 합니다.

 

## <a name="next-steps"></a>다음 단계

어플라이언스 지원 매트릭스를 [검토](migrate-appliance.md) 합니다.

