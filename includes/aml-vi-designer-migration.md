---
title: 포함 파일
description: 포함 파일
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541804"
---
1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. 작업 영역을 Enterprise edition으로 업그레이드 합니다.

    업그레이드 후 모든 시각적 인터페이스 실험은 디자이너에서 파이프라인 초안으로 변환 됩니다.
    
    > [!NOTE]
    > Visual interface 웹 서비스를 실시간 엔드포인트으로 변환 하기 위해 Enterprise edition으로 업그레이드할 필요는 없습니다.
    
1. 파이프라인 초안의 목록을 보려면 작업 영역의 디자이너 섹션으로 이동 합니다. 
    
    변환 된 웹 서비스는 **엔드포인트** > **실시간 엔드포인트**으로 이동 하 여 찾을 수 있습니다.

1. 파이프라인 초안을 선택 하 여 엽니다.

    변환 프로세스 중에 오류가 발생 한 경우 문제를 해결 하는 지침이 포함 된 오류 메시지가 표시 됩니다. 

### <a name="known-issues"></a>알려진 문제

 수동으로 해결 해야 하는 알려진 마이그레이션 문제는 다음과 같습니다.

- **데이터 가져오기** 또는 **데이터 모듈 내보내기**
        
    실험에서 **데이터 가져오기** 또는 **데이터 내보내기** 모듈이 있는 경우 데이터 원본을 업데이트 하 여 데이터 저장소를 사용 해야 합니다. 데이터 저장소를 만드는 방법에 대한 자세한 내용은 [Azure storage 서비스의 데이터에 액세스 하는 방법](../articles/machine-learning/how-to-access-data.md)을 참조 하세요. 사용자 편의를 위해 클라우드 저장소 계정 정보가 **데이터 가져오기** 또는 **데이터 내보내기** 모듈의 설명에 추가 되었습니다. 
      