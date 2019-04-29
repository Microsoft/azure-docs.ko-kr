---
title: Batch용 Azure CLI 시작 | Microsoft Docs
description: Azure CLI에서 Azure Batch 서비스 리소스를 관리하기 위한 Batch 명령에 대한 간단한 소개를 봅니다.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 07/24/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2bd67ebb977a37c75631f16fbbf4c7dbd6bf250
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782572"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Azure CLI를 사용하여 Batch 리소스 관리

Azure CLI는 Azure 리소스를 관리하는 Azure의 명령줄 환경입니다. macOS, Linux 및 Windows에서 사용할 수 있습니다. Azure CLI는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다. Azure CLI를 사용하여 Azure Batch 계정을 관리하고 풀, 작업 및 태스크와 같은 리소스를 관리할 수 있습니다. Azure CLI를 사용하면 Batch API, Azure Portal, Batch PowerShell cmdlet으로 수행하는 여러 동일한 작업을 스크립팅할 수 있습니다.

이 문서에서는 Batch와 함께 [Azure CLI 버전 2.0](https://docs.microsoft.com/cli/azure)을 사용하는 방법에 대해 간략히 설명합니다. Azure에서 CLI를 사용하는 방법에 대한 개요는 [Azure CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조하세요.

## <a name="set-up-the-azure-cli"></a>Azure CLI 설치

[Azure Cloud Shell](../cloud-shell/overview.md)에서 최신 Azure CLI를 실행할 수 있습니다. Azure CLI를 로컬로 설치하려면 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)에서 설명하는 단계를 수행합니다.

> [!TIP]
> 서비스 업데이트 및 향상을 최대한 활용하기 위해 Azure CLI 설치를 자주 업데이트하는 것이 좋습니다.
> 
> 

## <a name="command-help"></a>명령 도움말

명령에 `-h`를 추가하여 Azure CLI의 모든 명령에 대한 도움말 텍스트를 표시할 수 있습니다. 다른 모든 옵션은 생략합니다. 예를 들면 다음과 같습니다.

* `az` 명령에 대한 도움말을 보려면 `az -h`을 입력합니다.
* CLI에서 모든 Batch 명령의 목록을 가져오려면 `az batch -h`을 사용합니다.
* Batch 계정을 만드는 도움말을 보려면 `az batch account create -h`

확실하지 않은 경우 `-h` 명령줄 옵션을 사용하여 모든 Azure CLI 명령에 대한 도움말을 봅니다.



또한 [Batch용 Azure CLI 명령](/cli/azure/batch)에 대한 자세한 내용은 Azure CLI 참조 설명서를 참조하세요. 

## <a name="log-in-and-authenticate"></a>로그인 및 인증

Batch와 함께 Azure CLI를 사용하려면 로그인하고 인증해야 합니다. 다음과 같이 간단한 두 가지 단계를 수행합니다.

1. **Azure에 로그인합니다.** Azure에 로그인하면 [Batch Management 서비스](batch-management-dotnet.md) 명령을 포함하여 Azure Resource Manager 명령에 액세스할 수 있습니다.  
2. **Batch 계정에 로그인합니다.** 배치 계정에 로그인하면 Batch 서비스 명령에 액세스할 수 있습니다.   

### <a name="log-in-to-azure"></a>Azure에 로그인

Azure에 로그인할 수 있는 몇 가지 방법이 있으며, [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)에서 자세히 설명하고 있습니다.

1. [대화형으로 로그인합니다](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 명령줄에서 Azure CLI 명령을 직접 실행하면 대화형으로 로그인합니다.
2. [서비스 주체를 사용하여 로그인합니다](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 스크립트 또는 애플리케이션에서 Azure CLI 명령을 실행할 때 서비스 주체를 사용하여 로그인합니다.

이 문서에서는 Azure에 대화형으로 로그인하는 방법을 보여 줍니다. 명령줄에서 [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login)을 입력합니다.

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

`az login` 명령은 다음과 같이 인증하는 데 사용할 수 있는 토큰을 반환합니다. 제공하는 지침에 따라 웹 페이지를 열고 Azure에 토큰을 제출합니다.

![Azure에 로그인](./media/batch-cli-get-started/az-login.png)

샘플 셸 스크립트 섹션에서 나열하는 예제는 Azure에 대화형으로 로그인하여 Azure CLI 세션을 시작하는 방법을 보여줍니다. 로그인하면 배치 계정, 키, 애플리케이션 패키지 및 할당량을 포함한 Batch Management 리소스를 사용하는 명령을 호출할 수 있습니다.  

### <a name="log-in-to-your-batch-account"></a>Batch 계정에 로그인

Azure CLI를 사용하여 풀, 작업 및 태스크와 같은 Batch 리소스를 관리하려면 배치 계정에 로그인하여 인증해야 합니다. Batch 서비스에 로그인하려면 [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login) 명령을 사용합니다. 

Batch 계정에 대한 인증에는 다음 두 가지 옵션이 있습니다.

- **Azure AD(Azure Active Directory) 인증 사용** 

    Azure AD로 인증하는 것은 Batch와 함께 Azure CLI를 사용할 때의 기본값이며, 대부분의 시나리오에 적합합니다. 
    
    이전 섹션에서 설명한 대로 Azure에 대화형으로 로그인하면 자격 증명이 캐시되므로 Azure CLI에서 동일한 자격 증명을 사용하여 Batch 계정에 로그인할 수 있습니다. 서비스 주체를 사용하여 Azure에 로그인하는 경우에도 해당 자격 증명을 사용하여 Batch 계정에 로그인합니다.

    Azure AD의 이점은 RBAC(역할 기반 액세스 제어)를 제공한다는 것입니다. RBAC를 사용하면 사용자의 액세스 권한이 계정 키 소유 여부에 관계 없이 할당된 역할에 따라 달라집니다. 계정 키를 관리하는 대신 RBAC 역할을 관리하고 Azure AD에서 액세스와 인증을 처리하도록 할 수 있습니다.  

     Azure AD를 사용하여 Batch 계정에 로그인하려면 [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login) 명령을 호출합니다. 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **공유 키 인증 사용**

    [공유 키 인증](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key)은 계정 액세스 키를 사용하여 Batch 서비스에 대한 Azure CLI 명령을 인증합니다.

    Batch 명령을 자동으로 호출하도록 Azure CLI 스크립트를 만드는 경우 공유 키 인증 또는 Azure AD 서비스 주체를 사용할 수 있습니다. 일부 시나리오에서는 공유 키 인증을 사용하는 것이 서비스 주체를 만드는 것보다 더 간단할 수 있습니다.  

    공유 키 인증을 사용하여 로그인하려면 명령줄에 `--shared-key-auth` 옵션을 포함합니다.

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

샘플 셸 스크립트 섹션에서 나열하는 예제는 Azure AD와 공유 키를 모두 사용하는 Azure CLI로 Batch 계정에 로그인하는 방법을 보여줍니다.

## <a name="use-azure-batch-cli-extension-commands"></a>Azure Batch CLI 확장 명령 사용

Azure Batch CLI 확장을 설치하면 Azure CLI를 사용하여 코드를 작성하지 않고 일괄 작업을 처음부터 끝까지 실행할 수 있습니다. 확장에서 지원하는 Batch 명령은 JSON 템플릿을 사용하여 Azure CLI로 풀, 작업 및 태스크를 만들 수 있습니다. 또한 확장 CLI 명령을 사용하여 작업 입력 파일을 Batch 계정과 연결된 Azure Storage 계정에 업로드하고 여기에서 작업 출력 파일을 다운로드할 수 있습니다. 자세한 내용은 [Azure Batch CLI 템플릿 및 파일 전송 사용](batch-cli-templates.md)을 참조하세요.

## <a name="script-examples"></a>스크립트 예제

일반적인 태스크를 수행하려면 Batch에 대한 [CLI 스크립트 예제](cli-samples.md)를 참조하세요. 이러한 예제는 계정, 풀, 작업 및 태스크를 만들고 관리하는 데 사용할 수 있는 Batch용 Azure CLI의 여러 명령을 다룹니다. 

## <a name="json-files-for-resource-creation"></a>리소스를 만들기 위한 JSON 파일

풀 및 작업같은 Batch 리소스를 만들 때 매개 변수를 명령줄 옵션으로 전달하는 대신 새 리소스의 구성이 포함된 JSON 파일을 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```azurecli
az batch pool create my_batch_pool.json
```

명령줄 옵션만 사용하여 대부분의 Batch 리소스를 만들 수 있지만, 일부 기능에서는 리소스 세부 정보를 포함하는 JSON 형식의 파일을 지정해야 합니다. 예를 들어 시작 태스크에 대한 리소스 파일을 지정하려면 JSON 파일을 사용해야 합니다.

리소스를 만드는 데 필요한 JSON 구문을 보려면 [Batch REST API 참조][rest_api] 설명서를 참조하세요. REST API 참조의 "*리소스 종류* 추가" 항목마다 해당 리소스를 만들기 위한 샘플 JSON 스크립트가 포함되어 있습니다. 이러한 샘플 JSON 스크립트를 JSON 파일의 템플릿으로 사용하여 Azure CLI와 함께 사용할 수 있습니다. 예를 들어 풀을 만들기 위한 JSON 구문을 보려면 [계정에 풀 추가][rest_add_pool]를 참조하세요.

JSON 파일을 지정하는 샘플 스크립트는 [Batch로 작업 및 태스크 실행](./scripts/batch-cli-sample-run-job.md)을 참조하세요.

> [!NOTE]
> 리소스를 만들 때 JSON 파일을 지정하면 해당 리소스에 대한 명령줄에 지정한 다른 모든 매개 변수가 무시됩니다.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Batch 리소스에 대한 효율적 쿼리

각 Batch 리소스 유형은 배치 계정을 쿼리하고 해당 형식의 리소스를 나열하는 `list` 명령을 지원합니다. 예를 들어 작업에 있는 계정 및 태스크에 풀을 나열할 수 있습니다.

```azurecli
az batch pool list
az batch task list --job-id job001
```

`list` 작업을 사용하여 Batch 서비스를 쿼리할 때 OData 절을 지정하여 반환되는 데이터의 양을 제한할 수 있습니다. 모든 필터링이 서버 쪽에서 발생하므로 요청한 데이터만 전송됩니다. 나열 작업을 수행하는 경우 이러한 절을 사용하여 대역폭(및 시간)을 줄입니다.

다음 표에서는 Batch 서비스에서 지원하는 OData 절을 설명합니다.

| 절 | 설명 |
|---|---|
| `--select-clause [select-clause]` | 각 엔터티에 대한 속성의 하위 집합을 반환합니다. |
| `--filter-clause [filter-clause]` | 지정된 OData 식과 일치하는 엔터티만 반환합니다. |
| `--expand-clause [expand-clause]` | 단일 기본 REST 호출에서 엔터티 정보를 가져옵니다. expand 절은 현재 `stats` 속성만 지원합니다. |

OData 절을 사용하는 방법을 보여 주는 샘플 스크립트는 [Batch로 작업 및 태스크 실행](./scripts/batch-cli-sample-run-job.md)을 참조하세요.

OData 절을 사용하여 효율적인 목록 쿼리를 수행하는 방법에 대한 자세한 내용은 [효율적인 Azure Batch 서비스 쿼리](batch-efficient-list-queries.md)를 참조하세요.

## <a name="troubleshooting-tips"></a>문제 해결 팁

Azure CLI 문제를 해결할 때 도움이 될 수 있는 팁은 다음과 같습니다.

* `-h` 을 사용하여 모든 CLI 명령의 **도움말 텍스트** 를 봅니다.
* `-v` 및 `-vv`를 사용하여 **자세한 정보 표시** 명령 출력을 표시합니다. `-vv` 플래그가 포함되면 Azure CLI에서 실제 REST 요청과 응답을 표시합니다. 이러한 스위치는 전체 오류 출력을 표시하는 데 유용합니다.
* `--json` 옵션을 사용하여 **명령 출력을 JSON으로** 볼 수 있습니다. 예를 들어 `az batch pool show pool001 --json` 은 JSON 형식으로 pool001의 속성을 표시합니다. 그런 다음, 이 출력을 복사하고 수정하여 `--json-file`에서 사용할 수 있습니다(이 문서 앞부분의 JSON 파일 참조).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>다음 단계

* Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.
* Batch 리소스에 대한 자세한 내용은 [개발자를 위한 Azure Batch 개요](batch-api-basics.md)를 참조하세요.
* 코드를 작성하지 않고 Batch 템플릿을 사용하여 풀, 작업 및 태스크를 만드는 방법은 [Azure Batch CLI 템플릿 및 파일 전송 사용](batch-cli-templates.md)을 참조하세요.

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
