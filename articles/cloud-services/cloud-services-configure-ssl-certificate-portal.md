---
title: 클라우드 서비스에 대 한 TLS 구성 | Microsoft Docs
description: 웹 역할에 대 한 HTTPS 끝점을 지정 하는 방법 및 응용 프로그램 보안을 위해 TLS/SSL 인증서를 업로드 하는 방법을 알아봅니다. 이 예제는 Azure 포털을 사용합니다.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 33aa088efd7768153d4a17472d82e0826f4ffa6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742644"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Azure에서 응용 프로그램에 대 한 TLS 구성

> [!IMPORTANT]
> Azure [Cloud Services (확장 지원)](../cloud-services-extended-support/overview.md) 는 azure Cloud Services 제품에 대 한 새로운 Azure Resource Manager 기반 배포 모델입니다.이러한 변경으로 Azure Service Manager 기반 배포 모델에서 실행 되는 Azure Cloud Services는 Cloud Services (클래식)으로 이름이 바뀌고 모든 새 배포는 [Cloud Services (확장 된 지원)](../cloud-services-extended-support/overview.md)를 사용 해야 합니다.

이전에 SSL (Secure Socket Layer) 암호화로 알려진 TLS (Transport Layer Security)는 인터넷을 통해 전송 되는 데이터를 보호 하는 가장 일반적으로 사용 되는 방법입니다. 이 일반 작업에서는 웹 역할에 대 한 HTTPS 끝점을 지정 하는 방법 및 응용 프로그램 보안을 위해 TLS/SSL 인증서를 업로드 하는 방법을 설명 합니다.

> [!NOTE]
> 이 작업의 절차는 Azure Cloud Services에 적용됩니다. App Services에 대해서는 [이 항목](../app-service/configure-ssl-bindings.md)을 참조하세요.
>

이 작업에서는 프로덕션 배포를 사용합니다. 스테이징 배포에 대한 정보는 이 항목의 끝에 제공됩니다.

클라우드 서비스를 아직 만들지 않은 경우 먼저 [이 문서를](cloud-services-how-to-create-deploy-portal.md) 읽어보세요.

## <a name="step-1-get-a-tlsssl-certificate"></a>1 단계: TLS/SSL 인증서 가져오기
응용 프로그램에 대해 TLS를 구성 하려면 먼저이 목적으로 인증서를 발급 하는 신뢰할 수 있는 타사 CA (인증 기관)에서 서명 된 TLS/SSL 인증서를 받아야 합니다. 아직 없는 경우 TLS/SSL 인증서를 판매 하는 회사에서 받아야 합니다.

인증서는 Azure에서 TLS/SSL 인증서에 대 한 다음 요구 사항을 충족 해야 합니다.

* 인증서는 공개 키를 포함 해야 합니다.
* 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
* 인증서의 주체 이름은 클라우드 서비스 액세스에 사용되는 도메인과 일치해야 합니다. Cloudapp.net 도메인에 대 한 CA (인증 기관)에서 TLS/SSL 인증서를 가져올 수 없습니다. 서비스에 액세스할 때 사용할 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 애플리케이션 액세스에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다. 예를 들어 사용자 지정 도메인 이름이 **contoso.com** 인 경우 CA에서 **_. contoso.com_* 또는 **www \. contoso.com** 에 대 한 인증서를 요청 합니다.
* 인증서는 최소한 2048비트 암호화를 사용해야 합니다.

테스트용으로 자체 서명된 인증서를 [만들어](cloud-services-certs-create.md) 사용할 수 있습니다. 자체 서명된 인증서는 CA를 통해 인증되지 않으며 cloudapp.net 도메인을 웹 사이트 URL로 사용할 수 있습니다. 예를 들어 다음 작업에서는 인증서에서 사용되는 CN(일반 이름)이 **sslexample.cloudapp.net** 인 자체 서명된 인증서를 사용합니다.

다음으로 인증서에 대한 정보를 서비스 정의 및 서비스 구성 파일에 포함해야 합니다.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>2단계: 서비스 정의 및 구성 파일 수정
인증서를 사용하도록 애플리케이션을 구성하고 HTTPS 엔드포인트를 추가해야 합니다. 따라서 서비스 정의 및 서비스 구성 파일을 업데이트해야 합니다.

1. 개발 환경에서 서비스 정의 파일(CSDEF)을 열고 **WebRole** 섹션 내에 **Certificates** 섹션을 추가한 후 인증서(및 중간 인증서)에 대한 다음 정보를 포함합니다.

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   **Certificates** 섹션에서는 인증서의 이름, 위치 및 인증서가 위치한 저장소의 이름을 정의합니다.

   권한(`permissionLevel` 특성)은 다음 값 중 하나로 설정될 수 있습니다.

   | 권한 값 | 설명 |
   | --- | --- |
   | limitedOrElevated |**(기본값)** 모든 역할 프로세스는 프라이빗 키에 액세스할 수 있습니다. |
   | elevated |승격된 프로세스만 프라이빗 키에 액세스할 수 있습니다. |

2. 서비스 정의 파일에서 **엔드포인트** 섹션 내에 **InputEndpoint** 요소를 추가하여 HTTPS를 사용하도록 설정합니다.

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. 서비스 정의 파일에서 **Sites** 섹션 내에 **Binding** 요소를 추가합니다. 이 요소는 HTTPS 바인딩을 추가하여 엔드포인트를 사이트에 매핑합니다.

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   서비스 정의 파일에서 필요한 사항은 모두 변경했지만 인증서 정보를 서비스 구성 파일에 추가해야 합니다.
4. 서비스 구성 파일(CSCFG), ServiceConfiguration.Cloud.cscfg에서 사용 중인 인증서 값에 해당하는 **Certificates** 값을 추가합니다. 다음 코드 예제에서는 지문 값을 제외한 **Certificates** 섹션의 세부 정보를 제공합니다.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

이 예제에서는 지문 알고리즘에 **sha1** 을 사용합니다. 인증서의 지문 알고리즘에 적합한 값을 지정하세요.

서비스 정의 및 서비스 구성 파일이 업데이트되었으므로 Azure에 업로드할 배포를 패키지합니다. **cspack** 를 사용하는 경우 **/generateConfigurationFile** 플래그를 사용하지 않도록 하세요. 이 플래그는 방금 삽입한 인증서 정보를 덮어씁니다.

## <a name="step-3-upload-a-certificate"></a>3단계: 인증서 업로드
Azure Portal에 연결하고 다음을 수행합니다.

1. 포털의 **모든 리소스** 섹션에서 클라우드 서비스를 선택합니다.

    ![클라우드 서비스 게시](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. **인증서** 를 클릭합니다.

    ![인증서 아이콘 클릭](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. 인증서 영역 위쪽에서 **업로드** 를 클릭합니다.

    ![업로드 메뉴 항목 클릭](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. **파일**, **암호** 를 입력하고 데이터 입력 영역 아래쪽의 **업로드** 를 클릭합니다.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>4단계: HTTPS를 사용하여 역할 인스턴스에 연결
이제 Azure에서 배포가 실행되고 있으므로 HTTPS를 사용하여 연결할 수 있습니다.

1. **사이트 URL** 을 클릭하여 웹 브라우저를 엽니다.

   ![사이트 URL 클릭](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. 웹 브라우저에서 **http** 대신 **https** 를 사용하도록 링크를 수정한 다음 페이지를 방문합니다.

   > [!NOTE]
   > 자체 서명된 인증서를 사용하는 경우 자체 서명된 인증서와 연결된 HTTPS 엔드포인트로 이동하면 브라우저에 인증서오류가 표시됩니다. 신뢰할 수 있는 인증 기관에서 서명한 인증서를 사용하면 이 문제가 해결되지만 이 오류는 무시할 수 있습니다. 또 다른 옵션으로 사용자의 신뢰할 수 있는 인증 기관 인증서 저장소에 자체 서명된 인증서를 추가할 수 있습니다.
   >
   >

   ![사이트 미리 보기](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > 프로덕션 배포 대신 스테이징 배포에 TLS를 사용 하려는 경우 먼저 스테이징 배포에 사용 되는 URL을 결정 해야 합니다. 클라우드 서비스가 배포되면 `https://deployment-id.cloudapp.net/` 형식의 **배포 ID** GUID에 따라 스테이징 환경에 대한 URL이 결정됩니다.  
   >
   > GUID 기반 URL과 같은 CN(일반 이름)으로 인증서를 만듭니다(예: **328187776e774ceda8fc57609d404462.cloudapp.net**). 스테이징된 클라우드 서비스에 인증서를 추가하려면 포털을 사용합니다. 그런 다음 인증서 정보를 CSDEF 및 CSCFG 파일에 추가하고 애플리케이션을 다시 패키지하고 스테이징된 배포를 업데이트하여 새 패키지를 사용합니다.
   >

## <a name="next-steps"></a>다음 단계
* [클라우드 서비스의 일반 구성](cloud-services-how-to-configure-portal.md)
* [클라우드 서비스를 배포](cloud-services-how-to-create-deploy-portal.md)하는 방법을 알아봅니다.
* [사용자 지정 도메인 이름을](cloud-services-custom-domain-name-portal.md)구성 합니다.
* [클라우드 서비스를 관리합니다](cloud-services-how-to-manage-portal.md).



