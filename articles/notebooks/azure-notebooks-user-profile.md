---
title: Azure Notebooks에 사용할 사용자 프로필 및 ID
description: Azure Notebooks를 사용하여 사용자 프로필 및 ID를 만들고 관리하는 방법
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: b8c21b908ca9162a7e44c7af1e222babc6ee1eb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631977"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Azure Notebooks에 대한 프로필 및 사용자 ID

Azure Notebooks의 강력하고 공동 작업 공간 내, 사용자 프로필에서는 공용 이미지를 다른 사용자에게 보여줍니다.

[![Azure Notebooks 프로필 페이지](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

사용자 ID는 프로젝트와 Notebook을 공유하는 데 사용하는 URL의 일부입니다. 다음 목록에서는 다른 URL 패턴을 설명합니다.

- `https://notebooks.azure.com/<user_id>`: 프로필 페이지
- `https://notebooks.azure.com/<user_id>/projects`: 프로젝트 모든 프로젝트를 볼 수 있지만, 다른 사용자는 공용 프로젝트만 볼 수 있습니다.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: 프로젝트 파일
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: 특정 프로젝트의 클론
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: 특정 노트북 또는 파일의 HTML 미리 보기

## <a name="your-user-id"></a>사용자 ID

처음 Azure Notebooks에 로그인하는 경우 계정은 "anon-idr3ca"와 같은 임시 사용자 ID가 자동으로 할당됩니다. "anon-"으로 시작하는 사용자 ID인 경우 Azure Notebooks에는 로그인 할 때마다 사용자 ID를 변경하라는 메시지가 나타납니다.

![Azure Notebooks에 로그인 할 때 사용자 ID를 만들라는 메시지가 나타납니다.](media/accounts/create-user-id.png)

**사용자 ID 구성** 명령은 임시 사용자 이름 옆에 나타납니다.

![임시 ID를 사용하는 경우 나타나는 사용자 ID 명령을 구성합니다.](media/accounts/configure-user-id-command.png)

프로필 페이지에서 언제든지 사용자 ID를 변경할 수 있습니다.

사용자 ID 4 대 및 16 개 문자, 숫자 및 하이픈 간에 구성 되어야 합니다. 다른 문자는 허용되지 않으며 사용자 ID는 하이픈으로 시작하거나 끝날 수 없으며, 행에 여러 개의 하이픈을 사용할 수 없습니다. 모든 Azure Notebooks 계정에서 사용자 Id 고유 이기 때문에 메시지가 표시 될 수 있습니다, 그리고 "사용자 ID가 이미 사용에서 합니다." (메시지 나타나기도 Microsoft 상표 사용자 ID로 사용 하려는 경우) 이러한 경우에는 다른 사용자 ID를 선택 합니다.

> [!Important]
> ID를 변경하는 경우 이전 ID를 사용하여 공유한 URL을 사용할 수 없습니다. 사용자 ID를 이전 ID로 다시 변경하여 링크의 유효성을 다시 검사할 수 있습니다. 하지만 다른 사용자는 사용하지 않는 ID를 클레임할 수 있습니다.

## <a name="your-profile"></a>프로필

프로필은 URL, `https://notebooks.azure.com/<user_id>`에 공개적으로 볼 수 있는 정보로 구성 됩니다. 프로필 페이지에는 최근에 사용한 프로젝트 및 별점이 부여된 모든 프로젝트도 보여줍니다.

프로필을 편집하려면 프로필 페이지에서 **프로필 정보 편집** 명령을 사용합니다. 프로필의 섹션은 다음과 같습니다.

| 섹션 | 목차 |
| --- | --- |
| 프로필 사진 | 프로필 페이지에 표시되는 이미지입니다. |
| 계정 정보 | 표시 이름, 사용자 ID와 공용 이메일 계정입니다. 여기에서 이메일 계정은 다른 사용자가 사용자에게 연락할 수 있는 수단을 제공하며 Azure Notebooks에 로그인하는 데 사용하는 [계정](azure-notebooks-user-account.md)과는 다를 수 있습니다. |
| 프로필 정보 | 사용자의 위치, 회사, 직위, 웹 사이트 및 자신에 대한 간단한 설명입니다. |
| 소셜 프로필 | 공유하려는 경우 사용자의 GItHub, Twitter 및 Facebook ID입니다. |
| 개인 정보 설정 | 두 가지 명령을 제공합니다.<ul><li>**내 프로필 내보내기**: 사진, 프로필 정보 및 보안 로그를 포함하여 Azure Notebooks의 프로필에 저장하는 모든 정보가 포함된 *.zip* 파일을 만들고 다운로드합니다.</li><li>**내 계정 삭제**: Azure Notebooks에 저장된 모든 개인 정보를 영구적으로 삭제합니다.</li></ul> |
| 사이트 기능 활성화 | Azure Notebooks의 동작 측면을 컨트롤할 수 있습니다.<ul><li>**Notebook에 대한 통합 프런트 엔드**: 더 빠른 Notebook 시작 및 더 나은 지속성을 제공합니다.</li><li>**기본적으로 JupyterLab 실행**: Azure Notebooks는 기본적으로 대부분의 사용자를 위한 적합한 간단한 사용자 인터페이스를 제공 합니다. JupyterLab은 숙련된 사용자를 위한 강하지만 더 복잡한 인터페이스를 제공합니다.</li><li>**VNext 웹 사이트**: 이 설명서에 표시된 최신 웹 레이아웃을 사용하도록 설정합니다.</li></ul> |

## <a name="next-steps"></a>다음 단계  

> [!div class="nextstepaction"]
> [자습서: 선형 회귀를 위해 Jupyter 노트 실행 만들기](tutorial-create-run-jupyter-notebook.md)
