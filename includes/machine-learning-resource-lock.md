---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204468"
---
Azure를 사용하면 리소스를 삭제하거나 읽기 전용이 되도록 리소스에 _잠금_ 을 설정할 수 있습니다. __리소스를 잠그면 예기치 않은 결과가 발생할 수 있습니다.__ 리소스를 수정하지 않는 것처럼 보이는 일부 작업에는 실제로 잠금에 의해 차단되는 작업이 필요합니다. 

예를 들어 작업 영역의 리소스 그룹에 삭제 잠금을 적용하면 Azure ML 컴퓨팅 클러스터에 대한 크기 조정 작업이 차단됩니다.

리소스 잠금에 대한 자세한 내용은 [예기치 않은 변경을 방지하기 위한 리소스 잠금](../articles/azure-resource-manager/management/lock-resources.md)을 참조하세요.