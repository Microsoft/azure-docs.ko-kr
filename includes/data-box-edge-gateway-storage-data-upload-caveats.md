---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755215"
---
다음 주의 사항은 Azure로 이동되는 데이터에 적용됩니다.

- 둘 이상의 디바이스가 같은 컨테이너에 기록하지 않도록 하는 것이 좋습니다.
- 클라우드에 복사되는 개체와 동일한 이름을 사용하는 기존 Azure 개체(예: Blob 또는 파일)가 있는 경우 디바이스는 클라우드의 파일을 덮어씁니다.
- 공유 폴더 아래에 만들어진 빈 디렉터리 계층 구조(파일 없음)는 Blob 컨테이너로 업로드되지 않습니다.
- 끌어서를 사용 하 여 데이터를 복사 하 고 파일 탐색기를 사용 하 여 또는 명령줄을 통해 삭제할 수 있습니다. 복사할 파일의 집계 크기가 10GB 보다 큰 경우에 Robocopy 등 rsync 대량 복사 프로그램을 사용 하는 것이 좋습니다. 대량 복사 도구는 일시적인 오류에 대 한 복사 작업을 다시 시도 및 추가적인 복원 력을 제공 합니다.
- Azure Storage 컨테이너와 연결된 공유가 만들 때 공유에 대해 정의된 Blob 유형과 일치하지 않는 Blob을 업로드하는 경우 해당 Blob은 업데이트되지 않습니다. 예를 들어 디바이스에 블록 Blob 공유를 만듭니다. 페이지 Blob이 있는 기존 클라우드 컨테이너와 공유를 연결합니다. 해당 공유를 새로 고쳐 파일을 다운로드합니다. 이미 클라우드에 페이지 Blob으로 저장된 새로 고쳐진 파일 중 일부를 수정합니다. 업로드 실패가 표시됩니다.
