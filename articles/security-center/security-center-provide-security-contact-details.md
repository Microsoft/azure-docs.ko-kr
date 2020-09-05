---
title: Azure Security Center의 보안 연락처 세부 정보 제공 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 연락처 세부 정보를 제공하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: dda61b81ee2c357ddac29701832fe4780ea06859
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516302"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>보안 경고에 대 한 전자 메일 알림 설정 

조직의 올바른 사람이 사용자 환경의 보안 경고에 대 한 알림을 받을 수 있도록 하려면 **전자 메일 알림** 설정 페이지에서 전자 메일 주소를 입력 합니다.

알림을 설정할 때 특정 개인 또는 구독에 대 한 특정 Azure 역할을 가진 사용자에 게 전자 메일을 보내도록 구성할 수 있습니다. 

경고 피로을 방지 하기 위해 Security Center 보내는 메일의 볼륨을 제한 합니다. 각 구독에 대해 Security Center은 다음을 보냅니다.

- **심각도가 높은** 경고에 대해 하루 최대 **4 개의** 전자 메일
- **보통 심각도** 경고에 대해 하루에 최대 **2 개의** 전자 메일
- **심각도가 낮은** 경고에 대해 하루에 최대 **한 번** 전자 메일


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="보안 경고에 대 한 전자 메일을 받을 연락처의 세부 정보를 구성 합니다." :::

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|일반 공급|
|결정|무료 계층|
|필요한 역할 및 사용 권한:|**보안 관리자**<br>**구독 소유자** |
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) US Gov (부분)<br>![예](./media/icons/no-icon.png) 중국 .Gov, 기타 .Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>경고에 대 한 전자 메일 알림 설정 <a name="email"></a>

개인 또는 특정 Azure 역할의 모든 사용자에 게 전자 메일 알림을 보낼 수 있습니다.

1. Security Center의 **가격 & 설정** 영역에서 관련 구독을 선택 하 고 **전자 메일 알림을**선택 합니다.

1. 알림을 받을 받는 사람을 정의 합니다.

    - 드롭다운 목록에서 사용 가능한 역할을 선택 합니다.
    - 쉼표로 구분 된 특정 전자 메일 주소를 입력 합니다. 입력할 수 있는 전자 메일 주소 수에는 제한이 없습니다.

1. 보안 연락처 정보를 구독에 적용 하려면 **저장**을 선택 합니다.


## <a name="see-also"></a>참조
보안 경고에 대 한 자세한 내용은 다음을 참조 하세요.

* [보안 경고-참조 가이드](alerts-reference.md) --Azure Security Center의 위협 방지 모듈에서 볼 수 있는 보안 경고에 대해 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) --보안 경고를 관리 하 고 대응 하는 방법을 알아봅니다.
* [워크플로 자동화](workflow-automation.md) -사용자 지정 알림 논리를 사용 하 여 경고에 대 한 응답 자동화