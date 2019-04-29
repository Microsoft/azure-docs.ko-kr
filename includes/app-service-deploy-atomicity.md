---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: include file
ms.openlocfilehash: 8709956adee06e4e783ac5a7b317b2c4dec43e73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765661"
---
## <a name="what-happens-to-my-app-during-deployment"></a>배포하는 동안 앱에서 진행되는 작업

공식적으로 지원되는 모든 배포 방법은 앱의 `/home/site/wwwroot` 폴더에서 파일을 변경한다는 한 가지 공통점을 갖습니다. 이러한 파일은 프로덕션 환경에서 실행되는 것과 동일한 파일입니다. 따라서 잠겨 있는 파일로 인해 배포가 실패하거나, 모든 파일이 동시에 업데이트되는 것은 아니므로 배포 동안 프로덕션 환경의 앱이 예기치 않은 동작을 보일 수 있습니다. 이러한 문제를 방지하는 몇 가지 다른 방법이 있습니다.

- 배포하는 동안 앱을 중지하거나 앱에 대한 오프라인 모드를 사용하도록 설정합니다. 자세한 내용은 [배포하는 동안 잠긴 파일 처리](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)를 참조하세요.
- [자동 전환](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) 사용하도록 설정한 상태로 [스테이징 슬롯](../articles/app-service/deploy-staging-slots.md)에 배포합니다. 
- [패키지에서 실행](https://github.com/Azure/app-service-announcements/issues/84)을 대신 사용합니다.
