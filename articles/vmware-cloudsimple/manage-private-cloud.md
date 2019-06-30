---
title: CloudSimple 사설 클라우드를 통해 Azure의 VMware 솔루션 관리
description: CloudSimple 사설 클라우드 리소스 및 작업 관리에 사용 가능한 기능을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333156"
---
# <a name="manage-private-cloud-resources-and-activity"></a>사설 클라우드 리소스 및 작업 관리

사설 클라우드는 CloudSimple 포털에서 관리 됩니다.  사설 클라우드 및 CloudSimple 포털에서 다른 설정에는 활동 상태, 사용 가능한 리소스를 확인 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

액세스는 [CloudSimple 포털](access-cloudsimple-portal.md)합니다.

## <a name="view-the-list-of-private-clouds"></a>사설 클라우드 목록 보기

**사설 클라우드** 탭의 **리소스** 페이지는 구독에서 모든 사설 클라우드를 나열 합니다. VSphere 수가 클러스터, 위치, 사설 클라우드 및 리소스 정보가 현재 상태, 정보 이름을 포함 합니다.

![사설 클라우드 페이지](media/manage-private-cloud.png)

추가 정보 및 작업에 대 한 사설 클라우드를 선택 합니다.

## <a name="private-cloud-summary"></a>사설 클라우드 요약

선택한 사설 클라우드의 포괄적인 요약을 봅니다.  요약 페이지에서 사설 클라우드 배포 DNS 서버를 포함 합니다.  사설 클라우드의 DNS 서버에 전달 하는 온-프레미스 DNS 서버에서 DNS를 설정할 수 있습니다.  DNS 전달에 대 한 자세한 내용은 참조 하세요. [온-프레미스에서 사설 클라우드 vCenter에 대 한 이름 확인에 대 한 DNS 구성](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)합니다.

![사설 클라우드 요약](media/private-cloud-summary.png)

### <a name="available-actions"></a>사용 가능한 작업

* [VSphere 클라이언트를 시작](https://docs.azure.cloudsimple.com/vsphere-access/)합니다. 이 사설 클라우드에 대 한 vCenter 액세스 합니다.
* [노드를 구입](create-nodes.md)합니다. 이 사설 클라우드 노드를 추가 합니다.
* [확장](expand-private-cloud.md)합니다. 이 사설 클라우드 노드를 추가 합니다.
* **새로 고침**합니다. 이 페이지의 정보를 업데이트 합니다.
* **삭제**. 언제 든 지 사설 클라우드를 삭제할 수 있습니다. **를 삭제 하기 전에 모든 시스템 및 데이터를 백업 해야 합니다.** 사설 클라우드를 삭제 하면 모든 Vm을 vCenter 구성 및 데이터를 삭제 합니다. 클릭 **삭제** 선택한 사설 클라우드를 위한 요약 섹션에 있습니다. 삭제, 다음 모든 사설 클라우드 데이터를 안전 하 고 항상 규격 지우기 프로세스에서 지워집니다.
* [VSphere 권한을 변경](escalate-private-cloud-privileges.md)합니다.  이 사설 클라우드에서 귀하의 권한을 에스컬레이션 합니다.

## <a name="private-cloud-vlanssubnets"></a>사설 클라우드 VLAN/서브넷

선택한 사설 클라우드를 위한 정의 된 Vlan/서브넷 목록을 봅니다.  Vlan/서브넷에 만든 사설 클라우드를 만들 때 관리 목록에 포함 됩니다.

![사설 클라우드-Vlan/서브넷](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>사용 가능한 작업

* [VLAN/서브넷 추가](https://docs.azure.cloudsimple.com/create-vlan-subnet/)합니다. 이 사설 클라우드에 VLAN/하위 집합을 추가 합니다.

다음 작업에 대해 VLAN/서브넷 선택
* [방화벽 테이블 연결](https://docs.azure.cloudsimple.com/firewall/)합니다. 이 사설 클라우드를 방화벽 테이블을 연결 합니다.
* **편집**
* **삭제** (사용자 정의 Vlan/서브넷)

## <a name="private-cloud-activity"></a>사설 클라우드 활동

선택한 사설 클라우드를 위한 다음 정보를 봅니다.  작업 정보에는 선택한 사설 클라우드를 위한 모든 작업의 필터링 된 목록입니다.  이 페이지는 최대 25 개의 최근 활동을 표시합니다.

* 최근 경고
* 최근 이벤트
* 최근 태스크
* 최근 감사

![사설 클라우드-작업](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>클라우드 랙

랙 클라우드는 사설 클라우드의 구성 요소. 각 랙 용량 단위를 제공합니다. CloudSimple 만들거나 사설 클라우드를 확장 하는 경우 선택한 항목 기반 클라우드 랙을 자동으로 구성 합니다.  클라우드 랙의 전체 목록을 보려면에 할당 된 각 사설 클라우드를 포함 합니다.

![사설 클라우드-클라우드 랙](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere Management Network

VMware 관리 리소스 및 사설 클라우드에 현재 구성 된 virtual machines의 목록입니다. 소프트웨어 버전, 정규화 된 도메인 이름 (FQDN) 및 리소스의 IP 주소 정보에 포함 됩니다.

![사설 클라우드-vSphere 관리 네트워크](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* 자세한 내용은 [사설 클라우드](cloudsimple-private-cloud.md)