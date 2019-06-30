---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 6e0175173f17ae0958522517360b94ee80f3b2f9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148979"
---
## <a name="prerequisites"></a>필수 조건

### <a name="get-sample-code"></a>샘플 코드 가져오기

이 자습서에서는 [이전 자습서](../articles/container-registry/container-registry-tutorial-quick-task.md)의 단계를 이미 완료했고, 샘플 리포지토리를 포크 및 복제했다고 가정합니다. 아직 완료하지 않은 경우 계속 진행하기 전에 이전 자습서의 [필수 조건](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) 섹션에 있는 단계를 완료하세요.

### <a name="container-registry"></a>컨테이너 레지스트리

이 자습서를 완료하려면 Azure 구독에 Azure 컨테이너 레지스트리가 있어야 합니다. 레지스트리가 필요한 경우 [이전 자습서](../articles/container-registry/container-registry-tutorial-quick-task.md) 또는 [빠른 시작: Azure CLI를 사용하여 컨테이너 레지스트리 만들기](../articles/container-registry/container-registry-get-started-azure-cli.md)를 참조하세요.

## <a name="create-a-github-personal-access-token"></a>GitHub 개인용 액세스 토큰 만들기

Git 리포지토리에 커밋할 때 작업을 트리거하려면 ACR 작업에서 리포지토리에 액세스하기 위해 PAT가 필요합니다. PAT가 아직 없는 경우 다음 단계에 따라 GitHub에서 하나를 생성합니다.

1. [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new )에 있는 GitHub의 PAT 만들기 페이지로 이동합니다.
1. 토큰에 대한 짧은 **설명**을 입력합니다(예: "ACR 작업 데모").
1. **repo**(리포지토리) 아래에서 **repo:status** 및 **public_repo**를 사용하도록 설정합니다.

   ![GitHub에 있는 개인용 액세스 토큰 생성 페이지의 스크린샷][build-task-01-new-token]

1. **토큰 생성** 단추를 선택합니다(암호를 확인하라는 메시지가 표시될 수 있음).
1. 생성된 토큰을 복사하여 **보안 위치**에 저장합니다. 이 토큰은 다음 섹션에서 작업을 정의할 때 사용합니다.

   ![GitHub에 생성된 개인용 액세스 토큰의 스크린샷][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
