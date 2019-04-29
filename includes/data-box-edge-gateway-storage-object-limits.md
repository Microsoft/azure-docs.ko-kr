---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755222"
---
쓸 수 있는 Azure 개체의 크기는 다음과 같습니다. 업로드되는 모든 파일이 이러한 제한을 준수하는지 확인합니다.

| Azure 개체 형식 | 업로드 제한                                             |
|-------------------|-----------------------------------------------------------|
| 블록 Blob        | ~ 4.75TB                                                 |
| 페이지 Blob         | 1TB <br> 페이지 Blob 형식으로 업로드되는 모든 파일은 정렬된 512바이트(정수 배수)여야 합니다. 그렇지 않은 경우 업로드에 실패합니다. <br> VHD 및 VHDX는 정렬된 512바이트입니다. |
| Azure 파일         | 1TB <br> 페이지 Blob 형식으로 업로드되는 모든 파일은 정렬된 512바이트(정수 배수)여야 합니다. 그렇지 않은 경우 업로드에 실패합니다. <br> VHD 및 VHDX는 정렬된 512바이트입니다. |

> [!IMPORTANT]
> 파일 생성(스토리지 형식에 관계 없이)은 최대 5TB까지 허용됩니다. 그러나 앞의 표에 정의된 업로드 제한보다 크기가 큰 파일을 만드는 경우 파일이 업로드되지 않습니다. 파일을 수동으로 삭제해야 공간을 회수할 수 있습니다.