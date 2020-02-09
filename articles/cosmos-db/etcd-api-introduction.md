---
title: Azure Cosmos DB etcd API 소개
description: 이 문서에서는 Azure Cosmos DB의 etcd API의 개요 및 키 혜택을 제공
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205799"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API (미리 보기) 소개

Azure Cosmos DB는 업무용 응용 프로그램에 대한 Microsoft의 전역적으로 분산 된 다중 모델 데이터베이스 서비스입니다. 저장소, 99 번째 백분위 수에서 1 자리 밀리초 대기 시간 및 처리량의 탄력적인 확장 턴키 전역 배포을 제공 하 고 보장 된 고가용성, 업계 최고의 SLA로 지원된 합니다.

[Etcd](https://github.com/etcd-io/etcd) 분산된 키/값 저장소입니다. [Kubernetes](https://kubernetes.io/), etcd 상태 및 Kubernetes 클러스터의 구성이 저장 됩니다. 가용성, 안정성 및 etcd의 성능을 보장 하는 것은 전반적인 클러스터 상태, 확장성, 탄력성 가용성 및 Kubernetes 클러스터의 성능에 중요 합니다. 

Azure Cosmos DB의 etcd API에 대한 백 엔드 저장소로 Azure Cosmos DB를 사용할 수 있습니다 [Azure Kubernetes](../aks/index.yml)합니다. Azure Cosmos DB의 etcd API는 현재 미리 보기로 제공됩니다. Azure Cosmos DB etcd 유선 프로토콜을 구현합니다. Etcd API에서 Azure Cosmos DB를 사용 하 여 개발자가 자동으로 받습니다 매우 안정적 [사용 가능한](high-availability.md)를 [전역적으로 분산 된](distribute-data-globally.md) Kubernetes 합니다. 이 API는 완전 관리형된 클라우드 네이티브 PaaS 서비스에서 상태 관리 Kubernetes를 확장 하는 개발자를 수 있습니다. 

> [!NOTE]
> Azure Cosmos DB의 다른 Api와 달리 Azure portal, CLI 또는 Sdk를 통해 etcd API 계정을 프로 비전 할 수 없습니다. 만 사용 합니다; Resource Manager 템플릿을 배포 하 여 etcd API 계정을 프로 비전 할 수 자세한 단계를 참조 하세요 [Azure Cosmos DB를 사용 하 여 Azure Kubernetes를 프로 비전 하는 방법을](bootstrap-kubernetes-cluster.md) 문서. Azure Cosmos DB etcd API는 현재 제한 된 미리 보기 중입니다. 할 수 있습니다 [미리 보기에 등록](https://aka.ms/cosmosetcdapi-signup), 등록 양식을 작성 하 여 합니다.

## <a name="wire-level-compatibility"></a>유선 수준 호환성

Azure Cosmos DB etcd 버전 3의 유선 프로토콜을 구현 하 고 허용 합니다 [마스터 노드의](https://kubernetes.io/docs/concepts/overview/components/) API 서버를 로컬로 설치 된 etcd 환경에서 수행 하는 것 처럼 Azure Cosmos DB를 사용 하 합니다. API etcd TLS 상호 인증을 지원합니다. 

다음 다이어그램에서는 Kubernetes 클러스터의 구성 요소를 보여 줍니다. 클러스터 마스터 API 서버는 로컬에 설치 된 etcd 대신 Azure Cosmos DB etcd API를 사용합니다. 

![Etcd 유선 프로토콜을 구현 하는 azure Cosmos DB](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>주요 이점

### <a name="no-etcd-operations-management"></a>Etcd 운영 관리 없음

완전히 관리 되는 네이티브 클라우드 서비스인 Azure Cosmos DB를 설정 하 고 etcd 관리 Kubernetes 개발자를 위한 필요가 없습니다. Azure Cosmos DB의 etcd API 확장 가능 하 고 항상 사용 가능한 내결함성, 및 높은 성능을 제공 합니다. 롤링을 수행 하 여 여러 노드 간에 복제를 설정 하는 오버 헤드 업데이트, 보안 패치 및 Azure Cosmos DB에 의해 처리 됩니다 etcd 상태를 모니터링 합니다.

### <a name="global-distribution--high-availability"></a>전역 분산 및 고가용성 

Etcd API를 사용 하 여 Azure Cosmos DB 데이터 읽기에 대한 99.99% 가용성을 보장 하 고 여러 지역에 걸쳐 99.999% 가용성을 단일 지역에 씁니다. 

### <a name="elastic-scalability"></a>탄력적 확장성

Azure Cosmos DB는 읽기에 대한 탄력적 확장성을 제공 및 다른 지역에 걸쳐 쓰기 요청 합니다.
Kubernetes 클러스터 증가 함에 따라 Azure Cosmos DB의 etcd API 계정, 중단 시간 없이 탄력적으로 조정 합니다. 또한 Kubernetes 마스터 노드 대신 Azure Cosmos DB의 etcd 데이터를 저장 하면 더 유연한 마스터 노드 크기 조정 합니다. 

### <a name="security--enterprise-readiness"></a>보안 및 엔터프라이즈 준비

Kubernetes 개발자가 자동으로 가져오기 etcd 데이터가 Azure Cosmos DB에 저장 되는 경우는 [기본 제공 미사용 데이터 암호화](database-encryption-at-rest.md)를 [인증 및 규정 준수](compliance.md), 및 [백업 및 복원 기능](online-backup-and-restore.md) Azure Cosmos DB에서 지원 합니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB를 사용 하 여 Azure Kubernetes를 사용 하는 방법](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB의 주요 이점](introduction.md)
* [AKS 엔진 빠른 시작 가이드](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)