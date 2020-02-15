---
title: 도메인 독립적 작업 그룹 가용성 그룹 구성
description: Azure의 SQL Server 가상 머신에서 Active Directory 도메인 독립적 작업 그룹 Always On 가용성 그룹을 구성 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122676"
---
# <a name="configure-a-workgroup-availability-group"></a>작업 그룹 가용성 그룹 구성 

이 문서에서는 Always On 가용성 그룹을 사용 하 여 Active Directory 도메인 독립적 클러스터를 만드는 데 필요한 단계를 설명 합니다. 이를 작업 그룹 클러스터 라고도 합니다. 이 문서에서는 작업 그룹 및 가용성 그룹의 준비 및 구성과 관련 된 단계를 중점적으로 설명 하 고, 클러스터를 만들거나 가용성 그룹을 배포 하는 방법 등의 다른 문서에서 설명 하는 단계를 주석을 다. 


## <a name="prerequisites"></a>사전 요구 사항

작업 그룹 가용성 그룹을 구성 하려면 다음이 필요 합니다.
- 하나 이상의 Windows Server 2016 (또는 이상)를 2016 SQL Server 실행 하는 두 개 이상의 가상 컴퓨터는 동일한 가용성 집합 또는 다른 가용성 영역에 배포 된 고정 IP 주소를 사용 합니다. 
- 서브넷에 사용 가능한 IP 주소가 4 개 이상 있는 로컬 네트워크입니다. 
- SQL Server 내에서 sysadmin 권한이 있는 관리자 그룹의 각 컴퓨터에 대 한 계정입니다. 
- 포트 열기: TCP 1433, TCP 5022, TCP 59999. 

참조를 위해 다음 매개 변수는이 아티클에서 사용 되지만 필요에 따라 수정할 수 있습니다. 

| **이름** | **매개 변수** |
| :------ | :---------------------------------- |
| **눌러**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **클러스터 이름** | AGWGAG (10.0.0.6 입력) |
| **수신기** | AGListener (10.0.0.7) | 
| **DNS 접미사** | ag.wgcluster.example.com | 
| **작업 그룹 이름** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>DNS 접미사 설정 

이 단계에서는 두 서버에 대 한 DNS 접미사를 구성 합니다. `ag.wgcluster.example.com`)을 입력합니다. 이렇게 하면 연결 하려는 개체의 이름을 네트워크 내의 정규화 된 주소 (예: `AGNode1.ag.wgcluster.example.com`)로 사용할 수 있습니다. 

DNS 접미사를 구성 하려면 다음 단계를 수행 합니다.

1. 첫 번째 노드로 RDP를 서버 관리자를 엽니다. 
1. **로컬 서버** 를 선택 하 고 **컴퓨터 이름**아래에서 가상 컴퓨터의 이름을 선택 합니다. 
1. **이 컴퓨터의 이름을 바꾸려면** **변경 ...** 을 선택 합니다. 
1. 작업 그룹 이름 이름을 `AGWORKGROUP`와 같은 의미 있는 이름으로 변경 합니다. 

   ![작업 그룹 이름 변경](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. **자세히 ...** 를 선택 하 여 **DNS 접미사 및 NetBIOS 컴퓨터 이름** 대화 상자를 엽니다. 
1. **이 컴퓨터의 기본 dns 접미사**(예: `ag.wgcluster.example.com`) 아래에 dns 접미사 이름을 입력 한 다음 **확인**을 선택 합니다. 

   ![DNS 접미사 추가](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. 이제 **전체 컴퓨터 이름** 에 DNS 접미사가 표시 되는지 확인 한 다음 **확인** 을 선택 하 여 변경 내용을 저장 합니다. 

   ![DNS 접미사 추가](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. 서버를 다시 부팅 하 라는 메시지가 표시 되 면 서버를 다시 부팅 합니다. 
1. 가용성 그룹에 사용할 다른 모든 노드에서 이러한 단계를 반복 합니다. 

## <a name="edit-host-file"></a>호스트 파일 편집

Active directory는 없기 때문에 windows 연결을 인증할 수 있는 방법이 없습니다. 따라서 텍스트 편집기를 사용 하 여 호스트 파일을 편집 하 여 트러스트를 할당 합니다. 

호스트 파일을 편집 하려면 다음 단계를 수행 합니다.

1. 가상 컴퓨터에 RDP를 로그인 합니다. 
1. **파일 탐색기** 를 사용 하 여 `c:\windows\system32\drivers\etc`로 이동 합니다. 
1. **Hosts** 파일을 마우스 오른쪽 단추로 클릭 하 고 **메모장** (또는 다른 텍스트 편집기)을 사용 하 여 파일을 엽니다.
1. 파일의 끝에서 다음과 같이 각 노드, 가용성 그룹 및 수신기에 대 한 항목을 `IP Address, DNS Suffix #comment` 형식으로 추가 합니다. 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![IP 주소, 클러스터 및 수신기에 대 한 항목을 호스트 파일에 추가 합니다.](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>권한 설정

사용 권한을 관리 하는 Active Directory 없으므로 builtin이 아닌 로컬 관리자 계정이 클러스터를 만들도록 수동으로 허용 해야 합니다. 

이렇게 하려면 모든 노드의 관리 PowerShell 세션에서 다음 PowerShell cmdlet을 실행 합니다. 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>장애 조치 클러스터 만들기

이 단계에서는 장애 조치 (failover) 클러스터를 만듭니다. 이러한 단계를 잘 모르는 경우 [장애 조치 (failover) 클러스터 자습서](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)에서 수행할 수 있습니다.

자습서와 작업 그룹 클러스터에 대해 수행할 작업의 중요 한 차이점은 다음과 같습니다.
- **저장소**를 선택 취소 하 고 클러스터 유효성 검사를 실행할 때 **스토리지 공간 다이렉트** 합니다. 
- 클러스터에 노드를 추가 하는 경우 다음과 같이 정규화 된 이름을 추가 합니다.
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- **클러스터에 사용할 수 있는 모든 저장소를 추가**합니다 .를 선택 취소 합니다. 

클러스터가 만들어지면 고정 클러스터 IP 주소를 할당 합니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 노드 중 하나에서 **장애 조치(Failover) 클러스터 관리자**을 열고, 클러스터를 선택 하 고, **클러스터 코어 리소스** 에서 **이름: \<clusternam >** 을 마우스 오른쪽 단추로 클릭 한 다음 **속성**을 선택 합니다. 

   ![클러스터 이름에 대 한 시작 속성](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. **Ip** 주소에서 ip 주소를 선택 하 고 **편집**을 선택 합니다. 
1. **정적 사용**을 선택 하 고 클러스터의 IP 주소를 제공한 다음 **확인**을 선택 합니다. 

   ![클러스터에 대 한 고정 IP 주소를 제공 합니다.](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. 설정이 올바른지 확인 한 다음 **확인** 을 선택 하 여 저장 합니다.

   ![클러스터 속성 확인](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>클라우드 감시 만들기 

이 단계에서는 클라우드 공유 감시를 구성 합니다. 단계에 익숙하지 않은 경우 [장애 조치 (failover) 클러스터 자습서](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness)를 참조 하세요. 

## <a name="enable-availability-group-feature"></a>가용성 그룹 기능 사용 

이 단계에서는 가용성 그룹 기능을 사용 하도록 설정 합니다. 단계에 익숙하지 않은 경우 [가용성 그룹 자습서](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups)를 참조 하세요. 

## <a name="create-keys-and-certificate"></a>키 및 인증서 만들기

이 단계에서는 SQL 로그인이 암호화 된 끝점에서 사용 하는 인증서를 만듭니다. `c:\certs`와 같이 인증서 백업을 저장할 각 노드에 폴더를 만듭니다. 

첫 번째 노드를 구성 하려면 다음 단계를 수행 합니다. 

1. **SQL Server Management Studio** 를 열고 `AGNode1`와 같은 첫 번째 노드에 연결 합니다. 
1. **새 쿼리** 창을 열고 복잡 하 고 안전한 암호로 업데이트 한 후 다음 Transact-sql (t-sql) 문을 실행 합니다.

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. 다음으로, HADR 끝점을 만들고이 Transact-sql (T-sql) 문을 실행 하 여 인증에 인증서를 사용 합니다.

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **파일 탐색기** 를 사용 하 여 `c:\certs`와 같이 인증서가 있는 파일 위치로 이동 합니다. 
1. 첫 번째 노드에서 `AGNode1Cert.crt`와 같은 인증서의 복사본을 수동으로 만들고 두 번째 노드의 동일한 위치로 전송 합니다. 

두 번째 노드를 구성 하려면 다음 단계를 수행 합니다. 

1. `AGNode2`와 같은 **SQL Server Management Studio**를 사용 하 여 두 번째 노드에 연결 합니다. 
1. **새 쿼리** 창에서 복잡 하 고 안전한 암호로 업데이트 한 후 다음 Transact-sql (t-sql) 문을 실행 합니다. 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. 다음으로, HADR 끝점을 만들고이 Transact-sql (T-sql) 문을 실행 하 여 인증에 인증서를 사용 합니다.

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **파일 탐색기** 를 사용 하 여 `c:\certs`와 같이 인증서가 있는 파일 위치로 이동 합니다. 
1. `AGNode2Cert.crt`와 같은 인증서의 복사본을 두 번째 노드에서 수동으로 만들고 첫 번째 노드의 동일한 위치로 전송 합니다. 

클러스터에 다른 노드가 있는 경우 이러한 단계를 반복 하 여 각 인증서 이름을 수정 합니다. 

## <a name="create-logins"></a>로그인 만들기

인증서 인증은 여러 노드 간에 데이터를 동기화 하는 데 사용 됩니다. 이를 허용 하려면 다른 노드에 대 한 로그인을 만들고, 로그인에 대 한 사용자를 만들고, 백업 된 인증서를 사용할 로그인에 대 한 인증서를 만든 다음, 미러링 끝점에 connect를 부여 합니다. 

이렇게 하려면 먼저 `AGNode1`와 같이 첫 번째 노드에서 다음 Transact-sql (T-sql) 쿼리를 실행 합니다. 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

그런 다음 두 번째 노드에서 `AGNode2`와 같은 Transact-sql (Transact-sql) 쿼리를 실행 합니다. 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

클러스터에 다른 노드가 있는 경우 이러한 단계를 반복 하 여 각 인증서와 사용자 이름을 수정 합니다. 

## <a name="configure-availability-group"></a>가용성 그룹 구성

이 단계에서는 가용성 그룹을 구성 하 고 데이터베이스를 추가 합니다. 지금은 수신기를 만들지 마십시오. 단계를 잘 모르는 경우 [가용성 그룹 자습서](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group)를 참조 하세요. 장애 조치 (failover) 및 장애 복구 (failback)를 시작 하 여 모든 항목이 제대로 작동 하는지 확인 해야 합니다. 

   > [!NOTE]
   > 동기화 프로세스 중에 오류가 발생 한 경우 `NT AUTHORITY\SYSTEM` sysadmin 권한을 부여 하 여 첫 번째 노드 (예: 일시적으로 `AGNode1`)에서 클러스터 리소스를 만들어야 할 수 있습니다. 

## <a name="configure-load-balancer"></a>부하 분산 장치 구성

이 마지막 단계에서는 [Azure Portal](virtual-machines-windows-portal-sql-alwayson-int-listener.md) 또는 [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) 을 사용 하 여 부하 분산 장치를 구성 합니다.


## <a name="next-steps"></a>다음 단계

[AZ SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) 를 사용 하 여 가용성 그룹을 구성할 수도 있습니다. 


