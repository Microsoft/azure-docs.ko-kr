---
title: Azure Service Fabric CLI- sfctl mesh secretvalue | Microsoft Docs
description: Service Fabric CLI sfctl mesh secretvalue 명령에 대해 설명합니다.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 3f8e46f063d3e725e2174fd907169f3e0167586a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836951"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
메시 비밀 값 리소스를 가져오고 삭제합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| delete | 명명된 비밀 리소스의 지정된 값을 삭제합니다. |
| list | 지정된 비밀 리소스의 모든 값 이름을 나열합니다. |
| 표시 | 비밀 리소스의 지정된 버전 값을 검색합니다. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
명명된 비밀 리소스의 지정된 값을 삭제합니다.

이름으로 식별되는 비밀 값 리소스를 삭제합니다. 리소스의 이름은 일반적으로 해당 값과 연결된 버전입니다. 지정된 값이 사용 중인 경우 삭제가 실패합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --secret-name -n [필수] | 비밀 리소스의 이름입니다. |
| --version -v     [필수] | 비밀 버전의 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
지정된 비밀 리소스의 모든 값 이름을 나열합니다.

지정된 비밀 리소스의 모든 비밀 값 리소스에 대한 정보를 가져옵니다. 정보에는 비밀 값 리소스의 실제 값이 아닌 이름이 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --secret-name -n [필수] | 비밀 리소스의 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
비밀 리소스의 지정된 버전 값을 검색합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --secret-name -n [필수] | 비밀 리소스의 이름입니다. |
| --version -v     [필수] | 비밀 버전의 이름입니다. |
| --show-value | 비밀 버전의 실제 값을 표시합니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.