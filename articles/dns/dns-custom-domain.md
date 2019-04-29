---
title: Azure DNS와 Azure 리소스 통합
description: Azure DNS를 함께 사용하여 Azure 리소스에 대해 DNS를 제공하는 방법에 대해 알아봅니다.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/18/2019
ms.author: victorh
ms.openlocfilehash: 5c098c6c22b079d586c0bd808df9af4a737c17a8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096251"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure DNS를 사용하여 Azure 서비스에 대해 사용자 지정 도메인 설정 제공

Azure DNS는 사용자 지정 도메인을 지원하거나 FQDN(정규화된 도메인 이름)이 있는 모든 Azure 리소스에 대해 사용자 지정 도메인용 DNS를 제공합니다. 예로 Azure 웹 앱을 있고 사용자가 여 액세스 하려는 contoso.com 또는 www를 사용 하 여\.contoso.com을 FQDN으로 합니다. 이 문서에서는 사용자 지정 도메인을 사용하기 위해 Azure DNS로 Azure 서비스를 구성하는 과정을 안내합니다.

## <a name="prerequisites"></a>필수 조건

사용자 지정 도메인에 대해 Azure DNS를 사용하려면 먼저 도메인을 Azure DNS에 위임해야 합니다. 위임을 위해 이름 서버를 구성하는 방법에 대한 지침은 [도메인을 Azure DNS에 위임](./dns-delegate-domain-azure-dns.md)을 참조하세요. 도메인이 Azure DNS 영역에 위임되면 필요한 DNS 레코드를 구성할 수 있습니다.

[Azure Function App](#azure-function-app), [공용 IP 주소](#public-ip-address), [App Service(Web Apps)](#app-service-web-apps), [Blob Storage](#blob-storage) 및 [Azure CDN](#azure-cdn)에 대해 베니티 또는 사용자 지정 도메인을 구성할 수 있습니다.

## <a name="azure-function-app"></a>Azure Function App

Azure 함수 앱에 대해 사용자 지정 도메인을 구성하기 위해 함수 앱 자체의 구성은 물론 CNAME 레코드가 생성됩니다.
 
**Function App**으로 이동하고 함수 앱을 선택합니다. **플랫폼 기능**을 클릭하고 **네트워킹** 아래에서 **사용자 지정 도메인**을 클릭합니다.

![함수 앱 블레이드](./media/dns-custom-domain/functionapp.png)

**사용자 지정 도메인** 블레이드에서 현재 URL을 기록해 두세요. 이 주소는 생성된 DNS 레코드에 대한 별칭으로 사용됩니다.

![사용자 지정 도메인 블레이드](./media/dns-custom-domain/functionshostname.png)

DNS 영역으로 이동하고 **+ 레코드 집합**을 클릭합니다. **레코드 집합 추가** 블레이드에서 다음 정보를 입력하고 **확인**을 클릭하여 만듭니다.

|자산  |값  |Description  |
|---------|---------|---------|
|이름     | myfunctionapp        | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다.        |
|Type     | CNAME        | 별칭을 사용하는 CNAME 레코드를 사용합니다.        |
|TTL     | 1        | 1은 1시간 동안 사용됩니다.        |
|TTL 단위     | 시간        | 시간 측정으로 시간(Hour)이 사용됩니다.         |
|Alias     | adatumfunction.azurewebsites.net        | 이 예에서 별칭을 만드는 DNS 이름은 함수 앱에 기본적으로 제공된 DNS 이름인 adatumfunction.azurewebsites.net입니다.        |

다시 함수 앱으로 돌아가 **플랫폼 기능**을 클릭하고 **네트워킹** 아래에서 **사용자 지정 도메인**을 클릭한 후 **사용자 지정 호스트 이름** 아래에서 **+ 호스트 이름 추가**를 클릭합니다.

**호스트 이름 추가** 블레이드에서 **호스트 이름** 텍스트 필드에 CNAME 레코드를 입력하고 **유효성 검사**를 클릭합니다. 레코드를 찾으면 **호스트 이름 추가** 단추가 나타납니다. **호스트 이름 추가**를 클릭하여 별칭을 추가합니다.

![함수 앱 호스트 이름 추가 블레이드](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>공용 IP 주소

Resource Manager Vm, 클라우드 서비스, Load Balancer, Application Gateway와 같은 리소스를 해결 하는 공용 IP를 사용 하는 서비스 및 클래식 Vm의 경우 A 레코드는에 대 한 사용자 지정 도메인을 구성 합니다.

**네트워킹** > **공용 IP 주소**로 이동하고 공용 IP 리소스를 선택하고 **구성**을 클릭합니다. 표시된 IP 주소를 기록해 둡니다.

![공용 ip 블레이드](./media/dns-custom-domain/publicip.png)

DNS 영역으로 이동하고 **+ 레코드 집합**을 클릭합니다. **레코드 집합 추가** 블레이드에서 다음 정보를 입력하고 **확인**을 클릭하여 만듭니다.


|자산  |값  |Description  |
|---------|---------|---------|
|이름     | mywebserver        | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다.        |
|Type     | A        | 리소스가 IP 주소이므로 A 레코드를 사용합니다.        |
|TTL     | 1        | 1은 1시간 동안 사용됩니다.        |
|TTL 단위     | 시간        | 시간 측정으로 시간(Hour)이 사용됩니다.         |
|IP 주소     | `<your ip address>`       | 공용 IP 주소입니다.|

![A 레코드 만들기](./media/dns-custom-domain/arecord.png)

A 레코드가 생성되면 `nslookup`을 실행하여 레코드 확인의 유효성을 검사합니다.

![공용 ip dns 조회](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service(Web Apps)

다음 단계는 앱 서비스 웹앱에 대해 사용자 지정 도메인을 구성하는 과정을 안내합니다.

**App Service**로 이동하고 사용자 지정 도메인 이름을 구성하는 리소스를 선택하고 **사용자 지정 도메인**을 클릭합니다.

**사용자 지정 도메인** 블레이드에서 현재 URL을 기록해 두세요. 이 주소는 생성된 DNS 레코드에 대한 별칭으로 사용됩니다.

![사용자 지정 도메인 블레이드](./media/dns-custom-domain/url.png)

DNS 영역으로 이동하고 **+ 레코드 집합**을 클릭합니다. **레코드 집합 추가** 블레이드에서 다음 정보를 입력하고 **확인**을 클릭하여 만듭니다.


|자산  |값  |Description  |
|---------|---------|---------|
|이름     | mywebserver        | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다.        |
|Type     | CNAME        | 별칭을 사용하는 CNAME 레코드를 사용합니다. 리소스에서 IP 주소를 사용한 경우 A 레코드가 사용됩니다.        |
|TTL     | 1        | 1은 1시간 동안 사용됩니다.        |
|TTL 단위     | 시간        | 시간 측정으로 시간(Hour)이 사용됩니다.         |
|Alias     | webserver.azurewebsites.net        | 이 예에서 별칭을 만드는 DNS 이름은 웹앱에 기본적으로 제공된 DNS 이름인 webserver.azurewebsites.net입니다.        |


![CNAME 레코드 만들기](./media/dns-custom-domain/createcnamerecord.png)

사용자 지정 도메인 이름을 위해 구성된 앱 서비스로 돌아갑니다. **사용자 지정 도메인**을 클릭한 후 **호스트 이름**을 클릭합니다. 생성한 CNAME 레코드를 추가하려면 **+ 호스트 이름 추가**를 클릭합니다.

![그림 1](./media/dns-custom-domain/figure1.png)

프로세스가 완료되면 **nslookup**을 실행하여 이름 확인이 작동하는지 확인합니다.

![그림 1](./media/dns-custom-domain/finalnslookup.png)

App Service에 사용자 지정 도메인을 매핑하는 방법에 대한 자세한 내용은 [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)을 참조하세요.

사용자 지정 도메인을 구매해야 하는 경우 App Service 도메인에 대해 자세히 알아보려면 [Azure Web Apps에 대한 사용자 지정 도메인 이름 구입](../app-service/manage-custom-dns-buy-domain.md)을 참조하세요.

## <a name="blob-storage"></a>Blob 저장소

다음 단계에서는 asverify 메서드를 사용하여 Blob Storage 계정에 대해 CNAME 레코드를 구성하는 과정을 안내합니다. 이 메서드는 가동 중지 시간이 없음을 보장합니다.

**Storage** > **스토리지 계정**으로 이동하여 스토리지 계정을 선택하고 **사용자 지정 도메인**을 클릭합니다. 2단계 아래에서 FQDN을 기록해 둡니다. 이 값은 첫 번째 CNAME 레코드를 만드는 데 사용됩니다.

![Blob Storage 사용자 지정 도메인](./media/dns-custom-domain/blobcustomdomain.png)

DNS 영역으로 이동하고 **+ 레코드 집합**을 클릭합니다. **레코드 집합 추가** 블레이드에서 다음 정보를 입력하고 **확인**을 클릭하여 만듭니다.


|자산  |값  |Description  |
|---------|---------|---------|
|이름     | asverify.mystorageaccount        | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다.        |
|Type     | CNAME        | 별칭을 사용하는 CNAME 레코드를 사용합니다.        |
|TTL     | 1        | 1은 1시간 동안 사용됩니다.        |
|TTL 단위     | 시간        | 시간 측정으로 시간(Hour)이 사용됩니다.         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | 이 예에서 별칭을 만드는 DNS 이름은 저장소 계정에 기본적으로 제공된 DNS 이름인 asverify.adatumfunctiona9ed.blob.core.windows.net입니다.        |

**Storage** > **스토리지 계정**을 클릭하여 스토리지 계정으로 돌아가 스토리지 계정을 선택하고 **사용자 지정 도메인**을 클릭합니다. 텍스트 상자에 asverify 접두사 없이 생성한 별칭을 입력하고 [간접 CNAME 유효성 검사 사용]을 선택하고 **저장**을 클릭합니다. 이 단계가 완료되면 DNS 영역으로 돌아가 asverify 접두사 없이 CNAME 레코드를 만듭니다.  이후에는 cdnverify 접두사가 있는 CNAME 레코드를 안전하게 삭제할 수 있습니다.

![Blob Storage 사용자 지정 도메인](./media/dns-custom-domain/indirectvalidate.png)

`nslookup`을 실행하여 DNS 확인 유효성 검사

사용자 지정 도메인을 Blob Storage 엔드포인트에 매핑하는 방법에 대해 자세히 알아보려면 [Blob Storage 엔드포인트에 대한 사용자 지정 도메인 이름 구성](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)을 참조하세요.

## <a name="azure-cdn"></a>Azure CDN

다음 단계에서는 cdnverify 메서드를 사용하여 CDN 엔드포인트에 대해 CNAME 레코드를 구성하는 과정을 안내합니다. 이 메서드는 가동 중지 시간이 없음을 보장합니다.

**네트워킹** > **CDN 프로필**로 이동한 후 CDN 프로필을 선택합니다.

작업 중인 엔드포인트를 선택하고 **+ 사용자 지정 도메인**을 클릭합니다. 이 값으로 **엔드포인트 호스트 이름**은 CNAME 레코드가 가리키는 레코드입니다.

![CDN 사용자 지정 도메인](./media/dns-custom-domain/endpointcustomdomain.png)

DNS 영역으로 이동하고 **+ 레코드 집합**을 클릭합니다. **레코드 집합 추가** 블레이드에서 다음 정보를 입력하고 **확인**을 클릭하여 만듭니다.

|자산  |값  |Description  |
|---------|---------|---------|
|이름     | cdnverify.mycdnendpoint        | 이 값과 도메인 이름 레이블을 함께 사용하면 사용자 지정 도메인 이름에 대한 FQDN입니다.        |
|Type     | CNAME        | 별칭을 사용하는 CNAME 레코드를 사용합니다.        |
|TTL     | 1        | 1은 1시간 동안 사용됩니다.        |
|TTL 단위     | 시간        | 시간 측정으로 시간(Hour)이 사용됩니다.         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | 이 예에서 별칭을 만드는 DNS 이름은 저장소 계정에 기본적으로 제공된 DNS 이름인 cdnverify.adatumcdnendpoint.azureedge.net입니다.        |

**네트워킹** > **CDN 프로필**을 클릭하여 CDN 엔드포인트로 돌아가 CDN 프로필을 선택합니다. **+ 사용자 지정 도메인**을 클릭하고 cdnverify 접두사 없이 CNAME 레코드 별칭을 입력하고 **추가**를 클릭합니다.

이 단계가 완료되면 DNS 영역으로 돌아가 cdnverify 접두사 없이 CNAME 레코드를 만듭니다.  이후에는 cdnverify 접두사가 있는 CNAME 레코드를 안전하게 삭제할 수 있습니다. CDN에 대한 자세한 정보 및 중간 등록 단계 없이 사용자 지정 도메인을 구성하는 방법은 [Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure에서 호스트되는 서비스에 대해 역방향 DNS 구성](dns-reverse-dns-for-azure-services.md) 방법에 대해 알아봅니다.
