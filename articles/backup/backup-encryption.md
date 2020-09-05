---
title: Azure Backup에서 암호화
description: Azure Backup의 암호화 기능을 통해 백업 데이터를 보호 하 고 비즈니스의 보안 요구 사항을 충족 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: f55deba58cd7b725bd030409296794e5de911c09
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017909"
---
# <a name="encryption-in-azure-backup"></a>Azure Backup에서 암호화

모든 백업 된 데이터는 Azure Storage 암호화를 사용 하 여 클라우드에 저장 될 때 자동으로 암호화 되므로 보안 및 규정 준수 약정을 충족할 수 있습니다. 미사용 데이터는 256 비트 AES 암호화를 사용 하 여 암호화 되 고, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며, FIPS 140-2 규격입니다. 휴지 상태의 암호화 외에도 전송 중인 모든 백업 데이터는 HTTPS를 통해 전송 됩니다. 항상 Azure 백본 네트워크에 남아 있습니다.

## <a name="levels-of-encryption-in-azure-backup"></a>Azure Backup의 암호화 수준

두 수준에 암호화를 포함 하는 Azure Backup:

- **Recovery Services 자격 증명 모음의 데이터 암호화**
  - **플랫폼 관리 키 사용**: 기본적으로 모든 데이터는 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 이 암호화를 사용 하도록 설정 하기 위해 끝에서 명시적인 조치를 취할 필요는 없습니다. Recovery Services 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.
  - **고객 관리 키 사용**: Azure Virtual Machines를 백업할 때 사용자가 소유 하 고 관리 하는 암호화 키를 사용 하 여 데이터를 암호화 하도록 선택할 수 있습니다. Azure Backup를 사용 하면 Azure Key Vault에 저장 된 RSA 키를 사용 하 여 백업을 암호화할 수 있습니다. 백업 암호화에 사용 되는 암호화 키는 원본에 사용 된 것과 다를 수 있습니다. 데이터는 AES 256 기반 DEK (데이터 암호화 키)를 사용 하 여 보호 됩니다. 즉, 키를 사용 하 여 보호 됩니다. 이를 통해 데이터와 키에 대 한 모든 권한을 제공 합니다. 암호화를 허용 하려면 Recovery Services 자격 증명 모음에 Azure Key Vault 암호화 키에 대 한 액세스 권한을 부여 해야 합니다. 필요할 때마다 키를 사용 하지 않도록 설정 하거나 액세스를 취소할 수 있습니다. 그러나 자격 증명 모음에 대 한 항목을 보호 하기 전에 키를 사용 하 여 암호화를 사용 하도록 설정 해야 합니다. [자세한 내용은 여기를 참조](encryption-at-rest-with-cmk.md)하세요.
  - **인프라 수준 암호화**: 고객이 관리 하는 키를 사용 하 여 Recovery Services 자격 증명 모음에서 데이터를 암호화 하는 것 외에도 저장소 인프라에 추가 암호화 계층을 구성 하도록 선택할 수 있습니다. 이 인프라 암호화는 플랫폼에서 관리 됩니다. 고객 관리 키를 사용 하 여 미사용 암호화와 함께 사용 하면 백업 데이터의 2 계층 암호화를 사용할 수 있습니다. 미사용 암호화에 고유한 키를 사용 하도록 처음 선택 하는 경우에만 인프라 암호화를 구성할 수 있습니다. 인프라 암호화는 플랫폼 관리 키를 사용 하 여 데이터를 암호화 합니다.
- **백업 중인 작업에만 적용 되는 암호화**  
  - **Azure 가상 컴퓨터 백업**: Azure Backup는 사용자가 소유 하 고 관리 하는 [고객 관리 키](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) 뿐만 아니라 [플랫폼 관리 키](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#platform-managed-keys)를 사용 하 여 암호화 된 디스크가 있는 vm의 백업을 지원 합니다. 또한 [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade)를 사용 하 여 암호화 된 OS 또는 데이터 디스크를 포함 하는 Azure 가상 컴퓨터를 백업할 수 있습니다. ADE는 Windows Vm에 BitLocker를 사용 하 고 Linux Vm의 경우 DM을 사용 하 여 게스트 간 암호화를 수행 합니다.

>[!NOTE]
>인프라 암호화는 현재 제한 된 미리 보기로 제공 되며 미국 동부, 미국 West2, 미국 남부 중부, US Gov 애리조나 및 US .GOV 버지니아 지역 에서만 사용할 수 있습니다. 이러한 지역에서이 기능을 사용 하려는 경우 [이 양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) 작성 하 고에 전자 메일을 보내 주세요 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)
- 암호화에 대해 발생할 수 있는 질문에 대 한 [FAQ를 Azure Backup](backup-azure-backup-faq.md#encryption) 합니다.
