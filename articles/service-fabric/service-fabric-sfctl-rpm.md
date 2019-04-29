---
title: Azure Service Fabric CLI- sfctl rpm| Microsoft Docs
description: Service Fabric CLI sfctl rpm 명령을 설명합니다.
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
ms.openlocfilehash: 04080d75042bfa8a07533336936165e0abef051b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556358"
---
# <a name="sfctl-rpm"></a>sfctl rpm
복구 관리자 서비스에 쿼리하고 명령을 보냅니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| approve-force | 지정된 복구 작업의 승인을 강제 적용합니다. |
| delete | 완료된 복구 작업을 삭제합니다. |
| list | 지정된 필터와 일치하는 복구 작업 목록을 가져옵니다. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
지정된 복구 작업의 승인을 강제 적용합니다.

이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --task-id [필수] | 복구 작업의 ID입니다. |
| --version | 복구 작업의 현재 버전 번호입니다. 0이 아닌 경우 이 값이 복구 작업의 실제 현재 버전과 일치하는 경우에만 요청이 성공합니다. 0이면 버전 검사가 수행되지 않습니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
완료된 복구 작업을 삭제합니다.

이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --task-id [필수] | 삭제할 완료된 복구 작업의 ID입니다. |
| --version | 복구 작업의 현재 버전 번호입니다. 0이 아닌 경우 이 값이 복구 작업의 실제 현재 버전과 일치하는 경우에만 요청이 성공합니다. 0이면 버전 검사가 수행되지 않습니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
지정된 필터와 일치하는 복구 작업 목록을 가져옵니다.

이 API는 Service Fabric 플랫폼을 지원하며 코드에서 직접 사용할 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --executor-filter | 클레임된 작업이 목록에 포함되어야 하는 복구 실행기의 이름입니다. |
| --state-filter | 결과 목록에 포함되어야 하는 작업 상태를 지정하는 다음 값의 비트 OR입니다. <br> 1 - 생성됨 <br>2 - 클레임됨  <br>4 - 준비 중  <br>8 - 승인됨  <br>16 - 실행 중  <br>32 - 복원 중  <br>64 - 완료됨 |
| --task-id-filter | 일치 시킬 복구 작업 ID 접두사입니다. |

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