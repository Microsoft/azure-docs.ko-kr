---
title: Azure 인프라의 규정 준수 자동화에 대한 InSpec 사용
description: Azure 배포의 문제를 감지하려면 InSpec 사용 방법을 알아봅니다
keywords: azure, chef, devops, virtual machines, overview, automate, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: bdfa30b48c79a8910d503bb9e54a42c30e5adba6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629801"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Azure 인프라의 규정 준수 자동화에 대한 InSpec 사용

[InSpec](https://www.chef.io/inspec/)은 소프트웨어 엔지니어, 작업 및 보안 엔지니어 간에 공유할 수 있는 보안 및 규정 준수 규칙을 설명하는 Chef의 오픈 소스 언어입니다. InSpec는 읽기 쉽고 쓰기 쉬운 InSpec 코드에서 표현하는 필요한 상태를 인프라의 실제 상태와 비교하여 작동합니다. InSpec은 위반을 감지하고 보고서의 형태로 결과물을 표시하지만 재구성을 사용자에게 컨트롤하게 합니다.

리소스 및 가상 컴퓨터, 네트워크 구성, Azure Active Directory 설정 등을 포함 한 구독에서 리소스 그룹의 상태를 확인 하 InSpec를 사용할 수 있습니다.

이 문서에서는 Azure에서 보안 및 규정 준수를 더 쉽게 하기 위한 InSpec의 사용 혜택을 설명합니다.

## <a name="make-compliance-easy-to-understand-and-assess"></a>이해 및 평가하려면 규정 준수 쉽게 하기

준수 설명서 스프레드시트나 Word 문서 작성 요구 사항을 열어 둡니다 해석 합니다. InSpec으로 요구 사항을 버전이 지정되고 실행 가능하며 사람이 읽을 수 있는 코드로 변환합니다. 코드는 명확한 의도로 실제 테스트를 우호적으로 평가해야 하는 것에 대한 대화를 대신합니다.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>전반적인 문제를 검색하고 해당 재구성의 우선 순위를 지정합니다

InSpec의 에이전트 없는 검색 모드를 사용하면 노출 수준을 대규모로 신속하게 평가할 수 있습니다. 영향/심각도 채점에 대한 기본 제공 메타데이터는 재구성을 위해 초점을 맞출 영역을 결정하도록 도와줍니다. 또한 새로운 취약성이나 규정에 대응하여 규칙을 빠르게 작성하고 즉시 롤아웃할 수 있습니다.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>정책 게스트 구성을 사용 하 여 Azure 가상 머신 감사

유용한 Chef InSpec 정의 통해 Azure virtual machines 감사를 사용 하 여 azure에 직접 지 원하는 [Azure 정책 게스트 구성](/azure/governance/policy/concepts/guest-configuration)합니다. 제공 된 유용한 Chef InSpec 정 및 Azure Policy를 통해 다시 규정 준수 보고서에 Linux 가상 머신을 평가 하는 게스트 구성 합니다. 이러한 감사 결과 Azure Monitor 로그를 통해 보고 됩니다. 경고 및 다른 자동화 시나리오를 사용 하도록 설정 합니다.

## <a name="satisfy-audits"></a>감사 충족

InSpec을 사용하여 분기별 또는 연도별 등 미리 결정된 기간에 국한되지 않고 언제든 질문을 감사하는 데 대응할 수 있습니다. InSpec 테스트를 계속해서 실행하여 감사 결과에 놀라지 않고 정확한 준수 상태 및 내역을 이해하면서 감사 주기를 시작합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"] 
> [Azure Cloud Shell에서 InSpec 시도](https://shell.azure.com)