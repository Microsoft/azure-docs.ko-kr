---
title: 개발자를 위한 API 및 도구 - Azure Batch | Microsoft Docs
description: Azure Batch 서비스를 사용하여 솔루션을 개발하는 데 사용할 수 있는 API 및 도구에 대해 알아봅니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 912e3342b4e8031b4404dffb56f1add2cc705f8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721762"
---
# <a name="overview-of-batch-apis-and-tools"></a>Batch API 및 도구 개요

Azure Batch를 통한 병렬 워크로드 처리는 일반적으로 Batch API 중 하나를 사용하여 프로그래밍 방식으로 수행됩니다. 클라이언트 애플리케이션 또는 서비스는 Batch API를 통해 Batch 서비스와 통신할 수 있습니다. Batch API를 사용하면 가상 머신 또는 클라우드 서비스 중 하나인 계산 노드의 풀을 만들고 관리할 수 있습니다. 그런 다음 해당 노드에서 작업과 태스크를 실행하도록 예약할 수 있습니다. 

조직의 대규모 워크로드를 효율적으로 처리하거나 고객에게 서비스 프런트 엔드를 제공할 수 있으므로 요청 시 또는 일정에 따라 작업 및 태스크를 단일, 수백 또는 수천 개의 노드에서 실행할 수 있습니다. 또한 [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json)와 같은 도구에서 관리하는 대규모 워크플로의 일부로 Azure Batch를 사용할 수도 있습니다.

> [!TIP]
> Batch API에서 제공하는 기능을 좀더 자세히 알아볼 준비가 되면 [개발자를 위한 Batch 기능 개요](batch-api-basics.md)를 확인하세요.
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Batch 개발을 위한 Azure 계정
Batch 솔루션을 개발할 경우 Azure 구독에서는 다음 계정을 사용합니다.

* **Batch 계정** - 풀, 계산 노드, 작업 및 태스크를 포함하여 Azure Batch 리소스는 Azure [Batch 계정](batch-api-basics.md#account)과 연결됩니다. 애플리케이션에서 Batch 서비스를 요청할 때는 Azure Batch 계정 이름, 계정의 URL 및 액세스 키 또는 Azure Active Directory 토큰을 사용하여 요청을 인증합니다. Azure Portal 또는 프로그래밍 방식으로 [Batch 계정을 만들](batch-account-create-portal.md) 수 있습니다.
* **스토리지 계정** - Batch는 [Azure Storage][azure_storage]에 있는 파일에 대한 작업을 기본적으로 지원합니다. 거의 모든 Batch 시나리오에서는 Azure Blob Storage를 사용하여 태스크에서 실행하는 프로그램 및 프로그램에서 처리하는 데이터를 준비하고, 생성되는 출력 데이터를 저장합니다. Batch의 저장소 계정 옵션은 [Batch 기능 개요](batch-api-basics.md#azure-storage-account)를 참조하세요.

## <a name="batch-service-apis"></a>Batch 서비스 API

애플리케이션과 서비스는 직접 REST API 호출을 실행하거나 다음 클라이언트 라이브러리 중 하나 이상을 사용하여 Azure Batch 워크로드를 실행하고 관리할 수 있습니다.

| API | API 참조 | 다운로드 | 자습서 | 코드 샘플 | 자세한 정보 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |N/A |- |- | [지원되는 버전](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[자습서](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [릴리스 정보](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[자습서](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [추가 정보](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[자습서](batch-nodejs-get-started.md) |- | [추가 정보](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[추가 정보][api_sample_java] | [추가 정보](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch 관리 API

Batch용 Azure Resource Manager API는 Batch 계정에 대한 프로그래밍 방식 액세스를 제공합니다. 이러한 API를 사용하면 Microsoft.Batch 공급자를 통해 Batch 계정, 할당량, 애플리케이션 패키지 및 기타 리소스를 프로그래밍 방식으로 관리할 수 있습니다.  

| API | API 참조 | 다운로드 | 자습서 | 코드 샘플 |
| --- | --- | --- | --- | --- |
| **Batch 관리 REST** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch 관리 .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [자습서](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Batch 관리 Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Batch 관리 Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Batch 관리 Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Batch 명령줄 도구

이러한 명령줄 도구는 Batch 서비스 및 Batch 관리 API와 동일한 기능을 제공합니다. 

* [Batch PowerShell cmdlet][batch_ps]: [Azure PowerShell](/powershell/azure/overview) 모듈의 Azure Batch cmdlet을 사용하여 PowerShell로 Batch 리소스를 관리할 수 있습니다.
* [Azure CLI](/cli/azure): Azure CLI는 Batch 서비스 및 Batch 관리 서비스를 포함하여 여러 Azure 서비스와 상호 작용하기 위한 셸 명령을 제공하는 크로스 플랫폼 도구 세트입니다. Batch에서 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Batch 리소스 관리](batch-cli-get-started.md)를 참조하세요.

## <a name="other-tools-for-application-development"></a>애플리케이션 개발을 위한 기타 도구

Batch 애플리케이션 및 서비스를 빌드 및 디버깅하는 데 도움이 될 수 있는 추가 도구는 다음과 같습니다.

* [Azure Portal][portal]: Azure Portal에서 Batch 풀, 작업 및 태스크를 만들고 모니터링하고 삭제할 수 있습니다. 작업을 실행하는 동안 해당하는 리소스 풀 및 다른 리소스 풀에 대한 상태 정보를 보고 풀에 있는 계산 노드에서 파일을 다운로드할 수 있습니다. 예를 들어 문제를 해결하는 동안 실패한 작업의 `stderr.txt`를 다운로드할 수 있습니다. 또한 계산 노드에 로그인하는 데 사용할 수 있는 RDP(원격 데스크톱) 파일을 다운로드할 수 있습니다.
* [Azure Batch Explorer][batch_labs]: Batch Explorer(이전 이름은 BatchLabs)는 Azure Batch 애플리케이션을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. Mac, Linux 또는 Windows의 경우 [설치 패키지](https://azure.github.io/BatchExplorer/)를 다운로드합니다.
* [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard): Batch Shipyard는 Azure Batch에서 컨테이너 기반 일괄 처리 및 HPC 워크로드를 프로비저닝, 실행 및 모니터링하는 데 도움이 되는 도구입니다.
* [Azure Storage 탐색기][storage_explorer]: 엄격히 말해 Azure Batch 도구는 아니지만 스토리지 탐색기는 Batch 솔루션을 개발 및 디버깅하는 동안 유용할 수 있는 또 다른 도구입니다.

## <a name="additional-resources"></a>추가 리소스

- Batch 애플리케이션에서 이벤트 로깅에 대해 알아보려면 [Batch 솔루션의 진단 평가 및 모니터링에 대한 로그 이벤트](batch-diagnostics.md)를 참조하세요. Batch 서비스로 인해 발생한 이벤트에 대한 참조는 [Batch 분석](batch-analytics.md)을 참조하세요.
- 계산 노드의 환경 변수에 대한 정보는 [Azure Batch 계산 노드 환경 변수](batch-compute-node-environment-variables.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 배치를 사용하려는 사용자를 위한 중요한 정보는 [개발자를 Batch 기능 개요](batch-api-basics.md)를 참고합니다. 문서에는 Batch 애플리케이션을 빌드하는 동안 사용할 수 있는 풀, 노드, 작업 및 태스크와 같은 Batch 서비스 리소스 및 여러 API 기능에 대한 자세한 내용이 포함됩니다.
* [.NET용 Azure Batch 라이브러리 시작](tutorial-parallel-dotnet.md) 에서는 일반적인 Batch 워크플로를 사용하여 간단한 워크로드를 실행하는 데 C# 및 Batch .NET 라이브러리를 사용하는 방법을 알아봅니다. [Python 버전](tutorial-parallel-python.md) 및 [Node.js 자습서](batch-nodejs-get-started.md)도 사용할 수 있습니다.
* [GitHub의 코드 샘플][github_samples]을 다운로드하여 샘플 워크로드를 예약하고 처리하기 위해 C# 및 Python가 Batch와 상호 작용하는 방법을 확인합니다.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
