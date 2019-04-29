---
title: Azure 미리 보기 Sentinel fusion을 사용 하도록 설정 | Microsoft Docs
description: Azure Sentinel에서 fusion을 사용 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55d569d4a993a725137d7bfab37c113147fe81ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714936"
---
# <a name="enable-fusion"></a>퓨전 사용

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel에서 machine Learning은 처음부터 기본 제공 합니다. 보안 분석가, 보안 데이터 과학자와 엔지니어가 생산성을 위한 기계 학습 혁신을 사용 하 여 시스템을 설계 신중 하 게 했습니다. 이러한 한 혁신적인 경고 피로 위해 특히 Azure Sentinel Fusion 작성입니다.

Fusion은 그래프 기반 기계 학습 알고리즘을 사용 하 여 수백만 대의 Azure AD Id 보호와 같은 다른 제품에서 낮은 충실도 비정상적인 활동 및 Microsoft Cloud App Security의 관리 가능한 수치로로 결합할 수 간의 상관 관계 흥미로운 보안 사례입니다.

## <a name="enable-fusion"></a>퓨전 사용

1. Azure portal에서 Cloud Shell을 열려면 아이콘을 선택 합니다.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  에 **Cloud Shell 시작** 아래 열린 windows PowerShell을 선택 합니다.

3.  Azure Sentinel 배포할 구독을 선택 하 고 **저장소 만들기**합니다.

4. 인증 된 후 Azure 드라이브 빌드되기 명령 프롬프트에서 다음 명령을 실행 합니다.

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Fusion을 사용 하지 않도록 설정

위와 동일한 절차를 수행 하 고 다음 명령을 실행:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Fusion 상태 보기

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 Fusion을 사용 하도록 설정 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

