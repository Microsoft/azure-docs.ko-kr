---
title: CloudSimple 사설 클라우드 별 Azure VMware 솔루션
description: 완전 한 VMware 운영 연속성, 기존 도구, 기술 및 프로세스와의 호환성을 비롯 한 CloudSimple 개념 및 장점에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140720"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple 사설 클라우드 개요

CloudSimple 변환은 몇 분 안에 VMware 워크 로드를 공용 클라우드로 확장 합니다. CloudSimple 서비스를 사용 하 여 Azure 운영 체제 미 설치 인프라에 VMware를 기본적으로 배포할 수 있습니다. 배포는 Azure 위치에 상주 하며 Azure 클라우드의 나머지 부분과 완전히 통합 됩니다.

CloudSimple 솔루션은 완전 한 VMware 운영 연속성을 제공 합니다. 이 솔루션은 다음의 공용 클라우드 혜택을 제공 합니다.

* 탄력성
* 혁신
* 효율성

CloudSimple을 사용 하면 총 소유 비용을 줄일 수 있는 클라우드 소비 모델을 활용할 수 있습니다. 또한 주문형 프로 비전, 종 량 제 및 용량 최적화를 제공 합니다.

CloudSimple은와 완전히 호환 됩니다.

* 기존 도구
* 기술
* 프로세스

이러한 호환성을 통해 팀은 이러한 유형의 정책을 방해 하지 않고 Azure 클라우드에서 워크 로드를 관리할 수 있습니다.

* 네트워크
* 보안  
* 데이터 보호  
* 감사

CloudSimple은 인프라와 필요한 모든 네트워킹 및 관리 서비스를 관리 합니다. CloudSimple 서비스를 사용 하면 팀이 다음에 집중할 수 있습니다.

* 비즈니스 가치
* 애플리케이션 프로비저닝
* 비즈니스 연속성
* 고객 지원팀
* 정책 적용

## <a name="private-cloud-environment-overview"></a>사설 클라우드 환경 개요

사설 클라우드는 다음을 지 원하는 격리 된 VMware 스택입니다.

* ESXi 호스트
* vCenter
* vSAN
* NSX

사설 클라우드는 CloudSimple 포털을 통해 관리 됩니다. 자체 관리 도메인에 자체 vCenter 서버가 있습니다.

스택은 다음 위치에서 실행 됩니다.

* 전용 노드
* 격리 된 운영 체제 미 설치 하드웨어 노드

사용자는 다음을 포함 하 여 네이티브 VMware 도구를 통해 스택을 사용 합니다.

* vCenter
* NSX 관리자

Azure 위치에 전용 노드를 배포할 수 있습니다. 그런 다음 Azure 및 CloudSimple을 사용 하 여 관리할 수 있습니다. 사설 클라우드는 하나 이상의 vSphere 클러스터로 구성 되며 각 클러스터에는 3 ~ 16 개의 노드가 포함 됩니다.

구매한, 종 량 제 노드 또는 예약 된 전용 노드를 사용 하 여 사설 클라우드를 만들 수 있습니다.

다음 연결을 사용 하 여 사설 클라우드를 온-프레미스 환경 및 Azure 네트워크에 연결할 수 있습니다.

* 보안
* 개인 VPN
* Azure ExpressRoute

사설 클라우드 환경은 단일 실패 지점이 제거 되도록 설계 되었습니다.

* ESXi 클러스터는 vSphere 고가용성으로 구성 되며 복원 력을 위한 예비 노드가 하나 이상 있도록 크기가 지정 됩니다.
* vSAN은 중복 된 기본 저장소를 제공 합니다. vSan에는 단일 오류에 대 한 보호를 제공 하기 위해 3 개 이상의 노드가 필요 합니다. 대량 클러스터에 대해 더 높은 복원 력을 제공 하도록 vSAN을 구성할 수 있습니다.
* 저장소 오류 로부터 보호 하기 위해 RAID 10 저장소 정책을 사용 하 여 vCenter, PSC 및 NSX Manager Vm을 구성할 수 있습니다. vSphere HA는 노드 및 네트워크 오류 로부터 보호 합니다.

## <a name="scenarios-for-deploying-a-private-cloud"></a>사설 클라우드 배포 시나리오

사설 클라우드 배포에 대 한 몇 가지 예제 사용 사례는 다음과 같습니다.

### <a name="data-center-retirement-or-migration"></a>데이터 센터 사용 중지 또는 마이그레이션

* 기존 데이터 센터의 제한에 도달 하거나 하드웨어를 새로 고칠 때 추가 용량을 확보 하세요.
* 클라우드에 필요한 용량을 추가 하 고 하드웨어 새로 고침을 관리 하는 문제를 제거 합니다.
* 시간이 많이 걸리는 변환 또는 재 아키텍처와 비교 하 여 클라우드 마이그레이션의 위험 및 비용을 줄입니다.
* 익숙한 VMware 도구와 기술을 사용 하 여 클라우드 마이그레이션을 가속화 하세요. 클라우드에서 Azure 서비스를 사용 하 여 응용 프로그램의 속도를 현대화.

### <a name="expand-on-demand"></a>요청 시 확장

* 새 개발 환경 또는 계절 용량 버스트와 같은 예기치 않은 요구를 충족 하기 위해 클라우드로 확장 합니다.
* 필요할 때 새 용량을 만들고 필요한 만큼만 유지 합니다.
* 온-프레미스와 클라우드 둘 다에서 동일한 아키텍처 및 정책을 사용 하 여 선불 투자를 줄이고 프로 비전 속도를 가속화 하 고 복잡성을 줄입니다.

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 클라우드의 재해 복구 및 가상 데스크톱

* Azure 클라우드에서 데이터, 앱 및 데스크톱에 대 한 원격 액세스를 설정 합니다. 고대역폭 연결을 사용 하는 경우 데이터를 신속 하 게 업로드/다운로드 하 여 인시던트를 복구 합니다. 대기 시간이 짧은 네트워크는 사용자가 데스크톱 앱에서 요구 하는 빠른 응답 시간을 제공 합니다.

* CloudSimple 포털 및 익숙한 VMware 도구를 사용 하 여 클라우드의 모든 정책과 네트워킹을 복제 합니다. 복제는 DR 및 VDI 구현을 만들고 관리 하는 작업 및 위험을 줄여 줍니다.

### <a name="high-performance-applications-and-databases"></a>고성능 응용 프로그램 및 데이터베이스

* CloudSimple로 제공 되는 하이퍼 수렴 형 아키텍처를 사용 하 여 가장 까다로운 워크 로드를 실행 합니다.
* Oracle, Microsoft SQL server, 미들웨어 시스템 및 고성능의 SQL 데이터베이스를 실행 합니다.
* 고속 25gbps 네트워크 연결을 사용 하 여 클라우드를 고유한 데이터 센터로 경험해 보세요. 고속 연결을 사용 하면 성능을 저하 시 키 지 않고 온-프레미스, Azure의 VMware 및 Azure 개인 워크 로드에 걸쳐 있는 하이브리드 앱을 실행할 수 있습니다.

### <a name="true-hybrid"></a>진정한 하이브리드

* VMware와 Azure 서비스 간에 DevOps를 통합 합니다.
* 모든 워크 로드에서 적용할 수 있는 Azure 서비스 및 솔루션에 대 한 VMware 관리를 최적화 합니다.
* 데이터 센터를 확장 하거나 응용 프로그램을 다시 설계 하지 않고도 공용 클라우드 서비스에 액세스할 수 있습니다.
* Azure의 VMware 응용 프로그램에 대 한 id, 액세스 제어 정책, 로깅 및 모니터링을 중앙화 합니다.

## <a name="limits"></a>제한

다음 표에서는 사설 클라우드의 리소스에 대 한 노드 제한을 나열 합니다.

| 리소스 | 제한 |
|----------|-------|
| 사설 클라우드를 만들기 위한 최소 노드 수 | 3 |
| 사설 클라우드의 클러스터에 있는 최대 노드 수 | 16 |
| 사설 클라우드의 최대 노드 수 | 64 |
| 새 클러스터의 최소 노드 수 | 3 |

## <a name="next-steps"></a>다음 단계

* [사설 클라우드를 만드는](create-private-cloud.md) 방법 알아보기
* [사설 클라우드 환경을 구성](quickstart-create-private-cloud.md) 하는 방법 알아보기
