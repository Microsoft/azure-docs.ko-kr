---
title: Azure Service Fabric 클러스터에서 인증서 관리
description: Service Fabric 클러스터에서 새 인증서를 추가, 교체 및 제거하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b1ccf83e666f9106a31809ff41d55062826be78c
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869765"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Azure에서 서비스 패브릭 클러스터에 대한 인증서 추가 또는 제거
Service Fabric이 X.509 인증서를 사용하는 방법을 숙지하고 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 읽어보는 것이 좋습니다. 다음 과정으로 진행하기 전에 클러스터 인증서가 무엇이며 어떤 용도로 사용되는지를 이해해야 합니다.

Azure Service Fabrics SDK의 기본 인증서 로드 동작은 기본 구성 정의 또는 보조 구성 정의에 상관없이 정의된 인증서 중 향후 만료 날짜가 가장 먼 인증서를 배포하고 사용하는 것입니다. 이전 동작으로 되돌아가는 것은 권장되지 않는 고급 동작이므로 `Fabric.Code` 구성에서 "UseSecondaryIfNever" 설정 매개 변수 값을 false로 설정해야 합니다.

클러스터를 만드는 동안 클라이언트 인증서 외에도 인증서 보안을 구성할 때 Service Fabric을 사용하여 기본 인증서와 보조 인증서의 두 클러스터 인증서를 지정할 수 있습니다. 만드는 시점에서의 설정에 관한 자세한 내용은 [포털을 통해 Azure 클러스터 만들기](service-fabric-cluster-creation-via-portal.md) 또는 [Azure Resource Manager를 통해 Azure 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요. 만드는 시점에 클러스터 인증서를 하나만 지정하는 경우 해당 인증서가 기본 인증서로 사용됩니다. 클러스터를 만든 후 새 인증서를 보조 인증서로 추가할 수 있습니다.

> [!NOTE]
> 보안 클러스터의 경우 항상 하나 이상의 유효한(취소되지 않거나 만료되지 않은) 클러스터 인증서(기본 또는 보조)를 배포해야 하며 그러지 않으면 클러스터가 작동을 중지합니다. 모든 유효한 인증서가 만료되기 90일 전에 시스템은 해당 노드에 대해 경고 추적 및 경고 상태 이벤트를 생성합니다. 현재, Service Fabric이 이 문서에 대해 전송하는 이메일 또는 기타 알림은 없습니다. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>포털을 사용하여 보조 클러스터 인증서 추가
Azure Portal로는 보조 클러스터 인증서를 추가할 수 없습니다. Azure Powershell을 사용합니다. 이 프로세스는 이 문서의 뒷부분에서 설명합니다.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>포털을 사용하여 클러스터 인증서 제거
보안 클러스터의 경우 하나 이상의 유효한(해지되지 않고 만기되지 않은) 인증서가 항상 필요합니다. 향후 만료 날짜가 가장 멀게 배포된 인증서가 사용되며 인증서를 제거하면 클러스터가 작동을 멈춥니다. 만료된 인증서 또는 가장 빨리 만료될 사용되지 않는 인증서만 제거되도록 해야 합니다.

사용하지 않는 클러스터 보안 인증서를 제거하려면 보안 섹션으로 이동하여 사용하지 않은 인증서의 바로 가기 메뉴에서 '삭제' 옵션을 선택합니다.

기본으로 표시된 인증서를 제거하는 것이 사용자의 의도인 경우에는 미래의 만료 날짜가 기본 인증서보다 멀리 있는 보조 인증서를 배포하여 자동 롤오버 동작이 가능하도록 하고 자동 롤오버가 완료된 후 기존 인증서가 삭제되도록 해야 합니다.

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 보조 인증서 추가

이러한 단계에서는 Resource Manager의 작동 원리에 익숙하며, Resource Manager 템플릿을 사용하여 하나 이상의 Service Fabric 클러스터를 배포했고, 클러스터를 설정하는 데 사용한 템플릿이 있다고 가정합니다. 또한 JSON을 잘 사용하여 작업할 수 있다고 간주합니다.

> [!NOTE]
> 시작 지점으로 사용하거나 필요할 때 사용할 수 있는 샘플 템플릿 및 매개 변수를 찾으려는 경우 이 [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)에서 다운로드합니다. 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Resource Manager 템플릿 편집

편의를 위해 5-VM-1-NodeTypes-Secure_Step2.JSON 샘플에는 앞으로 수행할 모든 편집이 포함되어 있습니다. 이 샘플은 [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)에서 사용할 수 있습니다.

**모든 단계를 수행해야 합니다.**

1. 클러스터를 배포하는 데 사용한 Resource Manager 템플릿을 엽니다. (이전 리포지토리에서 샘플을 다운로드한 경우 5-VM-1-NodeTypes-Secure_Step1.JSON을 사용하여 보안 클러스터를 배포한 다음, 해당 템플릿을 엽니다.)

2. 형식이 “string”인 **새로운 두 매개 변수** “secCertificateThumbprint”와 “secCertificateUrlValue”를 템플릿의 매개 변수 섹션에 추가합니다. 다음 코드 조각을 복사하여 템플릿에 추가할 수 있습니다. 템플릿 원본에 따라 이미 이 항목을 정의했을 수 있습니다. 이 경우 다음 단계를 진행합니다. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. **Microsoft.ServiceFabric/clusters** 리소스를 변경합니다. 템플릿에서 “Microsoft.ServiceFabric/clusters” 리소스 정의를 찾습니다. 이 정의의 속성에서 "Certificate" JSON 태그를 찾을 수 있습니다. 이 태그는 다음 JSON 조각과 비슷합니다.
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    새 태그 "ThumbprintSecondary"를 추가하고 "[parameters('secCertificateThumbprint')]" 값을 지정합니다.  

    이제 리소스 정의는 다음처럼 보일 것입니다(템플릿 원본에 따라, 아래 조각과 정확히 같지는 않을 수 있음). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    **인증서를 교체**하려면 새 인증서를 기본으로 지정한 다음, 현재 인증서를 보조로 이동합니다. 그러면 한 배포 단계에서 현재 기본 인증서가 새 인증서로 교체됩니다.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. **모든** **Microsoft.Compute/virtualMachineScaleSets** 리소스 정의를 변경합니다. Microsoft.Compute/virtualMachineScaleSets 리소스 정의를 찾습니다. "publisher"로 스크롤: "virtualMachineProfile" 아래의 "Microsoft.Azure.ServiceFabric".

    Service Fabric 게시자 설정에서 다음과 같이 나타납니다.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    새 인증서 항목을 추가합니다.
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    이제 속성이 다음과 같이 표시됩니다.
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    **인증서를 교체**하려면 새 인증서를 기본으로 지정한 다음, 현재 인증서를 보조로 이동합니다. 이를 통해 하나의 배포 단계에서 현재 인증서가 새 인증서로 롤오버됩니다.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    이제 속성이 다음과 같이 표시됩니다.    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. **모든** **Microsoft.Compute/virtualMachineScaleSets** 리소스 정의를 변경합니다. Microsoft.Compute/virtualMachineScaleSets 리소스 정의를 찾습니다. "OSProfile" 아래 "VaultCertificates"로 스크롤합니다. 다음과 같이 표시됩니다.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    여기에 secCertificateUrlValue를 추가합니다. 다음 조각을 사용합니다.
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    이제 결과 JSON은 다음과 같이 표시됩니다.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> 템플릿의 모든 Nodetypes/Microsoft.Compute/virtualMachineScaleSets 리소스 정의에 대해 4, 5단계를 반복합니다. 하나라도 누락되면 인증서가 가상 머신 확장 집합에 설치되지 않으며 클러스터에서 클러스터 다운 등과 같은 예기치 않은 결과가 발생합니다(클러스터가 보안에 사용할 수 있는 유효한 인증서가 없이 끝나는 경우). 따라서 진행에 앞서 다시 한 번 확인하세요.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>위에서 추가한 새 매개 변수를 반영하도록 템플릿 파일 편집
[git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)의 샘플을 사용하여 후속 작업을 수행하는 경우 샘플 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON에서 변경하여 시작할 수 있습니다. 

Resource Manager 템플릿 매개 변수 파일을 편집하고 secCertificateThumbprint 및 secCertificateUrlValue에 대한 새로운 두 매개 변수를 추가합니다. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Azure에 템플릿 배포

- 이제 Azure에 템플릿을 배포할 준비가 되었습니다. Azure PS 버전 1+ 명령 프롬프트를 엽니다.
- Azure 계정에 로그인하고 특정 Azure 구독을 선택합니다. 이 단게는 둘 이상의 Azure 구독에 대해 액세스 권한이 있는 사용자들을 위한 중요한 단계입니다.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

템플릿을 배포하기 전에 테스트합니다. 현재 클러스터가 배포된 동일한 리소스 그룹을 사용합니다.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

리소스 그룹에 템플릿을 배포합니다. 현재 클러스터가 배포된 동일한 리소스 그룹을 사용합니다. New-AzResourceGroupDeployment 명령을 실행합니다. 기본값은 **incremental**이므로 모드는 지정할 필요가 없습니다.

> [!NOTE]
> Mode를 Complete로 설정하면 템플릿에 없는 리소스를 실수로 삭제할 수 있습니다. 따라서 이러한 경우에는 사용하지 않도록 합니다.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

다음은 동일한 powershell의 작성된 예제입니다.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

배포가 완료되면 새 인증서를 사용하여 클러스터에 연결하고 일부 쿼리를 수행합니다. 가능한 경우 그런 다음 기존 인증서를 삭제할 수 있습니다. 

자체 서명된 인증서를 사용하는 경우 로컬 TrustedPeople 인증서 저장소로 인증서를 가져와야 하는 것을 잊지 마세요.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
다음은 빠른 참조를 위해 제공되는 보안 클러스터 연결 명령입니다. 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
다음은 빠른 참조를 위해 제공되는 클러스터 상태 확인 명령입니다.

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>클러스터에 클라이언트 인증서를 배포합니다.

앞의 5단계에서 설명한 것과 같은 단계를 사용하여 Key Vault에서 인증서를 노드에 배포할 수 있습니다. 다른 매개 변수를 정의하여 사용하면 됩니다.


## <a name="adding-or-removing-client-certificates"></a>클라이언트 인증서 추가 또는 제거

클러스터 인증서 외에, Service Fabric 클러스터에 대한 관리 작업을 수행하는 클라이언트 인증서를 추가할 수 있습니다.

관리자 또는 읽기 전용 등, 두 종류의 클라이언트 인증서를 추가할 수 있습니다. 그런 다음 이 인증서를 사용하여 클러스터에서의 관리자 작업과 쿼리 작업에 대한 액세스를 제어합니다. 기본적으로 클러스터 인증서는 허용된 관리자 인증서 목록에 추가됩니다.

원하는 수의 클라이언트 인증서를 지정할 수 있습니다. 각 추가/삭제는 Service Fabric 클러스터에 대 한 구성 업데이트를 발생 합니다.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>포털을 통해 관리자 또는 읽기 전용 클라이언트 인증서 추가

1. 보안 섹션으로 이동하고 보안 섹션 맨 위의 ‘+ 인증’ 단추를 선택합니다.
2. ‘인증 추가’ 섹션에서 ‘인증 형식’ - ‘읽기 전용 클라이언트’ 또는 ‘관리자 클라이언트’를 선택합니다.
3. 이제 인증 방법을 선택합니다. 주체 이름을 사용하거나 지문으로 이 인증서를 찾아야 하는 경우 서비스 패브릭에 나타냅니다. 일반적으로 주체 이름을 인증 방법으로 사용하는 것은 보안상 좋지 않습니다. 

![클라이언트 인증서 추가][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>포털을 사용하여 관리자 또는 읽기 전용 클라이언트 인증서 삭제

클러스터 보안에서 사용되는 특정 인증서를 제거하려면 보안 섹션으로 이동하여 특정 인증서에 대한 바로 가기 메뉴에서 ‘삭제’ 옵션을 선택합니다.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에 애플리케이션 인증서 추가

애플리케이션에 사용하는 인증서를 클러스터에 배포하려면 [이 샘플 Powershell 스크립트](scripts/service-fabric-powershell-add-application-certificate.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
클러스터 관리에 대한 자세한 내용은 다음 문서를 읽어보세요.

* [서비스 패브릭 클러스터 업그레이드 프로세스 및 사용자 기대 수준](service-fabric-cluster-upgrade.md)
* [클라이언트에 대한 역할 기반 액세스 설정](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


