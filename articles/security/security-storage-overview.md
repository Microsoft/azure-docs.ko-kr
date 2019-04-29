---
title: Azure Storage에 사용될 수 있는 보안 기능
description: 이 문서에서는 Azure Storage에서 사용할 수 있는 핵심 Azure 보안 기능의 개요를 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2019
ms.author: terrylan
ms.openlocfilehash: ec0e8ae1bf657cda59f3d133db23106436e184e3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120895"
---
# <a name="azure-storage-security-overview"></a>Azure Storage 보안 개요

Azure Storage는 내구성, 가용성, 확장성을 활용하여 고객의 요구 사항을 충족하는 최신 응용 프로그램을 위한 클라우드 스토리지 솔루션입니다. Azure Storage는 포괄적인 보안 기능 집합을 제공합니다. 다음을 수행할 수 있습니다.

* RBAC(역할 기반 액세스 제어) 및 Azure Active Directory를 사용하여 저장소 계정의 보안을 유지할 수 있습니다.
* 클라이언트 쪽 암호화, HTTP 또는 SMB 3.0을 사용하여 애플리케이션과 Azure 간에 전송 중인 데이터의 보안을 유지할 수 있습니다.
* Storage 서비스 암호화를 사용하여 데이터가 Azure Storage에 기록될 때 자동으로 암호화되도록 설정할 수 있습니다.
* VM(가상 머신)에서 사용되는 OS 및 데이터 디스크가 Azure Disk Encryption을 통해 암호화되도록 설정할 수 있습니다.
* SAS(공유 액세스 서명)를 사용하여 Azure Storage의 데이터 개체에 대한 위임된 액세스 권한을 부여할 수 있습니다.
* 분석을 사용하여 사용자가 Storage에 액세스할 때 사용하는 인증 방법을 추적할 수 있습니다.

Azure Storage의 보안에 대해 보다 자세히 알아보려면 [Azure Storage 보안 가이드](../storage/common/storage-security-guide.md)를 참조하세요. 이 가이드에서는 Azure Storage의 보안 기능에 대해 자세히 설명합니다. 이러한 기능에는 저장소 계정 키, 전송 중인 데이터 및 미사용 데이터 암호화, 저장소 분석 등이 포함됩니다.


이 문서에서는 Azure Storage에서 사용할 수 있는 Azure 보안 기능의 개요를 제공합니다. 문서 링크는 각 기능에 대해 자세히 알아볼 수 있도록 세부 정보를 제공합니다.

## <a name="role-based-access-control"></a>역할 기반 Access Control

RBAC(역할 기반 액세스 제어)를 사용하여 저장소 계정의 보안을 유지할 수 있습니다. [알아야 할 사항](https://en.wikipedia.org/wiki/Need_to_know) 및 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 보안 원칙을 기반으로 액세스를 제한하는 것은 데이터 액세스에 보안 정책을 적용하고자 하는 조직의 경우 필수입니다. 특정 범위에서 그룹 및 애플리케이션에 적절한 RBAC 역할을 할당하여 이러한 액세스 권한을 부여합니다. Storage 계정 참여자와 같은 [기본 제공 RBAC 역할](../role-based-access-control/built-in-roles.md)을 사용하여 사용자에게 권한을 할당할 수 있습니다.

자세한 정보:

* [Azure Active Directory 역할 기반 액세스 제어](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>저장소 개체에 대한 위임된 액세스

공유 액세스 서명은 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. SAS는 지정된 권한 집합을 사용하여 지정된 기간 동안 저장소 계정의 개체로 제한된 권한을 클라이언트에 부여할 수 있다는 것입니다. 계정 선택키를 공유하지 않고도 제한된 권한을 부여할 수 있습니다.

SAS는 저장소 리소스에 인증된 액세스를 수행하는 데 필요한 모든 정보가 쿼리 매개 변수에 있는 URI입니다. SAS를 사용하여 저장소 리소스에 액세스하려는 클라이언트는 SAS를 적절한 생성자 또는 메서드에 전달하면 됩니다.

자세한 정보:

* [SAS 모델 이해](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Blob Storage에서 SAS 만들기 및 사용](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>전송 중 암호화

전송 중 암호화는 네트워크를 통해 전송되는 경우 데이터를 보호하는 메커니즘입니다. Azure Storage에서 다음을 사용하여 데이터를 보호할 수 있습니다.

* [전송 수준 암호화](../storage/common/storage-security-guide.md#encryption-in-transit)(예: Azure Storage 안팎으로 데이터를 전송하는 경우 HTTPS)
* [실시간 암호화](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares)(예: Azure 파일 공유에 대한 SMB 3.0 암호화)
* 저장소로 데이터가 전송되기 전에 암호화하고 저장소 외부로 전송된 후에 암호를 해독할 수 있도록 하는 [클라이언트 쪽 암호화](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage)

클라이언트 쪽 암호화에 대해 자세히 알아봅니다.

* [Microsoft Azure Storage용 클라이언트 쪽 암호화](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [클라우드 보안 컨트롤 시리즈: 전송 중 데이터 암호화](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>휴지 상태의 암호화

여러 조직에서 [미사용 데이터 암호화](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)는 데이터 프라이버시, 규정 준수 및 데이터 주권을 위한 필수 단계입니다. 휴지 상태의 데이터 암호화를 제공하는 세 가지 Azure 기능이 있습니다.

* [스토리지 서비스 암호화](../storage/common/storage-security-guide.md#encryption-at-rest)는 항상 활성화되어 있으며 Azure Storage에 데이터를 쓸 때 스토리지 서비스 데이터를 자동으로 암호화합니다.
* [클라이언트측 암호화](../storage/common/storage-security-guide.md#client-side-encryption)는 휴지 상태의 암호화 기능을 제공합니다.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines)을 사용하면 IaaS 가상 머신에서 사용되는 OS 디스크 및 데이터 디스크를 암호화할 수 있습니다.

Storage 서비스를 암호화에 대해 자세히 알아봅니다.

* [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)에 [Azure Storage 서비스 암호화](https://azure.microsoft.com/services/storage/)를 사용할 수 있습니다. 다른 Azure Storage 형식에 대한 자세한 내용은 [Azure Files](https://azure.microsoft.com/services/storage/files/), [Table Storage](https://azure.microsoft.com/services/storage/tables/) 및 [Queue Storage](https://azure.microsoft.com/services/storage/queues/)를 참조하세요.
* [휴지 상태의 데이터에 대한 Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure 디스크 암호화

가상 머신에 대한 Azure Disk Encryption는 조직의 보안 및 규정 준수 요구 사항을 해결하도록 도와줍니다. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에서 제어하는 키 및 정책을 사용하여 VM 디스크(부트 디스크 및 데이터 디스크 포함)를 암호화합니다.

VM에 대한 디스크 암호화는 Linux 및 Windows 운영 체제에 적합합니다. 또한 주요 자격 증명 모음을 사용하여 디스크 암호화 키 사용을 보호, 관리 및 감사하는 데 도움이 됩니다. VM 디스크의 모든 데이터는 Azure Storage 계정에 산업 표준 암호화 기술을 사용하여 휴지 상태에서 암호화됩니다. Windows용 디스크 암호화 솔루션은 [Microsoft BitLocker 드라이브 암호화](https://technet.microsoft.com/library/cc732774.aspx)를 기반으로 하고, Linux 솔루션은 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 기반으로 합니다.

자세한 정보

* [Windows 및 Linux IaaS Virtual Machines에 대한 Azure 디스크 암호화(영문)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>방화벽 및 가상 네트워크

Azure 저장소를 사용하면 저장소 계정에 대한 방화벽 규칙을 활성화할 수 있습니다. 활성화되면 다른 Azure 서비스의 요청을 포함하여 데이터에 대한 들어오는 요청을 차단합니다. 트래픽을 허용하는 예외를 구성할 수 있습니다. 기존 저장소 계정에서 또는 생성 중에 방화벽 규칙을 활성화할 수 있습니다.

이 기능을 사용하여 허용된 네트워크의 특정 집합으로 저장소 계정을 보호해야 합니다.

Azure 스토리지 방화벽 및 가상 네트워크에 대한 자세한 내용은 문서 [Azure Storage 방화벽 및 Virtual Network 구성](../storage/common/storage-network-security.md)을 검토하세요.

## <a name="azure-data-box"></a>Azure Data Box

네트워크를 사용할 수 없는 경우 Data Box, Data Box Disk 및 Data Box Heavy 디바이스를 통해 많은 양의 데이터를 Azure로 전송할 수 있습니다. 이러한 오프라인 데이터 전송 디바이스는 조직과 Azure 데이터 센터 간에 제공됩니다. 이러한 디바이스는 AES 암호화를 사용하여 전송 중인 데이터를 보호할 수 있고, 철저한 업로드 후 삭제 프로세스를 진행하여 디바이스에서 데이터를 삭제합니다.

Data Box Edge 및 Data Box Gateway는 사이트와 Azure 간에 데이터를 관리하는 네트워크 저장소 게이트웨이로 작동하는 온라인 데이터 전송 제품입니다. 온-프레미스 네트워크 디바이스인 Data Box Edge는 Azure와 데이터를 주고받으며 AI(인공 지능) 지원 에지 컴퓨팅을 사용하여 데이터를 처리합니다. Data Box Gateway는 저장소 게이트웨이 기능이 포함된 가상 어플라이언스입니다.

자세한 정보:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)
* [Azure Data Box Gateway](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>고급 위협 보호

Azure Storage는 스토리지 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 탐지하는 추가 보안 인텔리전스 계층을 위해 Advanced Threat Protection을 제공합니다. Advanced Threat Protection은 Azure Storage 진단 로그에서 Blob Storage에 대한 의심스러운 읽기, 쓰기 또는 삭제 요청을 모니터링합니다.

Advanced Threat Protection 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에서 확인할 수 있습니다. Azure Security Center에서는 탐지된 의심스러운 활동에 대한 세부 정보를 제공하고 잠재적인 위협을 조사하고 수정하는 작업을 권장합니다.

자세한 정보:

* [Azure Storage Advanced Threat Protection 개요](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용하여 키 자격 증명 모음 구독에서 디스크 암호화 키와 암호를 제어하고 관리할 수 있습니다. 또한 가상 머신 디스크에 있는 모든 데이터가 미사용 시 Azure Storage에 암호화되도록 합니다. 키 자격 증명 모음을 사용하여 키 및 정책 사용을 감사해야 합니다.

자세한 정보

* [Azure Key Vault란?](../key-vault/key-vault-overview.md)
