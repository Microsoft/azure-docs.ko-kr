---
title: '자습서: CLI를 사용하여 백업된 SAP HANA DB 관리'
description: 이 자습서에서는 Azure CLI를 사용하여 Azure VM에서 실행되는 백업된 SAP HANA 데이터베이스를 관리하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13cd6e33fbec261b52aae73b1b01fe26764885fe
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757356"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure VM의 SAP HANA 데이터베이스 관리

Azure CLI는 명령줄 또는 스크립트를 통해 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 설명서에서는 Azure VM에서 Azure CLI를 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법을 자세히 설명합니다. [Azure Portal](./sap-hana-db-manage.md)에서 이 단계를 수행해도 됩니다.

[Azure Cloud Shell](tutorial-sap-hana-backup-cli.md)을 사용하여 CLI 명령을 실행합니다.

이 자습서를 마치면 다음을 수행할 수 있습니다.

> [!div class="checklist"]
>
> * 백업 및 복원 작업 모니터링
> * SAP HANA 인스턴스에 추가된 새 데이터베이스 보호
> * 정책 변경
> * 보호 중지
> * 보호 다시 시작

[CLI를 사용하여 Azure에서 SAP HANA 데이터베이스 백업](tutorial-sap-hana-backup-cli.md)을 사용하여 SAP HANA 데이터베이스를 백업한 분들은 다음 리소스를 사용 중입니다.

* *saphanaResourceGroup*이라는 이름의 리소스 그룹
* *saphanaVault*라는 이름의 자격 증명 모음
* *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*이라는 이름의 보호된 컨테이너
* *saphanadatabase;hxe;hxe*이라는 이름의 백업된 데이터베이스/항목
* *westus2* Azure 지역의 리소스

Azure CLI를 사용하면 Azure Backup을 통해 백업되어 Azure VM에서 실행되고 있는 SAP HANA 데이터베이스를 간편하게 관리할 수 있습니다. 이 자습서에서는 각 관리 작업을 자세히 설명합니다.

## <a name="monitor-backup-and-restore-jobs"></a>백업 및 복원 작업 모니터링

완료된 작업 또는 현재 실행 중인 작업(백업 또는 복원)을 모니터링하려면 [az backup job list](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet을 사용합니다. 또한 CLI를 사용하면 [현재 실행 중인 작업을 일시 중단](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop)하거나 [작업이 완료될 때까지 대기](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)할 수 있습니다.

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

출력은 다음과 같이 표시됩니다.

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>정책 변경

SAP HANA 백업 구성을 기반으로 하는 정책을 변경하려면 [az backup policy set](/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) cmdlet을 사용합니다. 이 cmdlet의 name 매개 변수는 우리가 정책을 변경하려는 백업 항목을 참조합니다. 이 자습서에서는 SAP HANA 데이터베이스 *saphanadatabase;hxe;hxe*의 정책을 새 정책 *newsaphanaPolicy*로 바꿀 것입니다. 새 정책은 [az backup policy create](/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet을 사용하여 만들 수 있습니다.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

출력은 다음과 같습니다.

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>SAP HANA 인스턴스에 추가된 새 데이터베이스 보호

[SAP HANA 인스턴스를 Recovery Services 자격 증명 모음에 등록](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance)하면 이 인스턴스의 모든 데이터베이스가 자동으로 검색됩니다.

그러나 나중에 SAP HANA 인스턴스에 새 데이터베이스를 추가할 때는 [az backup protectable-item initialize](/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) cmdlet을 사용합니다. 이 cmdlet은 추가된 새 데이터베이스를 검색합니다.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

그 후 [az backup protectable-item list](/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) cmdlet을 사용하여 SAP HANA 인스턴스에서 검색된 모든 데이터베이스를 나열합니다. 하지만 백업이 이미 구성된 데이터베이스는 이 목록에서 제외됩니다. 백업할 데이터베이스가 검색된 후에는 [SAP HANA 데이터베이스에 백업 사용](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)을 참조하세요.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

백업하려는 새 데이터베이스는 다음과 비슷한 이 목록에 표시됩니다.

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA 데이터베이스 보호 중지

다음과 같은 몇 가지 방법으로 SAP HANA 데이터베이스 보호를 중지할 수 있습니다.

* 미래의 모든 백업 작업을 중지하고 모든 복구 지점을 삭제
* 미래의 모든 백업 작업을 중지하고 복구 지점을 원래 상태로 유지

복구 지점을 그대로 두기로 선택하는 경우 다음 세부 정보를 염두에 두어야 합니다.

* 모든 복구 지점은 영구적으로 유지되고, 모든 정리는 데이터 보관을 통해 보호 중지에서 중지됩니다.
* 보호된 인스턴스와 사용한 스토리지 요금이 청구됩니다.
* 백업을 중지하지 않고 데이터 원본을 삭제하면 새 백업이 실패합니다.

보호를 중지하는 각 방법을 좀 더 자세히 살펴보겠습니다.

### <a name="stop-protection-with-retain-data"></a>데이터 보관을 통해 보호 중지

데이터 보관을 통해 보호를 중지하려면 [az backup protection disable](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet을 사용합니다.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

출력은 다음과 같습니다.

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

이 작업의 상태를 확인하려면 [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

### <a name="stop-protection-without-retain-data"></a>데이터 보관 없이 보호 중지

데이터 보관 없이 보호를 중지하려면 [az backup protection disable](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet을 사용합니다.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

출력은 다음과 같습니다.

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

이 작업의 상태를 확인하려면 [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="resume-protection"></a>보호 다시 시작

데이터 보관을 통해 SAP HANA 데이터베이스에 대한 보호를 중지하는 경우 나중에 보호를 다시 시작할 수 있습니다. 백업된 데이터를 보관하지 않으면 보호를 다시 시작할 수 없습니다.

보호를 다시 시작하려면 [az backup protection resume](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) cmdlet을 사용합니다.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

출력은 다음과 같습니다.

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

이 작업의 상태를 확인하려면 [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용합니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal을 사용하여 Azure VM에서 실행 중인 SAP HANA 데이터베이스를 백업하는 방법은 [Azure VM에서 SAP HANA 데이터베이스 백업](./backup-azure-sap-hana-database.md)을 참조하세요.

* Azure Portal을 사용하여 Azure VM에서 실행 중인 백업된 SAP HANA 데이터베이스를 관리하는 방법은 [Azure VM에서 백업된 SAP HANA 데이터베이스 관리](./sap-hana-db-manage.md)를 참조하세요.
