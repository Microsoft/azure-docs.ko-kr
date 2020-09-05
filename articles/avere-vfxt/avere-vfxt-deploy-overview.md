---
title: 배포 개요 - Avere vFXT for Azure
description: 이 개요를 사용 하 여 Azure 용 Avere vFXT 클러스터를 배포 하는 방법을 알아봅니다. 관련 문서에는 특정 배포 지침이 있습니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88271229"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - 배포 개요

이 문서에서는 Avere vFXT for Azure 클러스터를 가동하고 실행하는 데 필요한 단계에 대해 간략히 설명합니다.

Azure Marketplace에서 vFXT 클러스터를 만들기 전과 후에 몇 가지 작업이 필요합니다. 시작부터 완료까지의 전체 프로세스를 명확히 이해하면 필요한 작업의 범위를 지정하는 데 도움이 됩니다.

## <a name="deployment-steps"></a>배포 단계

[시스템을 계획](avere-vfxt-deploy-plan.md)한 후에는 Avere vFXT 클러스터를 만들 수 있습니다.

Azure Marketplace의 Azure Resource Manager 템플릿은 필요한 정보를 수집하고 자동으로 전체 클러스터를 배포합니다.

VFXT 클러스터를 실행 한 후에는 사용 하기 전에 몇 가지 구성 단계를 수행 해야 합니다. 새 Blob 저장소 컨테이너를 만든 경우 데이터를 이동 하는 것이 좋습니다. NAS 저장소 시스템을 사용 하는 경우 클러스터를 만든 후에 추가 해야 합니다. 클라이언트를 클러스터에 연결 하는 것이 좋습니다.

이러한 모든 단계에 대한 개요는 다음과 같습니다.

1. 필수 조건 구성

   VM을 만들기 전에 Avere vFXT 프로젝트에 대한 새 구독을 만들고, 구독 소유권을 구성하고, 할당량을 확인하고, 필요한 경우 증가를 요청하고, Avere vFXT 소프트웨어 사용 약관에 동의해야 합니다. 자세한 지침은 [Avere vFXT 만들기 준비](avere-vfxt-prereqs.md)를 참조하세요.

1. Avere vFXT 클러스터 만들기

   Azure Marketplace를 사용하여 Azure 클러스터용 Avere vFXT를 만듭니다. 템플릿은 필요한 정보를 수집하고 최종 제품을 만드는 스크립트를 실행합니다.

   클러스터 만들기에는 다음 단계가 포함되며, 모든 단계는 마켓플레이스 템플릿에 의해 수행됩니다.

   * 필요한 경우 새 네트워크 인프라 및 리소스 그룹 만들기
   * 클러스터 컨트롤러 만들기

     클러스터 컨트롤러는 Avere vFXT 클러스터와 동일한 가상 네트워크에 상주하는 간단한 VM이며, 클러스터를 만들고 관리하는 데 필요한 사용자 지정 소프트웨어를 갖고 있습니다. 컨트롤러는 vFXT 노드를 만들고, 클러스터를 구성하며, 해당 수명 동안 클러스터를 관리하는 명령줄 인터페이스도 제공합니다.

     배포 하는 동안 새 가상 네트워크 또는 서브넷을 만드는 경우 컨트롤러는 공용 IP 주소를 갖게 됩니다. 즉, 컨트롤러는 가상 네트워크 외부에서 클러스터에 연결 하기 위한 점프 호스트로 사용할 수 있습니다.

   * 클러스터 노드 Vm 만들기

   * 개별 노드에서 클러스터 만들기

   * 필요에 따라 새 Blob 컨테이너를 만들고 클러스터의 백 엔드 저장소로 구성 합니다.

   클러스터 만들기에 대해서는 [vFXT 클러스터 배포](avere-vfxt-deploy.md)에 자세히 설명 되어 있습니다.

1. 클러스터 구성

   Avere vFXT 구성 인터페이스(Avere 제어판)에 연결하여 클러스터 설정을 사용자 지정합니다. 지원 모니터링에 참여 하 고 하드웨어 저장소 또는 추가 Blob 컨테이너를 사용 하는 경우 저장소 시스템을 추가 합니다.

   * [vFXT 클러스터에 액세스](avere-vfxt-cluster-gui.md)
   * [지원 사용](avere-vfxt-enable-support.md)
   * [스토리지 구성](avere-vfxt-add-storage.md)(필요한 경우)

1. 클라이언트 탑재

   [Avere vFXT 클러스터 탑재](avere-vfxt-mount-clients.md)의 지침에 따라 부하 분산과 클라이언트 머신에서 클러스터를 탑재하는 방법을 알아봅니다.

1. 데이터 추가(필요한 경우)

   Avere vFXT는 확장 가능한 다중 클라이언트 캐시 이므로 새로운 백 엔드 저장소 컨테이너로 데이터를 이동 하는 가장 좋은 방법은 다중 클라이언트 다중 스레드 파일 복사 전략을 사용 하는 것입니다.

   작업 집합 데이터를 새 Blob 컨테이너 또는 다른 백 엔드 저장소 시스템으로 이동 해야 하는 경우 [vFXT 클러스터로 데이터 이동](avere-vfxt-data-ingest.md)의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

[Avere vFXT 만들기 준비](avere-vfxt-prereqs.md) 를 계속 하 여 필수 구성 요소 작업을 완료 합니다.
