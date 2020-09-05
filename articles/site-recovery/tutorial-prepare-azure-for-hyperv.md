---
title: Azure Site Recovery를 사용하여 Hyper-V 재해 복구를 위한 Azure 준비
description: Azure Site Recovery를 사용하여 온-프레미스 Hyper-V VM 재해 복구를 위해 Azure를 준비하는 방법 알아보기
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: f4f040288574a9ddfc5f7c56878c7f1e795b09a3
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653854"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>Hyper-V 재해 복구를 위한 Azure 리소스 준비

 [Azure Site Recovery](site-recovery-overview.md)는 계획적 및 비계획적 작동 중단 동안 비즈니스 앱이 계속 실행되도록 하여 BCDR(비즈니스 연속성 및 재해 복구)를 지원합니다. Site Recovery는 복제, 장애 조치(failover), 복구를 포함하여 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 재해 복구를 오케스트레이션합니다.

이 자습서는 온-프레미스 Hyper-V VM에 대한 재해 복구를 설정하는 방법을 설명하는 시리즈 중 첫 번째입니다.

> [!NOTE]
> 시나리오의 가장 간단한 배포 경로를 보여주도록 자습서를 디자인했습니다. 이러한 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로를 보여주지는 않습니다. 자세한 내용은 각 해당 시나리오에 대한 "방법" 섹션을 참조하세요.

이 자습서에서는 온-프레미스 VM(Hyper-V)을 Azure에 복제하려는 경우 Azure 구성 요소를 준비하는 방법을 설명합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure 계정에 복제 권한이 있는지 확인합니다.
> * 복제된 컴퓨터의 이미지를 저장하는 Azure Storage 계정을 만듭니다.
> * VM 및 기타 복제 구성 요소에 대한 메타데이터 및 구성 정보를 저장하는 Recovery Services 자격 증명을 만듭니다.
> * Azure 네트워크 설정 장애 조치(failover) 후 만들어지는 Azure VM이 이 네트워크에 연결됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="sign-in"></a>로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="verify-account-permissions"></a>계정 권한 확인

Azure 체험 계정을 방금 만든 경우 본인이 해당 구독의 관리자가 됩니다. 관리자가 아닌 경우에는 관리자와 협력하여 필요한 권한을 할당받습니다. 새 가상 머신에 대한 복제를 사용하려면 다음 작업을 수행할 권한이 있어야 합니다.

- 선택한 리소스 그룹에 VM 만들기
- 선택한 가상 네트워크에 VM 만들기
- 선택한 스토리지 계정에 쓰기

이러한 작업을 완료하려면 계정에 가상 머신 참가자 기본 제공 역할이 할당되어야 합니다. 자격 증명 모음에서 Site Recovery 작업을 관리하려면 계정에 Site Recovery 참가자 기본 제공 역할이 할당되어야 합니다.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

복제된 컴퓨터의 이미지는 Azure Storage에 유지됩니다. 온-프레미스에서 Azure로 장애 조치할 때 스토리지에서 Azure VM이 만들어집니다. 스토리지 계정은 Recovery Services 자격 증명 모음과 동일한 영역에 있어야 합니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **리소스 만들기** > **스토리지** > **스토리지 계정 - BLOB, 파일, 테이블, 큐**를 선택합니다.
2. **스토리지 계정 만들기**에서 계정의 이름을 입력합니다.  선택한 이름은 Azure 내에서 고유해야 하고, 3 ~ 24자 길이이고, 소문자와 숫자만 사용해야 합니다. 이 자습서에서는 **contosovmsacct1910171607**을 사용합니다.
3. **배포 모델**에서 **Resource Manager**를 선택합니다.
4. **계정 종류**에서 **스토리지(범용 v1)** 를 선택합니다. Blob Storage를 선택하지 마세요.
5. **복제**에서 스토리지 중복에 대해 기본 **읽기 액세스 지역 중복 스토리지**를 선택합니다. 보안 전송 필요 설정을 사용 안 함 상태로 둡니다.
6. **성능**에서 **표준**을 선택합니다. 다음으로, **액세스 계층**에서 기본 옵션인 **핫 계층**을 선택합니다.
7. **구독**에서 새 스토리지 계정을 만들려는 구독을 선택합니다.
8. **리소스 그룹**에서 새 리소스 그룹을 입력합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 이 자습서에서는 **ContosoRG**를 사용합니다.
9. **위치**에서 스토리지 계정에 대한 지리적 위치를 선택합니다. 이 자습서에서는 **서유럽**를 사용합니다.
10. **만들기**를 선택하여 스토리지 계정을 만듭니다.

   ![스토리지 계정 만들기 옵션의 스크린샷](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>복구 서비스 자격 증명 모음 만들기

1. Azure Portal에서 **+리소스 만들기**를 선택하고, Azure Marketplace에서 Recovery Services를 검색합니다.
2. **Backup 및 Site Recovery(OMS)** 를 선택합니다. 다음으로, **Backup 및 Site Recovery** 페이지에서 **만들기**를 선택합니다.
1. **Recovery Services 자격 증명 모음 > 이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이 자습서에서는 **ContosoVMVault**가 사용됩니다.
2. 기존 **리소스 그룹**을 선택하거나 새 리소스 그룹을 만듭니다. 이 자습서에서는 **contosoRG**를 사용합니다.
3. **위치**에서 자격 증명 모음을 배치할 지역을 선택합니다. 이 자습서에서는 **서유럽**를 사용합니다.
4. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 **대시보드에 고정** > **만들기**를 선택합니다.

![Recovery Services 자격 증명 모음 만들기 페이지의 스크린샷](./media/tutorial-prepare-azure/new-vault-settings.png)

새 자격 증명 모음이 **대시보드** > **모든 리소스** 및 주 **Recovery Services 자격 증명 모음** 페이지에 표시됩니다.

## <a name="set-up-an-azure-network"></a>Azure 네트워크를 설정합니다

장애 조치(failover) 후 스토리지에서 만들어지는 Azure VM이 이 Azure 네트워크에 연결됩니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 선택합니다. 배포 모델로 선택되어 있는 Resource Manager를 그대로 둡니다.
2. **이름**에 네트워크 이름을 입력합니다. 이름은 Azure 리소스 그룹 내에서 고유해야 합니다. 이 자습서에서는 **ContosoASRnet**을 사용합니다.
3. 네트워크를 만들 리소스 그룹을 지정합니다. 이 자습서에서는 기존 리소스 그룹인 **contosoRG**를 사용합니다.
4. **주소 범위**에 네트워크 범위로 **10.0.0.0/24**를 입력합니다. 이 네트워크의 서브넷은 없습니다.
5. **구독**에서 네트워크를 만드는 데 사용할 구독을 선택합니다.
6. **위치**에서 **서유럽**를 선택합니다. 네트워크는 Recovery Services 자격 증명 모음과 동일한 지역에 있어야 합니다.
7. 네트워크에 서비스 엔드포인트가 없는 기본 DDoS 보호의 기본 옵션을 그대로 둡니다.
8. **만들기**를 선택합니다.

![가상 네트워크 만들기 옵션의 스크린샷](media/tutorial-prepare-azure/create-network.png)

가상 네트워크를 만드는 데 몇 초 정도 걸립니다. 가상 네트워크가 만들어지면 Azure Portal 대시보드에 표시됩니다.

## <a name="useful-links"></a>유용한 링크

자세한 정보:
- [Azure 네트워크](../virtual-network/virtual-networks-overview.md)
- [관리되는 디스크](../virtual-machines/managed-disks-overview.md)



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 재해 복구를 위한 온-프레미스 Hyper-V 인프라 준비](hyper-v-prepare-on-premises-tutorial.md)
