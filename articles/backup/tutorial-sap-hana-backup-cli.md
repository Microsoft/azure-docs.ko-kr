---
title: 자습서 - CLI를 사용하여 Azure에서 SAP HANA DB 백업
description: 이 자습서에서는 Azure CLI를 사용하여 Azure VM에서 실행되는 SAP HANA 데이터베이스를 Azure Backup Recovery Services 자격 증명 모음에 백업하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a0b6683183d6bf73b5376c6320106373ffd4ba78
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762405"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure VM에서 SAP HANA 데이터베이스 복원

Azure CLI는 명령줄 또는 스크립트를 통해 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 설명서에서는 Azure CLI를 사용하여 SAP HANA 데이터베이스를 백업하고 요청 시 백업을 트리거하는 방법을 자세히 설명합니다. [Azure Portal](./backup-azure-sap-hana-database.md)에서 이 단계를 수행해도 됩니다.

이 문서에서는 Azure VM에 SAP HANA 데이터베이스가 이미 설치되어 있다고 가정합니다. ([Azure CLI를 사용하여 VM 만들기](../virtual-machines/linux/quick-create-cli.md) 가능). 이 자습서를 마치면 다음을 수행할 수 있습니다.

> [!div class="checklist"]
>
> * Recovery Services 자격 증명 모음 만들기
> * SAP HANA 인스턴스를 등록하고 데이터베이스 검색
> * SAP HANA 데이터베이스에서 백업 사용
> * 주문형 백업 트리거

[현재 SAP HANA를 지원하는 시나리오](./sap-hana-backup-support-matrix.md#scenario-support)를 확인하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려면 Azure CLI 버전 xx.xxx.x 이상을 실행해야 합니다. CLI 버전을 찾으려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 Azure VM 또는 Azure VM에서 실행되는 워크로드(예: SQL 또는 HANA 데이터베이스)처럼 보호된 리소스의 백업 데이터를 저장하는 논리 컨테이너입니다. 보호된 리소스에 대한 백업 작업이 실행될 때 Recovery Services 자격 증명 모음 내에 복구 지점을 만듭니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

[az backup vault create](/cli/azure/backup/vault#az-backup-vault-create)를 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 보호하려는 VM으로 동일한 리소스 그룹 및 위치를 지정합니다. 이 [VM 빠른 시작](../virtual-machines/linux/quick-create-cli.md)에서 Azure CLI를 사용하여 VM을 만드는 방법을 알아보세요.

이 자습서에서는 다음을 사용합니다.

* *saphanaResourceGroup*이라는 이름의 리소스 그룹
* *saphanaVM*이라는 VM
* *westus2* 위치의 리소스

*saphanaVault*라는 자격 증명 모음을 만들겠습니다.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

기본적으로 Recovery Services 자격 증명 모음은 지역 중복 스토리지에 대해 설정됩니다. 지역 중복 스토리지는 주 지역에서 수백 마일 떨어져 있는 보조 Azure 지역에 백업 데이터가 복제되었음을 보장합니다. 스토리지 중복 설정을 수정해야 하는 경우 [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet을 사용합니다.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

자격 증명 모음이 성공적으로 만들어졌는지 확인하려면 [az backup vault list](/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-list) cmdlet을 사용합니다. 다음 응답이 표시됩니다.

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>SAP HANA 인스턴스 등록 및 보호

Azure 서비스에서 SAP HANA 인스턴스(SAP HANA가 설치된 VM)를 검색하려면 SAP HANA 머신에서 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 실행해야 합니다. 스크립트를 실행하기 전에 모든 [필수 구성 요소](./tutorial-backup-sap-hana-db.md#prerequisites)를 충족하는지 확인합니다. 스크립트가 수행하는 작업에 대한 자세한 내용은 [사전 등록 스크립트의 기능](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하세요.

스크립트가 실행되면 앞에서 만든 Recovery Services 자격 증명 모음에 SAP HANA 인스턴스를 등록할 수 있습니다. 인스턴스를 등록하려면 [az backup container register](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-register) cmdlet을 사용합니다. *VMResourceId*는 SAP HANA를 설치하기 위해 만든 VM의 리소스 ID입니다.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>VM이 자격 증명 모음과 동일한 리소스 그룹에 있지 않으면 *saphanaResourceGroup*은 자격 증명 모음이 생성된 리소스 그룹을 참조합니다.

SAP HANA 인스턴스를 등록하면 해당 인스턴스의 모든 현재 데이터베이스가 자동으로 검색됩니다. 그러나 나중에 추가될 수 있는 새 데이터베이스를 검색하려면 [등록된 SAP HANA 인스턴스에 추가된 새 데이터베이스 검색](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) 섹션을 참조하세요.

SAP HANA 인스턴스가 자격 증명 모음에 성공적으로 등록되었는지 확인하려면 [az backup container list](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) cmdlet을 사용합니다. 다음 응답이 표시됩니다.

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> 위의 출력에서 열 "name"은 컨테이너 이름을 참조합니다. 이 컨테이너 이름은 다음 섹션에서 백업을 사용하도록 설정하고 트리거하는 데 사용됩니다. 여기서는 *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*입니다.

## <a name="enable-backup-on-sap-hana-database"></a>SAP HANA 데이터베이스에서 백업 사용

[az backup protectable-item list](/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) cmdlet은 이전 단계에서 등록한 SAP HANA 인스턴스에서 검색된 모든 데이터베이스를 나열합니다.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

이 목록에서 백업하려는 데이터베이스를 찾아야 하며, 목록은 다음과 같이 표시됩니다.

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

위의 출력에서 볼 수 있듯이, SAP HANA 시스템의 SID는 HXE입니다. 이 자습서에서는 *hxehost* 서버에 상주하는 *saphanadatabase;hxe;hxe* 데이터베이스의 백업을 구성합니다.

데이터베이스에서 한 번에 하나씩 백업을 보호하고 구성하려면 [az backup protection enable-for-azurewl](/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurewl) cmdlet을 사용합니다. 사용할 정책의 이름을 입력합니다. CLI를 사용하여 정책을 만들려면 [az backup policy create](/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet을 사용합니다. 이 자습서에서는 *sapahanaPolicy* 정책을 사용합니다.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name saphanadatabase;hxe;hxe  \
    --protectable-item-type SAPHANADatabse \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

[az backup job list](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet을 사용하여 위의 백업 구성이 완료되었는지 확인할 수 있습니다. 출력은 다음과 같이 표시됩니다.

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

[az backup job list](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet은 등록, 백업 구성, 백업 데이터 삭제 등과 같은 다른 작업뿐 아니라 보호된 데이터베이스에서 실행되었거나 현재 실행 중인 모든 백업 작업(예약된 백업 또는 요청 시 백업)을 나열합니다.

>[!NOTE]
>Azure Backup은 Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업할 때 일광 절약 시간제 변경을 자동으로 조정하지 않습니다.
>
>필요에 따라 정책을 수동으로 수정합니다.

## <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

위의 섹션에서는 예약된 백업을 구성하는 방법을 자세히 설명했고, 이 섹션에서는 요청 시 백업을 트리거하는 방법을 설명합니다. 요청 시 백업을 트리거하려면 [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now) cmdlet을 사용합니다.

>[!NOTE]
> 요청 시 백업의 보존 정책은 데이터베이스의 기본 보존 정책에 의해 결정됩니다.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

출력은 다음과 같이 표시됩니다.

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

응답에서 작업 이름을 알 수 있습니다. 이 작업 이름은 [az backup job show](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용하여 작업 상태를 추적하는 데 사용할 수 있습니다.

>[!NOTE]
>전체 또는 차등 백업 예약 외에도 현재 수동으로 트리거할 수 있습니다. 로그 백업은 내부에서 SAP HANA를 통해 자동으로 트리거되고 관리됩니다.
>
> 증분 백업은 현재 Azure Backup에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* Azure VM에서 CLI를 사용하여 SAP HANA 데이터베이스를 복원하는 방법을 알아보려면 [Azure VM에서 CLI를 사용하여 SAP HANA 데이터베이스 복원](tutorial-sap-hana-restore-cli.md) 자습서를 계속 진행하세요.

* Azure Portal을 사용하여 Azure VM에서 실행 중인 SAP HANA 데이터베이스를 백업하는 방법은 [Azure VM에서 SAP HANA 데이터베이스 백업](./backup-azure-sap-hana-database.md)을 참조하세요.
