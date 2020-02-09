---
title: Azure Cosmos DB 계정에서 노트북 사용 (미리 보기)
description: Azure Cosmos DB의 기본 제공 노트북을 사용 하 여 포털 내에서 데이터를 분석 하 고 시각화할 수 있습니다. 이 문서에서는 Cosmos 계정에 대해이 기능을 사용 하도록 설정 하는 방법을 설명 합니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768012"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Azure Cosmos DB 계정에 대해 노트북 사용 (미리 보기)

> [!IMPORTANT]
> Azure Cosmos DB에 대한 기본 제공 노트북은 현재 오스트레일리아 동부, 미국 동부, 미국 동부 2, 서유럽, 미국 동부, 동남 아시아, 영국 남부, 유럽 서부 및 미국 서 부 2와 같은 Azure 지역에서 사용할 수 있습니다. 노트북을 사용 하려면 전자 필기장을 사용 하 [여 새 계정을 만들거나](#enable-notebooks-in-a-new-cosmos-account) 이러한 지역 중 하나에 있는 [기존 계정에서 노트북을 사용 하도록 설정](#enable-notebooks-in-an-existing-cosmos-account) 합니다.

Azure Cosmos DB의 기본 제공 Jupyter 노트북을 사용 하 여 Azure Portal에서 데이터를 분석 하 고 시각화할 수 있습니다. 이 문서에서는 Azure Cosmos DB 계정에 대해 이 기능을 사용하도록 설정하는 방법을 설명합니다.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>새 Cosmos 계정에서 노트북을 사용 하도록 설정
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **리소스 만들기** > **데이터베이스** > **Azure Cosmos DB**를 선택합니다.
1. **Azure Cosmos DB 계정 만들기** 페이지에서 **전자 필기장**을 선택 합니다. 
 
    ![Azure Cosmos DB 만들기 블레이드에서 노트북 옵션을 선택 합니다.](media/enable-notebooks/create-new-account-with-notebooks.png)
1. **검토 + 만들기**를 선택합니다. **네트워크** 및 **태그** 옵션을 건너뛸 수 있습니다. 
1. 계정 설정을 검토한 다음, **만들기**를 선택합니다. 계정을 만드는 데 몇 분이 걸립니다. 포털 페이지에 **배포가 완료됨**이 표시되기를 기다립니다. 

    ![Azure Portal 알림 창](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. **리소스로 이동**을 선택하여 Azure Cosmos DB 계정 페이지로 이동합니다. 

    ![Azure Cosmos DB 계정 페이지](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. **데이터 탐색기** 창으로 이동 합니다. 이제 전자 필기장 작업 영역이 표시 됩니다.

    ![새 Azure Cosmos DB 노트북 작업 영역](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>기존 Cosmos 계정에서 노트북을 사용 하도록 설정
기존 계정에서 노트북을 사용 하도록 설정할 수도 있습니다. 이 단계는 계정 마다 한 번만 수행 해야 합니다.

1. Cosmos 계정의 **데이터 탐색기** 창으로 이동 합니다.
1. **전자 필기장 사용**을 선택 합니다.

    ![데이터 탐색기에서 새 노트북 작업 영역 만들기](media/enable-notebooks/enable-notebooks-workspace.png)
1. 그러면 새 전자 필기장 작업 영역을 만들라는 메시지가 표시 됩니다. **설정 완료를 선택 합니다.**
1. 이제 계정이 노트북을 사용 하도록 설정 되었습니다.

## <a name="create-and-run-your-first-notebook"></a>첫 번째 노트북 만들기 및 실행

노트북을 사용할 수 있는지 확인 하려면 샘플 노트북에서 노트북 중 하나를 선택 합니다. 그러면 노트의 복사본이 작업 영역에 저장 되 고 열립니다.

이 예제에서는 **Gettingstarted를 사용 합니다. ipa**. 

![Geta 뷰를 시작 했습니다. ipynb 노트북](media/enable-notebooks/select-getting-started-notebook.png)

노트북을 실행 하려면:
1. Python 코드를 포함 하는 첫 번째 코드 셀을 선택 합니다. 
1. **실행** 을 선택 하 여 셀을 실행 합니다. **Shift + Enter** 를 사용 하 여 셀을 실행할 수도 있습니다.
1. 리소스 창을 새로 고쳐 만들어진 데이터베이스 및 컨테이너를 확인 합니다.

    ![시작 노트북 실행](media/enable-notebooks/run-first-notebook-cell.png)

**내 전자** 필기장 메뉴에서 **파일 업로드** **를 선택 하 여** 새 노트북을 만들거나 기존 노트북 (.ipokb) 파일을 업로드할 수도 있습니다. 

![새 노트북 만들기 또는 업로드](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>다음 단계

- [Jupyter 노트북 Azure Cosmos DB](cosmosdb-jupyter-notebooks.md) 의 이점에 대해 알아봅니다.
