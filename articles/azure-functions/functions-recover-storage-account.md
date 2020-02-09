---
title: '오류 문제 해결: Azure Functions 런타임에 연결할 수 없음'
description: 잘못된 스토리지 계정 문제를 해결하는 방법에 대해 알아봅니다.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063784"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>오류 해결: "Azure Functions 런타임에 연결할 수 없습니다."

이 문서는 Azure Portal에 표시 되는 다음과 같은 오류 문자열의 문제를 해결 하는 데 도움이 됩니다.

> "오류: Azure Functions 런타임에 연결할 수 없습니다. 저장소 구성에 대한 자세한 내용을 보려면 여기를 클릭 하십시오. "

Azure Functions 런타임를 시작할 수 없는 경우이 문제가 발생 합니다. 문제의 가장 일반적인 이유는 함수 앱이 해당 저장소 계정에 대한 액세스를 손실 하는 것입니다. 자세한 내용은 [Storage 계정 요구 사항](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)을 참조 하세요.

이 문서의 나머지 부분에서는 각 사례를 식별 하 고 해결 하는 방법을 비롯 하 여이 오류의 다음 원인을 해결 하는 데 도움이 됩니다.

## <a name="storage-account-was-deleted"></a>저장소 계정이 삭제 됨

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제 되 면 함수가 작동 하지 않습니다.

응용 프로그램 설정에서 저장소 계정 이름을 조회 하 여 시작 합니다. `AzureWebJobsStorage` 또는 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`에는 연결 문자열에서 래핑된 저장소 계정의 이름이 포함 됩니다. 자세한 내용은 [Azure Functions에 대한 앱 설정 참조](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)를 참조 하세요.

Azure Portal에서 저장소 계정을 검색 하 여 여전히 존재 하는지 확인 합니다. 저장소 계정을 삭제 한 경우 저장소 계정을 다시 만들고 저장소 연결 문자열을 대체 합니다. 함수 코드가 손실 되어 다시 배포 해야 합니다.

## <a name="storage-account-application-settings-were-deleted"></a>저장소 계정 응용 프로그램 설정이 삭제 되었습니다.

이전 단계에서 저장소 계정 연결 문자열을 찾을 수 없는 경우 삭제 되거나 덮어쓴 것일 수 있습니다. 응용 프로그램 설정을 삭제 하는 것은 응용 프로그램 설정을 설정 하기 위해 배포 슬롯 또는 Azure Resource Manager 스크립트를 사용 하는 경우 가장 일반적으로 발생 합니다.

### <a name="required-application-settings"></a>필수 애플리케이션 설정

* 필수:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 소비 계획 함수에 필요 합니다.
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

자세한 내용은 [Azure Functions에 대한 앱 설정 참조](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)를 참조 하세요.

### <a name="guidance"></a>지침

* 이러한 설정에 대해 "슬롯 설정"을 선택 하지 마세요. 배포 슬롯을 교환 하는 경우 함수 앱이 중단 됩니다.
* 이러한 설정은 자동 배포의 일부로 수정 하지 마십시오.
* 이러한 설정은 생성 시에 제공되어야 하며 유효한 상태여야 합니다. 이러한 설정을 포함 하지 않는 자동화 된 배포는 나중에 설정이 추가 된 경우에도 실행 되지 않는 함수 앱을 생성 합니다.

## <a name="storage-account-credentials-are-invalid"></a>저장소 계정 자격 증명이 잘못 되었습니다.

저장소 키를 다시 생성 하는 경우 앞에서 설명한 저장소 계정 연결 문자열을 업데이트 해야 합니다. 저장소 키 관리에 대한 자세한 내용은 [Azure Storage 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)를 참조 하세요.

## <a name="storage-account-is-inaccessible"></a>저장소 계정에 액세스할 수 없습니다.

함수 앱은 저장소 계정에 액세스할 수 있어야 합니다. 저장소 계정에 대한 함수 앱의 액세스를 차단 하는 일반적인 문제는 다음과 같습니다.

* 함수 앱은 저장소 계정에 대한 트래픽을 허용 하는 올바른 네트워크 규칙 없이 App Service Environment에 배포 됩니다.

* 저장소 계정 방화벽이 사용 하도록 설정 되어 있고 기능에서 들어오고 나가는 트래픽을 허용 하도록 구성 되어 있지 않습니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

## <a name="daily-execution-quota-is-full"></a>일일 실행 할당량이 꽉 찼습니다.

매일 실행 할당량이 구성 된 경우 함수 앱을 일시적으로 사용 하지 않도록 설정 하 여 많은 포털 컨트롤을 사용할 수 없게 됩니다. 

[Azure Portal](https://portal.azure.com)의 할당량을 확인 하려면 함수 앱에서 **플랫폼 기능** > **함수 앱 설정** 을 선택 합니다. 설정한 **일일 사용 할당량** 을 초과 하는 경우 다음 메시지가 표시 됩니다.

  > "함수 앱가 일일 사용 할당량에 도달 하 여 다음 24 시간 프레임까지 중지 되었습니다."

이 문제를 해결 하려면 일일 할당량을 제거 하거나 늘리고 앱을 다시 시작 합니다. 그렇지 않으면 다음 날까지 응용 프로그램의 실행이 차단 됩니다.

## <a name="app-is-behind-a-firewall"></a>앱이 방화벽 뒤에 있습니다.

다음 이유 중 하나로 인해 함수 런타임에 접근할 수 없습니다.

* 함수 앱은 [내부적으로 부하 분산 된 App Service Environment](../app-service/environment/create-ilb-ase.md) 에서 호스팅되며 인바운드 인터넷 트래픽을 차단 하도록 구성 됩니다.

* 함수 앱에는 인터넷 액세스를 차단 하도록 구성 된 [인바운드 IP 제한이](functions-networking-options.md#inbound-ip-restrictions) 있습니다. 

Azure Portal는 실행 중인 앱을 직접 호출 하 여 함수 목록을 가져오고 Kudu 엔드포인트에 대한 HTTP 호출을 수행 합니다. 플랫폼 수준 설정은 **플랫폼 기능** 탭에서 계속 사용할 수 있습니다.

App Service Environment 구성을 확인 하려면:
1. App Service Environment 있는 서브넷의 NSG (네트워크 보안 그룹)로 이동 합니다.
1. 인바운드 규칙의 유효성을 검사 하 여 응용 프로그램에 액세스 하는 컴퓨터의 공용 IP에서 들어오는 트래픽을 허용 합니다. 
   
앱을 실행 하는 가상 네트워크에 연결 된 컴퓨터 또는 가상 네트워크에서 실행 되는 가상 컴퓨터에서 포털을 사용할 수도 있습니다. 

인바운드 규칙 구성에 대한 자세한 내용은 [App Service Environment에 대한 네트워킹 고려 사항](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)의 "네트워크 보안 그룹" 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

함수 앱 모니터링에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [모니터 Azure Functions](functions-monitoring.md)
