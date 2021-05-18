---
title: Azure Service Fabric CLI- sfctl mesh code-package-log
description: sfctl, Azure Service Fabric 명령줄 인터페이스에 대해 알아봅니다. 지정된 코드 패키지에 대한 로그를 가져오기 위한 명령 목록이 포함되어 있습니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257298"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
지정된 서비스 복제본에 대해 지정된 코드 패키지의 컨테이너 관련 로그를 가져옵니다.

## <a name="commands"></a>명령

|명령|Description|
| --- | --- |
| Get | 컨테이너에서 로그를 가져옵니다. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
컨테이너에서 로그를 가져옵니다.

서비스 복제본의 지정된 코드 패키지에서 컨테이너 관련 로그를 가져옵니다.

### <a name="arguments"></a>인수

|인수|Description|
| --- | --- |
| --app-name --application-name[필수] | 애플리케이션 이름입니다. |
| --code-package-name           [필수] | 서비스의 코드 패키지 이름입니다. |
| --replica-name                [필수] | Service Fabric 복제본 이름입니다. |
| --service-name[필수] | 서비스의 이름입니다. |
| --tail | 로그의 끝에서 표시할 줄의 수입니다. 기본값은 100입니다. 전체 로그를 표시하는 'all'입니다. |

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
