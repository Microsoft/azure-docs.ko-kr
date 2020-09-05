---
title: Azure Migrate를 사용 하 여 VMware VM 검색의 범위 설정
description: Azure Migrate를 사용 하 여 VMware VM 평가 및 마이그레이션에 대 한 검색 범위를 설정 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dfc9c12edd93fc720ef716fd43b04e0c193d5803
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919729"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>VMware Vm에 대 한 검색 범위 설정

이 문서에서는 다음과 같은 경우 검색 하는 VMware Vm의 범위를 제한 하는 방법을 설명 합니다.

- Azure Migrate를 사용 하는 경우 [Azure Migrate 어플라이언스](migrate-appliance-architecture.md) 를 사용 하 여 vm 검색: 서버 평가 도구.
- [Azure Migrate 어플라이언스](migrate-appliance-architecture.md) 를 Azure Migrate 사용 하 여 vm 검색: 서버 마이그레이션 도구를 사용 하 여 VMware Vm을 Azure에 대 한 에이전트 없이 마이그레이션할 수 있습니다.

어플라이언스를 설정 하는 경우 vCenter Server에 연결 하 여 검색을 시작 합니다. 어플라이언스를 vCenter Server에 연결 하기 전에 검색을 vCenter Server 데이터 센터, 클러스터, 클러스터, 호스트, 호스트 폴더 또는 개별 Vm으로 제한할 수 있습니다. 범위를 설정 하려면 어플라이언스에서 vCenter Server 액세스 하는 데 사용 하는 계정에 대 한 사용 권한을 할당 합니다.

## <a name="before-you-start"></a>시작하기 전에

검색에 사용 Azure Migrate 하는 vCenter 사용자 계정을 설정 하지 않은 경우 [평가](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) 또는 [에이전트 없는 마이그레이션](tutorial-prepare-vmware.md#assign-permissions-to-an-account)에 대해 지금 수행 합니다.


## <a name="assign-permissions-and-roles"></a>권한 및 역할 할당

다음 두 가지 방법 중 하나를 사용 하 여 VMware inventory objects에 대 한 사용 권한을 할당할 수 있습니다.

- 어플라이언스에서 사용 하는 계정에서 범위를 지정할 개체에 대 한 필요한 권한이 있는 역할을 할당 합니다.
- 또는 데이터 센터 수준에서 계정에 역할을 할당 하 고 자식 개체에 전파 합니다. 그런 다음 범위에서 원하지 않는 모든 개체에 대해 계정에 **액세스 권한 없음** 역할을 지정 합니다. 모든 새 자식 개체에는 부모 로부터 상속 된 액세스 권한이 자동으로 부여 되기 때문에이 방법을 사용 하지 않는 것이 좋으며, 액세스 제어를 노출할 수도 있습니다.

인벤토리 검색은 vCenter VM 폴더 수준에서 범위를 지정할 수 없습니다. VM 폴더의 vm에 대 한 범위를 검색 해야 하는 경우 사용자를 만들고 필요한 각 VM에 대해 개별적으로 액세스 권한을 부여 합니다. 호스트 및 클러스터 폴더가 지원 됩니다.


### <a name="assign-a-role-for-assessment"></a>평가에 대 한 역할 할당

1. 검색에 사용 하는 어플라이언스 vCenter 계정에서 검색 하 고 평가 하려는 Vm을 호스트 하는 모든 부모 개체에 대해 **읽기 전용** 역할을 적용 합니다 (호스트, 클러스터, 호스트 폴더, 클러스터 폴더, 데이터 센터까지).
2. 이러한 권한을 계층의 자식 개체에 전파 합니다.

    ![권한 할당](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>에이전트 없는 마이그레이션에 대 한 역할 할당

1. 마이그레이션에 사용 하는 어플라이언스 vCenter 계정에서 검색 및 마이그레이션할 Vm을 호스트 하는 모든 부모 개체에 [필요한 권한이](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)있는 사용자 정의 역할을 적용 합니다.
2. 식별 하기 쉬운 항목으로 역할의 이름을 지정할 수 있습니다. 예를 들어 <em>Azure_Migrate</em>합니다.

## <a name="work-around-vm-folder-restriction"></a>VM 폴더 제한 문제 해결

현재 vCenter VM 폴더 수준에서 액세스가 부여 된 경우 서버 평가 도구는 Vm을 검색할 수 없습니다. VM 폴더에 따라 검색 및 평가의 범위를 지정할 경우이 해결 방법을 사용 합니다.

1. 검색 및 평가를 위해 범위를 지정할 VM 폴더에 있는 모든 Vm에 대 한 읽기 전용 권한을 할당 합니다.
2. Vm 호스트, 클러스터, 호스트 폴더, 클러스터 폴더를 호스트 하는 모든 부모 개체에 대 한 읽기 전용 액세스 권한을 데이터 센터에 부여 합니다. 모든 자식 개체에 권한을 전파할 필요가 없습니다.
3. 검색에 자격 증명을 사용 하려면 데이터 센터를 **컬렉션 범위로**선택 합니다.


역할 기반 액세스 제어를 설정 하면 해당 vCenter 사용자 계정에 테 넌 트 별 Vm에만 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[어플라이언스 설정](how-to-set-up-appliance-vmware.md)
