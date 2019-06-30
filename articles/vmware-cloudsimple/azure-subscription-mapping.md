---
title: Azure 구독 CloudSimple 하 여 Azure VMware 솔루션에서 리소스 풀에 매핑
description: Azure 구독에 Azure CloudSimple VMware 솔루션에서 리소스 풀을 매핑하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332907"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Azure 구독에 맵 리소스 풀에서 사설 클라우드

매핑 azure 구독을 사용 하면 사설 클라우드 vCenter에서 Azure 구독에 리소스 풀을 매핑할 수 있습니다. CloudSimple 서비스를 만든 구독에만 매핑할 수 있습니다.  Azure portal에서 VMware 가상 머신을 만들 매핑된 리소스 풀의 가상 머신을 배포 합니다.  CloudSimple 포털에서 볼 수 있으며 사설 클라우드에 대 한 Azure 구독을 관리할 수 있습니다.

구독 사설 클라우드의 여러 vCenter 리소스 풀에 매핑할 수 있습니다.  각 사설 클라우드에 리소스 풀을 매핑할 해야 합니다.  매핑된 리소스 풀만 Azure 포털에서 VMware 가상 컴퓨터를 만들기 위한 사용할 수 있습니다.

> [!IMPORTANT]
> 모든 자식 리소스 풀을 매핑하여 리소스 풀 매핑. 모든 자식 리소스 풀이 이미 매핑된 경우에 부모 리소스 풀을 매핑할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 구독에 있는 CloudSimple 서비스 및 사설 클라우드를 있다고 가정 합니다.  참조 CloudSimple 서비스를 만들려면 [빠른 시작-서비스를 만들](quickstart-create-cloudsimple-service.md)합니다.  사설 클라우드를 만들어야 하는 경우 참조 [빠른 시작-사설 클라우드 환경을 구성](quickstart-create-private-cloud.md)합니다.

구독에 vCenter 클러스터 (루트 리소스 풀)에 매핑할 수 있습니다.  새 리소스 풀을 만들려는 경우 참조 [리소스 풀 만들기](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) VMware 설명서 사이트 문서.

## <a name="default-resource-group"></a>기본 리소스 그룹

Azure portal에서 새 CloudSimple 가상 컴퓨터를 만드는 리소스 그룹을 선택할 수 있습니다.  매핑된 리소스 풀에서 사설 클라우드 vCenter에서 만든 가상 머신이 Azure 포털에서 볼 수 있습니다.  검색 된 가상 컴퓨터는 기본 Azure 리소스 그룹에 배치 됩니다.  기본 리소스 그룹의 이름을 변경할 수 있습니다.

## <a name="map-azure-subscription"></a>Azure 구독에 매핑

1. 액세스는 [CloudSimple 포털](access-cloudsimple-portal.md)합니다.

2. 엽니다는 **리소스** 페이지 및 매핑하려는 사설 클라우드를 선택 합니다.

3. 선택 **Azure 구독 매핑**합니다.

4. 클릭 **편집 Azure 구독 매핑**합니다.

5. 사용 가능한 리소스 풀에 매핑하려면 왼쪽에서 선택 하 고 오른쪽 화살표를 클릭 합니다.

6. 매핑을 제거할 오른쪽에서 선택 하 고 왼쪽 화살표를 클릭 합니다.

    ![Azure 구독](media/resources-azure-mapping.png)

7. **확인**을 클릭합니다.

## <a name="change-default-resource-group-name"></a>기본 리소스 그룹 이름 변경

1. 액세스는 [CloudSimple 포털](access-cloudsimple-portal.md)합니다.

2. 엽니다는 **리소스** 페이지 및 매핑하려는 사설 클라우드를 선택 합니다.

3. 선택 **Azure 구독 매핑**합니다.

4. 클릭 **편집** Azure 리소스 그룹 이름입니다.

    ![리소스 그룹 이름 편집](media/resources-edit-resource-group-name.png)

5. 리소스 그룹에 대 한 새 이름을 입력 하 고 클릭 **제출**합니다.

    ![새 리소스 그룹 이름 입력](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* 자세한 내용은 [CloudSimple 가상 컴퓨터](cloudsimple-virtual-machines.md)