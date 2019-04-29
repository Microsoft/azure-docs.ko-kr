---
title: Azure Storage에서 정적 웹 사이트 호스팅
description: 최신 웹 애플리케이션을 호스팅하는 비용 효율적이고 확장 가능한 솔루션을 제공하는 정적 웹 사이트 호스팅입니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 67d3dcad4ec73ee09ec40282b2fbdea945daefe4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122679"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스팅
Azure Storage GPv2 계정을 사용하면 *$web*이라는 스토리지 컨테이너에서 직접 정적 콘텐츠(HTML, CSS, JavaScript 및 이미지 파일)를 서비스할 수 있습니다. Azure Storage에 호스팅하면 [Azure Functions](/azure/azure-functions/functions-overview) 및 기타 PaaS 서비스를 포함한 서버리스 아키텍처를 사용할 수 있습니다.

정적 웹 사이트 호스팅과는 반대로, 서버 쪽 코드에 의존하는 동적 사이트는 [Azure App Service](/azure/app-service/overview)를 사용하여 호스팅하는 것이 가장 좋습니다.

## <a name="how-does-it-work"></a>어떻게 작동합니까?
저장소 계정에서 정적 웹 사이트를 호스팅하도록 설정하는 경우 기본 파일의 이름을 선택하고 필요에 따라 사용자 지정 404 페이지의 경로를 제공합니다. 기능이 활성화되면 *$web*이라는 컨테이너가 생성됩니다(아직 없는 경우).

*$web* 컨테이너에 포함된 파일은 다음과 같은 특징이 있습니다.

- 익명 액세스 요청을 통해 제공
- 개체 읽기 작업을 통해서만 사용 가능
- 대/소문자 구분
- 다음 패턴을 따르는 공용 웹에서 사용 가능:
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- 다음 패턴을 따르는 Blob Storage 엔드포인트를 통해 사용 가능:
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Blob Storage 엔드포인트를 사용하여 파일을 업로드합니다. 예를 들어 이 위치에 업로드된 파일은 다음과 같은 특징이 있습니다.

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

다음과 같은 위치의 브라우저에서 사용할 수 있습니다.

```bash
https://contoso.z4.web.core.windows.net/image.png
```

파일 이름을 입력하지 않으면 선택된 기본 파일 이름이 루트 및 하위 디렉터리에서 사용됩니다. 서버에서 404를 반환하고 관리자가 오류 문서 경로를 제공하지 않으면 기본 404 페이지가 사용자에게 반환됩니다.

> [!NOTE]
> 파일에 대 한 기본 공용 액세스 수준을 비공개로 설정 되었습니다. 파일은 익명 액세스 요청을 통해 제공 됩니다, 때문에이 설정이 무시 됩니다. 모든 파일에 대 한 공용 액세스 이며 RBAC 권한이 무시 됩니다.

## <a name="cdn-and-ssl-support"></a>CDN 및 SSL 지원

HTTPS를 통해 정적 웹 사이트 파일을 제공하려면 [Azure CDN을 사용하여 HTTPS를 통해 사용자 지정 도메인으로 Blob에 액세스](storage-https-custom-domain-cdn.md)를 참조하세요. 이 프로세스의 일부로, Blob 엔드포인트와는 반대로 *CDN이 웹 엔드포인트를 가리키도록 지정*해야 합니다. CDN 구성이 즉시 실행되지 않으므로 콘텐츠가 표시될 때까지 잠시 기다려야 합니다.

정적 웹 사이트를 업데이트 하는 경우에 CDN 끝점을 제거 하 여 CDN에 지 서버에 캐시 된 콘텐츠를 선택 취소 해야 합니다. 자세한 내용은 [Azure CDN 엔드포인트 제거](../../cdn/cdn-purge-endpoint.md)를 참조하세요.

## <a name="custom-domain-names"></a>사용자 지정 도메인 이름

[Azure Storage 계정에 대한 사용자 지정 도메인 이름을 구성](storage-custom-domain-name.md)하여 사용자 지정 도메인을 통해 정적 웹 사이트를 제공할 수 있습니다. Azure에 도메인을 호스팅하는 방법에 대한 자세한 내용은 [Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)를 참조하세요.

## <a name="pricing"></a>가격
정적 웹 사이트 호스팅은 추가 비용 없이 제공됩니다. Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="quickstart"></a>빠른 시작

### <a name="azure-portal"></a>Azure portal
https://portal.azure.com에서 Azure Portal을 열어서 시작한 후 GPv2 저장소 계정에서 다음 단계를 실행합니다.

1. **설정**을 클릭합니다.
2. **정적 웹 사이트**를 클릭합니다.
3. *인덱스 문서 이름*을 입력합니다. (일반적인 값은 *index.html)*
4. 선택 사항으로 사용자 지정 404 페이지의 *오류 문서 경로*를 입력합니다. (일반적인 값은 *404.html)*

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

다음으로, Azure Portal을 통해 *$web* 컨테이너에 자산을 업로드하거나 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 전체 디렉터리에 업로드합니다. 기능을 활성화할 때 선택한 *인덱스 문서 이름*과 일치하는 파일을 포함합니다.

마지막으로 웹 엔드포인트로 이동하여 웹 사이트를 테스트합니다.

### <a name="azure-cli"></a>Azure CLI
저장소 미리 보기 확장 설치:

```azurecli-interactive
az extension add --name storage-preview
```
구독이 여러 개 있는 경우 사용하려는 GPv2 저장소 계정의 구독으로 CLI를 설정합니다.

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
기능을 활성화합니다. 대괄호를 포함한 모든 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
웹 엔드포인트 URL에 대한 쿼리:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

소스 디렉터리의 *$web* 컨테이너에 개체를 업로드합니다. 명령에서 *$web* 컨테이너에 대한 참조를 올바르게 이스케이프해야 합니다. 예를 들어, Azure Portal의 CloudShell에서 Azure CLI를 사용하는 경우 다음에 표시된 대로 *$web* 컨테이너를 이스케이프합니다.

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>배포

저장소 컨테이너에 콘텐츠를 배포하는 데 사용할 수 있는 방법은 다음과 같습니다.

- [AZCopy](../common/storage-use-azcopy.md)
- [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
- [Visual Studio Code 확장](https://code.visualstudio.com/tutorials/static-website/getting-started)

항상 파일을 *$web* 컨테이너로 복사해야 합니다.

## <a name="metrics"></a>메트릭

정적 웹 사이트 페이지에 메트릭을 사용하려면 **설정** > **모니터링** > **메트릭**을 클릭합니다.

여러 메트릭 API에 연결하여 메트릭 데이터가 생성됩니다. 데이터를 반환하는 멤버에만 집중하기 위해 특정 시간 프레임에 사용된 API 멤버만 포털에 표시됩니다. 필요한 API 멤버를 선택할 수 있도록 가장 먼저 할 일은 시간 프레임을 확장하는 것입니다.

시간 프레임 단추를 클릭하고 **지난 24시간**을 선택한 다음, **적용**을 클릭합니다.

![Azure Storage 정적 웹 사이트 메트릭 시간 범위](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

다음으로, *네임스페이스* 드롭다운에서 **Blob**을 선택합니다.

![Azure Storage 정적 웹 사이트 메트릭 네임스페이스](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

그런 다음, **송신** 메트릭을 선택합니다.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

*집계* 선택기에서 **합계**를 선택합니다.

![Azure Storage 정적 웹 사이트 메트릭 집계](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

다음으로, **필터 추가** 단추를 클릭하고 *속성* 선택기에서 **API 이름**을 선택합니다.

![Azure Storage 정적 웹 사이트 메트릭 API 이름](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

마지막으로, *값* 선택기에서 **GetWebContent** 옆에 있는 상자를 선택하여 메트릭 보고서를 채웁니다.

![Azure Storage 정적 웹 사이트 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

사용하도록 설정되면 *$web* 컨테이너의 파일에 대한 트래픽 통계가 메트릭 대시보드에 보고됩니다.

## <a name="faq"></a>FAQ

**정적 웹 사이트 기능은 모든 유형의 저장소 계정에 사용할 수 있나요?**  
아니요, 정적 웹 사이트 호스팅은 GPv2 표준 저장소 계정에서만 사용할 수 있습니다.

**Storage VNET 및 방화벽 규칙은 새 웹 엔드포인트에서 지원되나요?**  
예, 새 웹 엔드포인트는 저장소 계정에 대해 구성된 VNET 및 방화벽 규칙을 따릅니다.

**웹 엔드포인트는 대/소문자를 구분하나요?**  
예, 웹 엔드포인트는 Blob 엔드포인트처럼 대/소문자를 구분합니다. 

## <a name="next-steps"></a>다음 단계
* [Azure CDN을 사용하여 HTTPS를 통한 사용자 지정 도메인으로 Blob 액세스](storage-https-custom-domain-cdn.md)
* [Blob 또는 웹 엔드포인트에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [첫 번째 서버 없는 웹앱 빌드](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [자습서: Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)
