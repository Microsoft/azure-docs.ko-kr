---
title: 요약-Azure Functions 및 서버를 사용하지 않는 플랫폼 보안
description: Azure Functions 및 서버를 사용하지 않는 플랫폼 보안 백서에 대한 요약
author: TomShinder
ms.author: TomSh
ms.date: 06/21/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 87010b3256fb8eef6871d76f80db2999760386b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587498"
---
# <a name="azure-functions-and-serverless-platform-security"></a>Azure Functions 및 서버를 사용하지 않는 플랫폼 보안
## <a name="abstract"></a>요약
대부분의 기업은 서버를 관리하려면 상당한 양의 리소스 및 시간이 필요한데 이는 비용이 추가됩니다. 기업은 더 적은 리소스를 사용하여 서버를 관리할 수 있다면 뛰어난 애플리케이션 빌드에 집중할 수 있습니다.  

서버를 사용하지 않는 컴퓨팅을 이용하면 앱을 실행하고 확장하는 데 필요한 인프라가 자동으로 관리되므로 모든 시간을 앱 빌드와 배포에 사용할 수 있습니다. 서버를 사용하지 않는 컴퓨팅은 서버, 인프라 및 운영 체제의 추상화입니다. 서버를 사용하지 않는 컴퓨팅은 클라우드에서 거의 실시간으로 발생하는 이벤트 및 트리거에 대한 반응으로 구동됩니다. 

완전히 관리되는 서비스인 서버 관리 및 용량 계획은 개발자에게는 표시되지 않습니다. 서버를 사용하지 않는 프레임워크를 사용하면 Azure Functions를 사용하여 서버를 사용하지 않는 애플리케이션을 개발 및 배포할 수 있습니다. 이는 함수 및 이벤트로 구성된 정교하고 이벤트로 구동되고 서버를 사용하지 않는 아키텍처를 빌드할 수 있는 구조 및 자동화를 제공하는 CLI(명령줄 인터페이스)입니다. Azure 함수는 마이크로 서비스처럼 독립적인 배포 단위입니다. 단일 작업을 수행하려면 가장 자주 작성되는 코드로서 클라우드에서 배포됩니다.

이런 이점에도 불구하고 서버를 사용하지 않는 보안은 자체 위험 요소를 다뤄야 합니다. 서버를 사용하지 않는 방법은 새 보안 문제를 도입하지 않지만 기존 보안 문제에 대한 방법은 포함하고 있어야 합니다. 이 백서에서는 이러한 보안 문제에 중점을 둡니다. 
* 서버를 사용하지 않는 플랫폼의 이점
* 서버를 사용하지 않는 컴퓨팅의 보안 문제
* Azure의 컨텍스트의 중요한 보안 문제 및 완화
* Microsoft 서버를 사용하지 않는 플랫폼의 보안

[백서 다운로드](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-functions-serverless-platform-security/Microsoft%20Serverless%20Platform.pdf)

