---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232622"
---
## <a name="locate-a-cloud-spatial-anchor"></a>클라우드 공간 앵커를 찾습니다.

이전에 업로드 된 클라우드 공간 앵커를 찾을 수 있는 Azure 공간 앵커 라이브러리를 사용 하는 주요 이유 중 하나입니다. 클라우드 공간 앵커를 찾으려면 해당 식별자를 알아야 해야 합니다. 응용 프로그램의 백 엔드 서비스에 제대로에 인증할 수 있는 모든 장치에 액세스할 수 있는 앵커 Id는 저장할 수 있습니다. 이 참조의 예 [자습서: 장치 공간 앵커 공유할](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)합니다.

인스턴스화하는 `AnchorLocateCriteria` 개체를 찾고 있는 및 호출 식별자를 설정할를 `CreateWatcher` 메서드를 제공 하 여 세션에서 프로그램 `AnchorLocateCriteria`.
