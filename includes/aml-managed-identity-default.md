---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147473"
---
**기본적으로 관리** 되는 id는 시스템 할당 관리 id 또는 첫 사용자 할당 관리 id입니다.

실행 중에는 두 가지 id 응용 프로그램이 있습니다.

1. 시스템은 id를 사용 하 여 사용자의 저장소 탑재, 컨테이너 레지스트리 및 datastores를 설정 합니다.

    * 이 경우 시스템은 기본 관리 id를 사용 합니다.

1. 사용자가 코드 내에서 제출 된 실행에 대 한 리소스에 액세스할 수 있도록 id를 적용 합니다.

    * 이 경우 자격 증명을 검색 하는 데 사용 하려는 관리 되는 id에 해당 하는 *client_id* 을 제공 합니다.
    * 또는 *DEFAULT_IDENTITY_CLIENT_ID* 환경 변수를 통해 사용자 할당 id의 클라이언트 id를 가져옵니다.

    예를 들어 기본 관리 id를 사용 하 여 데이터 저장소에 대 한 토큰을 검색 하려면 다음을 수행 합니다.

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```