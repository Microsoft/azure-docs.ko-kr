---
title: 상태에 Text Analytics를 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: 상태에 대 한 Text Analytics를 사용 하 여 비구조적 임상 텍스트에서 의료 정보를 추출 하 고 레이블 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 4ba7aa530699ab0e06ac42e3701265254b617f73
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167694"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>방법: 상태에 대 한 Text Analytics 사용 (미리 보기)

> [!NOTE]
> 상태 컨테이너에 대 한 Text Analytics 최근에 업데이트 되었습니다. 최근 변경 내용에 대 한 자세한 내용은 [새로운 기능](../whats-new.md) 을 참조 하세요. 최신 컨테이너를 끌어와 나열 된 업데이트를 사용 해야 합니다.

> [!IMPORTANT] 
> 상태 Text Analytics은 "있는 그대로" 및 "모든 오류를 포함 하 여" 제공 되는 미리 보기 기능입니다. 따라서 **상태 (미리 보기)에 대 한 Text Analytics는 프로덕션 환경에서 구현 하거나 배포 해서는** 안 됩니다. 상태에 대 한 Text Analytics는 질병 또는 기타 조건에 대 한 진단, 치료, 완화, 처리 또는 방지에 사용 하기 위한 의료 장치, 임상 지원, 진단 도구 또는 기타 기술로 사용할 수 없으며, Microsoft가 이러한 목적을 위해이 기능을 사용할 수 있는 라이선스 또는 권한이 부여 되지 않습니다. 이 기능은 의료 전문가의 전문 의료 통지, 의료 보험 통지, 진단, 처리 또는 임상를 위한 대체 방법으로 설계 되거나 구현 하거나 배포 하기 위한 것이 아니며, 사용 하지 않아야 합니다. 고객은 상태에 대 한 Text Analytics의 사용만을 담당 합니다. Microsoft는 의료 목적을 위해이 기능을 사용 하 여 제공 되는 Text Analytics 또는 모든 사람의 의료 요구 사항을 충족 하는 경우에만이 기능을 제공 합니다. 


상태에 대 한 Text Analytics는 의사 (컨테이너 화 된) 노트, 방전 요약, 임상 문서 및 전자 상태 레코드와 같은 구조화 되지 않은 텍스트에서 관련 의료 정보를 추출 하 고 레이블 하는 데 사용할 수 있는 서비스입니다.  

## <a name="features"></a>기능

상태 컨테이너에 대 한 Text Analytics 현재 특정 보안 및 데이터 관리 요구 사항을 충족 하는 고유한 개발 환경에서 영어 텍스트에 대 한 NER (명명 된 엔터티 인식), 관계 추출, 엔터티 부정 및 엔터티 링크를 수행 합니다.

#### <a name="named-entity-recognition"></a>[명명된 엔터티 인식](#tab/ner)

명명 된 엔터티 인식은 진단, 조제 이름, 증상/기호 또는 연령와 같은 하나 이상의 의미 체계 형식과 연결 될 수 있는 구조화 되지 않은 텍스트에서 언급 된 단어와 구를 검색 합니다.

> [!div class="mx-imgBorder"]
> ![상태 NER](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[관계 추출](#tab/relation-extraction)

관계 추출은 텍스트에 언급 된 개념 간의 의미 있는 연결을 식별 합니다. 예를 들어 "조건 시간" 관계는 조건 이름을 시간에 연결 하 여 찾을 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![상태 재지정](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[엔터티 연결](#tab/entity-linking)

엔터티 연결은 텍스트에 언급 된 명명 된 엔터티를 미리 정의 된 개념의 데이터베이스에 있는 개념에 연결 하 여 고유 엔터티를 구분 합니다. 예를 들어, 통합 의료 언어 시스템 (UMLS)이 있습니다.

> [!div class="mx-imgBorder"]
> ![상태 EL](../media/ta-for-health/health-entity-linking.png)

상태에 대 한 Text Analytics는[Umls](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(통합 의료 언어 시스템) Metathesaurus 기술 자료에 있는 상태 및 생명 공학 어휘에 연결 하는 것을 지원 합니다.

#### <a name="negation-detection"></a>[부정 감지](#tab/negation-detection) 

의료 콘텐츠의 의미는 부정과 같은 한정자의 영향을 받습니다 .이는 잘못 진단 된 경우 중요 한 함축을 가질 수 있습니다. 상태에 대 한 Text Analytics는 텍스트에 언급 된 여러 엔터티의 부정 검색을 지원 합니다. 

> [!div class="mx-imgBorder"]
> ![상태 NEG](../media/ta-for-health/health-negation.png)

---

지원 되는 엔터티의 전체 목록에 대해서는 Text Analytics에서 반환 된 [엔터티 범주](../named-entity-types.md?tabs=health) 를 참조 하세요.

## <a name="supported-languages"></a>지원되는 언어

상태 Text Analytics는 영어 문서만 지원 합니다.

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

컨테이너에 대 한 액세스를 요청 하는 [Cognitive Services 컨테이너 요청 양식을](https://aka.ms/cognitivegate) 작성 하 고 제출 합니다. 현재는 상태 사용에 대 한 Text Analytics 요금이 청구 되지 않습니다. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>컨테이너 설치

여러 가지 방법으로 컨테이너를 설치 하 고 실행할 수 있습니다. 

- [Azure Portal](text-analytics-how-to-install-containers.md?tabs=healthcare) 를 사용 하 여 Text Analytics 리소스를 만들고 Docker를 사용 하 여 컨테이너를 가져옵니다.
- 다음 PowerShell 및 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 스크립트를 사용 하 여 리소스 배포 컨테이너 구성을 자동화할 수 있습니다.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Azure Web App for Containers을 사용 하 여 컨테이너 설치

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) 는 클라우드에서 실행 중인 컨테이너 전용 azure 리소스입니다. 자동 크기 조정, docker 컨테이너 및 docker 작성, HTTPS 지원 등의 기본 기능을 제공 합니다.

> [!NOTE]
> Azure 웹 앱을 사용 하 여 다음 형식으로 도메인을 자동으로 가져옵니다.`<appservice_name>.azurewebsites.net`

Azure CLI를 사용 하 여이 PowerShell 스크립트를 실행 하 고 HTTPS를 통해 구독 및 컨테이너 이미지를 사용 하 여 Web App for Containers를 만듭니다. 첫 번째 요청을 제출 하기 전에 스크립트가 완료 될 때까지 기다립니다 (약 25-30 분).

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Azure Container Instance를 사용 하 여 컨테이너 설치

또한 ACI (Azure Container Instance)를 사용 하 여 배포를 보다 쉽게 만들 수 있습니다. ACI는 서버를 사용 하지 않는 관리 되는 Azure 환경에서 주문형 Docker 컨테이너를 실행할 수 있는 리소스입니다. 

Azure Portal를 사용 하 여 ACI 리소스를 배포 하는 단계는 [Azure Container Instances 사용 방법을](text-analytics-how-to-use-container-instances.md) 참조 하세요. 컨테이너 이미지를 사용 하 여 구독에서 ACI를 만드는 Azure CLI를 사용 하 여 아래 PowerShell 스크립트를 사용할 수도 있습니다.  첫 번째 요청을 제출 하기 전에 스크립트가 완료 될 때까지 기다립니다 (약 25-30 분).  최대 Cpu 수에 대 한 제한으로 인해 요청 당 5 개 이상의 매우 많은 문서 (각각 5000 자)를 전송 하려는 경우이 옵션을 선택 하지 마십시오.
가용성 정보는 [ACI 지역 지원](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability) 문서를 참조 하세요. 

> [!NOTE] 
> Azure Container Instances 기본 제공 도메인에 대 한 HTTPS 지원을 포함 하지 않습니다. HTTPS가 필요한 경우 인증서를 만들고 도메인을 등록 하는 등 수동으로 구성 해야 합니다. 아래 NGINX를 사용 하 여이 작업을 수행 하는 지침을 찾을 수 있습니다.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>보안 ACI 연결

기본적으로 컨테이너 API와 함께 ACI를 사용 하는 경우에는 보안이 제공 되지 않습니다. 일반적으로 컨테이너는 네트워크 브리지가 외부에서 보호 되는 pod의 일부로 실행 되기 때문입니다. 그러나 컨테이너 끝점을 비공개로 유지 하는 프런트 엔드 구성 요소를 사용 하 여 컨테이너를 수정할 수 있습니다. 다음 예에서는 [NGINX](https://www.nginx.com) 를 수신 게이트웨이로 사용 하 여 HTTPS/SSL 및 클라이언트 인증서 인증을 지원 합니다.

> [!NOTE]
> NGINX은 오픈 소스 고성능 HTTP 서버 및 프록시입니다. NGINX 컨테이너는 단일 컨테이너에 대 한 TLS 연결을 종료 하는 데 사용할 수 있습니다. 더 복잡 한 NGINX 수신 기반 TLS 종료 솔루션도 가능 합니다.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>수신 게이트웨이로 NGINX 설정

NGINX는 [구성 파일](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) 을 사용 하 여 런타임에 기능을 사용 하도록 설정 합니다. 다른 서비스에 대 한 TLS 종료를 사용 하도록 설정 하려면 SSL 인증서를 지정 하 여 TLS 연결을 종료 하 고 `proxy_pass` 서비스에 대 한 주소를 지정 해야 합니다. 아래에 예제가 나와 있습니다.


> [!NOTE]
> `ssl_certificate`NGINX 컨테이너의 로컬 파일 시스템 내에 경로를 지정 해야 합니다. 에 지정 된 주소는 `proxy_pass` NGINX 컨테이너의 네트워크 내에서 사용할 수 있어야 합니다.

NGINX 컨테이너는 아래에 탑재 된의 모든 파일을 `_.conf_` `/etc/nginx/conf.d/` HTTP 구성 경로에 로드 합니다.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>예 Docker 작성 파일

아래 예제에서는 NGINX를 배포 하 고 상태 컨테이너에 대해 Text Analytics를 배포 하기 위해 [docker 작성](https://docs.docker.com/compose/reference/overview) 파일을 만드는 방법을 보여 줍니다.

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

이 Docker 작성 파일을 시작 하려면 콘솔에서 파일의 루트 수준에 있는 다음 명령을 실행 합니다.

```bash
docker-compose up
```

자세한 내용은 [NGINX SSL 종료](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)에 대 한 NGINX 설명서를 참조 하세요.


## <a name="example-api-request"></a>API 요청 예
컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다.

아래 예제 말아 예를 사용 하 여 배포 된 컨테이너에 쿼리를 제출 하 고 `serverURL` 변수를 적절 한 값으로 바꿉니다.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

다음 JSON은 상태 API 요청의 POST 본문에 대 한 Text Analytics에 연결 된 JSON 파일의 예입니다.

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>API 응답 본문

다음 JSON은 상태 API 응답 본문에 대 한 Text Analytics의 예입니다.

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>부정 감지 출력

부정 감지를 사용 하는 경우, 경우에 따라 단일 부정 단어가 한 번에 여러 용어를 해결할 수 있습니다. 인식 된 엔터티의 부정은 플래그의 부울 값으로 JSON 출력에 표시 됩니다 `isNegated` .

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>관계 추출 출력

관계 추출 출력에는 관계의 *원본* 및 *대상*에 대 한 URI 참조가 포함 됩니다. 관계 역할이 인 엔터티 `ENTITY` 는 필드에 할당 됩니다 `target` . 관계 역할이 인 엔터티 `ATTRIBUTE` 는 필드에 할당 됩니다 `source` . 약어 관계는 양방향 `source` 및 `target` 필드를 포함 하며 `bidirectional` 로 설정 됩니다 `true` . 

```json
"relations": [
  {
      "relationType": "DosageOfMedication",
      "score": 1.0,
      "bidirectional": false,
      "source": "#/documents/2/entities/0",
      "target": "#/documents/2/entities/1",
      "entities": [
          {
              "id": "0",
              "role": "ATTRIBUTE"
          },
          {
              "id": "1",
              "role": "ENTITY"
          }
      ]
  },
...
]
```

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [명명 된 엔터티 범주](../named-entity-types.md)
* [새로운 기능](../whats-new.md)
