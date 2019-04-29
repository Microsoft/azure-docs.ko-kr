---
title: Azure Site Recovery를 사용한 VMware VM과 Azure 간 재해 복구를 위한 다중 테넌트 지원(CSP) 개요 | Microsoft Docs
description: 다중 테넌트 환경(CSP) 프로그램에서 VMWare와 Azure 간 재해 복구를 위해 제공되는 Azure Site Recovery 지원에 대해 간략하게 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: d227b8d038dd686bde9b031ca2c58adc7dd6d76b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718124"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>CSP를 사용한 VMware와 Azure 간 재해 복구를 위한 다중 테넌트 지원 개요

[Azure Site Recovery](site-recovery-overview.md)는 테넌트 구독을 위해 다중 테넌트 환경을 지원합니다. 또한 Microsoft CSP(클라우드 솔루션 공급자) 프로그램을 통해 생성 및 관리되는 테넌트 구독에 대한 다중 테넌트도 지원합니다.

이 아티클에서는 Azure 복제에 대해 다중 테넌트 VMware를 구현하고 관리하는 개요를 제공합니다.

## <a name="multi-tenant-environments"></a>다중 테넌트 환경

세 가지 주요 다중 테넌트 모델이 있습니다.

* **공유 HSP(호스팅 서비스 공급 기업)**: 파트너는 물리적 인프라를 소유하고 공유 리소스(vCenter, 데이터 센터, 실제 스토리지 등)를 사용하여 동일한 인프라에서 다중 테넌트 VM을 호스팅합니다. 파트너는 재해 복구 관리를 관리 서비스로 제공할 수 있으며 테넌트는 재해 복구를 셀프 서비스 솔루션으로 소유할 수 있습니다.

* **전용 호스팅 서비스 공급 기업**: 파트너는 물리적 인프라를 소유하지만 전용 리소스(여러 vCenter, 실제 데이터 저장소 등)를 사용하여 별도의 인프라에서 각 테넌트의 VM을 호스팅합니다. 파트너는 재해 복구 관리를 관리 서비스로 제공할 수 있으며 테넌트는 이를 셀프 서비스 솔루션으로 소유할 수 있습니다.

* **MSP(관리형 서비스 공급 기업)**: VM을 호스팅하는 실제 인프라를 고객이 소유하고 파트너는 재해 복구를 사용하고 관리합니다.

## <a name="shared-hosting-services-provider-hsp"></a>공유 HSP(호스팅 서비스 공급자)

다른 두 가지 시나리오는 공유 호스팅 시나리오의 하위 집합이며 동일한 원칙을 사용합니다. 차이점은 공유 호스팅 지침의 끝에 설명되어 있습니다.

다중 테넌트 시나리오의 기본 요구 사항은 테넌트를 격리하는 것입니다. 한 테넌트에서 다른 테넌트가 호스팅한 것을 확인할 수 없어야 합니다. 이 요구 사항은 셀프 서비스 환경에서는 중요할 수 있으나 파트너 관리형 환경에서는 그만큼 중요하지 않습니다. 이 아티클에서는 테넌트 격리가 필요한 것으로 가정합니다.

아키텍처는 다음 다이어그램에서 나타납니다.

![하나의 vCenter를 포함하는 공유 HSP](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**하나의 vCenter Server를 포함하는 공유 호스팅**

다이어그램에서 각 고객은 별도의 관리 서버를 가집니다. 이 구성은 테넌트별 VM에 테넌트 액세스를 제한하고 테넌트를 격리합니다. VMware VM 복제는 구성 서버를 사용하여 VM을 검색하고 에이전트를 설치합니다. vCenter 액세스 제어를 사용하여 VM 검색을 제한하는 것 외에도 동일한 원칙이 다중 테넌트 환경에 적용됩니다.

데이터 격리 요구 사항은 모든 민감한 인프라 정보(예: 액세스 자격 증명)를 테넌트에 공개하지 않는 상태로 유지함을 의미합니다. 이러한 이유로 관리 서버의 모든 구성 요소는 파트너가 독점적으로 통제하도록 하는 것이 좋습니다. 관리 서버 구성 요소는 다음과 같습니다.

* 구성 서버
* 프로세스 서버
* 마스터 대상 서버

별도 스케일 아웃 프로세스 서버도 파트너의 제어 하에 있습니다.

## <a name="configuration-server-accounts"></a>구성 서버 계정

다중 테넌트 시나리오의 모든 구성 서버는 두 개의 계정을 사용합니다.

- **vCenter 액세스 계정**: 이 계정을 사용하여 테넌트 VM을 검색합니다. vCenter 액세스 권한을 할당받았습니다. 액세스 누수를 방지하기 위해 파트너가 이러한 자격 증명을 구성 도구에 직접 입력하는 것이 좋습니다.

- **가상 머신 액세스 계정**: 이 계정은 자동 푸시를 사용하여 테넌트 VM에 모바일 서비스 에이전트를 설치하는 데 사용됩니다. 일반적으로 테넌트가 파트너에게 제공하거나 파트너가 직접 관리할 수 있는 도메인 계정입니다. 테넌트가 파트너와 세부 사항을 직접 공유하지 않으려는 경우 구성 서버에 대해 제한된 시간 액세스를 통해 자격 증명을 입력할 수 있습니다. 또는 파트너의 지원을 통해 모바일 서비스 에이전트를 수동으로 설치할 수 있습니다.

## <a name="vcenter-account-requirements"></a>vCenter 계정 요구 사항

특별한 역할이 할당된 계정을 사용하여 구성 서버를 구성합니다.

- 각 vCenter 개체에 대한 vCenter 액세스 계정에 역할 할당이 적용되고 자식 개체에게는 전파되지 않도록 해야 합니다. 액세스 전파가 다른 개체에도 우발적으로 액세스할 수 있기 때문에 이 구성을 사용하면 테넌트 격리가 보장됩니다.

    ![자식 개체로 전파 옵션](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- 대안으로 데이터 센터 개체에서 사용자 계정 및 역할을 할당하고 자식 개체에 전파하는 것이 있습니다. 그러면 특정 테넌트에 액세스하지 않아야 하는 계정에 모든 개체(예: 다른 테넌트에 속한 VM)에 대한 **액세스 없음** 역할이 부여됩니다. 이 구성은 복잡합니다. 모든 새로운 자식 개체에게는 부모로부터 상속된 액세스도 자동으로 부여되므로 우발적인 액세스 제어를 공개합니다. 따라서 첫 번째 방법을 사용하는 것이 좋습니다.

### <a name="create-a-vcenter-account"></a>vCenter 계정 만들기

1. 미리 정의된 *읽기 전용* 역할을 복제하여 새 역할을 만들고 편리한 이름을 부여합니다(예: 이 예제에서 표시된 것처럼 Azure_Site_Recovery).
2. 이 역할에 다음 권한을 할당합니다.

   * **데이터 저장소**: 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트
   * **네트워크**: 네트워크 할당
   * **리소스**: 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션
   * **작업**: 만들기 작업, 업데이트 작업
   * **VM - 구성**: 모두
   * **VM - 상호 작용** &gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치
   * **VM - 인벤토리** > 기존 항목에서 만들기, 새로 만들기, 등록, 등록 취소
   * **VM - 프로비전**> 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용
   * **VM - 스냅숏 관리** > 스냅숏 제거

       ![역할 편집 대화 상자](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. 다음과 같이 다양한 개체에 대해 vCenter 계정(테넌트 구성 서버에 사용)에 액세스 수준을 할당합니다.

>| Object | 역할 | 설명 |
>| --- | --- | --- |
>| vCenter | 읽기 전용 | 여러 개체를 관리하기 위해 vCenter 액세스를 허용하는 데만 필요합니다. 계정이 테넌트에 제공되지 않거나 vCenter의 모든 관리 작업에 사용되는 경우 이 권한을 제거할 수 있습니다. |
>| 데이터 센터 | Azure_Site_Recovery |  |
>| 호스트 및 호스트 클러스터 | Azure_Site_Recovery | 따라서 장애 조치(failover) 전 및 장애 복구(failback) 후에 액세스할 수 있는 호스트만 테넌트 VM을 가지도록 개체 수준에서 액세스가 이루어지는지 다시 확인합니다. |
>| 데이터 저장소 및 데이터 저장소 클러스터 | Azure_Site_Recovery | 앞과 동일합니다. |
>| 네트워크 | Azure_Site_Recovery |  |
>| 관리 서버 | Azure_Site_Recovery | CS 컴퓨터 외부에서 모든 구성 요소(CS, PS 및 MT)에 대한 액세스를 포함합니다. |
>| 테넌트 VM | Azure_Site_Recovery | 또한 특정 테넌트의 새로운 테넌트 VM이 이 액세스를 수행하는지, Azure Portal을 통해 이 테넌트를 검색할 수 없는지 확인합니다. |

이제 vCenter 계정 액세스가 완료되었습니다. 이 단계는 장애 복구 작업을 완료하는 최소 권한 요구 사항을 충족합니다. 이러한 액세스 권한은 기존 정책과 함께 사용할 수도 있습니다. 이전에 설명한 2단계의 역할 권한을 포함하도록 기존 권한을 수정합니다.

### <a name="failover-only"></a>장애 조치(Failover) 전용
장애 조치 전용(즉, 장애 복구 기능 없이)으로 재해 복구 작업을 제한하려면 다음과 같은 예외를 포함하는 이전 절차를 사용합니다.

- *Azure_Site_Recovery* 역할을 vCenter 액세스 계정에 할당하는 대신 *읽기 전용* 역할만을 해당 계정에 할당합니다. 이 권한 집합은 VM 복제 및 장애 조치(Failover)를 허용하지만 장애 복구(failback)는 허용하지 않습니다.
- 위 프로세스의 나머지 모든 것들은 그대로 유지됩니다. 테넌트를 격리하고 VM 검색을 제한하기 위해 모든 권한은 계속 개체 수준에서만 할당되며 자식 개체에는 전파되지 않습니다.

### <a name="deploy-resources-to-the-tenant-subscription"></a>리소스를 테넌트 구독에 배포

1. Azure Portal에서 리소스 그룹을 만들고 일반 프로세스에 대한 Recovery Services 자격 증명 모음을 배포합니다.
2. 자격 증명 모음 등록 키를 다운로드합니다.
3. 자격 증명 모음 등록 키를 사용하여 테넌트용 CS를 등록합니다.
4. vCenter server에 액세스할 계정 및 VM에 액세스할 계정 등 두 개의 액세스 계정에 대한 자격 증명을 입력합니다.

    ![관리자 구성 서버 계정](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>자격 증명 모음에서 서버 등록

1. Azure Portal에 있는 앞에서 만든 자격 증명 모음에서 만든 vCenter 계정을 사용하여 구성 서버에 vCenter Server를 등록합니다.
2. 일반 프로세스에 대한 Site Recovery를 위해 “준비 인프라” 프로세스를 완료합니다.
3. 이제 VM을 복제할 준비가 되었습니다. 테넌트의 VM이 **복제** > **가상 머신 선택**에 표시되는지만 확인합니다.

## <a name="dedicated-hosting-solution"></a>전용 호스팅 솔루션

다음 다이어그램에 표시된 것처럼 전용 호스팅 솔루션의 아키텍처 차이점은 각 테넌트의 인프라가 해당 테넌트에 대해서만 설정된다는 것입니다.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**여러 vCenter를 사용한 전용 호스팅 시나리오**

## <a name="managed-service-solution"></a>관리형 서비스 솔루션

다음 다이어그램에 나와 있는 것처럼 관리 서비스 솔루션의 아키텍처 차이점은 각 테넌트의 인프라가 다른 테넌트의 인프라와 물리적으로도 분리된다는 점입니다. 이 시나리오는 일반적으로 테넌트가 인프라를 소유하고 재해 복구를 관리할 솔루션 공급자가 필요한 경우 존재합니다.

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**여러 vCenter를 사용하는 관리 서비스 시나리오**

## <a name="next-steps"></a>다음 단계
- Site Recovery에서 역할 기반 액세스 제어에 대해 [자세히 알아봅니다](site-recovery-role-based-linked-access-control.md).
- [Azure에 VMware VM의 재해 복구를 설정](vmware-azure-tutorial.md)하는 방법에 대해 알아봅니다.
- [CSP를 통한 다중 테넌트 VMWare VM 복제](vmware-azure-multi-tenant-csp-disaster-recovery.md)에 대해 자세히 알아봅니다.
