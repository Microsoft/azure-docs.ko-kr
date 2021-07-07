---
title: Azure 암호화 개요 | Microsoft Docs
description: Azure의 암호화 옵션에 대해 알아봅니다. 미사용 암호화, 비행 암호화 및 Azure Key Vault 키 관리에 대 한 정보를 참조 하세요.
services: security
author: msmbaldwin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d7d438b369c863660a032f101e466b6fadf639fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879717"
---
# <a name="azure-encryption-overview"></a>Azure 암호화 개요

이 문서에서는 Microsoft Azure에서 암호화가 사용되는 방식에 대한 개요를 제공합니다. 저장 데이터 암호화, 전송 중 암호화, Azure Key Vault를 사용한 키 관리 등 암호화의 주요 영역을 다룹니다. 각 섹션에는 상세 정보 링크가 포함되어 있습니다.

## <a name="encryption-of-data-at-rest"></a>저장소 데이터 암호화

미사용 데이터에는 모든 디지털 형식으로 된, 실제 미디어의 영구 스토리지에 상주하는 정보가 포함됩니다. 미디어로는 자기 또는 광학 미디어의 파일, 보관된 데이터 및 데이터 백업이 포함될 수 있습니다. Microsoft Azure는 파일, 디스크, Blob 및 Table Storage 등 서로 다른 요구 사항을 충족하는 다양한 데이터 스토리지 솔루션을 제공합니다. Microsoft는 또한 [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md), [Azure Cosmos DB](../../data-factory/introduction.md) 및 Azure Data Lake를 보호하기 위한 암호화도 제공합니다.

저장 데이터 암호화는 SaaS(Software-as-a-Service), PaaS(Platform-as-a-Service) 및 IaaS(Infrastructure-as-a-Service) 클라우드 모델 간의 서비스에 사용 가능합니다. 이 문서에서는 Azure의 암호화 옵션을 사용하는 데 도움이 되는 리소스를 요약하여 제공합니다.

Azure에서 미사용 데이터를 암호화하는 방법에 대한 자세한 내용은 [Azure 저장소 데이터 암호화](encryption-atrest.md)를 참조하세요.

## <a name="azure-encryption-models"></a>Azure 암호화 모델

Azure는 서비스 관리 키를 사용하는 서버 쪽 암호화, Key Vault의 고객 관리 키 또는 고객 제어 하드웨어의 고객 관리 키를 포함한 다양한 암호화 모델을 지원합니다. 클라이언트 쪽 암호화를 통해 온-프레미스 또는 다른 안전한 위치에서 키를 관리하고 저장할 수 있습니다.

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화

클라이언트 쪽 암호화는 Azure 외부에서 수행됩니다. 다음을 포함합니다.

- 고객의 데이터 센터에서 실행 중인 애플리케이션 또는 서비스 애플리케이션으로 암호화된 데이터.
- Azure에서 수신될 때 이미 암호화된 데이터입니다.

클라이언트 쪽 암호화와 함께 클라우드 서비스 공급자는 암호화 키에 액세스할 수 없으며 이 데이터를 암호 해독할 수 없습니다. 키에 대한 완벽한 제어를 유지합니다.

### <a name="server-side-encryption"></a>서버 쪽 암호화

세 가지 서버 쪽 암호화 모델은 요구 사항에 따라 선택할 수 있는 서로 다른 키 관리 특성을 제공합니다.

- **서비스 관리형 키**: 낮은 오버헤드로 제어와 편의성을 모두 제공합니다.

- **고객 관리형 키**: BYOK(Bring Your Own Key)를 지원하거나 새 키를 생성하는 등 키를 제어하도록 허용합니다.

- **고객 제어형 하드웨어의 서비스 관리형 키**: Microsoft의 제어 범위 밖에 있는 독점적인 리포지토리에서 키를 관리할 수 있습니다. 이 특성을 HYOK(Host Your Own Key)라고 합니다. 그러나 구성이 복잡하고 대부분의 Azure 서비스는 이 모델을 지원하지 않습니다.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Windows BitLocker](/previous-versions/windows/it-pro/windows-vista/cc766295(v=ws.10)) 기술과 Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 사용하여 전체 볼륨 암호화로 운영 체제 디스크와 데이터 디스크를 모두 보호하는 [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md)으로 Windows 및 Linux 가상 머신을 보호할 수 있습니다.

암호화 키 및 비밀은 사용자의 [Azure Key Vault](../../key-vault/general/overview.md) 구독에서 보호됩니다. Azure Backup 서비스를 사용하여 KEK(키 암호화) 구성으로 암호화된 VM을 백업 및 복원할 수 있습니다.

### <a name="azure-storage-service-encryption"></a>Azure Storage 서비스 암호화

Azure Blob Storage와 Azure 파일 공유의 미사용 데이터는 서버 쪽 시나리오와 클라이언트 쪽 시나리오 모두에서 암호화할 수 있습니다.

[Azure SSE(Storage 서비스 암호화)](../../storage/common/storage-service-encryption.md)는 데이터를 저장하기 전에 자동으로 암호화하고, 데이터를 검색할 때 자동으로 암호 해독할 수 있습니다. 사용자는 이 프로세스를 전혀 인식하지 못합니다. Storage Service Encryption은 가장 강력한 블록 암호 중 하나인 256비트 [AES(Advanced Encryption Standard) 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용합니다. AES는 암호화, 암호 해독 및 키 관리를 투명하게 처리합니다.

### <a name="client-side-encryption-of-azure-blobs"></a>Azure Blob의 클라이언트 쪽 암호화

다양한 방법으로 Azure BLOB의 클라이언트 쪽 암호화를 수행할 수 있습니다.

.NET NuGet 패키지용 Azure Storage 클라이언트 라이브러리를 사용하여 Azure 스토리지에 데이터를 업로드하기 전에 클라이언트 애플리케이션 내에서 데이터를 암호화할 수 있습니다.

.NET NuGet 패키지용 Azure Storage 클라이언트 라이브러리에 대한 자세한 내용과 다운로드 방법은 [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage) 문서를 참조하세요.

Key Vault와 함께 클라이언트 쪽 암호화를 사용하면 Azure Storage 클라이언트 SDK로 생성되는 일회성 대칭 CEK(콘텐츠 암호화 키)를 사용하여 데이터가 암호화됩니다. CEK는 대칭 키 또는 비대칭 키 쌍 중 하나일 수 있는 KEK(키 암호화 키)를 사용하여 암호화됩니다. 로컬로 관리하거나 Key Vault에 저장할 수 있습니다. 암호화된 데이터는 그 후 Azure Storage에 업로드됩니다.

Key Vault를 사용한 클라이언트 쪽 암호화에 대해 자세히 알아보고 방법 지침을 시작하려면 [자습서: Key Vault를 사용하여 Azure Storage에서 BLOB 암호화 및 암호 해독](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)을 참조하세요.

마지막으로 Java용 Azure Storage 클라이언트 라이브러리를 사용하여 Azure Storage에 데이터를 업로드하기 전에 클라이언트 쪽 암호화를 수행하고 클라이언트에 데이터가 다운로드될 때 암호 해독할 수 있습니다. 또한 이 라이브러리는 스토리지 계정 키 관리를 위해 [Key Vault](https://azure.microsoft.com/services/key-vault/)와의 통합을 지원합니다.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Azure SQL Database에서 저장소 데이터 암호화

[Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)는 관계형 데이터, 공간, JSON 및 XML과 같은 구조를 지원하는 Azure의 범용 관계형 데이터베이스 서비스입니다. SQL Database는 TDE(투명한 데이터 암호화) 기능을 통한 서버 쪽 암호화와 Always Encrypted 기능을 통한 클라이언트 쪽 암호화를 모두 지원합니다.

#### <a name="transparent-data-encryption"></a>투명한 데이터 암호화

[Tde](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) 는 복구 중 가용성을 위해 데이터베이스 부트 레코드에 저장 된 Dek (데이터베이스 암호화 키)를 사용 하 여 [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 데이터 파일을 실시간으로 암호화 하는 데 사용 됩니다.

TDE는 AES 및 3DES(Triple Data Encryption Standard) 암호화 알고리즘을 사용하여 데이터 및 로그 파일을 보호합니다. 데이터베이스 파일의 암호화는 페이지 수준에서 수행됩니다. 암호화된 데이터베이스에서 페이지는 디스크에 기록되기 전에 암호화되고 메모리로 읽어올 때 암호 해독됩니다. 기본적으로 TDE는 이제 새로 만든 Azure SQL 데이터베이스에서 사용하도록 설정됩니다.

#### <a name="always-encrypted-feature"></a>Always Encrypted 기능

Azure SQL의 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 기능을 사용하면 클라이언트 애플리케이션의 데이터를 Azure SQL Database에 저장하기 전에 암호화할 수 있습니다. 또한 온-프레미스 데이터베이스 관리를 제3자에게 위임하여 데이터를 소유하고 볼 수 있는 사람과 데이터를 관리하지만 액세스할 수 없는 사람을 분리할 수 있습니다.

#### <a name="cell-level-or-column-level-encryption"></a>셀 수준 또는 열 수준 암호화

Azure SQL Database를 사용하면 Transact-SQL을 사용하여 데이터 열에 대칭 암호화를 적용할 수 있습니다. 이 방식을 [셀 수준 암호화 또는 CLE(열 수준 암호화)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)라고 합니다. 이 암호화를 사용하여 서로 다른 암호화 키를 사용하여 데이터의 특정 열 또는 특정 셀까지도 암호화할 수 있기 때문입니다. 이렇게 하면 페이지에서 데이터를 암호화하는 TDE보다 더 세분화된 암호화 기능이 제공됩니다.

CLE에는 대칭 또는 비대칭 키를 사용하고 인증서의 공개 키 또는 3DES를 사용하는 암호로 데이터를 암호화하는 데 사용할 수 있는 기본 제공 함수가 있습니다.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB 데이터베이스 암호화

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md)는 전 세계에 배포된 Microsoft의 멀티모델 데이터베이스입니다. 비휘발성 스토리지(반도체 드라이브)의 Cosmos DB에 저장된 사용자 데이터는 기본적으로 암호화됩니다. 이를 설정하거나 해제하는 컨트롤은 없습니다. 저장 데이터 암호화는 보안 키 스토리지 시스템, 암호화된 네트워크 및 암호화 API를 비롯한 수많은 보안 기술을 사용하여 구현되었습니다. 암호화 키는 Microsoft에서 관리하며 Microsoft 내부 지침에 따라 순환됩니다.

### <a name="at-rest-encryption-in-data-lake"></a>Data Lake의 미사용 암호화

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md)는 요구 사항이나 스키마의 공식 정의에 앞서 모든 형식의 데이터를 단일 위치에 수집하는 기업 전체의 리포지토리입니다. Data Lake Store는 계정 생성 중에 설정된 "기본적으로 켜져 있는" 미사용 데이터의 투명한 암호화를 지원합니다. 기본적으로 Azure Data Lake Store가 자동으로 키를 관리하지만, 사용자가 직접 관리할 수 있는 옵션이 제공됩니다.

세 가지 유형의 키가 데이터 암호화 및 암호 해독에 사용됩니다. 즉, MEK(마스터 암호화 키), DEK(데이터 암호화 키) 및 BEK(블록 암호화 키)입니다. MEK는 영구 미디어에 저장된 DEK를 암호화하는 데 사용되며 BEK는 DEK 및 데이터 블록에서 파생됩니다. 사용자 고유 키를 관리하는 경우 MEK를 순환시킬 수 있습니다.

## <a name="encryption-of-data-in-transit"></a>전송 중 데이터 암호화

Azure에서는 데이터가 한 위치에서 다른 위치로 이동함에 따라 데이터를 비공개로 유지하기 위한 다양한 메커니즘을 제공합니다.

### <a name="data-link-layer-encryption-in-azure"></a>Azure의 데이터 링크 계층 암호화

Azure 고객 트래픽이 데이터 센터 간에 이동 하는 경우 (또는 Microsoft를 대신 하 여), [IEEE 802.1 AE MAC 보안 표준](https://1.ieee802.org/security/802-1ae/) (macsec 라고도 함)을 사용 하는 데이터 링크 계층 암호화 방법이 기본 네트워크 하드웨어에 대 한 지점에서 지점으로 적용 됩니다. 패킷이 전송 되기 전에 장치에서 암호화 및 암호 해독 되어 물리적 "중간자" 또는 스누핑/wiretapping 공격을 방지 합니다. 이 기술은 네트워크 하드웨어 자체에 통합 되어 있으므로 네트워크 하드웨어에 대 한 회선 속도로 암호화 된 링크 대기 시간 증가를 제공 하지 않습니다. 이 MACsec 암호화는 지역 내 또는 지역 내에서 이동 하는 모든 Azure 트래픽에 대해 기본적으로 설정 되며, 사용 하도록 설정 하는 데 필요한 작업은 없습니다. 

### <a name="tls-encryption-in-azure"></a>Azure의 TLS 암호화

Microsoft는 클라우드 서비스와 고객 간에 이동할 때 TLS ( [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) ) 프로토콜을 사용 하 여 데이터를 보호할 수 있는 기능을 고객에 게 제공 합니다. Microsoft 데이터 센터는 Azure 서비스에 연결되는 클라이언트 시스템과 TLS 연결을 협상합니다. TLS는 강력한 인증, 메시지 개인 정보 및 무결성(메시지 변조, 가로채기 및 위조의 검색 가능), 상호 운용성, 알고리즘 유연성, 배포 및 사용 편의성을 제공합니다.

[PFS(Perfect Forward Secrecy)](https://en.wikipedia.org/wiki/Forward_secrecy)는 고유한 키로 고객의 클라이언트 시스템과 Microsoft 클라우드 서비스 간의 연결을 보호합니다. 또한 연결은 RSA 기반 2,048비트 암호화 키 길이를 사용합니다. 이러한 조합을 통해 누군가가 전송 중 데이터를 가로채거나 액세스하기 어렵게 할 수 있습니다.

### <a name="azure-storage-transactions"></a>Azure Storage 트랜잭션

Azure Portal을 통해 Azure Storage와 상호 작용하는 경우 모든 트랜잭션이 HTTPS를 통해 발생합니다. 또한 HTTPS를 통한 Storage REST API를 사용하여 Azure Storage와 상호 작용할 수 있습니다. 스토리지 계정에 보안 전송을 사용하도록 설정하여 스토리지 계정의 개체에 액세스하는 REST API를 호출할 때 HTTPS를 적용할 수 있습니다.

Azure Storage 개체에 대한 액세스를 위임하는 데 사용할 수 있는 [SAS(공유 액세스 서명)](../../storage/common/storage-sas-overview.md)에는 공유 액세스 서명을 사용할 때 HTTPS 프로토콜만 사용할 수 있도록 지정하는 옵션이 포함됩니다. 이 방식은 SAS 토큰이 있는 링크를 보내는 모든 사용자가 적절한 프로토콜을 사용할 수 있습니다.

Azure 파일 공유에 액세스하는 데 사용되는 [SMB 3.0](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn551363(v=ws.11)#BKMK_SMBEncryption)은 암호화를 지원하며 Windows Server 2012 R2, Windows 8, Windows 8.1 및 Windows 10에서 사용할 수 있습니다. 지역 간 액세스 및 데스크톱 액세스도 허용합니다.

클라이언트 쪽 암호화는 Azure Storage 인스턴스에 전송되기 전에 데이터를 암호화하므로 네트워크 간에 이동할 때 암호화됩니다.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Azure 가상 네트워크를 통해 SMB 암호화 

Windows Server 2012 이상을 실행하는 VM에서 [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server)을 사용하면 Azure Virtual Networks를 통해 전송되는 데이터를 암호화하여 데이터 전송 보안을 강화할 수 있습니다. 데이터를 암호화하면 변조 및 도청 공격을 방어하는 데 도움이 됩니다. 관리자는 전체 서버 또는 특정 공유에만 SMB 암호화를 사용할 수 있습니다.

기본적으로 공유 또는 서버에 SMB 암호화를 사용하도록 설정한 후에는 SMB 3.0 클라이언트만 암호화된 공유에 액세스할 수 있습니다.

## <a name="in-transit-encryption-in-vms"></a>VM의 전송 중 암호화

Windows를 실행 하는 Vm 간에 전송 되는 데이터는 연결의 특성에 따라 여러 가지 방법으로 암호화할 수 있습니다.

### <a name="rdp-sessions"></a>RDP 세션

Windows 클라이언트 컴퓨터 또는 RDP 클라이언트가 설치된 Mac에서 [RDP(원격 데스크톱 프로토콜)](/windows/win32/termserv/remote-desktop-protocol)를 사용하여 VM에 연결하고 로그인할 수 있습니다. RDP 세션에서 네트워크를 통해 전송 중인 데이터는 TLS로 보호할 수 있습니다.

Azure에서 원격 데스크톱을 사용하여 Azure의 Linux VM에 연결할 수도 있습니다.

### <a name="secure-access-to-linux-vms-with-ssh"></a>SSH로 Linux VM에 보안 액세스

[SSH(Secure Shell)](../../virtual-machines/linux/ssh-from-windows.md)를 사용하여 Azure에서 실행 중인 Linux VM에 연결하면 원격 관리가 가능합니다. SSH는 안전하지 않은 연결을 통해 안전하게 로그인할 수 있도록 암호화된 연결 프로토콜입니다. Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. 인증에 SSH 키를 사용하면 로그인 시 암호가 필요 없습니다. SSH는 인증에 퍼블릭/프라이빗 키 쌍(비대칭 암호화)을 사용합니다.

## <a name="azure-vpn-encryption"></a>Azure VPN 암호화

네트워크를 통해 전송되는 데이터의 개인 정보를 보호하기 위해 보안 터널을 만드는 가상 사설망을 통해 Azure에 연결할 수 있습니다.

### <a name="azure-vpn-gateways"></a>Azure VPN 게이트웨이

[Azure VPN 게이트웨이](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)는 공용 연결을 통해 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 전송하거나 가상 네트워크 간에 트래픽을 전송하는 데 사용할 수 있습니다.

사이트 간 VPN은 전송 암호화에 [IPsec](https://en.wikipedia.org/wiki/IPsec)을 사용합니다. Azure VPN Gateway는 기본 제안 집합을 사용합니다. Azure 기본 정책 집합보다는 특정 암호화 알고리즘 및 키 강도를 사용하여 사용자 지정 IPsec/IKE 정책을 사용하도록 Azure VPN Gateway를 구성할 수 있습니다.

### <a name="point-to-site-vpns"></a>지점 및 사이트 간 VPN

지점 및 사이트 간 VPN을 통해 개별 클라이언트 컴퓨터에서 Azure 가상 네트워크에 액세스가 가능합니다. [SSTP(Secure Socket Tunneling Protocol)](/previous-versions/technet-magazine/cc162322(v=msdn.10))는 VPN 터널을 만드는 데 사용됩니다. 방화벽을 트래버스할 수 있습니다(터널은 HTTPS 연결로 표시됨). 지점 및 사이트 간 연결에 사용자 고유의 내부 PKI(공개 키 인프라) 루트 CA(인증 기관)를 사용할 수 있습니다.

인증서 인증 또는 PowerShell을 사용하여 Azure Portal에서 가상 네트워크에 지점 및 사이트 간 VPN 연결을 구성할 수 있습니다.

지점 및 사이트 간 VPN으로 Azure 가상 네트워크에 연결하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

[인증서 인증을 사용하여 가상 네트워크에 지점 및 사이트 간 연결 구성: Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[인증서 인증을 사용하여 가상 네트워크에 지점 및 사이트 간 연결 구성: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>사이트 간 VPN 

사이트 간 VPN 게이트웨이 연결을 사용하여 IPsec/IKE(IKEv1 또는 IKEv2) VPN 터널을 통해 온-프레미스 네트워크를 Azure 가상 네트워크에 연결할 수 있습니다. 이 연결 유형은 외부 연결 공용 IP 주소가 할당된 온-프레미스 VPN 디바이스가 필요합니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 가상 네트워크에 사이트 간 VPN 연결을 구성할 수 있습니다.

자세한 내용은 다음을 참조하세요.

[Azure Portal에서 사이트 간 연결 만들기](../../vpn-gateway/tutorial-site-to-site-portal.md)

[PowerShell에서 사이트 간 연결 만들기](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[CLI를 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Data Lake의 전송 중 암호화

전송되는 데이터(즉, 동작 중인 데이터)는 항상 Data Lake Store에서 암호화됩니다. 영구 미디어에 저장하기 전에 데이터를 암호화하는 것 외에도, 전송 중에도 HTTPS를 사용하여 데이터를 항상 안전하게 보호합니다. HTTPS는 Data Lake Store REST 인터페이스에 지원되는 유일한 프로토콜입니다.

Data Lake의 전송 중 데이터 암호화에 대한 자세한 내용은 [Data Lake Store의 데이터 암호화](../../data-lake-store/data-lake-store-encryption.md)를 참조하세요.

## <a name="key-management-with-key-vault"></a>Key Vault으로 키 관리

키를 적절하게 보호 및 관리하지 않으면 암호화가 쓸모 없게 렌더링됩니다. Key Vault는 클라우드 서비스에 사용되는 암호화 키에 대한 액세스를 관리 및 제어할 수 있는 Microsoft의 권장 솔루션입니다. Azure Active Directory 계정을 통해 키에 액세스하기 위한 권한을 서비스 또는 사용자에게 할당할 수 있습니다.

Key Vault는 조직이 HSM(하드웨어 보안 모듈) 및 키 관리 소프트웨어를 구성, 패치, 유지 관리해야 하는 부담을 덜어줍니다. Key Vault를 사용하면 제어가 유지됩니다. Microsoft는 키를 절대로 볼 수 없고 애플리케이션은 키에 직접 액세스할 수 없습니다. 또한 HSM에서 키를 가져오거나 생성할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 보안 개요](./overview.md)
- [Azure 네트워크 보안 개요](network-overview.md)
- [Azure 데이터베이스 보안 개요](../../azure-sql/database/security-overview.md)
- [Azure 가상 머신 보안 개요](virtual-machines-overview.md)
- [저장 데이터 암호화](encryption-atrest.md)
- [데이터 보안 및 암호화 모범 사례](data-encryption-best-practices.md)