---
title: CloudSimple 사설 클라우드를 통해 Azure VMware 솔루션 만들기
description: 작업 유연성과 연속성을 사용 하 여 VMware 워크 로드를 클라우드를 확장할 CloudSimple 사설 클라우드를 만드는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332905"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드 만들기

사설 클라우드를 만드는 다양 한 네트워크 인프라에 대 한 일반적인 요구 사항 해결할 수 있습니다.

* **증가**합니다. 기존 인프라에 대 한 하드웨어 새로 고침 지점에 도달 하면 한 사설 클라우드를 사용 하면 필요한 새 하드웨어 투자 없이 사용한 확장 수 있습니다.

* **확장 빠른**합니다. 발생 해야 하는 모든 임시 또는 계획 되지 않은 용량을 하는 경우 사설 클라우드를 사용 하면 지연 시간 없이 추가 용량을 만들 수 있습니다.

* **보호를 증가**합니다. 3 개 이상의 노드가의 사설 클라우드, 자동 중복성 및 고가용성 보호 얻을 수 있습니다.

* **장기 인프라에서 요구**합니다. 데이터 센터 용량에 또는 비용 절감을 재구성 하려는 경우 사설 클라우드를 사용 하면 데이터 센터를 사용 중지 하 고 클라우드 기반 솔루션을 유지 하면서 엔터프라이즈 작업 호환로 마이그레이션할 수 있습니다.

사설 클라우드를 만들 때 단일 vSphere 클러스터 및 모든 관리는 해당 클러스터에 만든 Vm 가져옵니다.

## <a name="before-you-begin"></a>시작하기 전에

사설 클라우드를 만들려면 먼저 노드를 프로 비전 해야 합니다.  노드를 프로 비전에 대 한 자세한 내용은 참조 하세요. [CloudSimple-Azure에서 VMware 솔루션에 대 한 노드를 프로 비전](create-nodes.md) 문서.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

액세스는 [CloudSimple 포털](access-cloudsimple-portal.md)합니다.

## <a name="create-a-new-private-cloud"></a>새 사설 클라우드 만들기

1. 에 **리소스** 페이지에서 클릭 **새 사설 클라우드**합니다.

    ![-사설 클라우드를 만들기 시작 하는 방법](media/create-pc-button.png)

2. 사설 클라우드 리소스를 호스트 위치를 선택 합니다.

3. 사설 클라우드를 위한 프로 비전 CS28 또는 CS36 노드 형식 you'ev를 선택 합니다. 후자는 최대 계산 및 메모리 용량을 포함합니다.

4. 사설 클라우드를 위한 노드 수를 선택 합니다. 선택할 수 있습니다 최대 사용 가능한 노드 수가 해당 you'ev [프로 비전](create-nodes.md)합니다.

    ![기본 설정-사설 클라우드 만들기](media/create-private-cloud-basic-info.png)

5. **다음: 고급 옵션**합니다.

6. VSphere/vSAN 서브넷의 CIDR 범위를 입력 합니다. 게이트웨이 서브넷 또는 온-프레미스 또는 다른 Azure 서브넷 (가상 네트워크)를 사용 하 여 CIDR 범위가 겹치지 않는지 확인 합니다.  Azure 가상 네트워크에 정의 된 모든 CIDR 범위를 사용 하지 마세요.
    
    **CIDR 범위 옵션:**  /24, / 23은, 롤아웃의 경우/22, 또는 /21 합니다. / 24 CIDR 범위/23은 최대 9 개의 노드를 지 원하는 최대 41 노드,는 롤아웃의 경우/22 및 /21 CIDR 범위 지원 CIDR 범위는 최대 64 개의 노드 (사설 클라우드에 있는 노드의 최대 수)를 지원 합니다.

    > [!CAUTION]
    > 사설 클라우드 인프라에서 사용 하 여 vSphere/vSAN CIDR 범위에 IP 주소가 예약 되어 있습니다.  모든 가상 머신에서이 범위의 IP 주소를 사용 하지 마십시오.

7. **다음: 검토 및 만들기**합니다.

8. 설정을 검토 합니다. 설정을 변경 해야 할 경우 클릭 **이전**합니다.

9. **만들기**를 클릭합니다.

프로 비전 하는 사설 클라우드를 클릭 하면 시작 됩니다 만듭니다.  진행률을 모니터링할 수 있습니다 [작업](https://docs.azure.cloudsimple.com/activity/#tasks) CloudSimple 포털의 페이지입니다.  프로 비전 2 시간 30 분 걸릴 수 있습니다.  프로 비전이 완료 되 면 전자 메일을 받게 됩니다.

## <a name="next-steps"></a>다음 단계

* [사설 클라우드를 확장 합니다.](expand-private-cloud.md)