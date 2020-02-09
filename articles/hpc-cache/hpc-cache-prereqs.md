---
title: Azure HPC 캐시 필수 조건
description: Azure HPC 캐시를 사용 하기 위한 필수 구성 요소
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 90b84d936bda4e3a974e60934e82ac6c3389d85a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645772"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 캐시의 필수 구성 요소

Azure Portal를 사용 하 여 새 Azure HPC 캐시를 만들기 전에 사용자 환경이 이러한 요구 사항을 충족 하는지 확인 합니다.

## <a name="azure-subscription"></a>Azure 구독

유료 구독을 권장 합니다.

> [!NOTE]
> GA 릴리스의 처음 몇 달 동안 Azure HPC 캐시 팀은 액세스 목록에 구독을 추가 해야 캐시 인스턴스를 만드는 데 사용할 수 있습니다. 이 절차를 수행 하면 각 고객이 해당 캐시에서 고품질의 응답성을 유지할 수 있습니다. 액세스를 요청 하려면 [이 양식을](https://aka.ms/onboard-hpc-cache) 작성 하세요.

## <a name="network-infrastructure"></a>네트워크 인프라

캐시를 사용 하려면 먼저 두 개의 네트워크 관련 필수 구성 요소를 설정 해야 합니다.

* Azure HPC 캐시 인스턴스의 전용 서브넷
* 캐시에서 저장소 및 기타 리소스에 액세스할 수 있도록 DNS 지원

### <a name="cache-subnet"></a>캐시 서브넷

Azure HPC 캐시에는 다음과 같은 품질의 전용 서브넷이 필요 합니다.

* 서브넷은 사용 가능한 IP 주소가 64 이상 이어야 합니다.
* 서브넷은 클라이언트 컴퓨터와 같은 관련 서비스에 대해서도 다른 Vm을 호스트할 수 없습니다.
* 여러 Azure HPC 캐시 인스턴스를 사용 하는 경우 각각에는 자체 서브넷이 필요 합니다.

가장 좋은 방법은 각 캐시에 대 한 새 서브넷을 만드는 것입니다. 캐시를 만드는 과정의 일부로 새 가상 네트워크 및 서브넷을 만들 수 있습니다.

### <a name="dns-access"></a>DNS 액세스

캐시는 가상 네트워크 외부의 리소스에 액세스 하는 데 DNS가 필요 합니다. 사용 중인 리소스에 따라 사용자 지정 된 DNS 서버를 설정 하 고 해당 서버와 Azure DNS 서버 간에 전달을 구성 해야 할 수 있습니다.

* Azure Blob storage 엔드포인트 및 기타 내부 리소스에 액세스 하려면 Azure 기반 DNS 서버가 필요 합니다.
* 온-프레미스 저장소에 액세스 하려면 저장소 호스트 이름을 확인할 수 있는 사용자 지정 DNS 서버를 구성 해야 합니다.

Blob 저장소에만 액세스 해야 하는 경우에는 캐시에 대 한 기본 Azure 제공 DNS 서버를 사용할 수 있습니다. 그러나 다른 리소스에 액세스 해야 하는 경우에는 사용자 지정 DNS 서버를 만들고 Azure DNS 서버에 대 한 모든 Azure 관련 확인 요청을 전달 하도록 구성 해야 합니다.

또한 단순 DNS 서버를 사용 하 여 사용 가능한 모든 캐시 탑재 지점의 클라이언트 연결 부하를 분산할 수 있습니다.

Azure 가상 네트워크 [의 리소스에 대 한 이름 확인](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)의 azure virtual NETWORK 및 DNS 서버 구성에 대해 자세히 알아보세요.

## <a name="permissions"></a>권한

캐시 만들기를 시작 하기 전에 이러한 사용 권한 관련 필수 구성 요소를 확인 하십시오.

* 캐시 인스턴스는 가상 Nic (네트워크 인터페이스)를 만들 수 있어야 합니다. 캐시를 만드는 사용자에 게는 Nic를 만들기 위해 구독에서 충분 한 권한이 있어야 합니다.

* Blob storage를 사용 하는 경우 Azure HPC 캐시는 저장소 계정에 액세스 하기 위한 권한 부여가 필요 합니다. RBAC (역할 기반 액세스 제어)를 사용 하 여 Blob 저장소에 대 한 캐시 액세스를 제공 합니다. 저장소 계정 참가자 및 저장소 Blob 데이터 참가자 라는 두 개의 역할이 필요 합니다.

  [저장소 대상 추가](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 의 지침에 따라 역할을 추가 합니다.

## <a name="storage-infrastructure"></a>저장소 인프라

캐시는 Azure Blob 컨테이너 또는 NFS 하드웨어 저장소 내보내기를 지원 합니다. 캐시를 만든 후 저장소 대상을 추가 합니다.

각 저장소 유형에는 특정 필수 구성 요소가 있습니다.

### <a name="nfs-storage-requirements"></a>NFS 저장소 요구 사항

온-프레미스 하드웨어 저장소를 사용 하는 경우 캐시에는 서브넷의 데이터 센터에 대 한 고대역폭 네트워크 액세스 권한이 있어야 합니다. [Express](https://docs.microsoft.com/azure/expressroute/) 경로 또는 유사한 액세스를 사용 하는 것이 좋습니다.

NFS 백 엔드 저장소는 호환 되는 하드웨어/소프트웨어 플랫폼 이어야 합니다. 자세한 내용은 Azure HPC 캐시 팀에 문의 하세요.

### <a name="blob-storage-requirements"></a>Blob 저장소 요구 사항

캐시에서 Azure Blob 저장소를 사용 하려면 [Azure blob storage로 데이터 이동](hpc-cache-ingest.md)에서 설명한 대로 호환 되는 저장소 계정 및 빈 Blob 컨테이너 또는 Azure HPC 캐시 형식의 데이터로 채워진 컨테이너가 필요 합니다.

저장소 대상 추가를 시도 하기 전에 계정을 만드세요. 대상을 추가할 때 새 컨테이너를 만들 수 있습니다.

호환 되는 저장소 계정을 만들려면 다음 설정을 사용 합니다.

* 성능: **표준**
* 계정 종류: **StorageV2 (범용 v2)**
* 복제: **LRS (로컬 중복 저장소)**
* 액세스 계층 (기본값): **핫**

캐시와 동일한 위치에 있는 저장소 계정을 사용 하는 것이 좋습니다.
<!-- clarify location - same region or same resource group or same virtual network? -->

또한 위의 [권한](#permissions)에서 설명한 대로 Azure storage 계정에 대 한 캐시 응용 프로그램 액세스 권한을 부여 해야 합니다. [저장소 대상 추가](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 의 절차에 따라 캐시에 필요한 액세스 역할을 제공 합니다. 저장소 계정 소유자가 아닌 경우 소유자가이 단계를 수행 하도록 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal에서 [AZURE HPC 캐시 인스턴스를 만듭니다](hpc-cache-create.md) .
