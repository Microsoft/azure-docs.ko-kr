---
title: Azure Functions 런타임에 연결할 수 없는 문제를 해결하는 방법을 설명합니다.
description: 잘못된 저장소 계정 문제를 해결하는 방법에 대해 알아봅니다.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 6057fa52cd2f1e9b9fd525723f96ab66983fb5d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020301"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>"Functions 런타임에 연결할 수 없음" 문제를 해결하는 방법


## <a name="error-text"></a>오류 텍스트
이 문서에서는 Functions 포털에 표시되는 다음 오류를 해결하는 방법을 설명합니다.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>요약
Azure Functions 런타임을 시작할 수 없으면 이 문제가 발생합니다. 이 오류가 발생하는 가장 일반적인 원인은 함수 앱이 저장소 계정에 액세스할 수 없기 때문입니다. [여기서 저장소 계정 요구 사항에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>문제 해결
이 문서에서는 가장 흔히 발생하는 4가지 오류 사례, 해당 오류를 확인하는 방법 및 각 사례를 해결하는 방법을 안내합니다.

1. 저장소 계정이 삭제됨
1. 저장소 계정 애플리케이션 설정이 삭제됨
1. 저장소 계정 자격 증명이 잘못됨
1. 저장소 계정에 액세스할 수 없음
1. 일일 실행 할당량 포화 상태

## <a name="storage-account-deleted"></a>저장소 계정이 삭제됨

모든 함수 앱은 저장소 계정이 있어야 작동합니다. 해당 계정이 삭제되면 함수가 작동하지 않습니다.

### <a name="how-to-find-your-storage-account"></a>저장소 계정을 찾는 방법

먼저 애플리케이션 설정에서 저장소 계정 이름을 조회합니다. `AzureWebJobsStorage` 또는 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 중 하나에 저장소 계정 이름이 연결 문자열에 래핑된 상태로 포함되어 있습니다. 자세한 내용은 [이 문서의 애플리케이션 설정 참조](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)에서 확인하세요.

Azure Portal에서 저장소 계정을 검색하여 계정이 아직 있는지 확인합니다. 계정이 삭제된 경우에는 저장소 계정을 다시 만들고 저장소 연결 문자열을 바꿔야 합니다. 그러면 함수 코드가 손실되므로 다시 배포해야 합니다.

## <a name="storage-account-application-settings-deleted"></a>저장소 계정 애플리케이션 설정이 삭제됨

이전 단계에서 저장소 계정 연결 문자열을 찾지 못했다면 설정을 삭제했거나 덮어썼을 가능성이 높습니다. 일반적으로는 배포 슬롯 또는 Azure Resource Manager 스크립트를 사용하여 애플리케이션 설정을 지정할 때 앱 설정을 삭제합니다.

### <a name="required-application-settings"></a>필수 애플리케이션 설정

* 필수
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 사용 계획 함수의 경우 필수
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[이 문서에서 이러한 응용 프로그램 설정에 대해 자세히 살펴보세요.](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>지침

* 이러한 설정에 대해서는 "슬롯 설정"을 선택하지 않습니다. 배포 슬롯을 교환하면 함수가 손상됩니다.
* 자동화 된 배포의 일부로 이러한 설정을 수정 하지 마십시오.
* 이러한 설정은 생성 시에 제공되어야 하며 유효한 상태여야 합니다. 이러한 설정을 포함하지 않는 자동화된 배포를 사용하는 경우 나중에 설정을 추가하더라도 앱이 작동하지 않습니다.

## <a name="storage-account-credentials-invalid"></a>저장소 계정 자격 증명이 잘못됨

저장소 키를 다시 생성하는 경우에는 위의 저장소 계정 연결 문자열을 업데이트해야 합니다. [이 문서에서 저장소 키 관리에 대해 자세히 살펴보세요.](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>저장소 계정에 액세스할 수 없음

Function App은 저장소 계정에 액세스할 수 있어야 합니다. Functions의 저장소 계정 액세스를 차단하는 일반적인 문제는 다음과 같습니다.

* 저장소 계정에서 전송/수신하는 트래픽을 허용하는 올바른 네트워크 규칙을 포함하지 않고 앱 서비스 환경에 배포된 Function App
* 저장소 계정 방화벽이 사용하도록 설정되었는데 Functions에서 전송/수신하는 트래픽을 허용하도록 구성되지 않음 [이 문서에서 저장소 계정 방화벽 구성에 대해 자세히 살펴보세요.](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>일일 실행 할당량 포화 상태

매일 실행 할당량을 구성한 경우 Function App이 일시적으로 비활성화되고 포털 컨트롤은 대부분 사용할 수 없게 됩니다. 

* 확인하려면 포털에서 플랫폼 기능 열기 &gt; Function App 설정을 확인하세요. 할당량을 초과한 경우 다음 메시지가 표시됩니다.
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* 할당량을 제거하고 앱을 다시 시작하여 문제를 해결합니다.

## <a name="next-steps"></a>다음 단계

이제 Function App이 다시 작동하므로 빠른 시작과 개발자 참조를 확인하여 작업을 다시 진행하세요.

* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)  
  Azure Functions 빠른 시작을 사용하여 바로 첫 번째 함수를 만듭니다. 
* [Azure Functions 개발자 참조](functions-reference.md)  
  Azure Functions 런타임, 함수 코딩, 트리거 및 바인딩 정의에 대한 참조에 대해 더욱 기술적인 정보를 제공합니다.
* [Azure Functions 테스트](functions-test-a-function.md)  
  함수를 테스트하는 다양한 도구와 기법을 설명합니다.
* [Azure Functions 크기 조정 방법](functions-scale.md)  
  소비 호스팅 요금제, 올바른 요금제 선택 방법을 포함하여 Azure Functions에서 사용 가능한 서비스 요금제에 대해 설명합니다. 
* [Azure App Service에 대해 자세히 알아보기](../app-service/overview.md)  
  Azure Functions는 개발, 환경 변수, 진단 등의 주요 함수에 대한 Azure App Service를 활용합니다. 
