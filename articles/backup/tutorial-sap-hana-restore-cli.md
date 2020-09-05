---
title: 자습서 - CLI를 사용하여 Azure에서 SAP HANA DB 복원
description: 이 자습서에서는 Azure CLI를 사용하여 Azure VM에서 실행되는 Azure Backup Recovery Services 자격 증명 모음의 SAP HANA 데이터베이스를 백업하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a1dbf0593c7c9b65c4e285b7162411de6c01bbbf
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762286"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure VM의 SAP HANA 데이터베이스 복원

Azure CLI는 명령줄 또는 스크립트를 통해 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 설명서에서는 Azure CLI를 사용하여 백업된 SAP HANA 데이터베이스를 Azure VM에 복원하는 방법을 자세히 설명합니다. [Azure Portal](./sap-hana-db-restore.md)에서 이 단계를 수행해도 됩니다.

[Azure Cloud Shell](tutorial-sap-hana-backup-cli.md)을 사용하여 CLI 명령을 실행합니다.

이 자습서를 마치면 다음을 수행할 수 있습니다.

> [!div class="checklist"]
>
> * 백업된 데이터베이스의 복원 지점 보기
> * 데이터베이스 복원

이 자습서에서는 Azure Backup을 사용하여 백업된 SAP HANA 데이터베이스가 Azure VM에서 실행되고 있다고 가정합니다. [CLI를 사용하여 Azure에서 SAP HANA 데이터베이스 백업](tutorial-sap-hana-backup-cli.md)을 사용하여 SAP HANA 데이터베이스를 백업한 분들은 다음 리소스를 사용 중입니다.

* *saphanaResourceGroup*이라는 이름의 리소스 그룹
* *saphanaVault*라는 이름의 자격 증명 모음
* *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*이라는 이름의 보호된 컨테이너
* *saphanadatabase;hxe;hxe*이라는 이름의 백업된 데이터베이스/항목
* *westus2* Azure 지역의 리소스

## <a name="view-restore-points-for-a-backed-up-database"></a>백업된 데이터베이스의 복원 지점 보기

데이터베이스의 모든 복구 지점 목록을 보려면 다음과 같이 [az backup recoverypoint list](/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet을 사용합니다.

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

복구 지점 목록은 다음과 같이 표시됩니다.

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

보시는 것처럼, 위의 목록에는 각각 전체, 차등 및 로그 백업용으로 하나씩 총 3개의 복구 지점이 포함되어 있습니다.

>[!NOTE]
>[az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet을 사용하여 끊어지지 않은 모든 로그 백업 체인의 시작점과 끝점을 볼 수도 있습니다.

## <a name="prerequisites-to-restore-a-database"></a>데이터베이스 복원에 필요한 필수 구성 요소

데이터베이스 복원을 시작하기 전에 다음과 같은 전제 조건을 충족하는지 확인하세요.

* 동일한 Azure 지역에 있는 SAP HANA 인스턴스로만 데이터베이스를 복원할 수 있습니다.
* 대상 인스턴스를 원본과 동일한 자격 증명 모음에 등록해야 합니다.
* Azure Backup은 동일한 VM에 있는 두 개의 서로 다른 SAP HANA 인스턴스를 구분할 수 없습니다. 따라서 한 인스턴스의 데이터를 동일한 VM의 다른 인스턴스에 복원하는 것은 불가능합니다.

## <a name="restore-a-database"></a>데이터베이스 복원

Azure Backup에서는 다음과 같이 Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원할 수 있습니다.

* 로그 백업을 사용하여 특정 날짜 또는 시간(초)으로 복원합니다. Azure Backup은 선택한 시간을 기준으로 복원해야 하는 적절한 전체 차등 백업 및 로그 백업 체인을 자동으로 결정합니다.
* 특정 복구 지점으로 복원하려면 특정 전체 또는 차등 백업을 복원합니다.

데이터베이스를 복원하려면 [az restore restore-azurewl](/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet을 사용합니다. 그러려면 입력 중 하나로 복구 구성 개체가 필요합니다. 이 개체는 [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet을 사용하여 생성할 수 있습니다. 복구 구성 개체는 복원을 수행하는 모든 세부 정보를 포함하고 있습니다. 그 중 하나는 복원 모드인 **OriginalWorkloadRestore** 또는 **AlternateWorkloadRestore**입니다.

>[!NOTE]
> **OriginalWorkloadRestore** - 원래 원본과 동일한 SAP HANA 인스턴스에 데이터를 복원합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다. <br>
> **AlternateWorkloadRestore** - 대체 위치에 데이터베이스를 복원하고 원래 원본 데이터베이스를 유지합니다.

## <a name="restore-to-alternate-location"></a>대체 위치에 복원

데이터베이스를 대체 위치에 복원하려면 복원 모드로 **AlternateWorkloadRestore**를 사용합니다. 그런 다음, 복원 지점을 선택해야 합니다. 복원 지점은 이전 지정 시간일 수도 있고 복원 지점일 수도 있습니다.

이 자습서에서는 이전 복원 지점으로 복원하겠습니다. 데이터베이스의 [복원 지점 목록을 확인](#view-restore-points-for-a-backed-up-database)하고 복원하려는 지점을 선택합니다. 이 자습서에서는 이름이 *7660777527047692711*인 복원 지점을 사용합니다.

위의 복원 지점 이름과 복원 모드를 사용하여 [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet에서 복구 구성 개체를 만들어 보겠습니다. 이 cmdlet의 나머지 매개 변수는 무엇을 의미하는지 살펴보겠습니다.

* **--target-item-name** 복원된 데이터베이스에 사용할 이름입니다. 여기서는 *restored_database*라는 이름을 사용했습니다.
* **--target-server-name** Recovery Services 자격 증명 모음에 성공적으로 등록되고 복원할 데이터베이스와 동일한 Azure 지역에 있는 SAP HANA 서버의 이름입니다. 이 자습서에서는 우리가 앞에서 보호 처리한 *hxehost*라는 이름의 동일한 SAP HANA 서버에 데이터베이스를 복원하겠습니다.
* **--target-server-type** SAP HANA 데이터베이스를 복원하려면 **SapHanaDatabase**를 사용해야 합니다.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

위의 쿼리에 대한 응답은 다음과 비슷한 복구 구성 개체입니다.

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

이제 데이터베이스를 복원하기 위해 [az restore restore-azurewl](/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet을 실행합니다. 이 명령을 사용하기 위해 *recoveryconfig.json*라는 파일에 저장된 위의 json 출력을 입력합니다.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

출력은 다음과 같습니다.

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

응답에서 작업 이름을 알 수 있습니다. 이 작업 이름은 [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용하여 작업 상태를 추적하는 데 사용할 수 있습니다.

## <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

원래 위치로 복원하기 위해 **OrignialWorkloadRestore**를 복원 모드로 사용합니다. 그런 다음, 복원 지점을 선택해야 합니다. 복원 지점은 이전 지정 시간일 수도 있고 복원 지점일 수도 있습니다.

이 자습서에서는 복원할 이전 지정 시간으로 “28-11-2019-09:53:00”을 선택합니다. 이 복원 지점을 dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss 형식으로 입력할 수 있습니다. 복원할 올바른 지정 시간을 선택하려면 끊어지지 않은 로그 체인 백업의 간격을 나열하는 [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet을 사용합니다.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

위의 쿼리에 대한 응답은 다음과 비슷한 복구 구성 개체입니다.

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

이제 데이터베이스를 복원하기 위해 [az restore restore-azurewl](/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet을 실행합니다. 이 명령을 사용하기 위해 *recoveryconfig.json*라는 파일에 저장된 위의 json 출력을 입력합니다.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

출력은 다음과 같습니다.

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

응답에서 작업 이름을 알 수 있습니다. 이 작업 이름은 [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용하여 작업 상태를 추적하는 데 사용할 수 있습니다.

## <a name="restore-as-files"></a>파일로 복원

백업 데이터를 데이터베이스 대신 파일로 복원하려면 복원 모드로 **RestoreAsFiles**를 사용합니다. 그런 다음, 이전 특정 시점이거나 이전 복원 지점 중 하나일 수 있는 복원 지점을 선택합니다. 파일이 지정된 경로에 덤프되면 이러한 파일을 데이터베이스로 복원하려는 SAP HANA 컴퓨터로 가져올 수 있습니다. 이러한 파일을 임의의 컴퓨터로 이동할 수 있으므로 이제 구독 및 지역 간에 데이터를 복원할 수 있습니다.

이 자습서에서는 복원할 이전 특정 시점(`28-11-2019-09:53:00`) 및 동일한 SAP HANA 서버에서 백업 파일을 `/home/saphana/restoreasfiles`로 덤프할 위치를 선택합니다. 이 복원 지점을 **dd-mm-yyyy** 또는 **dd-mm-yyyy-hh:mm:ss** 형식으로 제공할 수 있습니다. 복원할 올바른 지정 시간을 선택하려면 끊어지지 않은 로그 체인 백업의 간격을 나열하는 [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) cmdlet을 사용합니다.

위의 복원 지점 이름과 복원 모드를 사용하는 경우 [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet을 사용하여 복구 구성 개체를 만들어 보겠습니다. 이 cmdlet의 나머지 매개 변수는 무엇을 의미하는지 살펴보겠습니다.

* **--target-container-name** Recovery Services 자격 증명 모음에 성공적으로 등록되고 복원할 데이터베이스와 동일한 Azure 지역에 있는 SAP HANA 서버의 이름입니다. 이 자습서에서는 데이터베이스를 파일로 보호한 *hxehost*라는 동일한 SAP HANA 서버에 복원합니다.
* **--rp-name** 특정 시점 복원의 경우 복원 지점 이름은 **DefaultRangeRecoveryPoint**입니다.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

위의 쿼리에 대한 응답은 다음과 같은 복구 구성 개체입니다.

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

이제 데이터베이스를 파일로 복원하기 위해 [az restore restore-azurewl](/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet을 실행합니다. 이 명령을 사용하려면 *recoveryconfig.json*이라는 파일에 저장되는 위의 json 출력을 입력합니다.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

출력은 다음과 같습니다.

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

응답에서 작업 이름을 알 수 있습니다. 이 작업 이름은 [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용하여 작업 상태를 추적하는 데 사용할 수 있습니다.

대상 컨테이너에 덤프되는 파일은 다음과 같습니다.

* 데이터베이스 백업 파일
* 카탈로그 파일
* JSON 메타데이터 파일(관련된 각 백업 파일에 대한)

일반적으로 네트워크 공유 경로 또는 탑재된 Azure 파일 공유 경로가 대상 경로로 지정되면 이를 통해 동일한 네트워크의 다른 컴퓨터 또는 동일한 Azure 파일 공유가 탑재된 다른 컴퓨터에서 이러한 파일에 더 쉽게 액세스할 수 있습니다.

>[!NOTE]
>등록된 대상 VM에 탑재된 Azure 파일 공유에서 데이터베이스 백업 파일을 복원하려면 루트 계정에 Azure 파일 공유에 대한 읽기/쓰기 권한이 있는지 확인합니다.

선택한 복원 지점 유형(**특정 시점** 또는 **전체 및 차등**)에 따라 대상 경로에 만들어진 하나 이상의 폴더를 볼 수 있습니다. `Data_<date and time of restore>`라는 폴더 중 하나에는 전체 및 차등 백업이 포함되고, `Log`라는 다른 폴더에는 로그 백업이 포함됩니다.

복원된 이러한 파일을 데이터베이스로 복원하려는 SAP HANA 서버로 이동합니다. 그런 다음, 다음 단계에 따라 데이터베이스를 복원합니다.

1. 다음 명령을 사용하여 백업 파일이 저장되는 폴더/디렉터리에 대한 사용 권한을 설정합니다.

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. 다음 명령 집합을 `<SID>adm`으로 실행합니다.

    ```bash
    su - <sid>adm
    ```

1. 복원에 사용할 카탈로그 파일을 생성합니다. 전체 백업에 대한 JSON 메타데이터 파일에서 **BackupId**를 추출합니다. 이는 나중에 복원 작업에서 사용됩니다. 전체 및 로그 백업이 서로 다른 폴더에 있는지 확인하고, 이러한 폴더에서 카탈로그 파일과 JSON 메타데이터 파일을 삭제합니다.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    위의 명령에서 다음을 수행합니다.

    * `<DataFileDir>` - 전체 백업이 포함된 폴더
    * `<LogFilesDir>` - 로그 백업이 포함된 폴더
    * `<PathToPlaceCatalogFile>` - 생성된 카탈로그 파일을 배치해야 하는 폴더

1. HANA Studio를 통해 새로 생성된 카탈로그 파일을 사용하여 복원하거나 새로 생성된 이 카탈로그를 사용하여 HDBSQL 복원 쿼리를 실행합니다. HDBSQL 쿼리는 아래에 나와 있습니다.

    * 특정 시점으로 복원하려면 다음을 수행합니다.

        복원된 새 데이터베이스를 만드는 경우 HDBSQL 명령을 실행하여 새 `<DatabaseName>` 데이터베이스를 만든 다음, 복원에 사용할 데이터베이스를 중지합니다. 그러나 기존 데이터베이스만 복원하는 경우 HDBSQL 명령을 실행하여 데이터베이스를 중지합니다.

        그런 다음, 다음 명령을 실행하여 데이터베이스를 복원합니다.

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - 복원하려는 새 데이터베이스 또는 기존 데이터베이스의 이름
        * `<Timestamp>` - 특정 시점 복원에 대한 정확한 타임스탬프
        * `<DatabaseName@HostName>` - 백업이 복원에 사용되는 데이터베이스의 이름 및 이 데이터베이스가 있는 **호스트**/SAP HANA 서버 이름. `USING SOURCE <DatabaseName@HostName>` 옵션은 데이터 백업(복원에 사용됨)이 대상 SAP HANA 컴퓨터와 다른 SID 또는 이름을 사용하는 데이터베이스임을 지정합니다. 따라서 백업이 수행된 동일한 HANA 서버에서 수행되는 복원에 대해서는 이를 지정할 필요가 없습니다.
        * `<PathToGeneratedCatalogInStep3>` - **3단계**에서 생성된 카탈로그 파일의 경로
        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업이 포함된 폴더
        * `<BackupIdFromJsonFile>` - **3단계**에서 추출된 **BackupId**

    * 특정 전체 또는 차등 백업으로 복원하려면 다음을 수행합니다.

        복원된 새 데이터베이스를 만드는 경우 HDBSQL 명령을 실행하여 새 `<DatabaseName>` 데이터베이스를 만든 다음, 복원에 사용할 데이터베이스를 중지합니다. 그러나 기존 데이터베이스만 복원하는 경우 HDBSQL 명령을 실행하여 데이터베이스를 중지합니다.

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 복원하려는 새 데이터베이스 또는 기존 데이터베이스의 이름
        * `<Timestamp>` - 특정 시점 복원에 대한 정확한 타임스탬프
        * `<DatabaseName@HostName>` - 백업이 복원에 사용되는 데이터베이스의 이름 및 이 데이터베이스가 있는 **호스트**/SAP HANA 서버 이름. `USING SOURCE <DatabaseName@HostName>` 옵션은 데이터 백업(복원에 사용됨)이 대상 SAP HANA 컴퓨터와 다른 SID 또는 이름을 사용하는 데이터베이스임을 지정합니다. 따라서 백업이 수행된 동일한 HANA 서버에서 수행되는 복원에 대해서는 이를 지정할 필요가 없습니다.
        * `<PathToGeneratedCatalogInStep3>` - **3단계**에서 생성된 카탈로그 파일의 경로
        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업이 포함된 폴더
        * `<BackupIdFromJsonFile>` - **3단계**에서 추출된 **BackupId**

## <a name="next-steps"></a>다음 단계

* Azure CLI를 사용하여 백업된 SAP HANA 데이터베이스를 관리하는 방법을 알아보려면 [Azure VM에서 CLI를 사용하여 SAP HANA 데이터베이스 관리](tutorial-sap-hana-backup-cli.md) 자습서를 계속 진행하세요.

* Azure Portal을 사용하여 Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원하는 방법은 [Azure VM에서 SAP HANA 데이터베이스 복원](./sap-hana-db-restore.md)을 참조하세요.
