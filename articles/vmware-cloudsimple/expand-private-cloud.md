---
title: CloudSimple 사설 클라우드를 통해 Azure의 VMware 솔루션 확장
description: 기존 또는 새 클러스터의 용량을 추가할 기존 CloudSimple 사설 클라우드를 확장 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332673"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드를 확장 합니다.

CloudSimple 사설 클라우드를 동적으로 확장 하는 유연성을 제공 합니다. 더 작은 구성으로 시작 하 고 더 많은 용량을 필요에 따라 다음 확장 수 있습니다. 또는 현재 요구에 따라 사설 클라우드를 만들고 소비 증가 함에 따라 다음 확장 수 있습니다.

사설 클라우드 하나 이상의 vSphere 클러스터로 구성 됩니다. 각 클러스터는 3 ~ 16 노드를 가질 수 있습니다.  사설 클라우드를 확장 하는 경우에 기존 클러스터에 노드 추가 또는 새 클러스터를 만듭니다. 기존 클러스터를 확장 하려면 추가 노드가 기존 노드와 동일한 유형 (SKU) 여야 합니다. 새 클러스터를 만들기 위해 노드는 다른 형식의 수 있습니다. 사설 클라우드 제한에 대 한 자세한 내용은 참조 제한에 대 한 섹션 [CloudSimple private cloud 개요](cloudsimple-private-cloud.md) 문서.

기본값을 사용 하 여 사설 클라우드 만들어집니다 **Datacenter** vCenter에서.  각 데이터 센터에서 최상위 관리 엔터티로 사용 됩니다.  새 클러스터 CloudSimple는 새 데이터 센터를 만들거나 기존 데이터 센터에 추가 선택을 제공 합니다.

새 클러스터 구성의 일부로, CloudSimple VMware 인프라를 구성합니다.  설정을은 vSAN 디스크 그룹, VMware 높은 가용성 및 분산 리소스 Scheduler (DRS)에 대 한 저장소 설정이 포함 합니다.

사설 클라우드는 여러 번 확장할 수 있습니다. 전체 노드 제한 내에서 유지 하는 경우에 확장을 수행할 수 있습니다. 사설 클라우드를 기존 클러스터에 추가 하거나 새로 만들 확장 될 때마다 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

사설 클라우드를 확장 하기 전에 노드를 프로 비전 해야 합니다.  노드를 프로 비전에 대 한 자세한 내용은 참조 하세요. [CloudSimple-Azure에서 VMware 솔루션에 대 한 노드를 프로 비전](create-nodes.md) 문서.  새 클러스터를 만들기 위해 동일한 SKU의 사용 가능한 노드가 3 개 이상 있어야 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="expand-a-private-cloud"></a>사설 클라우드를 확장 합니다.

1. [CloudSimple 포털에 액세스할](access-cloudsimple-portal.md)합니다.

2. 엽니다는 **리소스** 페이지를 확장 하려는 사설 클라우드를 선택 합니다.

3. 요약 섹션에서 클릭 **확장**합니다.

    ![사설 클라우드를 확장 합니다.](media/resources-expand-private-cloud.png)

4. 기존 클러스터를 확장 하거나 새 vSphere 클러스터를 만들 것인지 선택 합니다. 변경 하면 페이지의 요약 정보를 업데이트 됩니다.

    * 기존 클러스터를 확장 하려면 **기존 클러스터 확장**합니다. 확장 하 고 추가할 노드의 수를 입력 하려는 클러스터를 선택 합니다. 각 클러스터 노드를 16 개 까지가 있을 수 있습니다.
    * 새 클러스터를 추가 하려면 클릭 **새 클러스터 만들기**합니다. 클러스터의 이름을 입력합니다. 기존 데이터 센터를 선택 하거나 새 데이터 센터를 만들려면 이름을 입력 합니다. 노드 유형을 선택 합니다. 기존 vSphere 클러스터를 확장할 때 설치 되지 않지만 새 vSphere 클러스터를 만들 때 다른 노드 형식을 선택할 수 있습니다. 노드 수를 선택 합니다. 각 새 클러스터에 노드가 3 개 이상 있어야 합니다.

    ![사설 클라우드 확장-노드 추가](media/resources-expand-private-cloud-add-nodes.png)

5. 클릭 **제출** 사설 클라우드를 확장 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* 자세한 내용은 [사설 클라우드](cloudsimple-private-cloud.md)