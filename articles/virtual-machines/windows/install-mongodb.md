---
title: Azure에서 Windows VM에 MongoDB 설치 | Microsoft Docs
description: Windows Server 2012 R2를 실행하는 Azure VM에 Resource Manager 배포 모델을 사용하여 만든 MongoDB를 설치하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: afd8e6b47fb86985acde062af1fb38ec3af4e902
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711444"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Azure에서 Windows VM에 MongoDB를 설치 및 구성
[MongoDB](https://www.mongodb.org)는 인기 있는 오픈 소스 고성능 NoSQL 데이터베이스입니다. 이 문서에서는 Azure에서 Windows Server 2016 VM(가상 머신)에 MongoDB를 설치 및 구성하는 방법을 안내합니다. [Azure에서 Linux VM에 MongoDB를 설치](../linux/install-mongodb.md)할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건
MongoDB를 설치 및 구성하기 전에 VM을 만들고, 데이터 디스크를 추가해야 합니다. 다음 문서를 참조하여 VM을 만들고 데이터 디스크를 추가하세요.

* [Azure Portal](quick-create-portal.md) 또는 [Azure PowerShell](quick-create-powershell.md)을 사용하여 Windows Server VM을 만듭니다.
* [Azure Portal](attach-managed-disk-portal.md) 또는 [Azure PowerShell](attach-disk-ps.md)을 사용하여 Windows Server VM에 데이터 디스크를 추가합니다.

MongoDB 설치 및 구성을 시작하려면 원격 데스크톱을 사용하여 [Windows Server VM에 로그온](connect-logon.md)합니다.

## <a name="install-mongodb"></a>MongoDB 설치
> [!IMPORTANT]
> 인증 및 IP 주소 바인딩과 같은 MongoDB 보안 기능은 기본적으로 사용하도록 설정되어 있지 않습니다. MongoDB를 프로덕션 환경에 배포하기 전에 보안 기능을 사용하도록 설정해야 합니다. 자세한 내용은 [MongoDB 보안 및 인증](https://www.mongodb.org/display/DOCS/Security+and+Authentication)을 참조하세요.


1. 원격 데스크톱을 사용하여 VM에 연결한 후에는 작업 표시줄에서 Internet Explorer를 엽니다.
2. Internet Explorer가 처음으로 열리면 **권장 보안, 개인 정보 및 호환성 설정 사용**을 선택한 다음 **확인**을 클릭합니다.
3. Internet Explorer 향상된 보안 구성이 기본적으로 사용됩니다. 허용되는 사이트 목록에 MongoDB 웹 사이트를 추가합니다.
   
   * 오른쪽 위 모퉁이에서 **도구** 아이콘을 선택합니다.
   * **인터넷 옵션**에서 **보안** 탭을 선택한 다음 **신뢰할 수 있는 사이트** 아이콘을 선택합니다.
   * **사이트** 단추를 클릭합니다. 신뢰할 수 있는 사이트 목록에 *https://\*.mongodb.com*을 추가하고 대화 상자를 닫습니다.
     
     ![Internet Explorer 보안 설정 구성](./media/install-mongodb/configure-internet-explorer-security.png)
4. [MongoDB - 다운로드](https://www.mongodb.com/downloads) 페이지(https://www.mongodb.com/downloads))로 이동합니다.
5. 필요한 경우 **커뮤니티 서버** 버전을 선택한 후 *Windows Server 2008 R2 64비트 이상*의 최신 안정판을 선택합니다. 설치 관리자를 다운로드하려면 **다운로드(msi)** 를 클릭합니다.
   
    ![MongoDB 설치 관리자 다운로드](./media/install-mongodb/download-mongodb.png)
   
    다운로드가 완료되면 설치 관리자를 실행합니다.
6. 사용권 계약을 읽고 동의합니다. 메시지가 나타나면 **전체**를 선택합니다.
7. 또한 원하는 경우 MongoDB용 그래픽 인터페이스인 Compass를 설치할 수도 있습니다.
8. 마지막 화면에서 **설치**를 클릭합니다.

## <a name="configure-the-vm-and-mongodb"></a>VM 및 MongoDB 구성
1. path 변수는 MongoDB 설치 관리자를 통해 업데이트되지 않습니다. path 변수에 MongoDB `bin` 위치가 없으면 MongoDB 실행 파일을 사용할 때마다 전체 경로를 지정해야 합니다. path 변수에 위치를 추가하려면:
   
   * **시작** 메뉴를 마우스 오른쪽 단추로 클릭하고 **시스템**을 선택합니다.
   * **고급 시스템 설정**을 클릭한 다음 **환경 변수**를 클릭합니다.
   * **시스템 변수**에서 **경로**를 선택한 다음 **편집**을 클릭합니다.
     
     ![PATH 변수 구성](./media/install-mongodb/configure-path-variables.png)
     
     MongoDB `bin` 폴더에 경로를 추가합니다. 일반적으로 MongoDB는 *C:\Program Files\MongoDB*에 설치됩니다. VM의 설치 경로를 확인합니다. 다음은 `PATH` 변수에 기본 MongoDB 설치 위치를 추가하는 예제입니다.
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > `PATH` 변수에 위치를 추가하는 것을 나타내는 선행 세미콜론(`;`)을 추가합니다.

2. 데이터 디스크에 MongoDB 데이터 및 로그 디렉터리를 만듭니다. **시작** 메뉴에서 **명령 프롬프트**를 선택합니다. 다음 예에서는 F: 드라이브에 디렉터리를 만듭니다.
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. 다음 명령을 사용하여 MongoDB 인스턴스를 시작하 고, 데이터 및 로그 디렉터리를 적절하게 조정합니다.
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    MongoDB가 저널 파일을 할당하고 연결 수신을 시작할 때까지 몇 분 정도 걸릴 수 있습니다. `mongod.exe` 서버가 시작되어 저널 파일을 할당하면 모든 로그 메시지가 *F:\MongoLogs\mongolog.log* 파일로 보내집니다.
   
   > [!NOTE]
   > 명령 프롬프트는 MongoDB 인스턴스가 실행되는 동안 이 태스크에 계속 집중합니다. 명령 프롬프트 창을 열어 놓고 MongoDB를 계속 실행합니다. 또는 다음 단계에 설명된 대로 MongoDB를 서비스로 설치합니다.

4. 보다 강력한 MongoDB 경험을 원한다면 `mongod.exe`를 서비스로 설치합니다. 서비스를 만들면 MongoDB를 사용할 때마다 명령 프롬프트를 계속 실행 상태로 둘 필요가 없습니다. 다음과 같이 서비스를 만들고, 데이터 및 로그 디렉터리 경로를 적절하게 조정합니다.
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    앞의 명령은 MongoDB라는 이름의 서비스를 만들고 그에 대한 설명으로 "Mongo DB"를 붙입니다. 다음 매개 변수도 지정됩니다.
   
   * `--dbpath` 옵션은 데이터 디렉터리의 위치를 지정합니다.
   * 실행 중인 서비스는 명령 창에 결과가 표시되지 않으므로 `--logpath` 옵션을 사용하여 로그 파일을 지정해야 합니다.
   * `--logappend` 옵션은 서비스를 다시 시작할 때 기존 로그 파일에 출력을 추가하도록 지정합니다.
   
   MongoDB 서비스를 시작하려면 다음 명령을 실행합니다.
   
    ```
    net start MongoDB
    ```
   
    MongoDB 서비스를 만드는 방법에 대한 자세한 내용은 [MongoDB에 대한 Windows 서비스 구성](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service)을 참조하세요.

## <a name="test-the-mongodb-instance"></a>MongoDB 인스턴스 테스트
MongoDB를 단일 인스턴스로 실행하거나 서비스로 설치했으면, 이제 데이터베이스를 만들어 사용할 수 있습니다. MongoDB 관리 셸을 시작하려면 **시작** 메뉴에서 명령 프롬프트 창을 하나 더 열고 다음 명령을 입력합니다.

```
mongo  
```

`db` 명령으로 데이터베이스를 나열할 수 있습니다. 다음과 같이 일부 데이터를 삽입합니다.

```
db.foo.insert( { a : 1 } )
```

다음과 같이 일부 데이터를 검색합니다.

```
db.foo.find()
```

다음 예제와 유사하게 출력됩니다.

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

다음과 같이 `mongo` 콘솔을 종료합니다.

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>방화벽 및 네트워크 보안 그룹 규칙 구성
MongoDB를 설치하고 실행했으니, 원격으로 MongoDB에 연결할 수 있도록 Windows 방화벽에 있는 포트를 열어야 합니다. TCP 포트 27017을 허용하는 새 인바운드 규칙을 만들려면 관리 PowerShell 프롬프트를 열고 다음 명령을 입력합니다.

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

**고급 보안이 포함된 Windows 방화벽** 그래픽 관리 도구를 사용하여 규칙을 만들 수도 있습니다. TCP 포트 27017을 허용하는 새 인바운드 규칙을 만듭니다.

필요한 경우 기존 Azure 가상 네트워크 서브넷 외부에서 MongoDB에 액세스하도록 허용하는 네트워크 보안 그룹 규칙을 만듭니다. [Azure Portal](nsg-quickstart-portal.md) 또는 [Azure PowerShell](nsg-quickstart-powershell.md)을 사용하여 네트워크 보안 그룹 규칙을 만들 수 있습니다. Windows 방화벽 규칙과 마찬가지로 MongoDB VM의 가상 네트워크 인터페이스에 TCP 포트 27017을 허용합니다.

> [!NOTE]
> TCP 포트 27017은 MongoDB가 사용하는 기본 포트입니다. 수동으로 또는 서비스에서 `mongod.exe`를 시작하고 `--port` 매개 변수를 사용하여 이 포트를 변경할 수 있습니다. 포트를 변경하는 경우 이전 단계의 Windows 방화벽 및 네트워크 보안 그룹 규칙을 업데이트해야 합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Windows VM에 MongoDB를 설치하고 구성하는 방법을 배웠습니다. 이제 [MongoDB 설명서](https://docs.mongodb.com/manual/)(영문)의 고급 토픽에 따라 Windows VM의 MongoDB에 액세스할 수 있습니다.

