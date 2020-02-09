---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "70034421"
---
## <a name="validate-that-a-container-is-running"></a>컨테이너가 실행 중인지 확인 

컨테이너가 실행되고 있는지 확인하는 방법은 여러 가지가 있습니다. 해당 하는 컨테이너의 *외부 IP* 주소 및 노출 된 포트를 찾아 즐겨 찾는 웹 브라우저를 엽니다. 아래에서 다양 한 요청 Url을 사용 하 여 컨테이너가 실행 중인지 확인 합니다. 아래 나열 된 예제 요청 Url은 `http://localhost:5000`있지만 특정 컨테이너는 다를 수 있습니다. 컨테이너의 *외부 IP* 주소 및 노출 된 포트를 사용 해야 한다는 점에 유의 하세요.

| 요청 URL | 용도 |
|--|--|
| `http://localhost:5000/` | 컨테이너는 홈페이지를 제공합니다. |
| `http://localhost:5000/status` | 엔드포인트 쿼리를 발생 시 키 지 않고 컨테이너가 실행 중인지 유효성을 검사 하기 위해 HTTP GET을 사용 하 여 요청 됩니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다. |
| `http://localhost:5000/swagger` | 컨테이너는 엔드포인트에 대한 전체 설명서 집합과 **사용해 보기** 기능을 제공 합니다. 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |

![컨테이너의 홈페이지](./media/cognitive-services-containers-api-documentation/container-webpage.png)
