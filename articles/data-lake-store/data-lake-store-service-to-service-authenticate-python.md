---
title: '서비스 간 인증: Azure Active Directory를 사용하여 Azure Data Lake Storage Gen1로 Python 인증 | Microsoft Docs'
description: Azure Active Directory를 사용하여 Python으로 Azure Data Lake Storage Gen1 서비스 간 인증을 수행하는 방법을 알아봅니다.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 009aff2703829e6d30f93b3c8e3696724594f29b
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290770"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Python을 사용하여 Azure Data Lake Storage Gen1로 서비스 간 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-service-to-service-authenticate-java.md)
> * [.NET SDK 사용](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-service-to-service-authenticate-python.md)
> * [REST API 사용](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

이 문서에서는 Python SDK를 사용하여 Azure Data Lake Storage Gen1로 서비스 간 인증을 수행하는 방법을 배웁니다. Python을 사용한 Data Lake Storage Gen1 최종 사용자 인증의 경우 [을 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-python.md)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

* **Python**. Python을 [여기](https://www.python.org/downloads/)에서 다운로드할 수 있습니다. 이 문서에서는 Python 3.6.2를 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "웹" 애플리케이션 만들기**. [Azure Active Directory를 사용하여 Data Lake Storage Gen1로 서비스 간 인증](data-lake-store-service-to-service-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

## <a name="install-the-modules"></a>모듈 설치

Python을 통해 Data Lake Storage Gen1을 사용하려면 세 가지 모듈을 설치해야 합니다.

* Active Directory 등 Azure 모듈을 포함하는 `azure-mgmt-resource` 모듈.
* Azure Data Lake Storage Gen1 계정 관리 작업을 포함하는 `azure-mgmt-datalake-store` 모듈. 이 모듈에 대한 자세한 내용은 [Azure Data Lake Storage Gen1 관리 모듈 참조](/python/api/azure-mgmt-datalake-store/)를 참조하세요.
* Azure Data Lake Storage Gen1 파일 시스템 작업을 포함하는 `azure-datalake-store` 모듈. 이 모듈에 대한 자세한 내용은 [azure-datalake-store 파일 시스템 모듈 참조](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)를 참조하세요.

다음 명령을 사용하여 모듈을 설치합니다.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

1. 원하는 IDE에서 **mysample.py**와 같이 새 Python 애플리케이션을 만듭니다.

2. 다음 코드 조각을 추가하여 필요한 모듈 가져오기

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. mysample.py의 변경 내용을 저장합니다.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>계정 관리를 위해 클라이언트 암호로 서비스 간 인증

이 코드 조각을 사용 하 여 Data Lake Storage Gen1 계정 만들기, Data Lake Storage Gen1 계정 삭제 등의 Data Lake Storage Gen1에 대한 계정 관리 작업을 위해 Azure AD를 사용 하 여 인증 합니다. 다음 코드 조각은 기존 Azure AD "웹 앱" 응용 프로그램의 응용 프로그램/서비스 사용자에 대한 클라이언트 암호를 사용 하 여 비 대화형으로 응용 프로그램을 인증 하는 데 사용할 수 있습니다.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>파일 시스템 작업을 위해 클라이언트 암호로 서비스 간 인증

다음 코드 조각을 사용 하 여 폴더 만들기, 파일 업로드 등의 Data Lake Storage Gen1에 대한 파일 시스템 작업에 대해 Azure AD를 사용 하 여 인증 합니다. 다음 코드 조각은 응용 프로그램/서비스 주체에 대한 클라이언트 암호를 사용 하 여 비 대화형으로 응용 프로그램을 인증 하는 데 사용할 수 있습니다. 기존 Azure AD "Web App" 애플리케이션과 함께 사용합니다.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>다음 단계
이 문서에서는 Python을 사용하여 Data Lake Storage Gen1로 인증하는 서비스 간 인증을 사용하는 방법을 배웠습니다. 이제 다음 문서를 통해 Python을 Data Lake Storage Gen1과 함께 사용하는 방법을 살펴볼 수 있습니다.

* [Python을 사용한 Data Lake Storage Gen1에서의 계정 관리 작업](data-lake-store-get-started-python.md)
* [Python을 사용한 Data Lake Storage Gen1에서의 데이터 작업](data-lake-store-data-operations-python.md)


