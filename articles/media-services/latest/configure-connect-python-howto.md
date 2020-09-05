---
title: Azure Media Services v3 API에 연결-Python
description: 이 문서에서는 Python을 사용 하 여 Media Services v3 API에 연결 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 42fea1a4363684667ccb41f0406bb66ef00d5485
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265578"
---
# <a name="connect-to-media-services-v3-api---python"></a>Media Services v3 API에 연결-Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 서비스 사용자 로그인 메서드를 사용 하 여 Azure Media Services v3 Python SDK에 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Python.org](https://www.python.org/downloads/) 에서 Python 다운로드
- 환경 변수를 설정 해야 합니다. `PATH`
- [Media Services 계정 만들기](./create-account-howto.md) 리소스 그룹 이름 및 Media Services 계정 이름을 명심 해야 합니다.
- [Api 액세스](./access-api-howto.md) 항목의 단계를 따릅니다. 이후 단계에서 필요한 구독 ID, 응용 프로그램 ID (클라이언트 ID), 인증 키 (비밀) 및 테 넌 트 ID를 기록 합니다.

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토합니다.

## <a name="install-the-modules"></a>모듈 설치

Python을 사용 하 여 Azure Media Services 작업 하려면 이러한 모듈을 설치 해야 합니다.

* `azure-mgmt-resource`모듈은 Active Directory 용 Azure 모듈을 포함 합니다.
* `azure-mgmt-media`Media Services 엔터티를 포함 하는 모듈입니다.

명령줄 도구를 열고 다음 명령을 사용 하 여 모듈을 설치 합니다.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Python 클라이언트에 연결

1. 확장명을 사용 하 여 파일 만들기 `.py`
1. 자주 사용 하는 편집기에서 파일을 엽니다.
1. 파일에 다음 코드를 추가 합니다. 이 코드는 필요한 모듈을 가져오고 Media Services에 연결 하는 데 필요한 Active Directory 자격 증명 개체를 만듭니다.

      변수의 값을 [액세스 api](./access-api-howto.md) 에서 가져온 값으로 설정 합니다.

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

- [PYTHON SDK](https://aka.ms/ams-v3-python-sdk)를 사용 합니다.
- Media Services [Python 참조](https://aka.ms/ams-v3-python-ref) 설명서를 검토하세요.
