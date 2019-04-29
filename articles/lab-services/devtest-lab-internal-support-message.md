---
title: Azure DevTest Labs의 랩에 내부 지원 정책 추가 | Microsoft Docs
description: Azure DevTest Labs의 랩에 내부 지원 정책을 게시하는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: deb98c2c633200ab4be1d763a94fd2a04979a3b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562341"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs의 랩에 내부 지원 정책 추가

Azure DevTest Labs에서는 사용자에게 랩에 대한 지원 정보를 제공하는 내부 지원 정책을 사용하여 랩을 사용자 지정할 수 있습니다. 예를 들어 사용자가 랩의 리소스에 액세스하거나 문제를 해결하는 데 도움이 필요할 때 내부 지원에 연결할 수 있도록 연락처 정보를 제공할 수 있습니다. 팀이 지원에 연락하기 전에 액세스할 수 있는 내부 웹 사이트나 FAQ에 대한 링크를 제공할 수도 있습니다.

내부 지원 정책은 통상 그렇게 자주 변경되지 않는 랩 정보를 게시하기 위한 것입니다. 랩 정책에 대한 최신 업데이트와 같이 본질적으로 보다 일시적인 랩 정보를 사용자에게 알리려면 [랩에 공지 게시](devtest-lab-announcements.md)를 참조하세요.

지원 정책을 더 이상 적용할 수 없는 경우 간단히 사용하지 않도록 설정하거나 편집할 수 있습니다.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>기존 랩에 지원 정책을 추가하는 단계

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. 필요한 경우 **모든 서비스**를 선택하고 목록에서 **DevTest Labs**를 선택합니다. 대시보드의 **모든 리소스** 아래에 랩이 이미 표시되어 있을 수 있습니다.
1. 랩 목록에서 지원 정책을 추가하려는 랩을 선택합니다.  
1. 랩의 **개요** 영역에서 **구성 및 정책**을 선택합니다.  

    ![구성 및 정책 단추](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. **설정** 아래의 왼쪽에서 **내부 지원**을 선택합니다.

    ![내부 지원 단추](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. 이 랩에서 사용자를 위한 내부 지원 메시지를 만들려면 [사용]을 **예**로 설정합니다.

1. **지원 메시지** 필드에 랩 사용자에게 제공할 내부 지원 정책을 입력합니다. 지원 메시지에는 Markdown을 사용할 수 있습니다. 메시지 텍스트를 입력할 때 화면 맨 아래의 **미리 보기** 영역에서 메시지가 사용자에게 표시되는 모양을 확인할 수 있습니다.

    ![메시지를 만드는 내부 지원 화면입니다.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. 지원 정책을 게시할 준비가 되면 **저장**을 선택합니다.

이 지원 메시지를 랩 사용자에게 더 이상 표시하지 않으려는 경우 **내부 지원** 페이지로 돌아가서 **사용**을 **아니요**로 설정합니다.

## <a name="steps-for-users-to-view-the-support-message"></a>사용자가 지원 메시지를 보는 단계

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에서 랩을 선택합니다.

1. 랩의 **개요** 영역에서 **내부 지원**을 선택합니다.  

    ![내부 지원 단추](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. 지원 메시지가 게시되면 사용자는 내부 지원 창에서 이 메시지를 볼 수 있습니다.

    ![게시된 지원 메시지를 보여 주는 내부 지원 창](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
* 내부 지원 정책은 일반적으로 자주 변경되지 않는 지원 정보를 제공하는 데 사용됩니다. [랩에 공지를 게시](devtest-lab-announcements.md)하여 사용자에게 랩에 대한 임시 변경 사항이나 업데이트를 알리는 방법을 알아볼 수도 있습니다.
* [정책 및 일정 설정](devtest-lab-set-lab-policy.md)에서는 사용자 지정된 정책을 사용하여 구독에 기타 제한 사항 및 규칙을 적용하는 방법을 설명합니다.