---
title: 연결 끊기 Azure Lab Services에서 Vm 종료를 사용 하도록 설정 Microsoft Docs
description: 원격 데스크톱 연결의 연결이 끊어질 때 Vm의 자동 종료를 사용 하거나 사용 하지 않도록 설정 하는 방법에 대해 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117138"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>연결 끊기 시 Vm의 자동 종료 사용
이 문서에서는 원격 데스크톱 연결을 끊은 후 **Windows 10** 랩 vm (템플릿이나 학생)의 자동 종료를 사용 하거나 사용 하지 않도록 설정할 수 있는 방법을 보여 줍니다. 또한 사용자가 자동으로 종료 되기 전에 사용자가 다시 연결 될 때까지 Vm에서 대기할 기간을 지정할 수 있습니다.

랩 계정 관리자는 랩을 만드는 랩 계정에 대해이 설정을 구성할 수 있습니다. 자세한 내용은 [랩 계정에 대 한 연결 끊기 시 vm의 자동 종료 구성](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)을 참조 하세요. 랩 소유자는 랩을 만들 때 또는 랩을 만든 후에 설정을 재정의할 수 있습니다. 

## <a name="configure-when-creating-a-lab"></a>랩을 만들 때 구성
Lab 만들기 마법사의 3 단계 페이지에서이 기능을 사용 하거나 사용 하지 않도록 설정 하 고, 사용자가 자동으로 종료 되기 전에 다시 연결할 때까지 VM에서 대기할 기간을 지정할 수도 있습니다. 

![랩 생성 시 구성](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>랩을 만든 후 구성
다음 이미지와 같이 **설정** 페이지에서이 설정을 구성할 수 있습니다. 

![랩을 만든 후 구성](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Vm에 대 한 RDP 세션의 연결을 끊기 전에 VM에서 Windows 운영 체제 (OS)를 종료 하는 경우 자동 종료 기능이 제대로 작동 하지 않습니다.  

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [교실 labs 대시보드](use-dashboard.md)