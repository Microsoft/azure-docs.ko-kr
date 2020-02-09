---
title: Cloud Services에서 사용자 지정 도메인 이름 구성| Microsoft Docs
description: DNS 설정을 구성하여 사용자 지정 도메인에서 Azure 애플리케이션이나 데이터를 인터넷에 노출하는 방법을 알아봅니다.  이 예제는 Azure 포털을 사용합니다.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: b1f75a5f7a97907bf5b8bb460ff2df420d053f9e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386802"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성
클라우드 서비스를 만들면 Azure에서 **cloudapp.net**의 하위 도메인에 이 서비스를 할당합니다. 예를 들어 클라우드 서비스의 이름이 "contoso"인 경우 사용자가 `http://contoso.cloudapp.net`과 같은 URL에서 애플리케이션에 액세스할 수 있습니다. Azure는 가상 IP 주소도 할당합니다.

그러나 **contoso.com**등의 고유한 도메인 이름에도 애플리케이션을 표시할 수 있습니다. 이 문서에서는 클라우드 서비스 웹 역할에 대해 사용자 지정 도메인 이름을 예약 또는 구성하는 방법에 대해 설명합니다.

CNAME 및 A 레코드가 무엇인지 이미 알고 있나요? [설명을 건너뛰고 이동하세요](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> 이 작업의 절차는 Azure Cloud Services에 적용됩니다. App Services의 경우 [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](../app-service/app-service-web-tutorial-custom-domain.md)을 참조하세요. 스토리지 계정의 경우 [Azure Blob Storage 엔드포인트에 대한 사용자 지정 도메인 이름 구성](../storage/blobs/storage-custom-domain-name.md)을 참조하세요.
> 
> 

<p/>

> [!TIP]
> 새로운 Azure의 [안내 방식 연습](https://support.microsoft.com/kb/2990804)을 사용하면 작업을 보다 빠르게 수행할 수 있습니다.  이 연습을 통해 사용자 지정 도메인 이름을 연결하고 SSL을 사용하여 Azure Cloud Services 또는 Azure Websites와의 통신을 보호하는 등의 작업을 매우 쉽게 완료할 수 있습니다.
> 
> 

## <a name="understand-cname-and-a-records"></a>CNAME 및 A 레코드 이해
CNAME(또는 별칭 레코드) 및 A 레코드는 둘 다 도메인 이름을 특정 서버(또는 이 경우 서비스)에 연결할 수 있게 해 주지만 다르게 작동합니다. Azure 클라우드 서비스와 함께 A 레코드를 사용하는 경우의 몇 가지 특정 고려 사항도 있으며, 사용할 레코드를 결정하기 전에 고려해야 합니다.

### <a name="cname-or-alias-record"></a>CNAME 또는 별칭 레코드
CNAME 레코드는 **contoso.com** 또는 **www\.contoso.com**와 같은 *특정* 도메인을 정식 도메인 이름에 매핑합니다. 이 경우 정식 도메인 이름은 Azure 호스티드 애플리케이션의 **[myapp].cloudapp.net** 도메인 이름입니다. CNAME을 만들면 **[myapp].cloudapp.net**에 대한 별칭이 만들어집니다. CNAME 항목은 자동으로 **[myapp].cloudapp.net** 서비스의 IP 주소로 확인되기 때문에 클라우드 서비스의 IP 주소가 변경될 경우 아무 조치도 수행할 필요가 없습니다.

> [!NOTE]
> 일부 도메인 등록 기관는 contoso.com와 같은 루트 이름이 아니라 www\.contoso.com와 같은 CNAME 레코드를 사용 하는 경우 하위 도메인을 매핑할 수 있습니다. CNAME 레코드에 대한 자세한 내용은 등록 기관에서 제공하는 설명서인 [CNAME 레코드에 대한 Wikipedia 항목](https://en.wikipedia.org/wiki/CNAME_record) 또는 [IETF 도메인 이름 - 구현 및 사양](https://tools.ietf.org/html/rfc1035) 문서를 참조하세요.

### <a name="a-record"></a>A 레코드
*A* 레코드는 **contoso.com** 또는 **www\.contoso.com**와 같은 도메인 이나 **contoso.com\*** 와 같은 *와일드 카드 도메인* 을 IP 주소에 매핑합니다. Azure 클라우드 서비스의 경우 서비스의 가상 IP입니다. 따라서 CNAME 레코드에 대한 A 레코드의 주요 혜택은 **contoso.com**\*와 같이 와일드 카드를 사용 하는 하나의 항목을 사용할 수 있다는 것입니다 .이는 **mail.contoso.com**, **login.contoso.com**또는 **www\.contso.com**같은 여러 하위 도메인에 대한 요청을 처리 합니다.

> [!NOTE]
> A 레코드는 고정 IP 주소에 매핑되므로 변경 내용을 클라우드 서비스의 IP 주소로 자동으로 확인할 수 없습니다. 클라우드 서비스에서 사용 하는 IP 주소는 빈 슬롯 (프로덕션 또는 스테이징)에 처음 배포할 때 할당 됩니다. 슬롯에 대한 배포를 삭제 하는 경우 Azure에서 IP 주소를 해제 하 고 이후 슬롯에 대한 모든 배포에 새 IP 주소를 지정할 수 있습니다.
> 
> 편의상, 스테이징 배포와 프로덕션 배포 간에 전환하거나 기존 배포의 바로 업그레이드를 수행하는 경우 주어진 배포 슬롯(프로덕션 또는 스테이징)의 IP 주소가 지속됩니다. 이러한 작업을 수행하는 방법에 대한 자세한 내용은 [클라우드 서비스를 관리하는 방법](cloud-services-how-to-manage-portal.md)을 참조하세요.
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>사용자 지정 도메인에 대한 CNAME 레코드 추가
CNAME 레코드를 만들려면 등록 기관에서 제공한 도구를 사용하여 사용자 지정 도메인에 대한 새 항목을 DNS 테이블에 추가해야 합니다. 각 등록 기관은 서로 유사하지만 약간 다른 방법으로 CNAME 레코드를 지정하지만 개념은 동일합니다.

1. 이러한 방법 중 하나를 사용하여 클라우드 서비스에 할당된 **.cloudapp.net** 도메인 이름을 찾습니다.

   * [Azure Portal]에 로그인 하 여 클라우드 서비스를 선택 하 고 **개요** 섹션에서 **사이트 URL** 항목을 찾습니다.

       ![사이트 URL을 표시하는 한눈에 보기 섹션][csurl]

       **OR**
   * [Azure Powershell](/powershell/azure/overview)을 설치 및 구성하고 다음 명령을 사용합니다.

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     CNAME 레코드를 만들 때 필요하므로 두 방법 중 하나에서 반환된 URL에 사용된 도메인 이름을 저장합니다.
2. DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다.
3. 이제 CNAME을 선택하거나 입력할 수 있는 위치를 찾습니다. 드롭다운에서 레코드 유형을 선택하거나 고급 설정 페이지로 이동해야 할 수도 있습니다. **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 합니다.
4. 또한 **www\.customdomain.com**에 대한 별칭을 만들려는 경우 **WWW** 와 같이 CNAME에 대한 도메인 또는 하위 도메인 별칭을 제공 해야 합니다. 루트 도메인에 대한 별칭을 만들려는 경우 등록 기관의 DNS 도구에서 ' **\@** ' 기호로 표시될 수도 있습니다.
5. 정식 호스트 이름을 제공해야 합니다. 이 경우에는 애플리케이션의 **cloudapp.net** 도메인입니다.

예를 들어 다음 CNAME 레코드는 **www\.contoso.com** 에서 배포 된 응용 프로그램의 사용자 지정 도메인 이름인 **contoso.cloudapp.net**로 모든 트래픽을 전달 합니다.

| 별칭/호스트 이름/하위 도메인 | 정식 도메인 |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> **Www\.contoso.com** 방문자에 게는 실제 호스트 (contoso.cloudapp.net)가 표시 되지 않으므로 전달 프로세스는 최종 사용자에 게 표시 되지 않습니다.
> 
> 위 예제는 **www** 하위 도메인의 트래픽에만 적용됩니다. CNAME 레코드와 함께 와일드카드를 사용할 수 없으므로 각 도메인/하위 도메인에 대해 하나의 CNAME을 만들어야 합니다. *.contoso.com과 같은 하위 도메인에서 cloudapp.net 주소로 트래픽을 보내려는 경우 DNS 설정에서 **URL 리디렉션** 또는 **URL 전달** 항목을 구성하거나 A 레코드를 만들 수 있습니다.

## <a name="add-an-a-record-for-your-custom-domain"></a>사용자 지정 도메인에 대한 A 레코드 추가
A 레코드를 만들려면 먼저 클라우드 서비스의 가상 IP 주소를 찾아야 합니다. 그런 다음 등록 기관에서 제공한 도구를 사용하여 사용자 지정 도메인에 대한 새 항목을 DNS 테이블에 추가합니다. 각 등록 기관은 서로 유사하지만 약간 다른 방법으로 A 레코드를 지정하지만 개념은 동일합니다.

1. 다음 방법 중 하나를 사용하여 클라우드 서비스의 IP 주소를 가져옵니다.

   * [Azure Portal]에 로그인 하 여 클라우드 서비스를 선택 하 고 **개요** 섹션에서 **공용 IP 주소** 항목을 찾습니다.

       ![VIP를 표시하는 한눈에 보기 섹션][vip]

       **OR**
   * [Azure Powershell](/powershell/azure/overview)을 설치 및 구성하고 다음 명령을 사용합니다.

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     A 레코드를 만들 때 필요하므로 IP 주소를 저장합니다.
2. DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**로 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다.
3. 이제 A 레코드를 선택하거나 입력할 수 있는 위치를 찾습니다. 드롭다운에서 레코드 유형을 선택하거나 고급 설정 페이지로 이동해야 할 수도 있습니다.
4. 이 A 레코드를 사용할 도메인 또는 하위 도메인을 선택하거나 입력합니다. 예를 들어 **www\.customdomain.com**에 대한 별칭을 만들려는 경우 **www** 를 선택 합니다. 모든 하위 도메인에 대한 와일드카드 항목을 만들려면 '*****'를 입력합니다. **Mail.customdomain.com**, **login.customdomain.com**및 **www\.customdomain.com**와 같은 모든 하위 도메인을 포함 합니다.

    루트 도메인에 대한 A 레코드를 만들려는 경우 등록 기관의 DNS 도구에서 ' **\@** ' 기호로 표시될 수도 있습니다.
5. 제공된 필드에 클라우드 서비스의 IP 주소를 입력합니다. 그러면 A 레코드에 사용된 도메인 항목이 클라우드 서비스 배포의 IP 주소와 연결됩니다.

예를 들어 다음 A 레코드는 모든 트래픽을 **contoso.com**에서 배포된 애플리케이션의 IP 주소인 **137.135.70.239**로 전달합니다.

| 호스트 이름/하위 도메인 | IP 주소 |
| --- | --- |
| \@ |137.135.70.239 |

이 예제에서는 루트 도메인에 대한 A 레코드를 만드는 방법을 보여 줍니다. 모든 하위 도메인을 포함할 와일드카드 항목을 만들려면 '*****'를 하위 도메인으로 입력합니다.

> [!WARNING]
> Azure의 IP 주소는 기본적으로 동적입니다. 사용자의 IP 주소가 변경되지 않도록 [예약된 IP 주소](../virtual-network/virtual-networks-reserved-public-ip.md) 를 사용하려고 할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* [Cloud Services를 관리하는 방법](cloud-services-how-to-manage-portal.md)
* [CDN 콘텐츠를 사용자 지정 도메인에 매핑하는 방법](../cdn/cdn-map-content-to-custom-domain.md)
* [클라우드 서비스의 일반 구성](cloud-services-how-to-configure-portal.md)
* [클라우드 서비스를 배포](cloud-services-how-to-create-deploy-portal.md)하는 방법을 알아봅니다.
* [SSL 인증서](cloud-services-configure-ssl-certificate-portal.md)구성

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png



