---
title: Azure Media Services v3 API-Python에 연결
description: Python 사용 하 여 Media Services v3 API에 연결 하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733099"
---
# <a name="connect-to-media-services-v3-api---python"></a>Media Services v3 API-Python에 연결

이 아티클에서 Azure Media Services v3 Python SDK에 연결 하는 메서드는 서비스 보안 주체 로그인을 사용 하 여 합니다.

## <a name="prerequisites"></a>필수 조건

- Python 다운로드 [python.org](https://www.python.org/downloads/)
- 설정 해야 합니다 `PATH` 환경 변수
- [Media Services 계정 만들기](create-account-cli-how-to.md) Media Services 계정 이름과 리소스 그룹 이름을 기억해 두어야 합니다.
- 단계를 수행 합니다 [Api 액세스](access-api-cli-how-to.md) 항목입니다. 이후 단계에서 구독 ID, 응용 프로그램 ID (클라이언트 ID), 인증 키 (암호) 및 해야 하는 테 넌 트 ID를 기록 합니다.

## <a name="install-the-modules"></a>모듈 설치

Python을 사용 하 여 Azure Media Services를 사용 하려면 이러한 모듈을 설치 해야 합니다.

* `azure-mgmt-resource` Active Directory에 대 한 Azure 모듈을 포함 하는 모듈입니다.
* `azure-mgmt-media` Media Services 엔터티를 포함 하는 모듈입니다.

명령줄 도구를 열고 다음 명령을 사용 하 여 모듈을 설치 합니다.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Python 클라이언트에 연결

1. 이라는 파일을 만들고는 `.py` 확장
1. 원하는 편집기에서 파일을 엽니다.
1. 파일에 다음의 코드를 추가 합니다. 코드는 필수 모듈을 가져오는 하 고 Media Services에 연결 해야 하는 Active Directory 자격 증명 개체를 만듭니다.

      변수 값에서 가져온 값으로 설정 [Api 액세스](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. 파일 실행

## <a name="next-steps"></a>다음 단계

- [Python SDK](https://aka.ms/ams-v3-python-sdk)를 사용합니다.
- Media Services [Python 참조](https://aka.ms/ams-v3-python-ref) 설명서를 검토하세요.
