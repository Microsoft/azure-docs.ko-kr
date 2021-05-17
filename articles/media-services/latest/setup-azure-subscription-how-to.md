---
title: Azure 구독을 찾는 방법
description: 환경을 설정할 수 있도록 Azure 구독을 찾습니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: cli,portal, devx-track-azurecli
ms.openlocfilehash: f16cc83257dbf6419fc794f34e4cd6df033d3f8b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281418"
---
# <a name="find-your-azure-subscription"></a>Azure 구독 찾기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[포털](#tab/portal/)

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure 서비스 제목에서 구독을 선택합니다. (구독이 나열되지 않은 경우 Azure AD 테넌트를 전환해야 할 수도 있습니다.) 구독 ID는 두 번째 열에 나열됩니다.
1. 구독 ID를 복사하여 나중에 사용할 수 있도록 선택한 텍스트 문서에 붙여넣습니다.

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Azure CLI 사용

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the "task-" prepended to the file name. -->

### <a name="list-your-azure-subscriptions-with-cli"></a>CLI를 사용하여 Azure 구독 나열

[!INCLUDE [List your Azure subscriptions with CLI](./includes/task-list-set-subscription-cli.md)]

### <a name="see-also"></a>참조

* [Azure CLI](/cli/azure/ams)

---

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)
