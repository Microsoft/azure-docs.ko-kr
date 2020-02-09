---
title: Azure Storage에서 정적 웹 사이트를 호스팅합니다.
description: Azure Storage GPv2 계정의 컨테이너에서 직접 정적 콘텐츠 (HTML, CSS, JavaScript 및 이미지 파일)를 제공 하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 4214c4eb9fbe1d3e39d1ee16289f30b893b94653
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906609"
---
# <a name="host-a-static-website-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트를 호스팅합니다.

Azure Storage GPv2 계정의 컨테이너에서 직접 정적 콘텐츠 (HTML, CSS, JavaScript 및 이미지 파일)를 제공할 수 있습니다. 자세히 알아보려면 [Azure Storage의 정적 웹 사이트 호스팅](storage-blob-static-website.md)을 참조 하세요.

이 문서에서는 Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 정적 웹 사이트 호스팅을 사용 하도록 설정 하는 방법을 보여 줍니다.

<a id="portal" />

## <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

단계별 자습서는 [자습서: Blob Storage에서 정적 웹 사이트 호스팅](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)을 참조 하세요.

정적 웹 사이트 호스팅을 사용 하도록 설정한 후에는 웹 사이트의 공용 URL을 사용 하 여 브라우저에서 사이트의 페이지를 볼 수 있습니다.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 웹 사이트 URL을 찾습니다.

저장소 계정의 계정 개요 페이지 옆에 표시 되는 창에서 **정적 웹 사이트**를 선택 합니다. 사이트의 URL이 **기본 엔드포인트** 필드에 표시 됩니다.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[CLI (Azure 명령줄 인터페이스)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 사용 하 여 정적 웹 사이트 호스팅을 사용 하도록 설정할 수 있습니다.

1. 먼저 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)을 열거나 Azure CLI를 로컬로 [설치한](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 경우 Windows PowerShell과 같은 명령 콘솔 응용 프로그램을 엽니다.

2. Id가 둘 이상의 구독과 연결 된 경우 정적 웹 사이트를 호스트 하는 저장소 계정의 구독으로 활성 구독을 설정 합니다.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. 정적 웹 사이트 호스팅을 사용 하도록 설정 합니다.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   * 브라우저에서 존재 하지 않는 페이지를 요청 하는 경우 사용자에 게 표시 되는 오류 문서 이름으로 `<error-document-name>` 자리 표시자를 바꿉니다.

   * `<index-document-name>` 자리 표시자를 인덱스 문서 이름으로 바꿉니다. 이 문서는 일반적으로 "index .html"입니다.

4. 소스 디렉터리의 *$web* 컨테이너에 개체를 업로드합니다.

   > [!NOTE]
   > Azure Cloud Shell 사용 하는 경우 `$web` 컨테이너 (예: `\$web`)를 참조할 때 `\` 이스케이프 문자를 추가 해야 합니다. Azure CLI의 로컬 설치를 사용 하는 경우 이스케이프 문자를 사용할 필요가 없습니다.

   이 예에서는 Azure Cloud Shell 세션에서 명령을 실행 중인 것으로 가정 합니다.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   * `<source-path>` 자리 표시자를 업로드할 파일의 위치에 대 한 경로로 바꿉니다.

   > [!NOTE]
   > Azure CLI의 위치 설치를 사용 하는 경우 로컬 컴퓨터의 모든 위치에 대 한 경로를 사용할 수 있습니다 (예: `C:\myFolder`.
   >
   > Azure Cloud Shell를 사용 하는 경우 Cloud Shell에 표시 되는 파일 공유를 참조 해야 합니다. 이 위치는 클라우드 공유 자체의 파일 공유 또는 Cloud Shell에서 탑재 한 기존 파일 공유 일 수 있습니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [Azure Cloud Shell 파일 유지](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)를 참조 하세요.

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 웹 사이트 URL을 찾습니다.

웹 사이트의 공용 URL을 사용 하 여 브라우저에서 콘텐츠를 볼 수 있습니다.

다음 명령을 사용 하 여 URL을 찾습니다.

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

* `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

<a id="powershell" />

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell 모듈을 사용 하 여 정적 웹 사이트 호스팅을 사용 하도록 설정할 수 있습니다.

1. Windows PowerShell 명령 창을 엽니다.

2. 모듈 Az version 0.7 이상 Azure PowerShell 있는지 확인 합니다.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

3. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

4. Id가 둘 이상의 구독과 연결 된 경우 정적 웹 사이트를 호스트 하는 저장소 계정의 구독으로 활성 구독을 설정 합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

5. 사용 하려는 저장소 계정을 정의 하는 저장소 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

6. 정적 웹 사이트 호스팅을 사용 하도록 설정 합니다.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 브라우저에서 존재 하지 않는 페이지를 요청 하는 경우 사용자에 게 표시 되는 오류 문서 이름으로 `<error-document-name>` 자리 표시자를 바꿉니다.

   * `<index-document-name>` 자리 표시자를 인덱스 문서 이름으로 바꿉니다. 이 문서는 일반적으로 "index .html"입니다.

7. 소스 디렉터리의 *$web* 컨테이너에 개체를 업로드합니다.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * `<path-to-file>` 자리 표시자 값을 업로드 하려는 파일의 정규화 된 경로로 바꿉니다 (예: `C:\temp\index.html`).

   * `<blob-name>` 자리 표시자 값을 결과 blob에 지정할 이름으로 바꿉니다 (예: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>PowerShell을 사용 하 여 웹 사이트 URL 찾기

웹 사이트의 공용 URL을 사용 하 여 브라우저에서 콘텐츠를 볼 수 있습니다.

다음 명령을 사용 하 여 URL을 찾습니다.

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

* `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

<a id="metrics" />

---

## <a name="enable-metrics-on-static-website-pages"></a>정적 웹 사이트 페이지에서 메트릭 사용

메트릭을 사용 하도록 설정 하면 **$web** 컨테이너에 있는 파일에 대 한 트래픽 통계가 메트릭 대시보드에 보고 됩니다.

1. **설정** > **모니터링** > **메트릭**을 클릭 합니다.

   여러 메트릭 API에 연결하여 메트릭 데이터가 생성됩니다. 데이터를 반환하는 멤버에만 집중하기 위해 특정 시간 프레임에 사용된 API 멤버만 포털에 표시됩니다. 필요한 API 멤버를 선택할 수 있도록 하기 위해 첫 번째 단계는 시간 프레임을 확장 하는 것입니다.

2. 시간 프레임 단추를 클릭 하 고 **최근 24 시간** 을 선택한 다음 **적용**을 클릭 합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 시간 범위](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. *네임 스페이스* 드롭다운에서 **Blob** 을 선택 합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 네임스페이스](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 그런 다음, **송신** 메트릭을 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. *집계* 선택기에서 **합계**를 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 집계](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. **필터 추가** 단추를 클릭 하 고 *속성* 선택기에서 **API 이름** 을 선택 합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 API 이름](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. *값* 선택기에서 **getwebcontent** 옆의 확인란을 선택 하 여 메트릭 보고서를 채웁니다.

   ![Azure Storage 정적 웹 사이트 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>다음 단계

* 정적 웹 사이트를 사용 하 여 사용자 지정 도메인을 구성 하는 방법을 알아봅니다. [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑을](storage-custom-domain-name.md)참조 하세요.

