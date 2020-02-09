---
title: Azure Storage에서 정적 웹 사이트 호스팅
description: 최신 웹 애플리케이션을 호스팅하는 비용 효율적이고 확장 가능한 솔루션을 제공하는 정적 웹 사이트 호스팅입니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: a35239354d23f75361d5577d6b7efc8254943147
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906596"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스팅

*$Web*이라는 저장소 컨테이너에서 직접 정적 콘텐츠 (HTML, CSS, JavaScript 및 이미지 파일)를 제공할 수 있습니다. Azure Storage에서 콘텐츠를 호스팅하면 [Azure Functions](/azure/azure-functions/functions-overview) 및 기타 PaaS (Platform as a service) 서비스를 포함 하는 서버 리스 아키텍처를 사용할 수 있습니다.

> [!NOTE]
> 사이트가 서버 쪽 코드에 종속 되는 경우 대신 [Azure App Service](/azure/app-service/overview) 를 사용 합니다.

## <a name="setting-up-a-static-website"></a>정적 웹 사이트 설정

정적 웹 사이트 호스팅은 저장소 계정에서 사용 하도록 설정 해야 하는 기능입니다.

정적 웹 사이트 호스팅을 사용 하도록 설정 하려면 기본 파일의 이름을 선택한 다음 필요에 따라 사용자 지정 404 페이지의 경로를 제공 합니다. **$Web** 이라는 blob 저장소 컨테이너가 계정에 아직 없는 경우 하나를 만듭니다. 이 컨테이너에 사이트의 파일을 추가 합니다.

단계별 지침은 [Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website-how-to.md)을 참조 하세요.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$Web** 컨테이너의 파일은 대/소문자를 구분 하며 익명 액세스 요청을 통해 제공 되며 읽기 작업을 통해서만 사용할 수 있습니다.

## <a name="uploading-content"></a>콘텐츠 업로드

이러한 도구 중 하나를 사용 하 여 **$web** 컨테이너에 콘텐츠를 업로드할 수 있습니다.

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 모듈](storage-blob-static-website-how-to.md#powershell)
> * [AZCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 확장](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>콘텐츠 보기

사용자는 웹 사이트의 공용 URL을 사용 하 여 브라우저에서 사이트 콘텐츠를 볼 수 있습니다. Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 URL을 찾을 수 있습니다. 이 표를 가이드로 참조하세요.

|도구| 지침 |
|----|----|
|**Azure Portal** | [Azure Portal를 사용 하 여 웹 사이트 URL을 찾습니다.](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Azure CLI를 사용 하 여 웹 사이트 URL을 찾습니다.](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 모듈** | [PowerShell을 사용 하 여 웹 사이트 URL 찾기](storage-blob-static-website-how-to.md#powershell-find-url) |

사이트의 URL은 지역 코드를 포함 합니다. 예를 들어 `https://contosoblobaccount.z22.web.core.windows.net/` URL에는 지역 코드 `z22`포함 되어 있습니다.

이 코드는 URL에 남아 있어야 하지만 내부용 으로만 사용 되며 다른 방법으로는 해당 코드를 사용할 필요가 없습니다.

정적 웹 사이트 호스팅을 사용 하도록 설정할 때 지정한 인덱스 문서는 사용자가 사이트를 열고 특정 파일을 지정 하지 않을 때 표시 됩니다 (예: `https://contosoblobaccount.z22.web.core.windows.net`).  

서버에서 404 오류를 반환 하 고 웹 사이트를 사용 하도록 설정할 때 오류 문서를 지정 하지 않은 경우 기본 404 페이지가 사용자에 게 반환 됩니다.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 는 정적 웹 사이트에서 지원 되지 않습니다.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>웹 컨테이너의 공용 액세스 수준을 설정 하는 경우의 영향

**$Web** 컨테이너의 공용 액세스 수준을 수정할 수 있지만 이러한 파일은 익명 액세스 요청을 통해 제공 되므로 기본 정적 웹 사이트 엔드포인트에는 영향을 주지 않습니다. 이는 모든 파일에 대 한 공용 (읽기 전용) 액세스를 의미 합니다.

다음 스크린샷은 Azure Portal의 공용 액세스 수준 설정을 보여 줍니다.

![포털에서 공용 액세스 수준을 설정 하는 방법을 보여 주는 스크린샷](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

기본 정적 웹 사이트 엔드포인트에는 영향을 주지 않지만 공용 액세스 수준에 대 한 변경 내용은 주 blob service 엔드포인트에 영향을 줍니다.

예를 들어 **$web** 컨테이너의 공용 액세스 수준을 **Private (익명 액세스 없음)** 에서 **blob (blob에 대 한 익명 읽기 액세스**)로 변경 하는 경우 주 정적 웹 사이트 엔드포인트에 대 한 공용 액세스 수준이 변경 되지 않습니다 `https://contosoblobaccount.z22.web.core.windows.net/index.html`.

그러나 기본 blob 서비스 엔드포인트에 대 한 공용 액세스 `https://contosoblobaccount.blob.core.windows.net/$web/index.html` 개인에서 공용으로 변경 됩니다. 이제 사용자는 이러한 두 엔드포인트 중 하나를 사용 하 여 해당 파일을 열 수 있습니다.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>정적 웹 사이트 URL에 사용자 지정 도메인 매핑

사용자 지정 도메인을 통해 정적 웹 사이트를 사용할 수 있도록 설정할 수 있습니다. 

Azure Storage 기본적으로 지원 하기 때문에 사용자 지정 도메인에 대 한 HTTP 액세스를 사용 하는 것이 더 쉽습니다. Azure Storage에서 기본적으로 사용자 지정 도메인을 사용 하 여 HTTPS를 지원 하지 않기 때문에 HTTPS를 사용 하도록 설정 하려면 Azure CDN를 사용 해야 합니다. 단계별 지침은 [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md) 을 참조 하세요.

저장소 계정이 HTTPS를 통한 [보안 전송을 요구](../common/storage-require-secure-transfer.md) 하도록 구성 된 경우 사용자는 https 엔드포인트을 사용 해야 합니다. 

> [!TIP]
> Azure에서 도메인을 호스트 하는 것이 좋습니다. 자세한 내용은 [Azure DNS에서 도메인 호스팅](../../dns/dns-delegate-domain-azure-dns.md)을 참조 하세요.

## <a name="pricing"></a>가격 책정

정적 웹 사이트 호스팅을 무료로 사용할 수 있습니다. 사이트에서 활용 하는 blob 저장소 및 운영 비용에 대해서만 요금이 청구 됩니다. Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="metrics"></a>메트릭

정적 웹 사이트 페이지에서 메트릭을 사용 하도록 설정할 수 있습니다. 메트릭을 사용 하도록 설정 하면 **$web** 컨테이너에 있는 파일에 대 한 트래픽 통계가 메트릭 대시보드에 보고 됩니다.

정적 웹 사이트 페이지에서 메트릭을 사용 하도록 설정 하려면 [정적 웹 사이트 페이지에서 메트릭 사용](storage-blob-static-website-how-to.md#metrics)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Storage에서 정적 웹 사이트를 호스팅합니다.](storage-blob-static-website-how-to.md)
* [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [첫 번째 서버 없는 웹앱 빌드](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [자습서: Azure DNS에 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)
