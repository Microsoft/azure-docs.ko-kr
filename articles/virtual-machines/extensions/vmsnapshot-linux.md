---
title: Azure Backup용 VM 스냅숏 Linux 확장 | Microsoft Docs
description: VM 스냅숏 확장을 사용하여 Azure Backup에서 가상 머신의 애플리케이션 일치 백업을 수행합니다.
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhk
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: 1d6c89e596fa976161ee28d62885e77b9400a1f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799500"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Azure Backup용 VM 스냅숏 Linux 확장



Azure Backup은 워크로드를 온-프레미스에서 클라우드에 백업하고, 클라우드 리소스를 Recovery Services 자격 증명 모음에 백업하도록 지원합니다. Azure Backup은 VM 스냅숏 확장을 사용하여 VM을 종료하지 않고도 Azure 가상 머신의 애플리케이션 일치 백업을 수행합니다. Microsoft는 Azure Backup 서비스의 일부로 VM 스냅숏 Linux 확장을 게시하고 지원합니다. Azure Backup은 백업을 활성화한 후 트리거되는 첫 번째 예약된 백업의 일부로 확장을 설치합니다. 이 문서에서는 VM 스냅숏 확장에 지원되는 플랫폼, 구성 및 배포 옵션에 대해 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제
지원되는 운영 체제 목록은 [Azure Backup에 지원되는 운영 체제](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)를 참조하세요.

### <a name="internet-connectivity"></a>인터넷 연결

VM 스냅숏 확장을 사용하려면 가상 머신을 백업할 때 대상 가상 머신이 인터넷에 연결되어 있어야 합니다.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 VM 스냅숏 확장에 대한 스키마를 보여 줍니다. 확장에는 VM에서 스냅숏을 트리거한 백업 작업을 식별하기 위한 작업 ID, 스냅숏 작업 상태가 기록되는 상태 Blob URI, 예약된 스냅숏 시작 시간, 스냅숏 작업에 해당하는 로그가 기록되는 로그 Blob URI, VM 디스크 및 메타데이터에 대한 표현인 개체 문자열이 필요합니다.  이러한 설정은 중요한 데이터로 처리되어야 하므로 protected 설정 구성에 저장해야 합니다. Azure VM 확장으로 보호되는 설정 데이터는 암호화되어 대상 가상 머신에서만 해독됩니다. 이러한 설정은 Azure Backup 서비스에서 백업 작업의 일부로만 전달하는 것이 좋습니다.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | 문자열 |
| commandStartTimeUTCTicks | 6.36458E+17 | 문자열 |
| locale | en-us | 문자열 |
| objectStr | SAS URI 배열의 인코딩 - "blobSASUri": ["https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | 문자열 |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 문자열 |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | 문자열 |



## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 그러나 가상 머신에 VM 스냅숏 확장을 추가하려면 가상 머신에서 백업을 사용하도록 설정하는 것이 좋습니다. 이는 Resource Manager 템플릿을 통해 수행할 수 있습니다.  가상 머신에서 백업을 사용하도록 설정하는 Resource Manager 템플릿 샘플은 [Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)에 있습니다.


## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 가상 머신에서 백업을 사용하도록 설정할 수 있습니다. 백업을 사용하도록 설정한 후 첫 번째 예약된 백업 작업은 VM 스냅숏 확장을 VM에 설치합니다.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>오류 코드 및 해당 의미

문제 해결 정보는 [Azure VM 백업 문제 해결 가이드](../../backup/backup-azure-vms-troubleshoot.md)에 있습니다.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
