---
title: Azure Resource Manager 템플릿을 사용하여 ILB ASE 만들기 - App Service | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 내부 부하 분산 장치 ASE를 만드는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 35e0dc5dabaf1602b87ec6a8be86ed609f3ea12f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130758"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 ILB ASE를 만드는 방법

> [!NOTE] 
> 이 문서는 ASE(App Service Environment) v1에 관한 내용입니다. 사용하기가 더 쉽고 더 강력한 인프라에서 실행되는 최신 버전의 App Service Environment가 있습니다. 새 버전에 대한 자세한 내용은 [App Service Environment 소개](intro.md)를 참조하세요.
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>개요
App Service Environment는 공용 VIP 대신 가상 네트워크 내부 주소를 사용하여 만들 수 있습니다.  이 내부 주소는 ILB(내부 부하 분산 장치)라고 하는 Azure 구성 요소에서 제공됩니다.  Azure 포털을 사용하여 ILB ASE를 만들 수 있습니다.  Azure Resource Manager 템플릿을 통해 자동화 방식으로 만들 수도 있습니다.  이 문서에서는 Azure Resource Manager 템플릿으로 ILB ASE를 만드는 데 필요한 단계와 구문을 안내합니다.

ILB ASE 생성을 자동화하는 과정은 세 단계로 진행됩니다.

1. 먼저 공용 VIP 대신 내부 부하 분산 장치 주소를 사용하여 가상 네트워크에 기본 ASE가 생성됩니다.  이 단계의 일부로 루트 도메인 이름이 ILB ASE에 할당됩니다.
2. ILB ASE가 만들어지면 SSL 인증서가 업로드됩니다.  
3. 업로드된 SSL 인증서는 해당 "기본" SSL 인증서로 ILB ASE에 명시적으로 할당됩니다.  이 SSL 인증서는 ASE에 할당된 공용 루트 도메인을 사용하여 앱의 주소를 지정할 때 ILB ASE의 앱으로 이동되는 SSL 트래픽에 사용됩니다(예: https://someapp.mycustomrootcomain.com)).

## <a name="creating-the-base-ilb-ase"></a>기본 ILB ASE 만들기
Azure Resource Manager 템플릿 예제 및 관련 매개 변수 파일은 GitHub의 [여기][quickstartilbasecreate]에서 사용할 수 있습니다.

*azuredeploy.parameters.json* 파일에 있는 대부분의 매개 변수는 ILB ASE와 공용 VIP에 바인딩된 ASE 둘 다를 만들 때 공통적으로 적용됩니다.  아래 목록에서는 ILB ASE를 만들 때 특별한 고려 사항이 있거나 고유한 매개 변수를 호출합니다.

* *internalLoadBalancingMode*:  대부분의 경우 이 속성을 3으로 설정합니다. 이것은 포트 80/443의 HTTP/HTTPS 트래픽과 ASE의 FTP 서비스에서 수신하는 컨트롤/데이터 채널 포트가 ILB 할당 가상 네트워크 내부 주소에 바인딩될 것임을 의미합니다.  대신, 이 속성을 2로 설정하면 FTP 서비스 관련 포트(컨트롤 및 데이터 채널)는 ILB 주소에 바인딩되지만, HTTP/HTTPS 트래픽은 공용 VIP에 유지됩니다.
* *dnsSuffix*:  이 매개 변수는 ASE에 할당될 기본 루트 도메인을 정의합니다.  Azure App Service의 공용 변형에서 모든 웹앱용 기본 루트 도메인은 *azurewebsites.net*입니다.  그러나 ILB ASE는 고객의 가상 네트워크 내부에 있으므로 공용 서비스의 기본 루트 도메인을 사용하는 것은 적합하지 않습니다.  대신, ILB ASE에는 회사의 내부 가상 네트워크 내에서 사용하기 적합한 기본 루트 도메인이 있어야 합니다.  예를 들어 가상의 Contoso Corporation은 Contoso의 가상 네트워크 내에서만 확인 가능하고 액세스할 수 있는 앱에 기본 루트 도메인 *internal-contoso.com* 을 사용할 수 있습니다. 
* *ipSslAddressCount*:  이 매개 변수는 ILB ASE가 단일 ILB 주소만 가지므로 *azuredeploy.json* 파일에서 자동으로 기본값인 0을 갖습니다.  ILB ASE에 대한 명시적 IP-SSL 주소는 없으므로 ILB ASE에 대한 IP-SSL 주소 풀을 0으로 설정해야 합니다. 그러지 않으면 프로비전 오류가 발생합니다. 

ILB ASE에 대해 *azuredeploy.parameters.json* 파일이 채워진 경우 다음과 같은 PowerShell 코드 조각을 사용하여 ILB ASE를 만들 수 있습니다.  컴퓨터에는 Azure Resource Manager 템플릿 파일이 있는 위치와 일치하도록 파일 경로를 변경합니다.  또한 Azure Resource Manager 배포 이름 및 리소스 그룹 이름에 대해 고유한 값을 제공해야 합니다.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure Resource Manager 템플릿이 제출되고 ILB ASE가 만들어지는 데 몇 시간 정도 소요됩니다.  다 만들어지면 배포를 트리거한 구독에 대한 App Service Environment 목록의 포털 UX에 ILB ASE가 표시됩니다.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>"기본" SSL 인증서 업로드 및 구성
"기본" SSL 인증서가 앱에 대한 SSL 연결을 설정하는 데 사용하므로 ILB ASE를 만든 후에는 SSL 인증서를 ASE에 연결해야 합니다.  가상의 Contoso Corporation 예제를 계속 살펴보면, ASE의 기본 DNS 접미사가 *internal-contoso.com*인 경우 *https://some-random-app.internal-contoso.com*에 연결하기 위해 **.internal-contoso.com*에 유효한 SSL 인증서가 필요합니다. 

유효한 SSL 인증서를 구하는 방법에는 터미널 CA를 사용하거나, 외부 발급자로부터 인증서를 구입하거나, 자체 서명된 인증서를 사용하는 등 다양한 방법이 있습니다.  SSL 인증서의 소스에 관계 없이 다음과 같은 인증서 특성을 올바르게 구성해야 합니다.

* *주체*:  이 특성은 **.your-root-domain-here.com*으로 설정되어야 합니다.
* *주체 대체 이름*:  이 특성에는 **.your-root-domain-here.com* 및 **.scm.your-root-domain-here.com*이 둘 다 포함되어야 합니다.  두 번째 항목을 포함해야 하는 이유는 각 앱과 연결된 SCM/Kudu 사이트에 대한 SSL 연결이 *your-app-name.scm.your-root-domain-here.com*형식의 주소를 사용하여 만들어지기 때문입니다.

유효한 SSL 인증서가 있는 경우 두 가지 추가 준비 단계가 필요합니다.  SSL 인증서를 .pfx 파일로 변환/저장해야 합니다.  .pfx 파일에는 모든 중간 인증서 및 루트 인증서가 포함되어야 하며 암호로 보호되어야 합니다.

SSL 인증서는 Azure Resource Manager 템플릿을 사용하여 업로드되므로 결과 .pfx 파일을 Base64 문자열로 변환해야 합니다.  Azure Resource Manager 템플릿은 텍스트 파일이므로 .pfx 파일은 템플릿의 매개 변수로 포함될 수 있게 Base64 문자열로 변환해야 합니다.

아래의 PowerShell 코드 조각은 자체 서명된 인증서를 생성하고, 인증서를 .pfx 파일로 내보내고, .pfx 파일을 Base64 인코딩 문자열로 변환한 다음 Base64 인코딩 문자열을 개별 파일로 저장하는 예를 보여 줍니다.  Base64 인코딩을 위한 PowerShell 코드는 [PowerShell 스크립트 블로그][examplebase64encoding]에서 적용되었습니다.

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

SSL 인증서가 생성되고 Base64 인코딩 문자열로 변환되면 [기본 SSL 인증서 구성][configuringDefaultSSLCertificate]에 대한 GitHub의 Azure Resource Manager 템플릿 예제를 사용할 수 있습니다.

*azuredeploy.parameters.json* 파일의 매개 변수는 다음과 같습니다.

* *appServiceEnvironmentName*:  구성하는 ILB ASE의 이름입니다.
* *existingAseLocation*:  ILB ASE가 배포된 Azure 지역을 포함하는 텍스트 문자열입니다.  예를 들면 다음과 같습니다.  "미국 중남부"
* *pfxBlobString*:  .pfx 파일의 Base64 인코딩 문자열 표현입니다.  앞서 표시된 코드 조각을 사용하여 "exportedcert.pfx.b64"에 포함된 문자열을 복사한 후 *pfxBlobString* 특성의 값으로 붙여 넣습니다.
* *암호*:  .pfx 파일을 보호하는 데 사용되는 암호입니다.
* *certificateThumbprint*:  인증서의 지문입니다.  PowerShell에서 이 값을 검색하는 경우(예: 이전 코드 조각의 *$certificate.Thumbprint* ) 값을 있는 그대로 사용할 수 있습니다.  그러나 Windows 인증서 대화 상자의 값을 복사하는 경우 불필요한 공백을 제거해야 합니다.  *certificateThumbprint*는 다음과 비슷합니다.  AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*:  인증서를 식별하는 데 사용되는 직접 선택한 친숙한 문자열 식별자입니다.  이 이름은 SSL 인증서를 나타내는 *Microsoft.Web/certificates* 엔터티에 대한 고유한 Azure Resource Manager 식별자의 일부로 사용됩니다.  이름은 다음 접미사 \_yourASENameHere_InternalLoadBalancingASE와 함께 종료**해야** 합니다.  이 접미사는 포털에서 인증서가 ILB 지원 ASE 보안에 사용되는 표시기로 사용됩니다.

*azuredeploy.parameters.json* 을 축약한 예는 다음과 같습니다.

    {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

*azuredeploy.parameters.json* 파일이 채워진 경우 다음과 같은 PowerShell 코드 조각을 사용하여 기본 SSL 인증서를 구성할 수 있습니다.  컴퓨터에는 Azure Resource Manager 템플릿 파일이 있는 위치와 일치하도록 파일 경로를 변경합니다.  또한 Azure Resource Manager 배포 이름 및 리소스 그룹 이름에 대해 고유한 값을 제공해야 합니다.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Azure Resource Manager 템플릿이 제출된 후에 변경 내용을 적용하는 데 ASE 프런트 엔드당 대략 40분이 소요됩니다.  예를 들어 두 개의 프런트 엔드를 사용하는 기본 크기의 ASE가 있는 경우 템플릿을 완료하는 데 약 1시간 20분이 소요됩니다.  템플릿이 실행되는 동안에는 ASE 크기를 조정할 수 없습니다.  

템플릿이 완료되면 ILB ASE의 앱에 HTTPS를 통해 액세스할 수 있으며 기본 SSL 인증서를 사용하여 연결이 보호됩니다.  기본 SSL 인증서는 ILB ASE의 앱 주소는 애플리케이션 이름과 기본 호스트 이름 조합을 사용하여 지정됩니다.  예를 들어 *https://mycustomapp.internal-contoso.com*은 **.internal-contoso.com*에 기본 SSL 인증서를 사용합니다.

그러나 공용 다중 테넌트 서비스에서 실행 중인 앱의 경우와 마찬가지로, 개발자는 개별 앱에 대한 사용자 지정 호스트 이름을 구성한 다음 개별 앱에 대해 고유한 SNI SSL 인증서 바인딩을 구성할 수 있습니다.  

## <a name="getting-started"></a>시작
App Service Environment를 시작하려면 [App Service Environment 소개](app-service-app-service-environment-intro.md)를 참조하세요.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

