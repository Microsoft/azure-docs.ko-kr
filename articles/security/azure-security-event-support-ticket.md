---
title: 보안 이벤트 지원 티켓을 로그하는 방법 - Azure | Microsoft Docs
description: 잠재적인 보안 이벤트를 확인한 Azure Marketplace의 판매자로서, 적절한 티켓을 기록하는 방법을 알아야 합니다.
services: security
documentationcenter: na
author: DavidBosland
manager: lakoch
editor: v-dabosl
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: v-dabosl
ms.openlocfilehash: 243b6b7846719d3f848073e3aa973c9406ca59c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586606"
---
# <a name="how-to-log-a-security-event-support-ticket"></a>보안 이벤트 지원 티켓을 로그하는 방법

1. [게시자 지원](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636450758943226673)으로 이동하고 Microsoft 자격 증명으로 로그인합니다.
2. 문제 유형으로 [보안 이벤트]를 선택하고 [보안 인시던트] 및 [취약성] 범주 중 하나를 선택합니다.

    ![이벤트 유형 및 정의][1]

3. 문제 유형과 범주를 선택한 후 [**요청 시작**] 단추를 클릭합니다. 다음 정보를 제공하면 문제를 이해하는 데 도움이 됩니다.

    i. 문제 및/또는 취약성은 무엇인가요?

    ii. 취약점, 주시기 CVE (mitre.org) 하거나 CVSS3 v3 계산기 (https://www.first.org/cvss/calculator/3.0)합니다.

    iii. 해결 방법 또는 위험 완화가 있나요? 있다면 수정 단계를 제공해 주세요.

    iv. 고객에게 전송하려는 메시지가 있나요? 적절한 메시지를 작성하기 위해 노력할 것입니다(적용 가능한 경우).

4. 제출 확인 - 문제가 제출된 후 Microsoft는 1 영업일 내에 받았다는 응답을 하고 문제에 우선 순위와 심각도를 할당합니다.

    - 문제에 대해 Microsoft에 알릴 내용이 있는 경우 모든 통신에서 확인 번호를 사용합니다.
    - 언제든지 문제에 대한 진행률을 확인할 수 있습니다.

5. 다음 작업 문제 및 심각도에 따라 다음 단계를 수행할 수 있습니다.

    - 평가 결과를 귀하에게 알려 드릴 것입니다. 결과에 따라 귀하의 제품을 수정하도록 요청하거나 제거할 수 있습니다. 이 경우 영향을 받는 고객의 사용 중단을 최소화하기 위해 노력할 것입니다.
    - 인시던트/취약성이 고객에게 미치는 영향을 완화하기 위해 노력할 것입니다.


[1]: ./media/azure-security-event-support-ticket/chart.png
