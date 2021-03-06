---
title: 웹 서비스로 배포 된 모델에 대 한 인증 구성
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 웹 서비스로 배포 된 machine learning 모델에 대 한 인증을 구성 하는 방법에 대해 알아봅니다.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94447652"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>웹 서비스로 배포 된 모델에 대 한 인증 구성

Azure Machine Learning를 통해 학습 된 기계 학습 모델을 웹 서비스로 배포할 수 있습니다. 이 문서에서는 이러한 배포에 대 한 인증을 구성 하는 방법을 알아봅니다.

Azure Machine Learning에서 만든 모델 배포는 다음 두 가지 인증 방법 중 하나를 사용 하도록 구성할 수 있습니다.

* **키 기반**: 정적 키가 웹 서비스를 인증 하는 데 사용 됩니다.
* **토큰 기반**: 임시 토큰은 Azure Machine Learning 작업 영역에서 가져와 (Azure Active Directory 사용) 웹 서비스를 인증 하는 데 사용 되어야 합니다. 이 토큰은 일정 기간 후에 만료 되며 웹 서비스를 계속 사용 하려면 새로 고쳐야 합니다.

    > [!NOTE]
    > 토큰 기반 인증은 Azure Kubernetes Service에 배포 하는 경우에만 사용할 수 있습니다.

## <a name="key-based-authentication"></a>키 기반 인증

AKS (Azure Kubernetes Service)에 배포 된 웹 서비스에는 기본적으로 키 기반 인증이 *사용* 됩니다.

ACI (Azure Container Instances) 배포 된 서비스는 기본적으로 키 기반 인증을 *사용 하지 않도록* 설정 되어 있지만 `auth_enabled=True` aci 웹 서비스를 만들 때를 설정 하 여 사용 하도록 설정할 수 있습니다. 다음 코드는 키 기반 인증을 사용 하도록 설정 하 여 ACI 배포 구성을 만드는 예입니다.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

그런 다음, `Model` 클래스를 사용하여 배포에서 사용자 지정 ACI 구성을 사용할 수 있습니다.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

인증 키를 페치하려면 `aci_service.get_keys()`를 사용합니다. 키를 다시 생성하려면 `regen_key()` 함수를 사용하고 **Primary** 또는 **Secondary** 를 전달합니다.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>토큰 기반 인증

웹 서비스에 대한 토큰 인증을 사용하도록 설정하는 경우 사용자는 액세스할 수 있도록 웹 서비스에 대한 Azure Machine Learning JSON 웹 토큰을 제공해야 합니다. 토큰은 지정된 시간 프레임 후에 만료되므로 계속 호출하려면 새로 고쳐야 합니다.

* Azure Kubernetes Service에 배포하는 경우 토큰 인증이 **기본적으로 사용하지 않도록 설정** 됩니다.
* 토큰 인증은 Azure Container Instances에 배포할 때 **지원되지 않습니다**.
* 토큰 인증은 **키 기반 인증과 동시에 사용할 수 없습니다**.

토큰 인증을 제어 하려면 배포를 `token_auth_enabled` 만들거나 업데이트할 때 매개 변수를 사용 합니다.

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

토큰 인증을 사용하도록 설정하면 `get_token` 메서드를 사용하여 JWT(JSON 웹 토큰) 및 해당 토큰의 만료 시간을 검색할 수 있습니다.

> [!TIP]
> 서비스 주체를 사용 하 여 토큰을 가져오는 경우 토큰을 검색 하는 데 필요한 최소한의 액세스 권한을 부여 하려면 작업 영역에 대 한 **읽기 권한자** 역할에 할당 합니다.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 `refresh_by` 시간 이후 새 토큰을 요청해야 합니다. Python SDK 외부에서 토큰을 새로 고쳐야 하는 경우 한 가지 옵션은 앞에서 설명한 대로 서비스 주체 인증에서 REST API를 사용하여 `service.get_token()`을 주기적으로 호출하는 것입니다.
>
> Azure Kubernetes Service 클러스터와 동일한 지역에 Azure Machine Learning 작업 영역을 만드는 것이 좋습니다.
>
> 토큰으로 인증하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성되는 지역을 호출합니다. 작업 영역 영역을 사용할 수 없는 경우 클러스터가 작업 영역에서 다른 지역에 있는 경우에도 웹 서비스에 대 한 토큰을 가져올 수 없습니다. 따라서 작업 영역 영역을 다시 사용할 수 있을 때까지 Azure AD 인증를 사용할 수 없습니다.
>
> 또한 클러스터 지역과 작업 영역 영역 간의 거리가 클수록 토큰을 인출 하는 데 시간이 오래 걸립니다.

## <a name="next-steps"></a>다음 단계

배포 된 모델에 대 한 인증에 대 한 자세한 내용은 [웹 서비스로 배포 된 모델에 대 한 클라이언트 만들기](how-to-consume-web-service.md)를 참조 하세요.