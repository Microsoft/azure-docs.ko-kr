---
title: Azure Site Recovery의 물리적 서버 재해 복구 아키텍처
description: 이 문서에서는 Azure Site Recovery 서비스를 사용하여 온-프레미스 물리적 서버를 Azure로 재해 복구할 때 사용되는 구성 요소 및 아키텍처 개요를 제공합니다.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 089d981284986a2b6eb0ee7f1dbd401fc7ce4fcd
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162840"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>물리적 서버에서 Azure로의 재해 복구 아키텍처

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 사이트와 Azure 간에 물리적 Windows 및 Linux 서버를 복제, 장애 조치 및 복구할 때 사용되는 아키텍처와 프로세스에 대해 설명합니다.

## <a name="architectural-components"></a>아키텍처 구성 요소

다음 표와 그래픽은 물리적 서버를 Azure로 복제 하는 데 사용 되는 구성 요소에 대 한 개략적인 보기를 제공 합니다.

| **구성 요소** | **요구 사항** | **세부 정보** |
| --- | --- | --- |
| **Azure** | Azure 구독 및 Azure 네트워크. | 온-프레미스 물리적 컴퓨터의 복제 된 데이터는 Azure managed disks에 저장 됩니다. 장애 조치를 온-프레미스에서 Azure로 실행할 때 복제된 데이터를 사용하여 Azure VM을 만듭니다. Azure VM을 만들 때 Azure 가상 네트워크에 연결합니다. |
| **프로세스 서버** | 기본적으로 구성 서버와 함께 설치됩니다. | 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure Storage로 전송합니다.<br/><br/> 프로세스 서버도 복제하려는 서버에 모바일 서비스를 설치합니다.<br/><br/> 배포가 늘어나면 프로세스 서버로 실행하는 별도의 프로세스 서버를 추가하여 더 큰 복제 트래픽을 처리할 수 있습니다. |
| **마스터 대상 서버** | 기본적으로 구성 서버와 함께 설치됩니다. | Azure에서 장애 복구 중에 복제 데이터를 처리 합니다.<br/><br/> 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다. |
| **복제된 서버** | 복제하는 각 서버에 모바일 서비스가 설치됩니다. | 프로세스 서버에서 자동 설치를 허용하는 것이 좋습니다. 또는 서비스를 수동으로 설치 하거나 Configuration Manager와 같은 자동화 된 배포 방법을 사용할 수 있습니다. |

**물리적 서버에서 Azure로 아키텍처**

![구성 요소](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>복제 프로세스

1. 온-프레미스 및 Azure 구성 요소를 포함하여 배포를 설정합니다. Recovery Services 자격 증명 모음에서 복제 원본 및 대상을 지정하고 구성 서버를 설정하며 복제 정책을 만들고 모바일 서비스를 배포하며 복제를 사용하도록 설정합니다.
1. 컴퓨터는 복제 정책을 사용 하 여 복제 되 고 서버 데이터의 초기 복사본은 Azure storage로 복제 됩니다.
1. 초기 복제가 완료되면 Azure에 대한 델타 변경 내용의 복제가 시작됩니다. 컴퓨터에 대 한 추적 된 변경 내용은 _. hrl_ 확장명을 가진 파일에 저장 됩니다.
   - 컴퓨터는 복제 관리를 위해 HTTPS 포트 443 인바운드의 구성 서버와 통신 합니다.
   - 컴퓨터는 HTTPS 포트 9443 인바운드 (수정할 수 있음)의 프로세스 서버로 복제 데이터를 보냅니다.
   - 구성 서버는 HTTPS 포트 443 아웃 바운드를 통해 Azure를 사용 하 여 복제 관리를 오케스트레이션 합니다.
   - 프로세스 서버는 원본 컴퓨터에서 데이터를 수신 하 고,이를 최적화 및 암호화 하 고, HTTPS 포트 443 아웃 바운드를 통해 Azure storage로 보냅니다.
   - 다중 VM 일관성을 사용하도록 설정하면 복제 그룹의 컴퓨터는 20004 포트를 통해 서로 통신하게 됩니다. 다중 VM은 장애 조치 시(failover) 크래시 일관성 및 앱 일관성 복구 지점을 공유하는 복제 그룹에 여러 컴퓨터를 그룹화하는 경우 사용됩니다. 이러한 그룹은 컴퓨터가 동일한 워크 로드를 실행 하 고 일관성을 유지 해야 하는 경우에 유용 합니다.
1. 트래픽은 인터넷을 통해 Azure Storage 공용 엔드포인트에 복제됩니다. Azure ExpressRoute [공용 피어링](../expressroute/about-public-peering.md)을 사용할 수도 있습니다.

   > [!NOTE]
   > 온-프레미스 사이트 또는 Azure Express 경로 [개인 피어 링](concepts-expressroute-with-site-recovery.md#on-premises-to-azure-replication-with-expressroute)에서 사이트 간 VPN을 통해 복제가 지원 되지 않습니다.

**물리적 구성 요소에서 Azure로 복제 프로세스**

![복제 프로세스](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

복제를 설정한 후 재해 복구 훈련 (테스트 장애 조치)을 실행 하 여 모든 것이 예상 대로 작동 하는지 확인할 수 있습니다. 그런 다음 필요에 따라 장애 조치 (failover) 및 장애 복구를 수행할 수 있습니다. 다음 사항을 고려합니다.

- 계획된 장애 조치는 지원되지 않습니다.
- 온-프레미스 VMware VM으로 장애 복구 (failback)가 필요 합니다. 온-프레미스 물리적 서버를 Azure에 복제 하는 경우에도 온-프레미스 VMware 인프라가 필요 합니다.
- 단일 컴퓨터에 장애 조치를 수행하거나 복구 계획을 만들어서 여러 컴퓨터의 장애 조치합니다.
- 장애 조치를 실행하면 복제된 데이터에서 Azure VM이 Azure Storage에 만들어집니다.
- 초기 장애 조치 (failover)가 트리거되면 Azure VM에서 워크 로드에 대 한 액세스를 시작 하는 커밋을 커밋합니다.
- 기본 온-프레미스 사이트를 다시 사용할 수 있는 경우 장애 복구를 수행할 수 있습니다.
- 다음을 포함 하는 장애 복구 인프라를 설정 합니다.
  - **Azure의 임시 프로세스 서버**: Azure에서 장애 복구하려면 Azure에서 복제를 처리하는 프로세스 서버 역할을 하도록 Azure VM을 설정해야 합니다. 장애 복구 (failback)가 완료 되 면이 VM을 삭제할 수 있습니다.
  - **VPN 연결**: 장애 복구하려면 Azure 네트워크에서 온-프레미스 사이트로의 VPN 연결(또는 Azure ExpressRoute)이 필요합니다.
  - **별도의 마스터 대상 서버**: 기본적으로 장애 복구 (failback)는 온-프레미스 VMware VM의 구성 서버와 함께 설치 된 마스터 대상 서버에 의해 처리 됩니다. 대량의 트래픽을 장애 복구 해야 하는 경우 별도의 온-프레미스 마스터 대상 서버를 설정 해야 합니다.
  - **장애 복구 정책**: 온-프레미스 사이트에 다시 복제하려면 장애 복구 정책이 필요합니다. 이 정책은 온-프레미스에서 Azure로 복제 정책을 만들 때 자동으로 생성 됩니다.
  - **Vmware infrastructure**: 장애 복구 하려면 vmware 인프라가 필요 합니다. 실제 서버로 장애 복구할 수 없습니다.
- 구성 요소가 준비 되 면 다음 세 단계로 장애 복구 (failback)가 수행 됩니다.
  - **1 단계**: azure vm을 다시 보호 하 여 azure에서 온-프레미스 VMware vm으로 다시 복제 합니다.
  - **2 단계**: 온-프레미스 사이트로 장애 조치 (failover)를 실행 합니다.
  - **3 단계**: 워크 로드가 장애 복구 된 후 복제를 다시 활성화 합니다.

**Azure로부터 VMware 장애 복구**

![장애 복구](./media/physical-azure-architecture/enhanced-failback.png)

## <a name="next-steps"></a>다음 단계

Azure에 대 한 물리적 서버 재해 복구를 설정 하려면 [방법 가이드](physical-azure-disaster-recovery.md)를 참조 하세요.
