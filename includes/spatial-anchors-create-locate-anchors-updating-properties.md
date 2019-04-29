---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232351"
---
## <a name="update-properties"></a>속성 업데이트

앵커에 대 한 속성을 업데이트 하려면 사용 된 `UpdateAnchorProperties()` 메서드. 두 개 이상의 장치를 동시에 동일한 앵커에 대 한 속성을 업데이트 하려고 하는 경우 낙관적 동시성 모델을 사용 합니다. 즉, 첫 번째 쓰기 승리 합니다.  다른 모든 쓰기 "동시성" 오류가 발생 합니다: 속성의 새로 고침을 다시 시도 하기 전에 필요 합니다.
