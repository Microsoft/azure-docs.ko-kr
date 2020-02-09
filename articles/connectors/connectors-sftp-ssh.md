---
title: SSH를 사용 하 여 SFTP 서버에 연결
description: SSH 및 Azure Logic Apps를 사용하여 SFTP 서버에 대한 파일을 모니터링, 만들기, 관리, 전송 및 수신하는 작업 자동화
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 54a1d1183ac16f5ec3db5477cda75c6e1a776b3d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786890"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SSH 및 Azure Logic Apps를 사용하여 SFTP 파일 모니터링, 만들기 및 관리

[SSH(Secure Shell)](https://www.ssh.com/ssh/protocol/) 프로토콜을 사용하여 [SFTP(보안 파일 전송 프로토콜)](https://www.ssh.com/ssh/sftp/) 서버에서 파일을 모니터링, 만들기, 전송 및 수신하는 작업을 자동화하려면 Azure Logic Apps 및 SFTP-SSH 커넥터를 사용하여 통합 워크플로를 빌드하고 자동화할 수 있습니다. SFTP는 신뢰할 수 있는 데이터 스트림을 통해 파일 액세스, 파일 전송 및 파일 관리를 제공하는 네트워크 프로토콜입니다. 다음은 자동화할 수 있는 몇 가지 예제 작업입니다.

* 파일이 추가되거나 변경될 때 모니터링합니다.
* 파일 가져오기/만들기/복사/이름 바꾸기/업데이트/나열/삭제를 수행합니다.
* 폴더를 만듭니다.
* 파일 콘텐츠 및 메타데이터를 가져옵니다.
* 보관을 폴더로 추출합니다.

SFTP 서버에서 이벤트를 모니터링하는 트리거를 사용하고 다른 작업에서 출력을 사용하도록 할 수 있습니다. SFTP 서버에서 다양한 작업을 수행하는 작업을 사용할 수 있습니다. 또한 논리 앱의 다른 작업에서 SFTP 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어 정기적으로 SFTP 서버에서 파일을 검색하는 경우 Office 365 Outlook 커넥터 또는 Outlook.com 커넥터를 사용하여 해당 파일 및 해당 콘텐츠에 대한 이메일 경고를 보낼 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토하세요.

SFTP-SSH 커넥터와 SFTP 커넥터 간의 차이점을 보려면이 항목의 뒷부분에 있는 [sftp-ssh와 Sftp 비교](#comparison) 섹션을 검토 하세요.

## <a name="limits"></a>Limits

* 기본적으로 SFTP-SSH 작업은 1gb *또는 작은* 파일을 읽거나 쓸 수 있습니다. 15MB 보다 큰 파일을 처리 하기 위해 SFTP-SSH 작업은 [메시지 청크](../logic-apps/logic-apps-handle-large-messages.md)를 지원 합니다. 단, 파일 복사 작업은 파일 복사 작업의 경우를 제외 하 고는 15mb 파일만 처리할 수 있습니다. **파일 콘텐츠 가져오기** 작업은 메시지 청크를 암시적으로 사용 합니다.

* SFTP-SSH 트리거는 청크를 지원 하지 않습니다. 파일 콘텐츠를 요청 하는 경우 트리거는 15MB 미만의 파일만 선택 합니다. 64MB 보다 큰 파일을 가져오려면 대신 다음 패턴을 따릅니다.

  * **파일이 추가 되거나 수정 된 경우 (속성에만 해당)** 와 같이 파일 속성을 반환 하는 SFTP-SSH 트리거를 사용 합니다.

  * 전체 파일을 읽고 메시지 청크를 암시적으로 사용 하는 SFTP-SSH **파일 콘텐츠 가져오기** 작업을 사용 하 여 트리거를 수행 합니다.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH와 SFTP 비교

다음은 SFTP-SSH 커넥터와 SFTP 커넥터 간의 다른 주요 차이점입니다. 여기에서 SFTP-SSH 커넥터는 다음과 같은 기능을 갖습니다.

* 는 .NET을 지 원하는 SSH (open source Secure Shell) 라이브러리인 [SSH.NET library](https://github.com/sshnet/SSH.NET)를 사용 합니다.

* 기본적으로 SFTP-SSH 작업은 1gb *또는 작은* 파일을 읽거나 쓸 수 있습니다.

  15MB 보다 큰 파일을 처리 하기 위해 SFTP-SSH 작업에서 [메시지 청크](../logic-apps/logic-apps-handle-large-messages.md)를 사용할 수 있습니다. 그러나 파일 복사 작업은 메시지 청크를 지원 하지 않기 때문에 15MB 파일만 지원 합니다. SFTP-SSH 트리거는 청크를 지원 하지 않습니다. 용량이 많은 파일을 업로드 하려면 SFTP 서버의 루트 폴더에 대한 읽기 및 쓰기 권한이 모두 필요 합니다.

* SFTP 서버의 지정된 경로에 폴더를 만드는 **폴더 만들기** 작업을 제공합니다.

* SFTP 서버의 파일 이름을 바꾸는 **파일 이름 바꾸기** 작업을 제공합니다.

* *최대 1시간 동안* SFTP 서버에 대한 연결을 캐시합니다. 그러면 서버에 대한 연결에서 시도 수가 감소하며 성능이 개선됩니다. 이 캐싱 동작에 대한 기간을 설정하려면 SFTP 서버의 SSH 구성에서 [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) 속성을 편집합니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱에서 SFTP 계정에 액세스하도록 하는 SFTP 서버 주소 및 계정 자격 증명 SSH 프라이빗 키 및 SSH 프라이빗 키 암호에 대한 액세스도 필요합니다. 대량 파일을 업로드할 때 청크를 사용 하려면 SFTP 서버의 루트 폴더에 대한 읽기 및 쓰기 권한이 모두 필요 합니다. 그렇지 않으면 "401 권한 없음" 오류가 발생 합니다.

  > [!IMPORTANT]
  >
  > SFTP-SSH 커넥터는 이러한 프라이빗 키 형식, 알고리즘 및 지문*만*을 지원합니다.
  >
  > * **개인 키 형식**: OpenSSH 및 Ssh.com 형식의 RSA (Rivest rivest-shamir-adleman rivest-shamir-adleman) 및 DSA (디지털 서명 알고리즘) 키입니다. 개인 키가 PuTTY (ppk) 파일 형식인 경우 먼저 [키를 OpenSSH (pem) 파일 형식으로 변환](#convert-to-openssh)합니다.
  >
  > * **암호화 알고리즘**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC 및 AES-256-CBC
  >
  > * **지문**: MD5
  >
  > 논리 앱에 원하는 SFTP-SSH 트리거 또는 작업을 추가한 후에는 SFTP 서버에 대한 연결 정보를 제공 해야 합니다. 이 연결에 대한 SSH 개인 키를 제공 하는 경우 ***키를 수동으로 입력 하거나 편집 하지 마십시오***.이로 인해 연결이 실패할 수 있습니다. 대신 SSH 개인 키 파일에서 ***키를 복사*** 하 고 해당 키를 연결 정보에 ***붙여넣어야*** 합니다. 
  > 자세한 내용은이 문서 뒷부분의 [SSH를 사용 하 여 SFTP에 연결](#connect) 섹션을 참조 하세요.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* SFTP 계정에 액세스하려는 논리 앱입니다. SFTP-SSH 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). SFTP-SSH 작업을 사용하려면 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH 트리거 작동 방법

SFTP-a s s-SSH 트리거는 SFTP 파일 시스템을 폴링하고 마지막 폴링 이후 변경 된 파일을 찾는 방식으로 작동 합니다. 일부 도구를 통해 파일을 변경하는 경우 타임스탬프를 유지할 수 있습니다. 이러한 경우 트리거가 작동할 수 있도록 이 기능을 사용하지 않도록 설정해야 합니다. 아래에는 몇 가지 일반적인 설정이 나와 있습니다.

| SFTP 클라이언트 | 실행력 |
|-------------|--------|
| Winscp | **옵션** > **기본 설정** > **전송** > **편집** > **타임스탬프 보존** > **사용 안 함**으로 이동 |
| FileZilla | **전송** > **전송된 파일의 타임스탬프 보존** > **사용 안 함**으로 이동 |
|||

트리거는 새 파일을 찾으면 해당 파일이 완전한 상태이며 부분적으로 작성된 것이 아닌지 확인합니다. 예를 들어 트리거가 파일 서버를 확인할 때 파일을 변경하는 중일 수 있습니다. 부분적으로 작성된 파일이 반환되지 않도록 하기 위해 트리거는 최근 변경된 내용이 있는 파일의 타임스탬프를 기록하되 해당 파일을 즉시 반환하지는 않으며, 서버를 다시 폴링할 때만 해당 파일을 반환합니다. 이 동작으로 인해 트리거 폴링 간격의 최대 2배까지 지연이 발생하는 경우도 있습니다.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY 기반 키를 OpenSSH로 변환

개인 키가 PuTTY 형식 (PuTTY 개인 키) 파일 이름 확장명을 사용 하는 경우 먼저 키를 OpenSSH 형식으로 변환 합니다 .이 형식은 pem (Privacy Enhanced Mail) 파일 이름 확장명을 사용 합니다.

### <a name="unix-based-os"></a>Unix 기반 OS

1. PuTTY 도구가 시스템에 아직 설치 되어 있지 않은 경우에는 다음과 같은 작업을 수행 합니다.

   `sudo apt-get install -y putty`

1. 다음 명령을 실행 하 여 SFTP-SSH 커넥터에서 사용할 수 있는 파일을 만듭니다.

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   다음은 그 예입니다.

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. 아직 수행 하지 않은 경우 [최신 PuTTY 생성기 (puttygen) 도구를 다운로드](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)한 다음 도구를 시작 합니다.

1. 이 화면에서 **로드**를 선택 합니다.

   !["로드"를 선택 합니다.](./media/connectors-sftp-ssh/puttygen-load.png)

1. PuTTY 형식으로 개인 키 파일을 찾아 **열기**를 선택 합니다.

1. **변환** 메뉴에서 **Export OpenSSH key**를 선택 합니다.

   !["Export OpenSSH key"를 선택 합니다.](./media/connectors-sftp-ssh/export-openssh-key.png)

1. `.pem` 파일 이름 확장명을 사용 하 여 개인 키 파일을 저장 합니다.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>SSH를 사용하여 SFTP에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 빈 논리 앱의 경우 검색 상자에서 필터로 “sftp ssh”를 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다.

   또는

   기존 논리 앱의 경우 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 검색 상자에서 필터로 “sftp ssh”를 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가**를 선택합니다.

1. 연결에 필요한 정보를 입력합니다.

   > [!IMPORTANT]
   >
   > **SSH 프라이빗 키** 속성에 SSH 프라이빗 키를 입력할 때 이 속성에 대한 완전하고 올바른 값을 제공하는 데 도움이 되는 다음 추가 단계를 수행합니다. 잘못된 키로 인해 연결 실패가 발생합니다.

   모든 텍스트 편집기를 사용할 수 있지만 예를 들어 Notepad.exe를 사용하여 키를 올바르게 복사하고 붙여넣는 방법을 보여주는 샘플 단계는 다음과 같습니다.

   1. 텍스트 편집기에서 SSH 프라이빗 키 파일을 엽니다. 이러한 단계는 예제로 메모장을 사용합니다.

   1. 메모장의 **편집** 메뉴에서 **모두 선택**을 선택 합니다.

   1. **편집** > **복사**를 선택합니다.

   1. 추가한 SFTP-SSH 트리거 또는 작업에서 **SSH 프라이빗 키** 속성으로 복사한 *전체* 키를 붙여넣습니다. 이는 여러 줄을 지원합니다.  키를 ***붙여넣었는지 확인합니다***. ***키를 수동으로 입력하거나 편집하지 마십시오***.

1. 연결 세부 정보 입력이 완료되면 **만들기**를 선택합니다.

1. 이제 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="examples"></a>예시

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH 트리거: 파일이 추가되거나 수정되는 경우

이 트리거는 SFTP 서버에서 파일이 추가되거나 변경되는 경우 논리 앱 워크플로를 시작합니다. 예를 들어 콘텐츠가 지정된 조건을 충족하는지 여부에 따라 파일의 콘텐츠를 확인하고 콘텐츠를 가져오는 조건을 추가할 수 있습니다. 그런 다음, 파일의 콘텐츠를 가져오고 해당 콘텐츠를 SFTP 서버의 폴더에 넣는 작업을 추가할 수 있습니다.

**엔터프라이즈 예제**: 이 트리거를 사용하여 고객의 주문을 나타내는 새 파일에 대한 SFTP 폴더를 모니터링할 수 있습니다. 그런 다음, **파일 콘텐츠 가져오기**와 같은 SFTP 작업을 사용할 수 있으므로 추가로 처리할 주문의 콘텐츠를 가져오고 주문 데이터베이스에 해당 주문을 저장합니다.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-SSH 작업: 경로를 사용 하 여 콘텐츠 가져오기

이 작업은 SFTP 서버의 파일에서 콘텐츠를 가져옵니다. 따라서 예를 들어 이전 예제의 트리거와 파일의 콘텐츠가 충족해야 하는 조건을 추가할 수 있습니다. 조건이 true인 경우 콘텐츠를 가져오는 작업을 실행할 수 있습니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/sftpconnector/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
