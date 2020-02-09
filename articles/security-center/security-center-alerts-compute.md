---
title: Azure Security Center에서 클라우드 네이티브 컴퓨팅에 대 한 위협 감지 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 사용할 수 있는 클라우드 네이티브 계산 경고를 제공 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024863"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Azure Security Center에서 클라우드 네이티브 컴퓨팅에 대 한 위협 감지

네이티브 솔루션으로 Azure Security Center는 여러 대상의 공격 방법론 식별을 위한 내부 로그를 고유 하 게 표시 합니다. 이 문서에서는 다음 Azure 서비스에 대해 사용할 수 있는 경고를 제공 합니다.

* [Azure App Service](#app-services)
* [Azure 컨테이너](#azure-containers) 

> [!NOTE]
> 이러한 서비스는 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

## Azure App Service<a name="app-services"></a>

Security Center는 클라우드의 규모를 사용 하 여 App Service에서 실행 되는 응용 프로그램을 대상으로 하는 공격을 식별 합니다. 공격자는 웹 응용 프로그램을 검색 하 여 약점을 찾아 활용 합니다. 특정 환경으로 라우팅되기 전에 Azure에서 실행 중인 응용 프로그램에 대 한 요청은 여러 게이트웨이를 통해 수행 되며,이를 검사 하 고 기록 합니다. 그런 다음이 데이터를 사용 하 여 악용 및 공격자를 식별 하 고 나중에 사용 될 새 패턴을 알아봅니다.

Azure가 클라우드 공급자 인 표시 유형을 사용 하 여 Security Center App Service 내부 로그를 분석 하 여 여러 대상에 대 한 공격 방법론을 식별 합니다. 예를 들어, 방법론에는 광범위 한 검색 및 분산 공격이 포함 됩니다. 이러한 유형의 공격은 일반적으로 Ip의 작은 하위 집합에서 제공 되며 여러 호스트에서 비슷한 엔드포인트에 대 한 탐색 패턴을 보여 줍니다. 공격에 취약 한 페이지 또는 플러그 인을 검색 하 고 있으며 단일 호스트의 관점에서 식별할 수 없습니다.

Windows 기반 App Service 계획을 실행 하는 경우에는 Security Center 기본 샌드박스 및 Vm에도 액세스할 수 있습니다. 위에서 언급 한 로그 데이터와 함께 인프라는 circulating의 새로운 공격에서 고객 시스템의 손상에 이르기까지 이야기를 알려줄 수 있습니다. 따라서 웹 앱이 악용 된 후 Security Center 배포 된 경우에도 지속적인 공격을 검색할 수 있습니다.

Azure App Service 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azureappserv)을 참조 하세요.

## Azure 컨테이너<a name="azure-containers"></a>

Security Center는 컨테이너 화 된 환경에 대 한 실시간 위협 감지 기능을 제공 하 고 의심 스러운 활동에 대 한 경고를 생성 합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

서로 다른 수준에서 위협을 검색 합니다. 

* **호스트 수준** -Security Center의 에이전트 (표준 계층에서 사용 가능, 자세한 내용은 [가격 책정](security-center-pricing.md) 참조)에서 의심 스러운 활동에 대 한 Linux를 모니터링 합니다. 에이전트는 노드 또는 해당 노드에서 실행 중인 컨테이너에서 시작 된 의심 스러운 활동에 대 한 경고를 트리거합니다. 이러한 작업의 예로는 알려진 의심 스러운 IP 주소에 대 한 웹 셸 검색 및 연결이 있습니다.

    컨테이너 화 된 환경의 보안을 보다 심층적으로 파악 하기 위해 에이전트는 컨테이너 관련 분석을 모니터링 합니다. 권한 있는 컨테이너 만들기, API 서버에 대 한 의심 스러운 액세스 및 Docker 컨테이너 내에서 실행 되는 SSH (Secure Shell) 서버와 같은 이벤트에 대 한 경고를 트리거합니다.

    >[!NOTE]
    > 호스트에 에이전트를 설치 하지 않도록 선택한 경우 위협 검색 혜택 및 경고의 하위 집합만 받게 됩니다. 네트워크 분석과 악의적인 서버와의 통신과 관련 된 경고를 계속 받을 수 있습니다.

    호스트 수준 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-containerhost)을 참조 하세요.


* **AKS 클러스터 수준의**경우 Kubernetes 감사 로그 분석을 기반으로 하는 위협 검색 모니터링이 있습니다. 이 **에이전트** 없는 모니터링을 사용 하도록 설정 하려면 **가격 책정 & 설정** 페이지에서 구독에 Kubernetes 옵션을 추가 합니다 ( [가격 책정](security-center-pricing.md)참조). 이 수준에서 경고를 생성 하기 위해 Security Center는 AKS에서 검색 된 로그를 사용 하 여 AKS 관리 서비스를 모니터링 합니다. 이 수준에서 발생 하는 이벤트의 예로는 노출 된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기, 중요 한 탑재 생성 등이 있습니다.

    >[!NOTE]
    > Security Center는 구독 설정에서 Kubernetes 옵션을 사용 하도록 설정한 후 발생 하는 Azure Kubernetes 서비스 작업 및 배포에 대 한 검색 경고를 생성 합니다. 

    AKS 클러스터 수준 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-akscluster)을 참조 하세요.

또한 글로벌 보안 연구원 팀은 지속적으로 위협 환경을 모니터링 합니다. 검색 된 컨테이너 관련 경고 및 취약성을 추가 합니다.

## <a name="next-steps"></a>다음 단계

* App Service 계획에 대 한 자세한 내용은 [App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/plans/)를 참조 하세요.