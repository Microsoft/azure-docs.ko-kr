---
title: Azure Functions의 네트워킹에 대 한 질문과 대답
description: 몇 가지 가장 일반적인 질문 및 Azure Functions를 사용 하 여 네트워킹에 대 한 시나리오에 대 한 답변입니다.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 3cf6a0d080e2d8cafcab8e69a614b59a470c7aba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637049"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Azure Functions의 네트워킹에 대 한 질문과 대답

이 문서에서는 Azure Functions의 네트워킹에 대 한 질문과 대답을 나열합니다. 보다 포괄적인 개요를 참조 하세요 [네트워킹 옵션 함수](functions-networking-options.md)합니다.

## <a name="how-do-i-set-a-static-ip-in-functions"></a>함수에서 고정 IP를 설정 하는 방법

함수에 대 한 인바운드 및 아웃 바운드 IP가 정적 있어야 하는 유일한 방법은 중인 App Service Environment에서 함수를 배포 합니다. App Service Environment를 사용 하 여 자세한 내용은 문서를 사용 하 여 시작 [만들기 및 App Service Environment를 사용 하 여 내부 부하 분산 장치를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>내 함수에 인터넷 액세스를 어떻게 제한할 수 있습니까?

여러 가지 방법으로 인터넷 액세스를 제한할 수 있습니다.

* [IP 제한을](../app-service/app-service-ip-restrictions.md): IP 범위에서 함수 앱에 인바운드 트래픽을 제한 합니다.
* 모든 HTTP 트리거를 제거 합니다. 일부 응용 프로그램에서는 이것 만으로도 단순히 HTTP 트리거를 방지 하 고 다른 이벤트 소스를 사용 하 여 함수를 트리거합니다.

Azure portal 편집기 실행 중인 함수에 대 한 직접 액세스 해야 한다는 것을 염두에 두십시오. Azure portal 통해 코드 변경에는 해당 IP 허용 목록에 추가 하도록 포털을 찾아볼 하는 데 사용할 장치를 해야 합니다. 하지만 네트워크 제한을 플랫폼 기능 탭에서 아무 것도 사용할 수 있습니다.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>가상 네트워크에 함수 앱 내 제한 하는 어떻게 하나요?

모든 트래픽이 가상 네트워크를 통과 되도록 함수를 전체적으로 제한 하는 유일한 방법은 내부적으로 부하 분산 된 App Service Environment를 사용 하는 것입니다. 이 옵션 가상 네트워크 내에서 전용된 인프라에서 사이트를 배포 하 고 모든 트리거 및 가상 네트워크를 통해 트래픽을 보냅니다. 

App Service Environment를 사용 하 여 자세한 내용은 문서를 사용 하 여 시작 [만들기 및 App Service Environment를 사용 하 여 내부 부하 분산 장치를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>함수 앱에서 가상 네트워크의 리소스를 액세스 어떻게 하나요?

가상 네트워크 통합을 사용 하 여 실행 중인 함수에서 가상 네트워크의 리소스에에서 액세스할 수 있습니다. 자세한 내용은 [가상 네트워크 통합](functions-networking-options.md#virtual-network-integration)합니다.

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>서비스 끝점에 의해 보호 되는 리소스에 액세스 하려면 어떻게 해야 합니까?

가상 네트워크 통합 (현재 미리 보기로 제공)를 사용 하 여 실행 중인 함수에서 서비스 끝점 보안 리소스를 액세스할 수 있습니다. 자세한 내용은 [가상 네트워크 통합 미리 보기](functions-networking-options.md#preview-version-of-virtual-network-integration)합니다.

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>가상 네트워크의 리소스에서 함수를 트리거할 수는 방법

함수 앱 App Service Environment에 배포 해야만 리소스 가상 네트워크에서 함수를 트리거할 수 있습니다. App Service Environment를 사용 하 여 세부 정보를 참조 하세요 [만들기 및 App Service Environment를 사용 하 여 내부 부하 분산 장치를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.


## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>가상 네트워크에서 함수 앱을 배포 하려면 어떻게 해야 하나요?

App Service Environment에 배포 하는 유일한 방법은 전체적으로 가상 네트워크 내에 있는 함수 앱을 만듭니다. App Service Environment를 사용 하 여 내부 부하 분산 장치를 사용 하 여 자세한 내용은 문서를 사용 하 여 시작 [만들기 및 App Service Environment를 사용 하 여 내부 부하 분산 장치를 사용 하 여](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)입니다.

포괄적인 네트워크 격리 이하의 가상 네트워크 리소스에 단방향 액세스 해야 하는 시나리오에 대 한 참조를 [Functions 네트워킹 개요](functions-networking-options.md)합니다.

## <a name="next-steps"></a>다음 단계

네트워킹 및 함수에 대 한 자세한 정보를 알아보려면: 

* [가상 네트워크 통합을 사용 하 여 시작 하는 방법에 대 한 자습서를 수행 합니다.](./functions-create-vnet.md)
* [Azure Functions의 네트워킹 옵션에 자세히 알아보기](./functions-networking-options.md)
* [App Service 및 Functions를 사용 하 여 가상 네트워크 통합에 자세히 알아보기](../app-service/web-sites-integrate-with-vnet.md)
* [Azure에서 가상 네트워크에 자세히 알아보기](../virtual-network/virtual-networks-overview.md)
* [추가 네트워킹 기능 및 App Service Environment를 사용 하 여 컨트롤을 사용 하도록 설정](../app-service/environment/intro.md)
