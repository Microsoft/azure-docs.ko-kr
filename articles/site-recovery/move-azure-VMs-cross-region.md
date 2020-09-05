---
title: Azure Site Recovery를 사용하여 Azure VM을 다른 지역으로 이동
description: Azure Site Recovery를 사용하여 Azure 지역 간에 Azure IaaS VM을 이동합니다.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 463bd290c304d9436316c9e08778f37ce8bd0b55
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86135435"
---
# <a name="move-azure-vms-to-another-azure-region"></a>다른 Azure 지역으로 Azure VM 이동

안정성, 가용성, 관리 또는 거버넌스를 개선하기 위해 Azure IaaS(Infrastructure As a Service) 가상 머신을 한 지역에서 다른 지역으로 이동하고 싶을 수 있습니다. 이 자습서에서는 Azure Site Recovery를 사용하여 VM을 다른 Azure 지역으로 이동하는 방법을 보여줍니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 필수 조건 확인
> * 원본 VM 준비
> * 대상 Azure 지역 준비
> * 대상 Azure 지역에 데이터 복사
> * 구성 테스트
> * 이동 수행
> * 원본 지역에서 리소스 삭제


> [!IMPORTANT]
> 이 문서에서는 한 지역에서 다른 지역으로 Azure VM을 *있는 그대로* 이동하는 방법을 설명합니다. 목표가 VM을 가용성 영역으로 이동하여 인프라의 가용성을 개선하는 것이라면 [가용성 영역으로 Azure VM 이동](move-azure-vms-avset-azone.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- VM 이동을 시작하려는 *원본* Azure 지역에 Azure VM이 있는지 확인합니다.
- 선택한 [원본 지역 - 대상 Azure 지역 조합이 지원되는지](./azure-to-azure-support-matrix.md#region-support) 확인하여 대상 지역을 신중하게 결정합니다.
- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- [제한 사항 및 요구 사항 지원](azure-to-azure-support-matrix.md)을 검토합니다.
- 계정 권한을 확인합니다. Azure 체험 계정을 방금 만든 경우 *자신*이 구독에 대한 관리자입니다. 관리자가 아닌 경우에는 다음을 포함하는 필요한 권한을 부여해 줄 것을 관리자에게 요청합니다.
  -  VM에 대해 복제를 사용하도록 설정하고 Site Recovery를 사용하여 대상으로 데이터를 복사하려면 Azure 리소스에서 VM을 만들 수 있는 권한이 있어야 합니다. 기본 제공되는 Virtual Machine 참가자 역할에는 이러한 권한이 있습니다. 이 권한이 있으면 다음 작업을 수행할 수 있습니다.
        - 선택한 리소스 그룹에 VM 만들기
        - 선택한 가상 네트워크에 VM 만들기
        - 선택한 스토리지 계정에 쓰기

  - Site Recovery 작업을 관리할 수 있는 권한도 있어야 합니다. Site Recovery 참가자 역할은 Azure Recovery Services 자격 증명 모음에서 Site Recovery 작업을 관리하는 데 필요한 모든 권한을 보유합니다.

## <a name="prepare-the-source-vms"></a>원본 VM 준비

1. 이동하려는 Azure VM에 최신 루트 인증서가 있는지 확인합니다. 이 루트 인증서가 없으면 보안 제약으로 인해 대상 지역으로의 데이터 복사를 사용하도록 설정할 수 없습니다.

    - Windows VM의 경우 최신 Windows 업데이트를 설치하여 신뢰할 수 있는 모든 루트 인증서가 컴퓨터에 있도록 합니다. 연결이 끊어진 환경에서는 조직의 표준 Windows 업데이트 및 인증서 업데이트 프로세스를 따릅니다.
    - Linux VM의 경우 Linux 배포자의 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 인증서 해지 목록을 가져옵니다.
2. 이동하려는 VM의 네트워크 연결을 제어하기 위해 인증 프록시를 사용하지 않도록 합니다.
3. 이동하려는 VM이 인터넷에 액세스할 수 없고 방화벽 프록시를 사용하여 아웃바운드 액세스를 제어하는 경우 [요구 사항](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)을 확인합니다.
4. 원본 네트워킹 레이아웃 및 현재 사용 중인 모든 리소스(부하 분산 장치, 네트워크 보안 그룹, 확인용 공용 IP 등)를 문서로 정리합니다.

## <a name="prepare-the-target-region"></a>대상 Azure 지역 준비

1. Azure 구독에서 재해 복구에 사용되는 대상 지역에 VM을 만들 수 있는지 확인합니다. 필요한 경우 지원팀에 문의하여 필요한 할당량을 사용하도록 설정합니다.

2. 구독에 원본 VM을 지원할 수 있을 만큼 충분한 리소스가 있는지 확인합니다. Site Recovery를 사용하여 대상에 데이터를 복사하는 경우 동일한 크기 또는 사용 가능한 가장 비슷한 크기의 대상 VM을 선택합니다.

3. 원본 네트워킹 레이아웃에서 식별된 모든 구성 요소의 대상 리소스를 만듭니다. 이를 통해 VM은 대상 지역에서도 원본 지역과 동일한 모든 기능을 보유하게 됩니다.

   사용자가 원본 VM에 대해 복제를 사용하도록 설정하면 Azure Site Recovery는 가상 네트워크 및 스토리지 계정을 자동으로 검색하고 만듭니다. 이러한 리소스를 미리 만든 후 복제 사용 설정 단계의 일환으로 VM에 할당할 수도 있습니다. 하지만 대상 지역에서 다른 리소스는 수동으로 만들어야 합니다. 다음 문서를 참조하여 원본 VM 구성에 따라 가장 일반적으로 사용되는 네트워크 리소스를 만드세요.

   - [네트워크 보안 그룹](../virtual-network/manage-network-security-group.md)
   - [부하 분산 장치](../load-balancer/index.yml)
   - [공용 IP](../virtual-network/virtual-network-public-ip-address.md)
    
   그 외의 네트워킹 구성 요소는 [Azure 네트워킹 설명서](../index.yml?pivot=products&panel=network)를 참조하세요. 

4. 이동하기 전에 구성을 테스트하려면 대상 지역에서 수동으로 [비프로덕션 네트워크를 만듭니다](../virtual-network/quick-create-portal.md). 설정을 테스트해도 프로덕션 환경을 최소로만 중단하므로 테스트 과정을 진행하는 것이 좋습니다.
    
## <a name="copy-data-to-the-target-region"></a>대상 Azure 지역에 데이터 복사
다음 단계에서는 Azure Site Recovery를 사용하여 대상 Azure 지역에 데이터를 복사합니다.

### <a name="create-the-vault-in-any-region-except-the-source"></a>원본을 제외한 모든 지역에 자격 증명 모음 만들기

1. [Azure Portal](https://portal.azure.com) > **Recovery Services**에 로그인합니다.
2. **리소스 만들기** > **관리 도구** > **Backup 및 Site Recovery**를 선택합니다.
3. **이름**에서 **ContosoVMVault**라는 이름을 지정합니다. 구독이 두 개 이상인 경우 적절한 구독을 선택합니다.
4. **ContosoRG** 리소스 그룹을 만듭니다.
5. Azure 지역을 지정합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 책정 정보](https://azure.microsoft.com/pricing/details/site-recovery/)를 참조하세요.
6. Recovery Services 자격 증명 모음에서 **개요** > **ConsotoVMVault** >  **+복제**를 선택합니다.
7. **원본**에서 **Azure**를 선택합니다.
8. **원본 위치**에서 VM이 현재 실행 중인 원본 Azure 지역을 선택합니다.
9. Azure Resource Manager 배포 모델을 선택합니다. 그런 다음, **원본 구독** 및 **원본 리소스 그룹**을 선택합니다.
10. **확인**을 선택하여 설정을 저장합니다.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure VM에 대한 복제를 사용하도록 설정하고 데이터 복사 시작

Site Recovery는 구독 및 리소스 그룹과 연관된 VM 목록을 검색합니다.

1. 이동하려는 VM을 선택하고 **확인**을 선택합니다.
2. **설정**에서 **재해 복구**를 선택합니다.
3. **재해 복구 구성** > **대상 지역**에서 복제할 대상 지역을 선택합니다.
4. 기본 대상 리소스를 사용할지 또는 미리 만든 리소스를 사용할지를 선택합니다.
5. **복제 사용**을 선택하여 작업을 시작합니다.

   ![복제 사용](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>구성 테스트


1. 자격 증명 모음으로 이동합니다. **설정** > **복제된 항목**에서 대상 지역으로 이동하려는 가상 머신을 선택합니다. 그런 후 **테스트 장애 조치(failover)** 를 선택합니다.
2. **테스트 장애 조치(Failover)** 에서 장애 조치에 사용할 복구 지점을 선택합니다.

   - **가장 최근에 처리됨**: VM을 Site Recovery 서비스에서 처리된 최신 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다. 데이터를 처리하는 데 시간을 소비하지 않으므로 이 옵션을 사용하면 낮은 RTO(복구 시간 목표)가 제공됩니다.
   - **최신 앱 일치**: 모든 VM을 최신 앱 일치 복구 지점으로 장애 조치(failover)합니다. 타임스탬프가 표시됩니다.
   - **Custom**: 복구 시점을 선택합니다.

3. 구성을 테스트하기 위해 Azure VM을 이동할 대상 Azure 가상 네트워크를 선택합니다.

   > [!IMPORTANT]
   > 대상 Azure 지역의 프로덕션 네트워크가 아닌 별도의 Azure VM 네트워크를 테스트 장애 조치(failover)에 사용하는 것이 좋습니다.

4. 이동 테스트를 시작하려면 **확인**을 선택합니다. 진행률을 추적하려면 VM을 선택하여 해당 **속성**을 엽니다. 또는 자격 증명 모음에서 **테스트 장애 조치(failover)** 작업을 선택합니다. 그런 다음, **설정** > **작업** > **Site Recovery 작업**을 선택합니다.
5. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM이 실행 중이고, 규모가 적절히 조정되었으며, 적절한 네트워크에 연결되어 있는지 확인하세요.
6. 테스트용으로 만든 VM을 삭제하려면 복제된 항목에서 **테스트 장애 조치(failover) 정리**를 선택합니다. **메모**에서 테스트와 관련된 관찰 결과를 기록한 후 저장합니다.

## <a name="perform-the-move-and-confirm"></a>이동 수행 및 확인

1. **설정** > **복제된 항목**의 자격 증명 모음으로 이동한 후 가상 머신을 선택하고 **장애 조치(failover)** 를 선택합니다.
1. **장애 조치(failover)** 에서 **최신**을 선택합니다. 
2. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 원본 VM을 종료하려고 합니다. 그렇지만 종료가 실패하더라도 장애 조치(failover)는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
3. 작업이 완료되면 VM이 예상대로 대상 Azure 지역에 표시되는지 확인합니다.
4. **복제된 항목**에서 VM을 마우스 오른쪽 단추로 클릭하고 **커밋**을 선택합니다. 이렇게 하면 이동이 완료됩니다. 커밋 작업이 완료될 때까지 기다립니다.

## <a name="discard-the-resources-from-the-source-region"></a>원본 지역에서 리소스 삭제

- VM으로 이동한 후 **복제 사용 안 함**을 선택합니다. 그러면 VM에 대한 데이터 복사 프로세스가 중지됩니다.

  > [!IMPORTANT]
  > 이 단계를 완료하면 이동 후에 Site Recovery 복제에 대해 요금이 부과되지 않습니다.

원본 리소스를 다시 사용하지 않으려면 다음 단계를 수행합니다.

1. [원본 VM 준비](#prepare-the-source-vms)의 4단계에서 나열한 원본 지역의 모든 관련 네트워크 리소스를 삭제합니다.
2. 원본 Azure 지역에서 해당 스토리지 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure VM을 다른 Azure 지역으로 이동하는 방법을 살펴보았습니다. 이제 해당 VM에 대해 재해 복구를 구성할 수 있습니다.

> [!div class="nextstepaction"]
> [마이그레이션 후 재해 복구 설정](azure-to-azure-quickstart.md)
