---
title: Azure DevTest Labs 인프라 강화
description: 이 문서에서는 Azure DevTest Labs 인프라 강화를 위한 지침을 제공합니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 25a088686c739c53feadd6354baf75f3147bdc33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561492"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Azure DevTest Labs 인프라 강화
엔터프라이즈급으로 DevTest Labs를 구현하기 전에 몇 가지 중요한 사항을 결정해야 합니다. 높은 수준에서 이러한 결정 사항을 파악하는 조직은 향후에도 적절한 디자인 관련 결정을 내릴 수 있습니다. 하지만 이러한 결정 사항으로 인해 조직의 개념 증명 시작이 지연되어서는 안 됩니다. 초기 강화 계획에 포함되는 세 가지 주요 영역은 다음과 같습니다.

- 네트워킹 및 보안
- 구독 토폴로지
- 역할 및 책임

## <a name="networking-and-security"></a>네트워킹 및 보안
네트워킹 및 보안은 어떤 조직에서나 운영의 토대가 되는 요소입니다. 엔터프라이즈급 배포를 진행하려면 훨씬 더 심층적인 분석이 필요하지만, 개념 증명을 올바르게 완료하는 데 필요한 요구 사항의 수는 배포보다는 적습니다. 개념 증명 시에 중점적으로 파악해야 하는 몇 가지 주요 영역은 다음과 같습니다.

- **Azure 구독** - DevTest Labs를 배포하려면 Azure 구독에 액세스할 수 있어야 하며 리소스를 만드는 데 적합한 권한이 있어야 합니다. 기업계약, 종량제 등의 여러 가지 방식을 통해 Azure 구독 액세스 권한을 얻을 수 있습니다. Azure 구독 액세스 권한을 얻는 방법에 대한 자세한 내용은 [엔터프라이즈용 Azure 라이선싱](https://azure.microsoft.com/pricing/enterprise-agreement/)을 참조하세요.
- **온-프레미스 리소스 액세스** – DevTest Labs의 리소스가 온-프레미스 리소스에 액세스할 수 있어야 하는 조직도 있습니다. 이 경우 온-프레미스 환경에서 Azure로의 보안 연결이 필요합니다. 그러므로 배포를 시작하기 전에 VPN 또는 Express 경로 연결을 설정/구성해야 합니다. 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.
- **추가 보안 요구 사항** - 개념 증명을 구현하기 전에 컴퓨터 정책, 공용 IP 주소 액세스, 인터넷 연결 등의 기타 보안 요구 사항을 검토해야 할 수 있습니다. 

## <a name="subscription-topology"></a>구독 토폴로지
구독 토폴로지는 기업에 DevTest Labs를 배포할 때 반드시 고려해야 하는 디자인 관련 요소입니다. 하지만 개념 증명이 완료될 때까지 모든 사항을 확정해야 하는 것은 아닙니다. 엔터프라이즈 구현에 필요한 구독 수를 평가할 때 고려할 수 있는 두 가지 상반된 방식은 다음과 같습니다. 

- 전체 조직에 구독 하나 사용
- 사용자별 구독 사용

다음 섹션에서는 각 방식의 장점에 대해 중점적으로 설명합니다.

### <a name="one-subscription"></a>구독 하나 사용
대기업에서는 구독 하나를 사용하는 방식을 효율적으로 관리할 수 없는 경우가 많습니다. 그러나 구독 수를 제한하는 경우에는 다음과 같은 이점이 제공됩니다.

- **예측** - 기업의 구독 비용을 예측할 수 있습니다.  구독이 하나이면 모든 리소스가 단일 풀에 포함되므로 예산을 책정하기가 훨씬 쉬워집니다. 이 방식에서는 청구 주기의 특정 시점에 비용 제어 조치를 취할 시기를 더 간단하게 결정할 수 있습니다.
- **관리 효율성** Vm 아티팩트, 수식, 네트워크 구성, 사용 권한, 정책, 등은 때문 쉽습니다. 모든 업데이트는 많은 구독에서 업데이트할 대조적으로 하나의 구독에만 필요 합니다.
- **네트워킹** - 온-프레미스 연결 기능이 필요한 기업의 경우 구독이 하나이면 네트워킹 작업이 훨씬 간소화됩니다. 추가 구독이 있으면 여러 구독에서 가상 네트워크를 연결해야 하는데(허브-스포크 모델), 그러려면 추가 구성과 관리를 수행해야 하며 IP 주소 공간도 추가로 필요합니다.
- **팀 공동 작업** - 모든 작업자가 같은 구독에서 작업을 하므로 공동 작업을 더 쉽게 수행할 수 있습니다. 예를 들어 동료에게 VM을 다시 할당하거나 팀 리소스를 공유하는 등의 작업이 간소화됩니다.

### <a name="subscription-per-user"></a>사용자별 구독 사용
사용자별로 별도의 구독을 사용하는 방식 역시 단일 구독과 동일한 기회를 제공합니다. 여러 구독을 사용하는 경우의 이점은 다음과 같습니다.

- **Azure 크기 조정 할당량** - 할당량 도달 우려로 인해 도입이 지연되는 상황이 발생하지 않습니다. 예를 들어 이 문서 작성 시점에 Azure에서는 구독당 저장소 계정 200개를 포함할 수 있습니다. 그리고 Azure 내 대다수 서비스에는 운영 할당량도 있으며 대부분의 할당량(전체는 아님)은 사용자 지정할 수 있습니다. 이 사용자별 구독 모델에서는 대부분의 할당량에 도달하는 상황이 발생할 가능성이 거의 없습니다. 현재 Azure 크기 조정 할당량에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
- **지불 거절** - 조직에서 현재 모델을 사용해 비용을 처리할 수 있으므로 그룹이나 개별 개발자에 대한 지불 거절이 훨씬 쉬워집니다.
- **소유권 및 권한** - DevTest Labs의 환경에서는 단순한 권한과 소유권이 사용됩니다. 즉, 개발자에게 구독 수준 액세스 권한을 제공하며 네트워킹 구성, 랩 정책, VM 관리 등의 모든 작업에 대해서는 전적으로 개발자가 책임을 집니다.

하지만 기업에서는 제약 조건이 많아 위의 두 가지 극단적 방식을 사용하기가 어려울 수 있습니다. 그러므로 이 두 방식의 중간에 해당하는 방식으로 구독을 설정해야 할 수 있습니다. 모범 사례에 따르면, 조직은 충 구독 수를 늘려야 하는 기능을 고려하면서 가능한 한 적은 수의 구독 사용을 목표로 해야 합니다. 이처럼 구독 토폴로지는 DevTest Labs의 엔터프라이즈 배포에서 중요한 요소이기는 하지만 구독 토폴로지로 인해 개념 증명이 지연되어서는 안 됩니다. 조직의 구독 및 랩 세분성을 결정하는 방법에 대한 추가 정보는 [거버넌스](devtest-lab-guidance-governance-policy-compliance.md) 문서에 나와 있습니다.

## <a name="roles-and-responsibilities"></a>역할 및 책임
DevTest Lab 개념 증명에는 각기 책임이 정의된 세 가지 기본 역할(구독 소유자, DevTest Labs 소유자, DevTest Labs 사용자)이 포함되며 필요에 따라 참가자도 포함될 수 있습니다.

- **구독 소유자** – 구독 소유자에게는 사용자 할당, 정책 관리, 네트워킹 토폴로지 작성/관리, 할당량 증가 요청 등 Azure 구독을 관리하기 위한 권한이 있습니다. 자세한 내용은 [이 문서](../role-based-access-control/rbac-and-directory-admin-roles.md)(영문)를 읽어보세요.
- **DevTest Labs 소유자** - DevTest Labs 소유자에게는 랩에 대한 모든 관리 권한이 있습니다. 이 사용자는 사용자 추가/제거, 비용 설정 관리, 일반 랩 설정, 기타 VM/아티팩트 기반 작업을 담당합니다. 랩 소유자에게는 모든 DevTest Labs 사용자 권한도 있습니다.
- **DevTest Labs 사용자** – DevTest Labs 사용자는 랩에서 가상 머신을 만들고 사용할 수 있습니다. 이 개별 사용자에게는 직접 만드는 VM에 대한 최소한의 관리 기능(VM 시작/중지/삭제/구성)이 제공됩니다. 하지만 다른 사용자의 VM을 관리할 수는 없습니다.

## <a name="next-steps"></a>다음 단계
이 시리즈의 다음 문서를 참조하세요. [Azure DevTest Labs의 구현 오케스트레이션](devtest-lab-guidance-orchestrate-implementation.md)