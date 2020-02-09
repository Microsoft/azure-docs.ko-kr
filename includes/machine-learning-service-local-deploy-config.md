---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: c288ea4a2c62999f27b3f07c39df40dae7ab1426
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926985"
---
`deploymentconfig.json` 문서의 항목은 [Localwebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py)에 대한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대한 매개 변수의 매핑에 대해 설명 합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. 로컬 대상의 경우 값이 `local`이어야 합니다. |
| `port` | `port` | 서비스의 HTTP 엔드포인트을 노출할 로컬 포트입니다. |

이 JSON은 CLI에서 사용할 수 있는 배포 구성의 예입니다.

```json
{
    "computeType": "local",
    "port": 32267
}
```
