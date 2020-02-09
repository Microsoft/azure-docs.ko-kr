---
title: 보안 고려 사항
description: Azure Data Factory의 데이터 이동 서비스가 데이터를 보호하는 데 사용하는 기본 보안 인프라에 대해 설명합니다.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/15/2018
ms.openlocfilehash: e809c88d8a0a0efeb12e9f2a472a497349fdfa1b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927509"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Azure Data Factory에서 데이터 이동을 위한 보안 고려 사항
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> * [버전 1](v1/data-factory-data-movement-security-considerations.md)
> * [현재 버전](data-movement-security-considerations.md)

이 문서에서는 Azure Data Factory의 데이터 이동 서비스가 데이터를 보호하는 데 사용하는 기본 보안 인프라에 대해 설명합니다. Data Factory 관리 리소스는 Azure 보안 인프라를 기반으로 하며 Azure가 제공하는 모든 가능한 보안 수단을 사용합니다.

Data Factory 솔루션에서 하나 이상의 데이터 [파이프라인](concepts-pipelines-activities.md)를 만듭니다. 파이프라인은 함께 작업을 수행하는 활동의 논리적 그룹화입니다. 이 파이프라인은 데이터 팩터리가 작성된 지역에 상주합니다. 

Data Factory가 몇몇 지역에서만 지원되더라도 데이터 이동 서비스는 [전역적으로 지원](concepts-integration-runtime.md#integration-runtime-location)되며 데이터 준수, 효율성 및 네트워크 송신 비용 감소를 보장합니다. 

Azure Data Factory는 인증서를 사용하여 암호화된 클라우드 데이터 저장소에 대한 링크된 서비스 자격 증명을 제외한 모든 데이터를 저장하지 않습니다. Data Factory를 사용하면 데이터 기반 워크플로를 만들어서 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 간의 데이터 이동을 조율하고 다른 지역 또는 온-프레미스 환경에서 [컴퓨팅 서비스](compute-linked-services.md)를 사용하여 데이터의 처리를 조율할 수 있습니다. 또한 SDK 및 Azure Monitor를 사용하여 워크플로를 모니터링하고 관리할 수 있습니다.

Data Factory는 다음에 대해 인증을 받았습니다.

| **[CSA STAR 인증](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Azure 규정 준수 및 Azure의 자체 인프라 보안 방법에 관심이 있다면 [Microsoft 보안 센터](https://microsoft.com/en-us/trustcenter/default.aspx)를 방문해 보세요. 모든 Azure 규정 준수 제품에 대한 최신 목록은 https://aka.ms/AzureCompliance 를 확인하세요.

이 문서에서는 다음 두 가지 데이터 이동 시나리오에서 보안 고려 사항을 검토합니다. 

- **클라우드 시나리오**: 이 시나리오에서는 원본과 대상 모두 인터넷을 통해 공개적으로 액세스할 수 있습니다. 여기에는 Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, Salesforce와 같은 SaaS 서비스, FTP 및 OData와 같은 웹 프로토콜과 같은 관리 클라우드 스토리지 서비스가 포함됩니다. 지원되는 데이터 원본의 전체 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)에서 확인하세요.
- **하이브리드 시나리오**: 이 시나리오에서는 원본 또는 대상 중 하나는 온-프레미스 회사 네트워크 내부 또는 방화벽 뒤에 있습니다. 또는 데이터 저장소는 프라이빗 네트워크 또는 가상 네트워크(가장 자주 원본)에 있으며 공개적으로 액세스할 수 없습니다. 가상 머신에서 호스팅되는 데이터베이스 서버도 이 시나리오에 해당합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>클라우드 시나리오

### <a name="securing-data-store-credentials"></a>데이터 저장소 자격 증명 보안

- **Azure Data Factory 관리 저장소에 암호화된 자격 증명을 저장합니다**. Data Factory는 Microsoft에서 관리하는 인증서로 암호화하여 데이터 저장소 자격 증명을 보호합니다. 이러한 인증서는 2년마다 갱신됩니다(인증서 갱신 및 자격 증명 마이그레이션 포함). Azure Storage 보안에 대한 자세한 내용은 [Azure Storage 보안 개요](../security/fundamentals/storage-overview.md)를 참조하세요.
- **Azure Key Vault에 자격 증명을 저장합니다**. 또한 데이터 저장소의 자격 증명을 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 저장할 수 있습니다. Data Factory는 활동을 실행하는 동안 자격 증명을 검색합니다. 자세한 내용은 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요.

### <a name="data-encryption-in-transit"></a>전송 중 암호화
클라우드 데이터 저장소가 HTTPS 또는 TLS를 지원하는 경우 Data Factory의 데이터 이동 서비스와 클라우드 데이터 저장소 간의 모든 데이터 전송은 보안 채널 HTTPS 또는 TLS를 통해 이루어집니다.

> [!NOTE]
> Azure SQL Database와 Azure SQL Data Warehouse에 대한 모든 연결은 항상 데이터를 데이터베이스로/에서 전송하는 중에 암호화(SSL/TLS)가 필요합니다. JSON을 사용하여 파이프라인을 작성하는 동안 암호화 속성을 추가하고 연결 문자열에서 **true**로 설정합니다. Azure Storage의 경우 연결 문자열에 **HTTPS**를 사용할 수 있습니다.

> [!NOTE]
> Oracle에서 데이터를 이동하면서 전송 중에 암호화를 사용하려면 아래 옵션 중 하나를 따르세요.
> 1. Oracle 서버에서 OAS(Oracle Advanced Security)로 이동하여 3DES(Triple-DES Encryption) 및 AES(Advanced Encryption Standard)를 지원하는 암호화 설정을 구성합니다. 자세한 내용은 [여기](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)를 참조하세요. ADF는 Oracle에 대한 연결을 설정할 때 OAS에서 구성한 암호화 방법을 사용하도록 해당 암호화 방법을 자동으로 협상합니다.
> 2. ADF에서는 연결 문자열에 EncryptionMethod=1을 추가할 수 있습니다(연결된 서비스). 이렇게 하면 SSL/TLS가 암호화 방법으로 사용됩니다. 이를 사용하려면 암호화 충돌을 방지하기 위해 Oracle 서버 쪽의 OAS에서 비SSL 암호화 설정을 사용하지 않도록 설정해야 합니다.

> [!NOTE]
> 사용된 TLS 버전은 1.2입니다.

### <a name="data-encryption-at-rest"></a>휴지 상태의 암호화
일부 데이터 저장소가 미사용 데이터 암호화를 지원합니다. 이러한 데이터 저장소에 데이터 암호화 메커니즘을 사용하는 것이 좋습니다. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Azure SQL Data Warehouse의 TDE(투명한 데이터 암호화)는 미사용 데이터에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호하는 데 도움을 줍니다. 이 동작은 클라이언트에 대해 투명합니다. 자세한 내용은 [SQL Data Warehouse에서 데이터베이스 보호](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md)를 참조하세요.

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database는 애플리케이션을 변경할 필요 없이 실시간으로 데이터 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호하는 TDE(투명한 데이터 암호화)도 지원합니다. 이 동작은 클라이언트에 대해 투명합니다. 자세한 내용은 [SQL Database 및 Data Warehouse를 위한 투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
또한 Azure Data Lake Store는 계정에 저장된 데이터에 대한 암호화를 제공합니다. 사용할 경우 Data Lake Store는 자동으로 데이터를 영구 저장하기 전에 데이터를 암호화하고, 검색하기 전에 데이터를 해독하므로 데이터에 액세스하는 클라이언트는 투명합니다. 자세한 내용은 [Azure Data Lake Store의 데이터 보안](../data-lake-store/data-lake-store-security-overview.md)을 참조하세요. 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage 및 Azure Table Storage
Azure Blob Storage 및 Azure Table Storage는 자동으로 스토리지에 영구히 저장하기 전에 데이터를 암호화하고 검색하기 전에 데이터를 해독하는 SSE(저장소 서비스 암호화)를 지원합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3은 미사용 데이터의 클라이언트 및 서버 암호화를 모두 지원합니다. 자세한 내용은 [암호화를 사용하여 데이터 보호](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html)를 참조하세요.

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift는 미사용 데이터에 대한 클러스터 암호화를 지원합니다. 자세한 내용은 [Amazon Redshift 데이터베이스 암호화](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)를 참조하세요. 

#### <a name="salesforce"></a>Salesforce
Salesforce는 모든 파일, 첨부 파일 및 사용자 정의 필드의 암호화를 허용하는 Shield Platform Encryption을 지원합니다. 자세한 내용은 [웹 서버 OAuth 인증 흐름 이해](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm)를 참조하세요.  

## <a name="hybrid-scenarios"></a>하이브리드 시나리오
하이브리드 시나리오에서는 자체 호스팅 통합 런타임을 온-프레미스 네트워크, 가상 네트워크(Azure) 또는 가상 프라이빗 클라우드(Amazon) 내부에 설치해야 합니다. 자체 호스팅 통합 런타임에서 로컬 데이터 저장소에 액세스할 수 있어야 합니다. 자체 호스팅 통합 런타임에 대한 자세한 내용은 [자체 호스팅 통합 런타임을 만들고 구성하는 방법](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)을 참조하세요. 

![자체 호스팅 통합 런타임 채널](media/data-movement-security-considerations/data-management-gateway-channels.png)

명령 채널은 Data Factory의 데이터 이동 서비스와 자체 호스팅 통합 런타임 간의 통신을 허용합니다. 통신에는 활동과 관련된 정보가 들어 있습니다. 데이터 채널은 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터를 전송하는 데 사용됩니다.    

### <a name="on-premises-data-store-credentials"></a>온-프레미스 데이터 저장소 자격 증명
자격 증명은 데이터 팩터리 내에 저장 하거나 Azure Key Vault 런타임 중에 [data factory에서 참조할](store-credentials-in-key-vault.md) 수 있습니다. 데이터 팩터리 내에 자격 증명을 저장 하는 경우 자체 호스팅 통합 런타임에서 항상 암호화 된 상태로 저장 됩니다. 
 
- **자격 증명을 로컬로 저장**합니다. JSON에서 연결 문자열 및 자격 증명과 함께 **AzDataFactoryV2LinkedService** cmdlet을 직접 사용 하는 경우 연결 된 서비스는 자체 호스팅 통합 런타임에 암호화 되어 저장 됩니다.  이 경우 자격 증명은 가장 안전 하 게 azure 백엔드 서비스를 통해 전달 되 고,이는 마지막으로 암호화 되 고 저장 되는 자체 호스팅 통합 컴퓨터에 전달 됩니다. 자체 호스팅 통합 런타임은 Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx)를 사용하여 중요한 데이터 및 자격 증명 정보를 암호화합니다.

- **Azure Key Vault에 자격 증명을 저장합니다**. 또한 데이터 저장소의 자격 증명을 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 저장할 수 있습니다. Data Factory는 활동을 실행하는 동안 자격 증명을 검색합니다. 자세한 내용은 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)을 참조하세요.

- **Azure 백 엔드를 통해 자격 증명을 자체 호스팅 통합 런타임으로 이동 하지 않고 자격 증명을 로컬에 저장**합니다. 데이터 팩터리 백 엔드를 통해 자격 증명을 전달 하지 않고 자체 호스팅 통합 런타임에서 로컬로 자격 증명을 암호화 하 고 저장 하려면 [Azure Data Factory에서 온-프레미스 데이터 저장소에 대한 자격 증명 암호화](encrypt-credentials-self-hosted-integration-runtime.md)의 단계를 따르세요. 모든 커넥터가 이 옵션을 지원합니다. 자체 호스팅 통합 런타임은 Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx)를 사용하여 중요한 데이터 및 자격 증명 정보를 암호화합니다. 

   **AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet을 사용 하 여 연결 된 서비스 자격 증명 및 연결 된 서비스의 중요 한 세부 정보를 암호화할 수 있습니다. 그런 다음 반환 된 JSON (연결 문자열의 **Encryptedcredential** 요소 포함)을 사용 하 여 **AzDataFactoryV2LinkedService** cmdlet을 사용 하 여 연결 된 서비스를 만들 수 있습니다.  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임에서 연결된 서비스를 암호화하는 동안 사용되는 포트
기본적으로 PowerShell은 보안 통신을 위해 자체 호스팅 통합 런타임을 사용 하는 컴퓨터에서 포트 8060을 사용 합니다. 필요한 경우 이 포트를 변경할 수 있습니다.  

![게이트웨이용 HTTPS 포트](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>전송 중 암호화
모든 데이터 전송은 보안 채널 HTTPS 및 TLS over TCP를 통해 Azure 서비스와의 통신 중 man-in-the-middle 공격을 방지합니다.

또한 [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) 또는 [Azure ExpressRoute](../expressroute/expressroute-introduction.md)를 사용하여 온-프레미스 네트워크와 Azure 사이의 통신 채널을 더욱 안전하게 보호할 수 있습니다.

Azure Virtual Network는 클라우드의 사용자 네트워크를 논리적으로 나타내는 표현입니다. IPSec VPN(사이트 간) 또는 ExpressRoute(프라이빗 피어링)를 설정하여 온-프레미스 네트워크를 가상 네트워크에 연결할 수 있습니다.    

다음 표는 하이브리드 데이터 이동을 위한 원본 및 대상 위치의 다양한 조합에 따라 네트워크 및 자체 호스팅 통합 런타임 구성 권장 사항을 요약한 것입니다.

| 원본      | 대상                              | 네트워크 구성                    | 통합 런타임 설정                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| 온-프레미스 | 가상 네트워크에 배포된 가상 머신 및 클라우드 서비스 | IPSec VPN(지점 및 사이트 간 또는 사이트 간) | 자체 호스팅 통합 런타임을 가상 네트워크의 Azure 가상 컴퓨터에 설치 해야 합니다.  |
| 온-프레미스 | 가상 네트워크에 배포된 가상 머신 및 클라우드 서비스 | ExpressRoute(프라이빗 피어링)           | 자체 호스팅 통합 런타임을 가상 네트워크의 Azure 가상 컴퓨터에 설치 해야 합니다.  |
| 온-프레미스 | 공개 엔드포인트가 있는 Azure 기반 서비스 | Express 경로 (Microsoft 피어 링)            | 자체 호스팅 통합 런타임을 온-프레미스 또는 Azure 가상 머신에 설치할 수 있습니다. |

다음 이미지는 ExpressRoute 및 IPSec VPN(Azure Virtual Network 사용)을 사용하여 온-프레미스 데이터베이스와 Azure 서비스 간에 데이터를 이동시키기 위한 자체 호스팅 통합 런타임의 사용법을 보여 줍니다.

**ExpressRoute**

![게이트웨이가 있는 ExpressRoute 사용](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![게이트웨이가 있는 IPSec VPN](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>IP 주소에 대한 방화벽 구성 및 허용 목록 설정

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>온-프레미스/개인 네트워크에 대한 방화벽 요구 사항  
기업에서는 기업 방화벽이 조직의 중앙 라우터에서 실행됩니다. Windows 방화벽은 자체 호스팅 통합 런타임이 설치된 로컬 컴퓨터에서 디먼으로 실행됩니다. 

다음 표는 아웃바운드 포트 및 회사 방화벽에 대한 도메인 요구 사항을 제공합니다.

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> 각 데이터 원본에서 요구 하는 대로 회사 방화벽 수준에서 도메인에 대한 포트를 관리 하거나 허용 목록을 설정 해야 할 수 있습니다. 이 표는 Azure SQL Database, Azure SQL Data Warehouse 및 Azure Data Lake Store만을 예제로 사용합니다.   

다음 표에서는 Windows 방화벽에 대한 인바운드 포트 요구 사항을 제공합니다.

| 인바운드 포트 | 설명                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | 자체 호스팅 통합 런타임에서 온-프레미스 데이터 저장소에 대한 자격 증명을 안전하게 설정하기 위해 [Azure Data Factory의 온-프레미스 데이터 저장소에 대한 자격 증명 암호화](encrypt-credentials-self-hosted-integration-runtime.md)에 설명된 대로 PowerShell Encryption cmdlet에서, 그리고 자격 증명 관리자 애플리케이션에서 필요합니다. |

![게이트웨이 포트 요구 사항](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>데이터 저장소에서 IP 구성 및 허용 목록 설정
또한 클라우드의 일부 데이터 저장소는 저장소에 액세스 하는 컴퓨터의 IP 주소를 허용 해야 합니다. 자체 호스팅 integration runtime 컴퓨터의 IP 주소가 방화벽에서 적절 하 게 허용 되는지 또는 적절 하 게 구성 되었는지 확인 합니다.

다음 클라우드 데이터 저장소를 사용 하려면 자체 호스팅 통합 런타임 컴퓨터의 IP 주소를 허용 해야 합니다. 이러한 데이터 저장소 중 일부에는 기본적으로 허용 목록이 필요 하지 않을 수 있습니다. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Storage](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

**자체 호스팅 통합 런타임을 다른 데이터 팩터리에서 공유할 수 있습니까?**

예. 자세한 내용은 [여기](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/)를 참조하세요.

**자체 호스팅 통합 런타임 작동에 필요한 포트 요구 사항은 무엇입니까?**

자체 호스팅 통합 런타임은 HTTP 기반 연결을 만들어서 인터넷에 액세스하게 합니다. 자체 호스팅 통합 런타임에서 이 연결을 만들려면 아웃바운드 포트 443이 열려야 합니다. 자격 증명 관리자 응용 프로그램에 대한 컴퓨터 수준 (회사 방화벽 수준이 아님) 에서만 인바운드 포트 8060을 엽니다. Azure SQL Database 또는 Azure SQL Data Warehouse가 원본 또는 대상으로 사용되는 경우 포트 1433도 열어야 합니다. 자세한 내용은 [방화벽 구성 및 IP 주소에 대한 허용 목록 설정](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 섹션을 참조 하세요. 


## <a name="next-steps"></a>다음 단계
Azure Data Factory 복사 활동 성능에 대한 자세한 내용은 [복사 활동 성능 및 조정 가이드](copy-activity-performance.md)를 참조하세요.

 
