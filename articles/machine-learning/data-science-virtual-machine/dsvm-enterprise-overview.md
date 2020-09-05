---
title: 팀 분석 및 AI 환경
titleSuffix: Azure Data Science Virtual Machine
description: 엔터프라이즈 팀 환경에 Data Science VM을 배포하기 위한 패턴입니다.
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: b1357c9bb125cb881ac4aa6dd31c9dcaf53954f0
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919942"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Data Science Virtual Machine 기반 팀 분석 및 AI 환경 
[DSVM](overview.md)(Data Science Virtual Machine)은 미리 빌드된 AI(인공 지능) 및 데이터 분석용 소프트웨어가 포함된 다양한 환경을 Azure 플랫폼에 제공합니다.

일반적으로 DSVM은 개별 분석 데스크톱으로 사용되었습니다. 개별 데이터 과학자는 미리 빌드된 이 공유 분석 환경을 통해 생산성을 향상시킵니다. 대규모 분석 팀에서 데이터 과학자 및 AI 개발자를 위한 환경을 계획함에 따라 반복되는 주제 중 하나는 개발 및 실험을 위한 공유 분석 인프라입니다. 이 인프라는 데이터 과학 및 분석 팀 전체의 협업과 일관성을 용이하게 하는 엔터프라이즈 IT 정책에 따라 관리됩니다.

공유 인프라를 사용하면 분석 환경의 IT 사용률을 향상시킬 수 있습니다. 일부 조직에서는 팀 기반 데이터 과학/분석 인프라를 *분석 샌드박스*라고 합니다. 이를 사용하면 데이터 과학자가 다양한 데이터 자산에 액세스하여 데이터를 빠르게 이해할 수 있습니다. 또한 이 샌드박스 환경에서는 데이터 과학자가 프로덕션 환경에 영향을 주지 않고 실험을 실행하고, 가설의 유효성을 검사하며, 예측 모델을 빌드할 수 있습니다.

DSVM은 Azure 인프라 수준에서 작동하므로, IT 관리자가 엔터프라이즈의 IT 정책을 준수하며 작동하도록 DSVM을 쉽게 구성할 수 있습니다. DSVM은 다양한 공유 아키텍처를 매우 유연하게 구현하면서도 회사 데이터 자산에 대한 액세스를 제어할 수 있습니다.

이 섹션에서는 DSVM을 팀 기반 데이터 과학 인프라로 배포하는 데 사용할 수 있는 몇 가지 패턴 및 지침을 설명합니다. 이러한 패턴의 구성 요소는 Azure IaaS(Infrastructure as a Service)에서 제공되므로 모든 Azure VM에 적용됩니다. 이 시리즈의 문서에서는 이러한 표준 Azure 인프라 기능을 DSVM에 적용하는 데 중점을 둡니다.

엔터프라이즈 팀 분석 환경의 주요 구성 요소는 다음과 같습니다.

* [DSVM의 자동 크기 조정 풀](dsvm-pools.md)
* [공통 ID 및 풀의 아무 DSVM에서나 작업 영역에 액세스](dsvm-common-identity.md)
* [데이터 원본에 안전하게 액세스](dsvm-secure-access-keys.md)


이 시리즈에서는 위의 각 항목에 대한 지침 및 포인터를 제공합니다. 대규모 엔터프라이즈 구성에 DSVM을 배포하기 위한 모든 고려 사항과 요구 사항을 다루지는 않습니다. 엔터프라이즈에서 DSVM 인스턴스를 구현하면서 사용할 수 있는 몇 가지 다른 Azure 리소스는 다음과 같습니다.

* [네트워크 보안](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [모니터링](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) 및 [관리](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [로깅 및 감사](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Azure RBAC(Azure 역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [정책 설정 및 적용](../../governance/policy/overview.md)
* [맬웨어 방지](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [암호화](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)
* [데이터 검색 및 거버넌스](https://docs.microsoft.com/azure/data-catalog/)

마지막으로 [Azure 아키텍처 센터](https://docs.microsoft.com/azure/architecture/)는 클라우드 기반 분석 인프라를 구축하고 관리하기 위한 자세한 엔드투엔드 아키텍처와 모델을 제공합니다.