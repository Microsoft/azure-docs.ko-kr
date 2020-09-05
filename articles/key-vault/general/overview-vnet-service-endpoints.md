---
title: Azure Key Vault의 가상 네트워크 서비스 엔드포인트 - Azure Key Vault | Microsoft Docs
description: 사용 시나리오를 포함 하 여 지정 된 가상 네트워크에 대 한 액세스를 제한 하는 Azure Key Vault의 가상 네트워크 서비스 끝점을 사용 하는 방법을 알아봅니다.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 891ea69dc6d53370f5f73f8bb3072a9795f07635
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190581"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault의 가상 네트워크 서비스 엔드포인트

Azure Key Vault의 가상 네트워크 서비스 엔드포인트를 사용하면 지정된 가상 네트워크에 대한 액세스를 제한할 수 있습니다. 엔드포인트를 사용하면 IPv4(인터넷 프로토콜 버전 4) 주소 범위 목록에 대한 액세스를 제한할 수도 있습니다. 해당 소스 외부에서 키 자격 증명 모음에 연결하는 모든 사용자는 액세스가 거부됩니다.

이 제한에서 한 가지 중요한 예외가 있습니다. 사용자가 신뢰할 수 있는 Microsoft 서비스를 허용하도록 옵트인한 경우 해당 서비스에서 방화벽을 통해 연결할 수 있습니다. 예를 들어 이러한 서비스에는 Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager 및 Azure Backup 등이 있습니다. 이러한 사용자는 유효한 Azure Active Directory 토큰을 제공해야 하며, 요청된 작업을 수행하기 위해 사용 권한(액세스 정책으로 구성된)이 있어야 합니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.

## <a name="usage-scenarios"></a>사용 시나리오

기본적으로 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 [Key Vault 방화벽 및 가상 네트워크](network-security.md)를 구성할 수 있습니다. 특정 Azure 가상 네트워크 및 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여하여 애플리케이션에 대한 보안 네트워크 경계를 빌드하도록 할 수 있습니다.

> [!NOTE]
> Key Vault 방화벽 및 가상 네트워크 규칙은 Key Vault의 [데이터 평면](secure-your-key-vault.md#data-plane-access-control)에만 적용됩니다. Key Vault 제어 평면 작업(예: 작업 만들기, 삭제 및 수정 그리고 액세스 정책 설정, 방화벽 및 가상 네트워크 규칙 설정)은 방화벽 및 가상 네트워크 규칙의 영향을 받지 않습니다.

서비스 엔드포인트를 사용할 수 있는 방법의 몇 가지 예는 다음과 같습니다.

* Key Vault를 사용하여 암호화 키, 애플리케이션 비밀 및 인증서를 저장하고, 공용 인터넷에서 키 자격 증명 모음에 대한 액세스를 차단하려고 합니다.
* 사용자 애플리케이션 또는 지정된 호스트의 간단한 목록만 키 자격 증명 모음에 연결할 수 있도록 키 자격 증명 모음에 대한 액세스를 잠그려고 합니다.
* Azure 가상 네트워크에서 실행되는 애플리케이션이 있으며, 이 가상 네트워크는 모든 인바운드 및 아웃바운드 트래픽에 대해 잠겨 있습니다. 애플리케이션은 비밀 또는 인증서를 가져오거나 암호화 키를 사용하려면 여전히 Key Vault에 연결해야 합니다.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault 방화벽 및 가상 네트워크 구성

방화벽 및 가상 네트워크를 구성하는 데 필요한 단계는 다음과 같습니다. 이러한 단계는 PowerShell, Azure CLI 또는 Azure Portal을 사용하든 관계없이 적용됩니다.

1. [Key Vault 로깅](logging.md)을 사용하도록 설정하여 자세한 액세스 로그를 확인합니다. 이렇게 하면 방화벽 및 가상 네트워크 규칙이 키 자격 증명 모음에 대한 액세스를 막는 경우 진단하는 데 도움이 됩니다. (이 단계는 선택 사항이지만 강력 권장됩니다.)
2. 대상 가상 네트워크 및 서브넷에 대한 **키 자격 증명 모음의 서비스 엔드포인트**를 사용하도록 설정합니다.
3. 특정 가상 네트워크, 서브넷 및 IPv4 주소 범위에서 해당 키 자격 증명 모음에 대한 액세스를 제한하려면 키 자격 증명 모음에 대한 방화벽 및 가상 네트워크 규칙을 설정합니다.
4. 이 키 자격 증명 모음을 신뢰할 수 있는 모든 Microsoft 서비스에서 액세스할 수 있게 하려면 **신뢰할 수 있는 Azure 서비스**가 Key Vault에 연결하도록 허용하는 옵션을 설정합니다.

자세한 내용은 [Azure Key Vault 방화벽 및 가상 네트워크 구성](network-security.md)을 참조하세요.

> [!IMPORTANT]
> 방화벽 규칙이 적용되면 사용자의 요청이 허용되는 가상 네트워크 또는 IPv4 주소 범위에서 시작되는 경우에만 사용자는 Key Vault [데이터 평면](secure-your-key-vault.md#data-plane-access-control) 작업을 수행할 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 이동할 수 있다고 해도 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 Key Vault 선택기도 마찬가지입니다. 방화벽 규칙이 사용자의 클라이언트 머신을 금지하는 경우 해당 사용자는 키 자격 증명 모음 목록을 확인할 수 있지만 키를 나열하지는 못합니다.


> [!NOTE]
> 다음과 같은 구성 제한 사항을 고려해야 합니다.
> * 최대한 127개 가상 네트워크 규칙 및 127개 IPv4 규칙이 허용됩니다. 
> * "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 대신 개별 IP 주소 규칙을 사용하여 이러한 범위를 구성합니다.
> * IP 네트워크 규칙은 공용 IP 주소에 대해서만 허용됩니다. 프라이빗 네트워크용으로 예약된 IP 주소 범위(RFC 1918에 정의)는 IP 규칙에서 허용되지 않습니다. 개인 네트워크에는 **10.** , **172.16-31** 및 **192.168.** 로 시작하는 주소가 포함됩니다. 
> * 현재 IPv4 주소만 지원됩니다.

## <a name="trusted-services"></a>신뢰할 수 있는 서비스

**신뢰할 수 있는 서비스 허용** 옵션을 사용하도록 설정하는 경우 키 자격 증명 모음에 액세스하도록 허용되는 신뢰할 수 있는 서비스 목록은 다음과 같습니다.

|신뢰할 수 있는 서비스|지원 되는 사용 시나리오|
| --- | --- |
|Azure Virtual Machines 배포 서비스|[고객 관리 Key Vault에서 VM으로 인증서를 배포합니다](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Azure Resource Manager 템플릿 배포 서비스|[배포 하는 동안 보안 값을 전달](../../azure-resource-manager/templates/key-vault-parameter.md)합니다.|
|Azure 애플리케이션 Gateway v2 SKU|[Key Vault 인증서를 사용 하는 TLS 종료](/azure/application-gateway/key-vault-certs)|
|Azure Disk Encryption 볼륨 암호화 서비스|가상 머신을 배포하는 동안 BitLocker 키(Windows VM) 또는 DM 암호(Linux VM) 및 키 암호화 키에 대한 액세스를 허용합니다. 그러면 [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)이 설정됩니다.|
|Azure Backup|Azure Virtual Machines를 백업하는 동안 [Azure Backup](../../backup/backup-introduction-to-azure-backup.md)을 사용하여 관련 키 및 비밀의 백업 및 복원을 허용합니다.|
|Exchange Online 및 SharePoint Online|[고객 키](/microsoft-365/compliance/customer-key-overview)를 사용하여 Azure Storage 서비스 암호화를 위한 고객 키에 대한 액세스를 허용합니다.|
|Azure Information Protection|[Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)을 위해 테넌트 키 액세스 허용|
|Azure App Service|[Key Vault를 통해 Azure Web App 인증서를 배포합니다](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Azure SQL Database 및 Data Warehouse에 대한 BYOK(Bring Your Own Key) 지원을 사용하여 투명한 데이터 암호화](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Azure Key Vault에서 고객 관리 키를 사용 하 여 저장소 서비스 암호화](../../storage/common/storage-service-encryption-customer-managed-keys.md)합니다.|
|Azure Data Lake Store|고객 관리 키를 사용하여 [Azure Data Lake Store의 데이터 암호화](../../data-lake-store/data-lake-store-encryption.md).|
|Azure Databricks|[빠르고 쉬우며 공동 작업이 가능한 Apache Spark 기반 분석 서비스](../../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[MSI를 사용 하 여 Key Vault에서 사용자 지정 도메인에 대 한 인증서 배포](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure 데이터 팩터리|[Data Factory에서 Key Vault의 데이터 저장소 자격 증명 페치](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[고객이 관리 하는 키 시나리오의 key vault에 대 한 액세스 허용](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[고객이 관리 하는 키 시나리오의 key vault에 대 한 액세스 허용](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure Import/Export| [Azure Key Vault에서 Import/Export 서비스에 고객 관리형 키 사용](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)
|Azure Container Registry|[고객 관리 키를 사용 하는 레지스트리 암호화](../../container-registry/container-registry-customer-managed-keys.md)<br><br/>[다른 레지스트리에 아티팩트 전송](../../container-registry/container-registry-transfer-images.md)

> [!NOTE]
> 해당 서비스가 Key Vault에 액세스할 수 있도록 하려면 관련 Key Vault 액세스 정책을 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [키 자격 증명 모음 보안](secure-your-key-vault.md))
* [Azure Key Vault 방화벽 및 가상 네트워크 구성](network-security.md)
