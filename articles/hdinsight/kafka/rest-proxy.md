---
title: Apache Kafka REST 프록시-Azure HDInsight
description: Azure HDInsight에서 Kafka REST 프록시를 사용 하 여 Apache Kafka 작업을 수행 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b53fc3af71ce872c9ca9f513139c8179fd4165ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031407"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>REST 프록시를 사용 하 여 Azure HDInsight에서 Apache Kafka 클러스터와 상호 작용

Kafka REST 프록시를 사용 하면 HTTP를 통해 REST API를 통해 Kafka 클러스터와 상호 작용할 수 있습니다. 즉, Kafka 클라이언트는 가상 네트워크 외부에 있을 수 있습니다. 또한 클라이언트는 Kafka 라이브러리를 사용 하는 대신 Kafka 클러스터에 메시지를 보내고 받을 수 있는 간단한 HTTP 호출을 만들 수 있습니다.  

## <a name="background"></a>배경

### <a name="architecture"></a>Architecture

REST 프록시가 없으면 Kafka 클라이언트는 Kafka 클러스터 또는 피어 링 VNet과 동일한 VNet에 있어야 합니다. REST 프록시를 사용 하면 어디에 있든 데이터 생산자 나 소비자를 연결할 수 있습니다. REST 프록시를 배포 하면 클러스터에 대 한 새 공용 엔드포인트이 생성 되어 포털 설정에서 찾을 수 있습니다.

API에서 지원 되는 작업의 전체 사양은 [APACHE KAFKA REST 프록시 API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)를 참조 하세요.

### <a name="security"></a>보안

Kafka REST 프록시에 대 한 액세스는 Azure Active Directory 보안 그룹을 사용 하 여 관리 됩니다. 자세한 내용은 [Azure Active Directory 그룹을 사용 하 여 앱 및 리소스 액세스 관리](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)를 참조 하세요.

REST 프록시를 사용 하도록 설정 하 고 Kafka 클러스터를 만들 때 REST 엔드포인트에 대 한 액세스 권한이 있어야 하는 AAD 보안 그룹을 제공 합니다. REST 프록시에 액세스 해야 하는 Kafka 클라이언트 (응용 프로그램)는 그룹 소유자가이 그룹에 등록 해야 합니다. 그룹 소유자는 포털을 통해 또는 Powershell을 통해이 작업을 수행할 수 있습니다.

REST 프록시 엔드포인트에 요청을 하기 전에 클라이언트 응용 프로그램은 적절 한 보안 그룹의 멤버 자격을 확인 하기 위해 OAuth 토큰을 가져와야 합니다. OAuth 토큰의 작동 방식에 대 한 자세한 내용은 [oauth 2.0 코드 부여 흐름을 사용 하 여 Azure Active Directory 웹 응용 프로그램에 대 한 액세스 권한 부여](../../active-directory/develop/v1-protocols-oauth-code.md)를 참조 하세요. Python에서 OAuth 토큰을 가져오는 예제는 [클라이언트 응용 프로그램 샘플](#client-application-sample) 을 참조 하세요.

클라이언트 응용 프로그램에 OAuth 토큰이 있으면 REST 프록시에 대 한 HTTP 요청에서 해당 토큰을 전달 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure AD에 애플리케이션을 등록합니다. Kafka REST 프록시와 상호 작용 하기 위해 작성 하는 클라이언트 응용 프로그램은이 응용 프로그램의 ID와 암호를 사용 하 여 Azure에 인증 합니다.
1. Azure AD 보안 그룹을 만들고 Azure AD에 등록 한 응용 프로그램을 보안 그룹에 추가 합니다. 이 보안 그룹은 REST 프록시와 상호 작용할 수 있는 응용 프로그램을 제어 하는 데 사용 됩니다. Azure AD 그룹을 만드는 방법에 대 한 자세한 내용은 [기본 그룹 만들기 및 Azure Active Directory를 사용 하 여 구성원 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조 하세요.

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>REST 프록시를 사용 하는 Kafka 클러스터 만들기

1. Kafka 클러스터 만들기 워크플로 중에 "보안 + 네트워킹" 탭에서 "Kafka REST 프록시 사용" 옵션을 선택 합니다.

     ![Kafka REST 프록시를 사용 하도록 설정 하 고 보안 그룹을 선택 합니다.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. **보안 그룹 선택**을 클릭 합니다. 보안 그룹 목록에서 REST 프록시에 액세스 하려는 보안 그룹을 선택 합니다. 검색 상자를 사용 하 여 적절 한 보안 그룹을 찾을 수 있습니다. 맨 아래에 있는 **선택** 단추를 클릭 합니다.

     ![Kafka REST 프록시를 사용 하도록 설정 하 고 보안 그룹을 선택 합니다.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. [Azure Portal를 사용 하 여 Azure HDInsight에서 Apache Kafka 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started)에 설명 된 대로 클러스터를 만드는 나머지 단계를 완료 합니다.

1. 클러스터가 만들어지면 클러스터 속성으로 이동 하 여 Kafka REST 프록시 URL을 기록 합니다.

     ![REST 프록시 URL 보기](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>클라이언트 응용 프로그램 샘플

아래 python 코드를 사용 하 여 Kafka 클러스터에서 REST 프록시와 상호 작용할 수 있습니다. 이 코드는 다음을 수행합니다.

1. Azure AD에서 OAuth 토큰을 인출 합니다.
1. 지정 된 토픽을 만듭니다.
1. 해당 항목에 메시지를 보냅니다.
1. 해당 항목의 메시지를 사용 합니다.

Python에서 OAuth 토큰을 가져오는 방법에 대 한 자세한 내용은 [Python AuthenticationContext 클래스](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)를 참조 하세요. Kafka REST 프록시를 통해 생성 또는 삭제 되지 않은 항목이 반영 되는 동안 지연이 표시 될 수 있습니다. 이 지연은 캐시 새로 고침으로 인 한 것입니다.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

코드 샘플을 사용 하려면 다음 단계를 수행 합니다.

1. Python이 설치 된 컴퓨터에 샘플 코드를 저장 합니다.
1. `pip3 install adal` 및 `pip install msrestazure`를 실행 하 여 필요한 python 종속성을 설치 합니다.
1. 코드를 수정 하 고 사용자 환경에 대 한 다음 속성을 업데이트 합니다.
    1.  *테넌트 ID* – 구독이 인 Azure 테넌트입니다.
    1.  *클라이언트 id* – 보안 그룹에 등록 한 응용 프로그램의 id입니다.
    1.  *클라이언트 암호* -보안 그룹에 등록 한 응용 프로그램에 대 한 암호입니다.
    1.  *Kafkarest_endpoint* – [배포 섹션](#create-a-kafka-cluster-with-rest-proxy-enabled)에 설명 된 대로 클러스터 개요의 "속성" 탭에서이 값을 가져옵니다. 다음 형식 이어야 합니다.-`https://<clustername>-kafkarest.azurehdinsight.net`
3. 명령줄에서를 실행 하 여 python 파일을 실행 `python <filename.py>`

## <a name="next-steps"></a>다음 단계

* [Kafka REST 프록시 API 참조 문서](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [자습서: Apache Kafka 생산자 및 소비자 Api 사용](apache-kafka-producer-consumer-api.md)