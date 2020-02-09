---
title: '가상 머신에서 복사 하 여 붙여넣기: Azure 방호'
description: 이 문서에서는 요새를 사용 하 여 Azure VM에서 복사 하 여 붙여 넣는 방법에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989538"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>가상 컴퓨터에 복사 하 여 붙여넣기: Azure 방호

이 문서에서는 Azure 방호를 사용 하는 경우 가상 컴퓨터에서 텍스트를 복사 하 여 붙여 넣는 방법을 설명 합니다. VM을 사용 하려면 먼저 [요새 호스트를 만드는](bastion-create-host-portal.md)단계를 수행 해야 합니다. 그런 다음 [RDP](bastion-connect-vm-rdp.md) 또는 [SSH](bastion-connect-vm-ssh.md)중 하나를 사용 하 여 작업 하려는 VM에 연결 합니다.

고급 클립보드 API 액세스를 지 원하는 브라우저의 경우 로컬 장치에서 응용 프로그램 간에 복사 하 여 붙여 넣는 것과 같은 방식으로 로컬 장치와 원격 세션 사이에서 텍스트를 복사 하 여 붙여넣을 수 있습니다. 다른 브라우저의 경우에는 요새 클립보드 액세스 도구 팔레트를 사용할 수 있습니다.

   ![클립보드 허용](./media/bastion-vm-manage/allow.png)

텍스트 복사/붙여넣기만 지원 됩니다. 직접 복사 및 붙여넣기의 경우, 사용자의 브라우저에서 사용자의 대화 상자에 대한 액세스를 요청할 수 있습니다. 웹 페이지에서 클립보드에 액세스할 **수** 있습니다.

## <a name="to"></a>원격 세션에 복사

[Azure Portal ](https://portal.azure.com)를 사용 하 여 가상 머신에 연결한 후 다음 단계를 완료 합니다.

1. 로컬 장치의 텍스트/콘텐츠를 로컬 클립보드에 복사 합니다.
1. 원격 세션 중에 두 화살표를 선택 하 여 방호 클립보드 액세스 도구 팔레트를 시작 합니다. 화살표는 세션의 왼쪽 가운데에 있습니다.

   ![도구 팔레트](./media/bastion-vm-manage/left.png)

   ![클립보드](./media/bastion-vm-manage/clipboard.png)
1. 일반적으로 복사 된 텍스트는 자동으로 방호 복사 붙여넣기 팔레트에 표시 됩니다. 텍스트가 없으면 색상표의 텍스트 영역에 텍스트를 붙여넣습니다.
1. 텍스트 영역이 텍스트 영역에 있으면 원격 세션에 붙여 넣을 수 있습니다.

   ![넣은](./media/bastion-vm-manage/local.png)

## <a name="from"></a>원격 세션에서 복사

[Azure Portal ](https://portal.azure.com)를 사용 하 여 가상 머신에 연결한 후 다음 단계를 완료 합니다.

1. 원격 세션에서 원격 클립보드로 텍스트/콘텐츠를 복사 합니다 (Ctrl + C 사용).

   ![도구 팔레트](./media/bastion-vm-manage/remote.png)
1. 원격 세션 중에 두 화살표를 선택 하 여 방호 클립보드 액세스 도구 팔레트를 시작 합니다. 화살표는 세션의 왼쪽 가운데에 있습니다.

   ![클립보드](./media/bastion-vm-manage/clipboard2.png)
1. 일반적으로 복사 된 텍스트는 자동으로 방호 복사 붙여넣기 팔레트에 표시 됩니다. 텍스트가 없으면 색상표의 텍스트 영역에 텍스트를 붙여넣습니다.
1. 텍스트 영역이 텍스트 영역에 있으면 로컬 장치에 붙여 넣을 수 있습니다.

   ![넣은](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>다음 단계

[요새 FAQ](bastion-faq.md)를 읽어 보세요.