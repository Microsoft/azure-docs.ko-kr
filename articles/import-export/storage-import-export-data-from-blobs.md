---
title: Azure Import/Export를 사용하여 Azure Blob에서 데이터 내보내기 | Microsoft Docs
description: Azure Portal에서 내보내기 작업을 만들어 Azure Blob에서 데이터를 전송하는 방법을 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: e878be5351362923e163c0a6f617b96ab72a36d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177550"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Azure Import/Export 서비스를 사용하여 Azure Blob Storage에서 데이터 내보내기

이 문서에서는 Azure Import/Export 서비스를 사용하여 Azure Blob Storage에서 많은 양의 데이터를 안전하게 내보내는 방법에 대한 단계별 지침을 제공합니다. 서비스를 사용하려면 빈 드라이브를 Azure 데이터 센터에 배송해야 합니다. 서비스에서 스토리지 계정의 데이터를 드라이브로 내보낸 다음, 드라이브를 다시 배송합니다.

## <a name="prerequisites"></a>필수 구성 요소

내보내기 작업을 만들어 Azure Blob Storage에서 데이터를 전송하기 전에 이 서비스에 대한 다음 필수 조건 목록을 신중하게 검토하고 완료해야 합니다.
다음이 필요합니다.

- Import/Export 서비스에 사용할 수 있는 활성 Azure 구독이 있어야 합니다.
- Azure Storage 계정이 하나 이상 있어야 합니다. [Import/Export 서비스에 지원되는 스토리지 계정 및 스토리지 유형](storage-import-export-requirements.md) 목록을 참조하세요. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](../storage/common/storage-account-create.md)(영문)을 참조하세요.
- [지원되는 형식](storage-import-export-requirements.md#supported-disks)에 속한 적절한 개수의 디스크가 있어야 합니다.
- FedEx/DHL 계정이 있습니다. FedEx/DHL 이외의 캐리어를 사용 하려는 경우에는 Azure Data Box 운영 팀에 문의 하세요 `adbops@microsoft.com` .
  - 계정은 유효해야 하고, 잔액이 있어야 하며, 반품 기능이 있어야 합니다.
  - 내보내기 작업의 추적 번호를 생성합니다.
  - 모든 작업에는 별도의 추적 번호가 있어야 합니다. 추적 번호가 동일한 여러 작업은 지원되지 않습니다.
  - 운송업체 계정이 없는 경우, 다음으로 이동합니다.
    - [FedEx 계정을 만들거나](https://www.fedex.com/en-us/create-account.html)
    - [DHL 계정 만들기](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1단계: 내보내기 작업 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

다음 단계를 수행하여 Azure Portal에서 내보내기 작업을 만듭니다.

1. <https://portal.azure.com/>에 로그온합니다.
2. **가져오기/내보내기 작업** 을 검색 합니다.

    ![가져오기/내보내기 작업 검색](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **+새로 만들기** 를 선택합니다.

    ![+ 새로 만들기를 선택 하 여 새를 만듭니다. ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **기본 사항** 에서

   1. 구독을 선택합니다.
   1. 리소스 그룹을 선택 하거나 **새로 만들기** 를 선택 하 고 새 리소스 그룹을 만듭니다.
   1. 가져오기 작업을 설명하는 이름을 입력합니다. 이름을 사용하여 작업 진행 상황을 추적합니다.
       * 이름에는 소문자, 숫자 및 하이픈만 포함할 수 있습니다.
       * 이름은 문자로 시작해야 하며, 공백은 포함할 수 없습니다.

   1. **Azure에서 내보내기** 를 선택합니다.

    ![내보내기 주문의 기본 옵션](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    계속 하려면 **>다음: 작업 세부 정보** 를 선택 합니다.

5. **작업 세부 정보** 에서:

   1. 현재 데이터가 있는 Azure 지역을 선택 합니다.
   1. 데이터를 내보낼 저장소 계정을 선택 합니다. 사용자의 위치에 가까운 저장소 계정을 사용 합니다.

      자동 전송 위치는 선택한 스토리지 계정의 지역에 따라 자동으로 채워집니다.

   1. 저장소 계정에서 빈 드라이브 또는 드라이브로 내보낼 blob 데이터를 지정 합니다. 다음 세 가지 방법 중 하나를 선택 합니다.

      - 스토리지 계정의 Blob 데이터를 모두 내보내도록 선택합니다(**모두 내보내기**).

        ![모두 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - **선택한 컨테이너 및 blob** 을 선택 하 고 내보낼 컨테이너와 blob을 지정 합니다. 여러 선택 방법 중 하나를 사용할 수 있습니다. **추가** 옵션을 선택 하면 선택 문자열을 추가할 수 있는 오른쪽에 패널이 열립니다.

        |옵션|설명|
        |------|-----------|      
        |**컨테이너 추가**|컨테이너의 모든 blob을 내보냅니다.<br>**컨테이너 추가** 를 선택 하 고 각 컨테이너 이름을 입력 합니다.|
        |**Blob 추가**|내보낼 개별 blob을 지정 합니다.<br>**Blob 추가** 를 선택 합니다. 그런 다음 컨테이너 이름으로 시작 하 여 blob에 대 한 상대 경로를 지정 합니다. 루트 컨테이너를 지정하려면 *$root* 를 사용합니다.<br>처리 중에 오류가 발생하지 않도록 방지하려면 다음 스크린샷과 같이 유효한 형식의 Blob 경로를 제공해야 합니다. 자세한 내용은 [유효한 Blob 경로의 예](#examples-of-valid-blob-paths)를 참조하세요.|
        |**접두사 추가**|접두사를 사용 하 여 컨테이너의 유사 하 게 명명 된 컨테이너 또는 유사 하 게 명명 된 blob 집합을 선택 합니다. 접두사는 컨테이너 이름의 접두사, 전체 컨테이너 이름 또는 전체 컨테이너 이름 뒤에 blob 이름의 접두사가 올 수 있습니다. |

        ![선택한 컨테이너 및 Blob 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - **Blob 목록 파일 (XML 형식)에서 내보내기** 를 선택 하 고 저장소 계정에서 내보낼 blob의 경로 및 접두사 목록이 포함 된 XML 파일을 선택 합니다. XML 파일을 생성 하 고 저장소 계정에 대 한 컨테이너에 저장 해야 합니다. 파일은 비워 둘 수 없습니다.

      > [!IMPORTANT]
      > XML 파일을 사용 하 여 내보낼 blob을 선택 하는 경우 XML에 유효한 경로 및/또는 접두사가 포함 되어 있는지 확인 합니다. 파일이 잘못 되었거나 지정 된 경로와 일치 하는 데이터가 없는 경우에는 주문이 부분 데이터로 종료 되거나 데이터를 내보내지 않습니다.

       컨테이너에 XML 파일을 추가 하는 방법에 대 한 자세한 내용은 [xml 파일을 사용 하 여 순서 내보내기](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)를 참조 하세요.

      ![Blob 목록 파일에서 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > 데이터를 복사 하는 동안 내보낼 blob을 사용 하는 경우 Azure Import/Export 서비스는 blob의 스냅숏을 만들고 스냅숏을 복사 합니다.

   계속 하려면 **다음: 배송 >** 을 선택 합니다.

6. **배송** 중:

    - 드롭다운 목록에서 운송업체를 선택합니다. FedEx/DHL 이외의 캐리어를 사용 하려는 경우 드롭다운에서 기존 옵션을 선택 합니다. 에서 `adbops@microsoft.com`  사용 하려는 운송 업체와 관련 된 정보를 사용 하 여 Azure Data Box 운영 팀에 문의 하세요.
    - 운송업체에서 만든 유효한 운송업체 계정 번호를 입력합니다. 내보내기 작업이 완료 되 면 Microsoft는이 계정을 사용 하 여 드라이브를 사용자에 게 다시 제공 합니다.
    - 완전 하 고 유효한 연락처 이름, 전화 번호, 전자 메일, 주소, 구/군/시, 우편 번호, 시/도 및 국가/지역을 제공 합니다.

        > [!TIP]
        > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

    **검토 + 만들기** 를 선택하여 계속 진행합니다.

7. **검토 + 만들기** 에서 다음을 수행 합니다.

   1. 작업에 대한 세부 정보를 검토합니다.
   1. 작업 이름과 Azure로의 배송 디스크에 제공되는 Azure 데이터 센터 배송 주소를 기록합니다.

      > [!NOTE]
      > 항상 Azure Portal에 기록된 데이터 센터로 디스크를 보냅니다. 디스크가 잘못된 데이터 센터로 배송되면 작업이 처리되지 않습니다.

   1. 개인 정보 및 원본 데이터 삭제의 주문에 대 한 **용어** 를 검토 합니다. 조건에 동의 하면 약관의 확인란을 선택 합니다. 주문에 대 한 유효성 검사가 시작 됩니다.

   ![내보내기 순서 검토 및 만들기](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 단계를 사용 하 여 Azure Portal에서 내보내기 작업을 만들 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>작업 만들기

1. Az [extension add](/cli/azure/extension#az_extension_add) 명령을 사용 하 여 [az import export](/cli/azure/ext/import-export/import-export) 확장을 추가 합니다.

    ```azurecli
    az extension add --name import-export
    ```

1. 디스크를 받을 수 있는 위치의 목록을 가져오려면 [az import-export location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) 명령을 사용 합니다.

    ```azurecli
    az import-export location list
    ```

1. 다음 [az import-export create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) 명령을 실행 하 여 기존 저장소 계정을 사용 하는 내보내기 작업을 만듭니다.

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

   이 작업은 저장소 계정의 모든 blob을 내보냅니다. **--Export** 에 대해이 값을 바꿔서 내보내기에 대 한 blob을 지정할 수 있습니다.

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   이 매개 변수 값은 루트 컨테이너의 *logo.bmp* 이라는 blob를 내보냅니다.

   또한 접두사를 사용 하 여 컨테이너의 모든 blob을 선택 하는 옵션도 있습니다. **--Export** 에 대해이 값을 바꿉니다.

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   자세한 내용은 [유효한 Blob 경로의 예](#examples-of-valid-blob-paths)를 참조하세요.

   > [!NOTE]
   > 내보낼 Blob가 데이터 복사 중에 사용되는 경우 Azure Import/Export 서비스는 Blob의 스냅샷을 가져와 해당 스냅샷을 복사합니다.

1. [Az import-export list](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) 명령을 사용 하 여 리소스 그룹 myierg에 대 한 모든 작업을 볼 수 있습니다.

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. 작업을 업데이트 하거나 작업을 취소 하려면 [az import-export update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) 명령을 실행 합니다.

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

다음 단계를 사용 하 여 Azure PowerShell에서 내보내기 작업을 만듭니다.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az. ImportExport** PowerShell 모듈은 미리 보기 상태 이지만 cmdlet을 사용 하 여 별도로 설치 해야 합니다 `Install-Module` . 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>작업 만들기

1. 디스크를 받을 수 있는 위치의 목록을 가져오려면 [AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) cmdlet을 사용 합니다.

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. 다음 [AzImportExport](/powershell/module/az.importexport/new-azimportexport) 예제를 실행 하 여 기존 저장소 계정을 사용 하는 내보내기 작업을 만듭니다.

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

   이 작업은 저장소 계정의 모든 blob을 내보냅니다. **-Exportbloblistblobpath** 에 대해이 값을 바꿔서 내보낼 blob을 지정할 수 있습니다.

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   이 매개 변수 값은 루트 컨테이너의 *logo.bmp* 이라는 blob를 내보냅니다.

   또한 접두사를 사용 하 여 컨테이너의 모든 blob을 선택 하는 옵션도 있습니다. **-Exportbloblistblobpath** 에 대해이 값을 바꿉니다.

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   자세한 내용은 [유효한 Blob 경로의 예](#examples-of-valid-blob-paths)를 참조하세요.

   > [!NOTE]
   > 내보낼 Blob가 데이터 복사 중에 사용되는 경우 Azure Import/Export 서비스는 Blob의 스냅샷을 가져와 해당 스냅샷을 복사합니다.

1. [AzImportExport](/powershell/module/az.importexport/get-azimportexport) cmdlet을 사용 하 여 리소스 그룹 myierg에 대 한 모든 작업을 볼 수 있습니다.

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. 작업을 업데이트 하거나 작업을 취소 하려면 [AzImportExport](/powershell/module/az.importexport/update-azimportexport) cmdlet을 실행 합니다.

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>2단계: 드라이브 배송

필요한 드라이브 수를 알지 못하는 경우 [드라이브 수를 확인](#check-the-number-of-drives)합니다. 드라이브 수를 알고 있으면 드라이브 배송을 계속 진행합니다.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3단계: 추적 정보를 사용하여 작업 업데이트

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>4단계: 디스크 받기

대시보드에서 작업이 완료되었다고 보고하면 디스크가 배송되고 포털에서 배송 추적 번호를 사용할 수 있습니다.

1. 내보낸 데이터가 있는 드라이브를 받은 후에는 BitLocker 키를 가져와서 드라이브의 잠금을 해제해야 합니다. Azure Portal에서 내보내기 작업으로 이동합니다. **가져오기/내보내기** 탭을 클릭합니다.
2. 목록에서 내보내기 작업을 선택하고 클릭합니다. **암호화** 로 이동 하 여 키를 복사 합니다.

   ![내보내기 작업의 BitLocker 키 보기](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. BitLocker 키를 사용하여 디스크의 잠금을 해제합니다.

내보내기가 완료되었습니다.

## <a name="step-5-unlock-the-disks"></a>5 단계: 디스크 잠금 해제

다음 명령을 사용 하 여 드라이브 잠금을 해제 합니다.

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

다음은 샘플 입력의 예입니다.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

지금은 작업을 삭제 하거나 그대로 둘 수 있습니다. 작업은 90 일 후 자동으로 삭제 됩니다.

## <a name="check-the-number-of-drives"></a>드라이브 수 확인

이 *선택적* 단계는 내보내기 작업에 필요한 드라이브 수를 결정 하는 데 도움이 됩니다. [지원되는 OS 버전](storage-import-export-requirements.md#supported-operating-systems)을 실행하는 Windows 시스템에서 이 단계를 수행합니다.

1. Windows 시스템에서 [WAImportExport 버전 1을 다운로드](https://www.microsoft.com/download/details.aspx?id=42659)합니다.
2. `waimportexportv1` 기본 폴더에 압축을 풉니다. 예: `C:\WaImportExportV1`
3. 관리자 권한으로 PowerShell 또는 명령줄 창을 엽니다. 압축을 푼 폴더로 디렉터리를 변경하려면 다음 명령을 실행합니다.

   `cd C:\WaImportExportV1`

4. 선택한 Blob에 필요한 디스크 수를 확인하려면 다음 명령을 실행합니다.

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    다음 표에는 매개 변수가 나와 있습니다.

    |명령줄 매개 변수|설명|
    |--------------------------|-----------------|
    |**/logdir**|선택 사항입니다. 로그 디렉터리입니다. 이 디렉터리에 자세한 로그 파일이 기록됩니다. 지정하지 않으면 현재 디렉터리가 로그 디렉터리로 사용됩니다.|
    |**/sn**|필수 요소. 내보내기 작업에 대한 스토리지 계정의 이름입니다.|
    |**/sk**|컨테이너 SAS가 지정되지 않은 경우에만 필요합니다. 내보내기 작업에 대한 스토리지 계정의 계정 키입니다.|
    |**/csas:**|스토리지 계정 키가 지정되지 않은 경우에만 필요합니다. 내보내기 작업에서 내보낼 Blob을 나열하기 위한 컨테이너 SAS입니다.|
    |**/ExportBlobListFile:**|필수 요소. 내보낼 Blob에 대한 Blob 경로 또는 Blob 경로 접두사 목록을 포함하고 있는 XML 파일의 경로입니다. Import/Export 서비스 REST API의 [작업 배치](/rest/api/storageimportexport/jobs) 작업에서 `BlobListBlobPath` 요소에 사용되는 파일 형식입니다.|
    |**/DriveSize:**|필수 요소. 내보내기 작업에 사용할 드라이브의 크기 ( *예:* 500, 1.5 TB)입니다.|

    [PreviewExport 명령 예제](#example-of-previewexport-command)를 참조하세요.

5. 내보내기 작업을 위해 배송될 드라이브에 읽거나 쓸 수 있는지 확인합니다.

### <a name="example-of-previewexport-command"></a>PreviewExport 명령 예제

다음 예제에서는 `PreviewExport` 명령을 보여 줍니다.

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

내보내기 Blob 목록 파일에는 다음과 같이 Blob 이름과 Blob 접두사가 포함될 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

Azure Import/Export 도구는 내보낼 모든 Blob을 나열하고 필요한 오버헤드를 고려하여 지정된 크기의 드라이브에 패키지하는 방법을 계산한 다음 Blob 및 드라이브 사용 정보를 보유하는 데 필요한 드라이브 수를 추정합니다.

정보 로그가 생략된 출력의 예제는 다음과 같습니다.

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>유효한 Blob 경로의 예

다음 표에는 유효한 Blob 경로의 예가 있습니다.

   | 선택기 | Blob 경로 | 설명 |
   | --- | --- | --- |
   | 시작 단어 |/ |스토리지 계정의 모든 Blob을 내보냄 |
   | 시작 단어 |/$root/ |루트 컨테이너의 모든 Blob을 내보냄 |
   | 시작 단어 |/book |접두사 **book** |
   | 시작 단어 |/music/ |컨테이너 **music** |
   | 시작 단어 |/music/love |접두사 **love** 로 시작하는 컨테이너 **music** 의 모든 Blob을 내보냄 |
   | 같음 |$root/logo.bmp |루트 컨테이너의 Blob **logo.bmp** 를 내보냄 |
   | 같음 |videos/story.mp4 |컨테이너 **videos** 의 Blob **story.mp4** 를 내보냄 |

## <a name="next-steps"></a>다음 단계

- [작업 및 드라이브 상태 보기](storage-import-export-view-drive-status.md)
- [Import/Export 요구 사항 검토](storage-import-export-requirements.md)
