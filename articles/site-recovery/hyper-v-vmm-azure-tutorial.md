---
title: Azure Site Recovery를 사용하여 Hyper-V(VMM 사용) 재해 복구 설정
description: Site Recovery를 사용하여 System Center VMM 클라우드의 온-프레미스 Hyper-V VM에서 Azure로의 재해 복구를 설정하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: f32103adce184a67cec9e5a778ac1d1e6f330f4d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86130233"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>VMM 클라우드의 온-프레미스 Hyper-V VM에서 Azure로의 재해 복구 설정

이 자습서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 System Center VMM(Virtual Machine Manager)에서 관리하는 온-프레미스 Hyper-V VM(가상 머신)을 Azure에 복제하도록 설정하는 방법에 대해 설명합니다. VMM을 사용하지 않는 경우 [이 자습서를 따릅니다](hyper-v-azure-tutorial.md).

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 복제 원본 및 대상을 선택합니다.
> * 온-프레미스 Site Recovery 구성 요소 및 대상 복제 환경을 포함한 원본 복제 환경을 설정합니다.
> * VMM VM 네트워크와 Azure 가상 네트워크 간의 네트워크 매핑을 설정합니다.
> * 복제 정책을 만듭니다.
> * VM의 복제를 사용하도록 설정합니다.

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 자세한 지침은 [Site Recovery 설명서](./index.yml)의 **방법 가이드** 섹션에 있는 문서를 검토하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 다음 자습서를 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
1. [온-프레미스 Hyper-V 서버 준비](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>복제 목표 선택

1. Azure Portal에서 **Recovery Services 자격 증명 모음**으로 이동하여 [Azure 준비](tutorial-prepare-azure.md#create-a-recovery-services-vault) 자습서에서 만든 **ContosoVMVault** 자격 증명 모음을 선택합니다.
1. **시작**에서 **Site Recovery** > **인프라 준비**를 차례로 선택하고, 다음 설정을 구성합니다.
    1. **보호 목표** > **머신이 어디에 있나요?** 에서 **온-프레미스**를 선택합니다.
    1. **머신을 복제할 위치를 선택하세요.** 에서 **Azure**를 선택합니다.
    1. **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용**을 선택합니다.
    1. **System Center VMM을 사용하여 Hyper-V 호스트를 관리하나요?** 에서 **예**를 선택합니다.
1. **확인**을 선택합니다.

   ![복제 목표](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>배포 계획 확인

1. **배포 계획**에서 대규모 배포를 계획 중인 경우 페이지의 링크를 통해 Hyper-V용 Deployment Planner를 다운로드합니다. Hyper-V 배포 계획에 대해 [자세히 알아보세요](hyper-v-deployment-planner-overview.md).
1. 이 자습서에서는 Deployment Planner가 필요하지 않습니다. **배포 계획을 완료하셨나요**에서 **나중에 수행**을 선택한 다음, **확인**을 선택합니다.

## <a name="set-up-the-source-environment"></a>원본 환경 설정

원본 환경을 설정하는 경우 Azure Site Recovery 공급자를 VMM 서버에 설치하고, 이 서버를 자격 증명 모음에 등록합니다. Azure Recovery Services 에이전트를 각 Hyper-V 호스트에 설치합니다.

1. **인프라 준비**에서 **원본**을 선택합니다.
1. **원본 준비**에서 **+ VMM**을 선택하여 VMM 서버를 추가합니다. **서버 추가**의 **서버 유형**에서 **System Center VMM 서버**가 표시되는지 확인합니다.
1. Microsoft Azure Site Recovery Provider용 설치 관리자를 다운로드합니다.
1. 자격 증명 모음 등록 키를 다운로드합니다. 이 키는 공급자 설치를 실행할 때 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.
1. Microsoft Azure Recovery Services 에이전트 설치 관리자를 다운로드합니다.

   ![공급자, 등록 키 및 에이전트 다운로드](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>VMM 서버에 공급자 설치

1. Azure Site Recovery 공급자 설치 마법사의 **Microsoft Update**에서 Microsoft Update를 사용하여 공급자 업데이트를 확인하도록 옵트인합니다.
1. **설치**에서 공급자의 기본 설치 위치를 적용하고, **설치**를 선택합니다.
1. 설치 후에 Microsoft Azure Site Recovery 등록 마법사에서 **자격 증명 모음 설정**, **찾아보기**를 선택하고, **키 파일**에서 다운로드한 자격 증명 모음 키 파일을 선택합니다.
1. Azure Site Recovery 구독 및 자격 증명 모음 이름(**ContosoVMVault**)을 지정합니다. 자격 증명 모음에서 식별할 수 있도록 VMM 서버의 식별 이름을 지정합니다.
1. **프록시 설정**에서 **프록시 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
1. 데이터를 암호화하는 데 사용되는 인증서의 기본 위치를 적용합니다. 장애 조치(failover) 시에는 암호화된 데이터의 암호가 해독됩니다.
1. **클라우드 메타데이터 동기화**에서 **클라우드 메타데이터를 Site Recovery 포털에 동기화하세요.** 를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 그런 다음 **등록**을 선택합니다.
1. 서버가 자격 증명 모음에 등록되면 **마침**을 선택합니다.

등록이 완료되면 Azure Site Recovery에서 서버의 메타데이터가 검색되며 VMM 서버가 **Site Recovery 인프라**에 표시됩니다.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Hyper-V 호스트에 Recovery Services 에이전트 설치

복제하려는 VM이 포함된 각 Hyper-V 호스트에 에이전트를 설치합니다.

Microsoft Azure Recovery Services 에이전트 설치 마법사에서 다음 설정을 구성합니다.

1. **필수 구성 요소 확인**에서 **다음**을 선택합니다. 누락된 필수 구성 요소가 자동으로 설치됩니다.
1. **설치 설정**에서 설치 위치 및 캐시 위치를 적용합니다. 캐시 드라이브에는 5GB 이상의 스토리지 공간이 필요합니다. 600GB 이상의 여유 공간이 있는 드라이브를 사용하는 것이 좋습니다. 그런 후 **설치**를 선택합니다.
1. **설치**에서 설치가 완료되면 **닫기**를 선택하여 마법사를 완료합니다.

   ![에이전트 설치](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>대상 환경 설정

1. **인프라 준비** > **대상**을 선택합니다.
1. 구독을 선택하고 장애 조치(failover) 후 Azure VM이 생성될 리소스 그룹 **ContosoRG**를 선택합니다.
1. **Resource Manager** 배포 모델을 선택합니다.

Site Recovery에서 호환되는 Azure Storage 계정 및 네트워크가 하나 이상 있는지 확인합니다.

## <a name="configure-network-mapping"></a>네트워크 매핑 구성

1. **Site Recovery 인프라** > **네트워크 매핑** > **네트워크 매핑**에서 **+ 네트워크 매핑** 아이콘을 선택합니다.
1. **네트워크 매핑 추가**에서 **원본 System Center VMM** 서버를 선택합니다. **대상**에 대해 Azure를 선택합니다.
1. 장애 조치(failover) 후 구독과 배포 모델을 확인합니다.
1. **원본 네트워크**에서 원본 온-프레미스 VM 네트워크를 선택합니다.
1. **대상 네트워크**에서 장애 조치 후에 만들어지는 복제본 Azure VM이 위치할 Azure 네트워크를 선택합니다. 그런 다음 **확인**을 선택합니다.

   ![네트워크 매핑](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>복제 정책 설정

1. **인프라 준비** > **복제 설정** >  **+만들기 및 연결**을 차례로 선택합니다.
1. **만들기 및 연결 정책**에서 정책 이름을 지정합니다. 여기서는 **ContosoReplicationPolicy**를 사용합니다.
1. 기본 설정을 적용하고, **확인**을 선택합니다.
   - **복사 빈도**는 초기 복제 후 델타 데이터가 5분마다 복제됨을 나타냅니다.
   - **복구 지점 보존**은 각 복구 지점이 2시간 동안 보존됨을 나타냅니다.
   - **앱 일치 스냅샷 빈도**는 앱 일치 스냅샷을 포함하는 복구 지점이 1시간마다 만들어짐을 나타냅니다.
   - **초기 복제 시작 시간**은 초기 복제가 즉시 시작됨을 나타냅니다.
   - **Azure에 저장된 데이터 암호화**가 기본값(**끄기**)으로 설정되고, Azure에 저장된 데이터가 암호화되지 않음을 나타냅니다.
1. 정책이 만들어지면 **확인**을 선택합니다. 새 정책을 만들면 해당 정책이 VMM 클라우드에 자동으로 연결됩니다.

## <a name="enable-replication"></a>복제 사용

1. **애플리케이션 복제**에서 **원본**을 선택합니다.
1. **원본** - VMM 클라우드를 선택합니다. 그런 다음 **확인**을 선택합니다.
1. **대상**에서 대상(Azure) 및 자격 증명 모음 구독을 확인하고, **Resource Manager** 모델을 선택합니다.
1. **contosovmsacct1910171607** 스토리지 계정 및 **ContosoASRnet** Azure 네트워크를 선택합니다.
1. **가상 머신** > **선택**에서 복제하려는 VM을 선택합니다. 그런 다음 **확인**을 선택합니다.

   **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료되면 초기 복제가 완료되고 VM에서 장애 조치할 준비가 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [재해 복구 훈련 실행](tutorial-dr-drill-azure.md)
