---
title: Azure Security Center 규정 준수 대시보드에서 동적 규정 준수 모니터링으로 업데이트하는 방법 | Microsoft Docs
description: 규정 준수 패키지 업데이트
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 28b6a70297efb8d8237f085e1f5b08dbb6a09072
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522609"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>규정 준수 대시보드의 표준 세트 사용자 지정

Azure Security Center는 리소스 구성을 업계 표준, 규정 및 벤치마크의 요구 사항과 지속적으로 비교합니다. **규정 준수 대시보드**는 특정 규정 준수 제어 및 요구 사항을 충족하는 방법에 따라 규정 준수 상태에 대한 인사이트를 제공합니다.


## <a name="overview-of-compliance-packages"></a>규정 준수 패키지 개요

업계 표준, 규정 표준 및 벤치마크는 보안 센터에서 *규정 준수 패키지*로 표시됩니다.  각 패키지는 Azure Policy에 정의된 이니셔티브입니다. 대시보드에서 평가로 매핑된 규정 준수 데이터를 보려면 **보안 정책** 페이지 내에서 관리 그룹 또는 구독에 규정 준수 패키지를 추가합니다. ([보안 정책 작업](tutorial-security-policy.md)에서 Azure Policy 및 이니셔티브에 대해 자세히 알아봅니다.)

선택한 범위에 표준 또는 벤치마크를 온보딩하면 해당 범위에 이니셔티브를 할당하고 규정 준수 대시보드에 평가로 매핑된 모든 관련 규정 준수 데이터가 포함된 표준이 표시됩니다. 온보딩된 표준에 대한 요약 보고서를 다운로드할 수도 있습니다.

또한 Microsoft는 규정 표준 자체를 추적하고 시간이 지남에 따라 일부 패키지에서 해당 범위를 자동으로 개선합니다. Microsoft가 이니셔티브에 대한 새 콘텐츠(표준의 더 많은 컨트롤에 매핑되는 새 정책)를 릴리스하면 추가 콘텐츠가 대시보드에 자동으로 표시됩니다.

> [!TIP]
> Microsoft가 릴리스한 새 콘텐츠로, 시간이 지남에 따라 개선되는 한 가지 표준은 **Azure CIS 1.1.0(신규)** (보다 공식적으로는 [CIS Microsoft Azure Foundations Benchmark 버전 1.1.0](https://www.cisecurity.org/benchmark/azure/))입니다. 모든 Security Center 환경에서 기본적으로 구성되는 Azure CIS의 표현인 "Azure CIS 1.1.0"과 함께 대시보드에 이를 추가해야 합니다. 해당 패키지는 정적 규칙 세트를 사용합니다. 최신 패키지에는 추가 정책이 포함되어 있으며 시간이 지남에 따라 자동으로 업데이트됩니다. 아래 설명된 대로 새 동적 패키지로 업데이트합니다.


## <a name="available-packages"></a>사용 가능한 패키지

NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, 영국 공식 및 영국 NHS, 캐나다 연방 PBMM 및 Azure CIS 1.1.0(신규)과 같은 표준을 추가하면 Azure CIS 1.1.0을 보다 완전하게 표현할 수 있습니다. 

또한 공통 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 대해 Microsoft에서 작성한 Azure 관련 지침인 **Azure 보안 벤치마크**를 추가할 수 있습니다. ([Azure Security Benchmark에 대해 자세히 알아봅니다](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

추가 표준은 사용할 수 있게 되면 대시보드에서 지원됩니다. 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>대시보드에 규정 표준 추가

다음 단계에서는 패키지를 추가하여 지원되는 규정 표준 중 하나를 준수하는지 모니터링하는 방법을 설명합니다.

> [!NOTE]
> 소유자 또는 정책 기여자인 사용자만 규정 준수 표준을 추가하는 데 필요한 권한을 갖습니다. 

1. Security Center의 사이드바에서 **규정 준수**를 선택하여 규정 준수 대시보드를 엽니다. 여기에서 현재 선택된 구독에 현재 할당된 규정 준수 표준을 확인할 수 있습니다.   

1. 페이지 맨 위에서 **규정 준수 정책 관리**를 선택합니다. 정책 관리 페이지가 표시됩니다.

1. 규정 준수 상태를 관리할 구독 또는 관리 그룹을 선택합니다. 

    > [!TIP]
    > 모든 중첩된 리소스에 대해 규정 준수 데이터를 집계하고 추적할 수 있도록 표준이 적용되는 가장 높은 범위를 선택하는 것이 좋습니다. 

1. 조직과 관련된 표준을 추가하려면 **더 많은 표준 추가**를 클릭합니다. 

1. **규정 준수 표준 추가** 페이지에서 사용 가능한 표준에 대한 패키지를 검색할 수 있습니다. 사용 가능한 몇 가지 표준은 다음과 같습니다.

    - **Azure Security Benchmark**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO 및 영국 NHS**
    - **캐나다 PBMM**
    
    ![Azure Security Center의 규정 준수 대시보드에 규정 패키지 추가](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. **추가** 를 선택 하 고 범위, 매개 변수, 수정 등의 특정 이니셔티브에 필요한 모든 세부 정보를 입력 합니다.

1. Security Center의 사이드바에서 **규정 준수**를 다시 선택하여 규정 준수 대시보드로 돌아갑니다.
    * 새 표준이 업계 및 규정 표준 목록에 표시됩니다. 
    * **Azure CIS 1.1.0(신규)** 을 추가한 경우 Azure CIS 1.1.0 규정 준수의 원래 *정적* 보기도 함께 유지됩니다. 나중에 자동으로 제거될 수 있습니다.

    > [!NOTE]
    > 새로 추가된 표준이 규정 준수 대시보드에 표시되는 데 몇 시간 정도 걸릴 수 있습니다.

    [![이전 및 새 Azure CIS를 표시하는 규정 준수 대시보드](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 문서에서는 추가 표준 규정 준수를 모니터링하기 위해 **규정 준수 패키지를 추가**하는 방법에 대해 알아보았습니다. 

기타 관련 자료는 다음 문서를 참조하세요. 

- [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [보안 센터 규정 준수 대시보드](security-center-compliance-dashboard.md)
- [보안 정책 작업](tutorial-security-policy.md)