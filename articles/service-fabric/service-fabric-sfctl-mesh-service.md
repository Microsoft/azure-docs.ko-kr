---
title: Azure Service Fabric CLI - sfctl mesh service
description: Azure Service Fabric 명령줄 인터페이스인 sfctl에 대해 자세히 알아봅니다. 애플리케이션 리소스의 서비스 세부 정보를 가져오기 위한 명령 목록이 포함되어 있습니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 68ea876d9951b49a6683cc74df8b9107fd942e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245673"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
애플리케이션 리소스의 서비스를 나열하고 서비스 세부 정보를 가져옵니다.

## <a name="commands"></a>명령

|명령|Description|
| --- | --- |
| list | 모든 서비스 리소스를 나열합니다. |
| show | 지정된 이름의 서비스 리소스를 가져옵니다. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
모든 서비스 리소스를 나열합니다.

애플리케이션 리소스의 모든 서비스에 대한 정보를 가져옵니다. 정보에는 서비스에 대한 설명과 기타 속성이 포함됩니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --app-name --application-name[필수] | 애플리케이션 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
지정된 이름의 서비스 리소스를 가져옵니다.

지정된 이름의 서비스 리소스에 대한 정보를 가져옵니다. 정보에는 서비스에 대한 설명과 기타 속성이 포함됩니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --app-name --application-name[필수] | 애플리케이션 이름입니다. |
| --name -n[필수] | 서비스의 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|Description|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](./scripts/sfctl-upgrade-application.md)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
