---
title: 개발자를 위한 API 및 도구
description: Azure Batch 서비스를 사용하여 솔루션을 개발하는 데 사용할 수 있는 API 및 도구에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146405"
---
# <a name="overview-of-batch-apis-and-tools"></a>Batch API 및 도구 개요

Azure Batch를 통한 병렬 워크로드 처리는 일반적으로 Batch API 중 하나를 사용하여 프로그래밍 방식으로 수행됩니다. 클라이언트 애플리케이션 또는 서비스는 Batch API를 통해 Batch 서비스와 통신할 수 있습니다. Batch API를 사용하면 가상 머신 또는 클라우드 서비스 중 하나인 컴퓨팅 노드의 풀을 만들고 관리할 수 있습니다. 그런 다음 해당 노드에서 작업과 태스크를 실행하도록 예약할 수 있습니다.

조직의 대규모 워크로드를 효율적으로 처리하거나 고객에게 서비스 프런트 엔드를 제공할 수 있으므로 요청 시 또는 일정에 따라 작업 및 태스크를 단일, 수백 또는 수천 개의 노드에서 실행할 수 있습니다. 또한 [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json)와 같은 도구에서 관리하는 대규모 워크플로의 일부로 Azure Batch를 사용할 수도 있습니다.

> [!TIP]
> Azure Batch에서 사용되는 기능 및 워크플로에 대한 자세한 내용은 [Batch 서비스 워크플로 및 리소스](batch-service-workflow-features.md)를 참조하세요.

## <a name="azure-accounts-for-batch-development"></a>Batch 개발을 위한 Azure 계정

Batch 솔루션을 개발할 경우 Azure 구독에서는 다음 계정을 사용합니다.

- **Batch 계정** - 풀, 컴퓨팅 노드, 작업 및 태스크를 포함하여 Azure Batch 리소스는 Azure [Batch 계정](accounts.md)과 연결됩니다. 애플리케이션에서 Batch 서비스를 요청할 때는 Azure Batch 계정 이름, 계정의 URL 및 액세스 키 또는 Azure Active Directory 토큰을 사용하여 요청을 인증합니다. Azure Portal 또는 프로그래밍 방식으로 [배치 계정을 만들 수 있습니다](batch-account-create-portal.md).
- **스토리지 계정** - Batch는 [Azure Storage](../storage/index.yml)에서 파일 작업을 기본적으로 지원합니다. 거의 모든 Batch 시나리오에서는 Azure Blob Storage를 사용하여 태스크에서 실행하는 프로그램 및 프로그램에서 처리하는 데이터를 준비하고, 생성되는 출력 데이터를 저장합니다. 일반적으로 각 배치 계정은 하나의 해당 스토리지 계정과 연결됩니다.

## <a name="service-level-and-management-level-apis"></a>서비스 수준 및 관리 수준 API

Azure Batch에는 서비스 수준과 관리 수준을 위한 두 가지 API 세트가 있습니다. 두 API 세트는 대개 유사하게 명명되지만 서로 다른 결과를 반환합니다.

관리 API의 작업만 활동 로그에서 추적됩니다. 서비스 수준 API는 Azure Resource Management 계층(management.azure.com)을 우회하며 기록되지 않습니다.

예를 들어 [풀을 삭제하기 위한 Batch 서비스 API](/rest/api/batchservice/pool/delete)는 배치 계정(`DELETE {batchUrl}/pools/{poolId}`)을 직접 대상으로 합니다.

한편 [풀을 삭제하기 위한 Batch 관리 API<t2/>는 management.azure.com 계층(](/rest/api/batchmanagement/pool/delete))을 대상으로 합니다.`DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>Batch 서비스 API

애플리케이션과 서비스는 직접 REST API 호출을 실행하거나 다음 클라이언트 라이브러리 중 하나 이상을 사용하여 Azure Batch 워크로드를 실행하고 관리할 수 있습니다.

| API | API 참조 | 다운로드 | 자습서 | 코드 샘플 | 추가 정보 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[Azure REST API-Docs](/rest/api/batchservice/) |해당 없음 |- |- | [지원되는 버전](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[.NET 용 Azure SDK-Docs](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[자습서](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [릴리스 정보](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Python 용 Azure SDK-Docs](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[자습서](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [추가 정보](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[JavaScript 용 Azure SDK-Docs](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[자습서](batch-nodejs-get-started.md) |- | [추가 정보](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Java 용 Azure SDK-Docs](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [추가 정보](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch 관리 API

Batch용 Azure Resource Manager API는 Batch 계정에 대한 프로그래밍 방식 액세스를 제공합니다. 이러한 API를 사용하면 Microsoft.Batch 공급자를 통해 Batch 계정, 할당량, 애플리케이션 패키지 및 기타 리소스를 프로그래밍 방식으로 관리할 수 있습니다.  

| API | API 참조 | 다운로드 | 자습서 | 코드 샘플 |
| --- | --- | --- | --- | --- |
| **Batch 관리 REST** |[Azure REST API-Docs](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch 관리 .NET** |[.NET 용 Azure SDK-Docs](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [자습서](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Batch 관리 Python** |[Python 용 Azure SDK-Docs](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Batch 관리 Node.js** |[JavaScript 용 Azure SDK-Docs](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Batch 관리 Java** |[Java 용 Azure SDK-Docs](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Batch 명령줄 도구

이러한 명령줄 도구는 Batch 서비스 및 Batch 관리 API와 동일한 기능을 제공합니다. 

- [Batch PowerShell cmdlet](/powershell/module/az.batch/): [Azure PowerShell](/powershell/azure/) 모듈의 Azure Batch cmdlet을 사용하여 PowerShell로 Batch 리소스를 관리할 수 있습니다.
- [Azure CLI](/cli/azure): Azure CLI는 Batch 서비스 및 Batch 관리 서비스를 포함하여 여러 Azure 서비스와 상호 작용하기 위한 셸 명령을 제공하는 크로스 플랫폼 도구 세트입니다. Batch에서 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Batch 리소스 관리](batch-cli-get-started.md)를 참조하세요.

## <a name="other-tools-for-application-development"></a>애플리케이션 개발을 위한 기타 도구

다음과 같은 추가 도구는 Batch 애플리케이션 및 서비스를 빌드 및 디버그하는 데 도움이 될 수 있습니다.

- [Azure 포털](https://portal.azure.com/): Azure Portal에서 Batch 풀, 작업 및 태스크를 만들고 모니터링하고 삭제할 수 있습니다. 작업을 실행하는 동안 해당하는 리소스 풀 및 다른 리소스 풀에 대한 상태 정보를 보고 풀에 있는 컴퓨팅 노드에서 파일을 다운로드할 수 있습니다. 예를 들어 문제를 해결하는 동안 실패한 작업의 `stderr.txt`를 다운로드할 수 있습니다. 또한 컴퓨팅 노드에 로그인하는 데 사용할 수 있는 RDP(원격 데스크톱) 파일을 다운로드할 수 있습니다.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): Batch Explorer(이전 이름은 BatchLabs)는 Azure Batch 애플리케이션을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. Mac, Linux 또는 Windows의 경우 [설치 패키지](https://azure.github.io/BatchExplorer/)를 다운로드합니다.
- [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard): Batch Shipyard는 Azure Batch에서 컨테이너 기반 일괄 처리 및 HPC 워크로드를 프로비저닝, 실행 및 모니터링하는 데 도움이 되는 도구입니다.
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/): 엄격히 말해 Azure Batch 도구는 아니지만 Storage Explorer는 Batch 솔루션을 개발 및 디버깅하는 동안 유용할 수 있는 또 다른 도구입니다.

## <a name="additional-resources"></a>추가 리소스

- Batch 애플리케이션에서 이벤트 기록에 대해 알아보려면 [진단 평가 및 모니터링을 위한 Batch 메트릭, 경고 및 로그](batch-diagnostics.md)를 참조하세요.
- Batch 서비스로 인해 발생한 이벤트에 대한 참조 정보는 [Batch 분석](batch-analytics.md)을 참조하세요.
- 컴퓨팅 노드의 환경 변수에 대한 정보는 [Azure Batch 런타임 환경 변수](batch-compute-node-environment-variables.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업(job) 및 작업(task)과 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [.NET용 Azure Batch 라이브러리 시작](tutorial-parallel-dotnet.md) 에서는 일반적인 Batch 워크플로를 사용하여 간단한 워크로드를 실행하는 데 C# 및 Batch .NET 라이브러리를 사용하는 방법을 알아봅니다. [Python 버전](tutorial-parallel-python.md) 및 [Node.js 자습서](batch-nodejs-get-started.md)도 사용할 수 있습니다.
- [GitHub의 코드 샘플](https://github.com/Azure-Samples/azure-batch-samples)을 다운로드하여 샘플 워크로드를 예약하고 처리하기 위해 C# 및 Python이 Batch와 상호 작용하는 방법을 확인합니다.
