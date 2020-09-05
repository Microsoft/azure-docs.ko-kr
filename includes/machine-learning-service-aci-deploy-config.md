---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79486107"
---
`deploymentconfig.json` 문서의 항목은 [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py)에 대한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대한 매개 변수 간의 매핑에 대해 설명합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. ACI의 경우 값은 `ACI`여야 합니다. |
| `containerResourceRequirements` | 해당 없음 | CPU 및 메모리 엔터티에 대한 컨테이너입니다. |
| &emsp;&emsp;`cpu` | `cpu_cores` | 할당할 CPU 코어 수입니다. 기본값은 `0.1`입니다 |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 이 웹 서비스에 할당할 메모리 양(GB)입니다. 기본값은 `0.5`입니다 |
| `location` | `location` | 이 웹 서비스를 배포할 Azure 지역입니다. 지정하지 않으면 작업 영역 위치가 사용됩니다. 사용 가능한 지역에 대한 자세한 내용은 여기에서 확인할 수 있습니다. [ACI 지역](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | 이 웹 서비스에 대해 인증을 사용할지 여부. 기본값은 False입니다. |
| `sslEnabled` | `ssl_enabled` | 이 웹 서비스에 대해 SSL을 사용할지 여부. 기본값은 False입니다. |
| `appInsightsEnabled` | `enable_app_insights` | 이 웹 서비스에 대해 AppInsights를 사용할지 여부. 기본값은 False입니다. |
| `sslCertificate` | `ssl_cert_pem_file` | SSL을 사용하는 경우 필요한 인증서 파일 |
| `sslKey` | `ssl_key_pem_file` | SSL을 사용하는 경우 필요한 키 파일 |
| `cname` | `ssl_cname` | SSL을 사용하는 경우의 cname |
| `dnsNameLabel` | `dns_name_label` | 채점 엔드포인트에 대한 DNS 이름 레이블 지정하지 않으면 채점 엔드포인트에 대해 고유한 DNS 이름 레이블이 생성됩니다. |

다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예제입니다.

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```