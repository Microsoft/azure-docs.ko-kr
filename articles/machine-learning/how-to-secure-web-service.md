---
title: TLS를 사용 하는 보안 웹 서비스
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 통해 배포 된 웹 서비스를 보호 하기 위해 HTTPS를 사용 하도록 설정 하는 방법에 대해 알아봅니다. Azure Machine Learning TLS 버전 1.2을 사용 하 여 웹 서비스로 배포 된 모델을 보호 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0afb7906e102e4f0fb49245949b08618da1693ec
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265680"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning를 통해 배포 된 웹 서비스를 보호 하는 방법을 보여 줍니다.

[HTTPS](https://en.wikipedia.org/wiki/HTTPS) 를 사용 하 여 웹 서비스에 대 한 액세스를 제한 하 고 클라이언트에서 제출 하는 데이터를 보호 합니다. HTTPS는 둘 간의 통신을 암호화 하 여 클라이언트와 웹 서비스 간의 통신을 보호 하는 데 도움이 됩니다. 암호화는 [TLS (전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)를 사용 합니다. Tls는 TLS의 선행 작업 인 *SSL(Secure Sockets Layer)* (SSL) 라고도 합니다.

> [!TIP]
> Azure Machine Learning SDK는 보안 통신과 관련 된 속성에 "SSL" 이라는 용어를 사용 합니다. 이는 웹 서비스에서 *TLS*를 사용 하지 않는다는 의미는 아닙니다. SSL은 보다 일반적으로 인식 되는 용어입니다.
>
> 특히 Azure Machine Learning를 통해 배포 된 웹 서비스는 TLS 버전 1.1만 지원 합니다.

TLS 및 SSL은 모두 암호화 및 id 확인에 도움이 되는 *디지털 인증서*를 사용 합니다. 디지털 인증서의 작동 방식에 대 한 자세한 내용은 위키백과 토픽 [공개 키 인프라](https://en.wikipedia.org/wiki/Public_key_infrastructure)를 참조 하세요.

> [!WARNING]
> 웹 서비스에 HTTPS를 사용 하지 않는 경우 서비스로 전송 되는 데이터는 인터넷을 통해 다른 사용자에 게 표시 될 수 있습니다.
>
> 또한 HTTPS를 사용 하면 클라이언트가 연결 하는 서버의 신뢰성을 확인할 수 있습니다. 이 기능은 메시지 가로채기 ( [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ) 공격 으로부터 클라이언트를 보호 합니다.

웹 서비스의 보안을 유지 하는 일반적인 프로세스는 다음과 같습니다.

1. 도메인 이름을 가져옵니다.

2. 디지털 인증서를 가져옵니다.

3. TLS를 사용 하는 웹 서비스를 배포 또는 업데이트 합니다.

4. 웹 서비스를 가리키도록 DNS를 업데이트합니다.

> [!IMPORTANT]
> AKS (Azure Kubernetes Service)에 배포 하는 경우 사용자 고유의 인증서를 구매 하거나 Microsoft에서 제공 하는 인증서를 사용할 수 있습니다. Microsoft의 인증서를 사용 하는 경우 도메인 이름 또는 TLS/SSL 인증서를 가져올 필요가 없습니다. 자세한 내용은이 문서의 [TLS 및 배포 사용](#enable) 섹션을 참조 하세요.

[배포 대상](how-to-deploy-and-where.md)간에를 보호 하는 경우 약간의 차이가 있습니다.

## <a name="get-a-domain-name"></a>도메인 이름 가져오기

도메인 이름을 아직 소유 하 고 있지 않은 경우 *도메인 이름 등록 기관*에서 하나를 구입 합니다. 프로세스와 가격은 등록 기관 사이에서 다릅니다. 등록자는 도메인 이름을 관리 하는 도구를 제공 합니다. 이러한 도구를 사용 하 여 FQDN (정규화 된 도메인 이름) (예: www \. contoso.com)을 웹 서비스를 호스팅하는 IP 주소에 매핑합니다.

## <a name="get-a-tlsssl-certificate"></a>TLS/SSL 인증서 가져오기

TLS/SSL 인증서 (디지털 인증서)를 가져오는 방법에는 여러 가지가 있습니다. 가장 일반적인 것은 CA ( *인증 기관* )에서 구입 하는 것입니다. 인증서를 가져오는 위치와 관계 없이 다음 파일이 필요 합니다.

* **인증서**입니다. 인증서는 전체 인증서 체인을 포함 해야 하며 "PEM 인코딩" 이어야 합니다.
* **키**. 키도 PEM으로 인코딩해야 합니다.

인증서를 요청할 때 웹 서비스에 사용할 주소의 FQDN (예: www contoso.com)을 제공 해야 합니다 \. . 인증서에 스탬프 된 주소와 클라이언트에서 사용 하는 주소를 비교 하 여 웹 서비스의 id를 확인 합니다. 이러한 주소가 일치 하지 않으면 클라이언트에서 오류 메시지를 가져옵니다.

> [!TIP]
> 인증 기관에서 인증서 및 키를 PEM 인코딩 파일로 제공할 수 없는 경우 [OpenSSL](https://www.openssl.org/) 와 같은 유틸리티를 사용 하 여 형식을 변경할 수 있습니다.

> [!WARNING]
> *자체 서명* 된 인증서는 개발용 으로만 사용 합니다. 프로덕션 환경에서는 사용 하지 마세요. 자체 서명된 인증서로 인해 클라이언트 애플리케이션에 문제가 발생할 수 있습니다. 자세한 내용은 클라이언트 응용 프로그램에서 사용 하는 네트워크 라이브러리 설명서를 참조 하세요.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> TLS를 사용 하도록 설정 하 고 배포

TLS를 사용 하는 서비스를 배포 (또는 다시 배포) 하려면 해당 되는 모든 위치에서 *ssl_enabled* 매개 변수를 "True"로 설정 합니다. *Ssl_certificate* 매개 변수를 *인증서* 파일의 값으로 설정 합니다. *Ssl_key* 를 *키* 파일의 값으로 설정 합니다.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>AKS 및 필드 프로그래밍 가능 게이트 배열 (FPGA)에 배포

  > [!NOTE]
  > 이 단원의 정보는 디자이너에 대 한 보안 웹 서비스를 배포할 때에도 적용 됩니다. Python SDK를 사용 하는 방법을 잘 모르는 경우 [python 용 AZURE MACHINE LEARNING SDK 란 무엇 인가요?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 참조 하세요.

AKS에 배포할 때 새 AKS 클러스터를 만들거나 기존 클러스터를 연결할 수 있습니다. 클러스터를 만들거나 연결 하는 방법에 대 한 자세한 내용은 [Azure Kubernetes Service 클러스터에 모델 배포](how-to-deploy-azure-kubernetes-service.md)를 참조 하세요.
  
-  새 클러스터를 만드는 경우 **[AksCompute ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 를 사용 합니다 provisioning_configuration.
- 기존 클러스터를 연결 하는 경우 **[AksCompute ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 를 사용 합니다 attach_configuration. 둘 다 **enable_ssl** 메서드가 있는 구성 개체를 반환 합니다.

**Enable_ssl** 방법은 Microsoft에서 제공 하는 인증서 또는 구입한 인증서를 사용할 수 있습니다.

  * Microsoft에서 인증서를 사용 하는 경우 *leaf_domain_label* 매개 변수를 사용 해야 합니다. 이 매개 변수는 서비스에 대 한 DNS 이름을 생성 합니다. 예를 들어 "contoso" 값은 도메인 이름 "contoso \<six-random-characters> . \<azureregion> cloudapp.azure.com ", 여기서 \<azureregion> 은 서비스를 포함 하는 지역입니다. 필요에 따라 *overwrite_existing_domain* 매개 변수를 사용 하 여 기존 *leaf_domain_label*를 덮어쓸 수 있습니다.

    TLS를 사용 하는 서비스를 배포 (또는 다시 배포) 하려면 해당 되는 모든 위치에서 *ssl_enabled* 매개 변수를 "True"로 설정 합니다. *Ssl_certificate* 매개 변수를 *인증서* 파일의 값으로 설정 합니다. *Ssl_key* 를 *키* 파일의 값으로 설정 합니다.

    > [!IMPORTANT]
    > Microsoft에서 인증서를 사용 하는 경우 사용자 고유의 인증서 또는 도메인 이름을 구입할 필요가 없습니다.

    다음 예제에서는 Microsoft에서 TLS/SSL 인증서를 사용 하도록 설정 하는 구성을 만드는 방법을 보여 줍니다.

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * *구매한 인증서*를 사용 하는 경우 *ssl_cert_pem_file*, *ssl_key_pem_file*및 *ssl_cname* 매개 변수를 사용 합니다. 다음 예제에서는 *pem* 파일을 사용 하 여 구매한 TLS/SSL 인증서를 사용 하는 구성을 만드는 방법을 보여 줍니다.

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

*Enable_ssl*에 대 한 자세한 내용은 [enable_ssl AksProvisioningConfiguration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) 및 [enable_ssl AksAttachConfiguration () (영문)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)를 참조 하세요.

### <a name="deploy-on-azure-container-instances"></a>Azure Container Instances에 배포

Azure Container Instances에 배포 하는 경우 다음 코드 조각에 나와 있는 것 처럼 TLS 관련 매개 변수에 대 한 값을 제공 합니다.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

자세한 내용은 [Aciwebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)를 참조 하세요.

## <a name="update-your-dns"></a>DNS 업데이트

다음으로는 웹 서비스를 가리키도록 DNS를 업데이트해야 합니다.

+ **Container Instances:**

  도메인 이름 등록자의 도구를 사용 하 여 도메인 이름에 대 한 DNS 레코드를 업데이트 합니다. 레코드는 서비스의 IP 주소를 가리켜야 합니다.

  클라이언트에서 도메인 이름에 대해 구성 된 등록 기관 및 "TTL (time to live)"에 따라 도메인 이름을 확인 하려면 몇 분 또는 몇 시간 동안 지연 될 수 있습니다.

+ **AKS의 경우:**

  > [!WARNING]
  > Microsoft의 인증서를 사용 하 여 서비스를 만드는 *leaf_domain_label* 사용 하는 경우 클러스터에 대 한 DNS 값을 수동으로 업데이트 하지 마십시오. 값은 자동으로 설정 해야 합니다.

  왼쪽 창의 **설정** 아래에 있는 **구성** 탭에서 AKS 클러스터의 공용 IP 주소에 대 한 DNS를 업데이트 합니다. 다음 이미지를 참조 하세요. 공용 IP 주소는 AKS 에이전트 노드 및 기타 네트워킹 리소스를 포함 하는 리소스 그룹에 생성 된 리소스 형식입니다.

  [![Azure Machine Learning: TLS를 사용 하 여 웹 서비스 보안](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>TLS/SSL 인증서 업데이트

TLS/SSL 인증서가 만료 되 고 갱신 되어야 합니다. 일반적으로이는 매년 발생 합니다. 다음 섹션의 정보를 사용 하 여 Azure Kubernetes Service에 배포 된 모델에 대 한 인증서를 업데이트 하 고 갱신할 수 있습니다.

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft에서 생성 한 인증서 업데이트

인증서가 원래 Microsoft에서 생성 된 경우 ( *leaf_domain_label* 를 사용 하 여 서비스를 만드는 경우) 다음 예제 중 하나를 사용 하 여 인증서를 업데이트 합니다.

> [!IMPORTANT]
> * 기존 인증서가 여전히 유효한 경우 `renew=True` (SDK) 또는 `--ssl-renew` (CLI)를 사용 하 여 구성을 강제로 갱신 합니다. 예를 들어 기존 인증서가 여전히 10 일간 유효 하 고를 사용 하지 않는 경우 `renew=True` 인증서를 갱신 하지 못할 수 있습니다.
> * 서비스를 처음 배포할 때는 패턴을 `leaf_domain_label` 사용 하 여 DNS 이름을 만드는 데 사용 됩니다 `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` . 원래 생성 된 6 자리를 포함 하 여 기존 이름을 유지 하려면 원래 값을 사용 `leaf_domain_label` 합니다. 생성 된 6 자리는 포함 하지 마세요.

**SDK 사용**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**CLI 사용**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

자세한 내용은 다음 참조 문서를 참조 하세요.

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>사용자 지정 인증서 업데이트

인증서가 원래 인증 기관에서 생성 된 경우 다음 단계를 사용 합니다.

1. 인증 기관에서 제공 하는 설명서를 사용 하 여 인증서를 갱신 합니다. 이 프로세스는 새 인증서 파일을 만듭니다.

1. SDK 또는 CLI를 사용 하 여 새 인증서로 서비스를 업데이트 합니다.

    **SDK 사용**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **CLI 사용**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

자세한 내용은 다음 참조 문서를 참조 하세요.

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>TLS 사용 안 함

Azure Kubernetes Service에 배포 된 모델에 대해 TLS를 사용 하지 않도록 설정 하려면를 사용 하 여를 만든 `SslConfiguration` `status="Disabled"` 다음 업데이트를 수행 합니다.

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>다음 단계
방법 배우기:
+ [웹 서비스로 배포 된 machine learning 모델 사용](how-to-consume-web-service.md)
+ [Azure virtual network 내에서 실험 및 유추를 안전 하 게 실행](how-to-enable-virtual-network.md)
