---
title: Azure Security Center에서 지 원하는 플랫폼 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 지 원하는 플랫폼의 목록을 제공 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: f02e45804234fc8f8aa2b966db7ddce5efc9cfd1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998835"
---
# <a name="supported-platforms"></a>지원되는 플랫폼 

이 페이지에서는 Azure Security Center에서 지 원하는 플랫폼 및 환경을 보여 줍니다.

## <a name="combinations-of-environments"></a>환경 조합 <a name="vm-server"></a>

Azure Security Center는 여러 유형의 하이브리드 환경에서 가상 머신 및 서버를 지원 합니다.

* Azure만
* Azure 및 온-프레미스
* Azure 및 기타 클라우드
* Azure, 다른 클라우드 및 온-프레미스

Azure 구독에서 활성화 된 Azure 환경의 경우 Azure Security Center는 구독 내에 배포 되는 IaaS 리소스를 자동으로 검색 합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

Security Center [Log Analytics 에이전트](../azure-monitor/platform/agents-overview.md#log-analytics-agent)에 따라 달라 집니다. 다음 페이지에 설명 된 대로 컴퓨터에서이 에이전트에 대해 지원 되는 운영 체제 중 하나를 실행 하 고 있는지 확인 합니다.

* [Windows에서 지원 되는 운영 체제에 대 한 Log Analytics 에이전트](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Linux 지원 운영 체제에 대 한 Log Analytics 에이전트](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

또한 Log Analytics 에이전트가 [데이터를 전송 하도록 제대로 구성](security-center-enable-data-collection.md#manual-agent) 되어 있는지 확인 Security Center

> [!TIP]
> Windows 및 Linux에서 사용할 수 있는 특정 Security Center 기능에 대 한 자세한 내용은 [컴퓨터의 기능 적용](security-center-services.md)을 참조 하세요.

## <a name="managed-virtual-machine-services"></a>관리 되는 가상 컴퓨터 서비스 <a name="virtual-machine"></a>

Azure Kubernetes (AKS), Azure Databricks 등의 일부 Azure 관리 서비스의 일부로도 고객 구독에서 가상 컴퓨터를 만들 수 있습니다. Security Center는 이러한 가상 컴퓨터를 검색 하 고, 지원 되는 OS를 사용할 수 있는 경우 Log Analytics 에이전트를 설치 및 구성할 수 있습니다.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

클라우드 서비스에서 실행 되는 가상 컴퓨터도 지원 됩니다. 프로덕션 슬롯에서 실행되는 클라우드 서비스 웹 및 작업자 역할만 모니터링됩니다. 클라우드 서비스에 대한 자세한 내용은 [Azure Cloud Services 개요](../cloud-services/cloud-services-choose-me.md)를 참조하세요.

Azure Stack에 있는 Vm에 대 한 보호도 지원 됩니다. Azure Stack와 Security Center의 통합에 대 한 자세한 내용은 [Security Center에 Azure Stack virtual machines](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)등록을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Security Center Log Analytics 에이전트를 사용 하 여 데이터를 수집](security-center-enable-data-collection.md)하는 방법을 알아봅니다.
- [Security Center에서 데이터를 관리 하 고 보호](security-center-data-security.md)하는 방법을 알아봅니다.
- [Azure Security Center를 채택 하기 위한 디자인 고려 사항을 계획 하 고 이해](security-center-planning-and-operations-guide.md)하는 방법을 알아봅니다.