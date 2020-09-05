---
title: 로컬 Azure Storage 개발에 Azurite emulator 사용
description: Azurite 오픈 소스 에뮬레이터는 Azure storage 응용 프로그램을 테스트 하기 위한 무료 로컬 환경을 제공 합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/15/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 35a93669bf301b3be1d560a0777751c12f3c9d14
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001912"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>로컬 Azure Storage 개발에 Azurite 에뮬레이터 사용

Azurite 오픈 소스 에뮬레이터는 Azure blob 및 queue storage 응용 프로그램을 테스트 하기 위한 무료 로컬 환경을 제공 합니다. 응용 프로그램이 로컬에서 작동 하는 방식에 만족 하는 경우 클라우드에서 Azure Storage 계정을 사용 하도록 전환 합니다. 에뮬레이터는 Windows, Linux 및 macOS에서 플랫폼 간 지원을 제공 합니다.

Azurite는 향후 저장소 에뮬레이터 플랫폼입니다. Azurite는 [Azure Storage 에뮬레이터](storage-use-emulator.md)를 대체 합니다. Azurite는 최신 버전의 Azure Storage Api를 지원 하도록 계속 업데이트 됩니다.

로컬 시스템에서 Azurite를 설치 하 고 실행 하는 여러 가지 방법이 있습니다.

  1. [Azurite Visual Studio Code 확장을 설치 하 고 실행 합니다.](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [NPM를 사용 하 여 Azurite 설치 및 실행](#install-and-run-azurite-by-using-npm)
  1. [Azurite Docker 이미지를 설치 하 고 실행 합니다.](#install-and-run-the-azurite-docker-image)
  1. [GitHub 리포지토리에서 Azurite 복제, 빌드 및 실행](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Azurite Visual Studio Code 확장을 설치 하 고 실행 합니다.

Visual Studio Code 내에서 **확장** 창을 선택 하 고 **확장: MARKETPLACE**에서 *Azurite* 를 검색 합니다.

![Visual Studio Code 확장 마켓플레이스](media/storage-use-azurite/azurite-vs-code-extension.png)

브라우저에서 [Visual Studio Code 확장 시장을](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) 탐색할 수도 있습니다. **설치** 단추를 선택 하 Visual Studio Code를 열고 Azurite 확장 페이지로 직접 이동 합니다.

확장 프로그램은 다음과 같은 Visual Studio Code 명령을 지원 합니다. 명령 팔레트를 열려면 Visual Studio Code에서 F1 키를 누릅니다. 

   - **Azurite: Clean** -모든 Azurite services 지 속성 데이터를 다시 설정 합니다.
   - **Azurite: 클린 blob** service-blob 서비스 정리
   - **Azurite: 큐 서비스 정리** -큐 서비스 정리
   - **Azurite:** 모든 Azurite 서비스를 닫습니다.
   - **Azurite: Blob 서비스를 닫습니다.** blob service를 닫습니다.
   - **Azurite: Queue service를 닫습니다.** 큐 서비스를 닫습니다.
   - **Azurite: 시작** -모든 Azurite 서비스 시작
   - **Azurite: Blob service** 시작-blob service 시작
   - **Azurite: 큐 서비스 시작** -큐 서비스 시작

Visual Studio Code 내에서 Azurite를 구성 하려면 확장 창을 선택 합니다. **Azurite**에 대 한 **관리** (기어) 아이콘을 선택 합니다. **확장 설정**을 선택 합니다.

![Azurites 확장 설정 구성](media/storage-use-azurite/azurite-configure-extension-settings.png)

다음 설정이 지원 됩니다.

   - **Azurite: Blob Host** -Blob service 수신 대기 끝점입니다. 기본 설정은 127.0.0.1입니다.
   - **Azurite: Blob port** -Blob service 수신 대기 포트입니다. 기본 포트는 1만입니다.
   - **Azurite: Cert** -HTTPS 모드를 사용 하도록 설정 하기 위해 로컬에서 신뢰할 수 있는 PEM 또는 PFX 인증서 파일 경로에 대 한 경로입니다.
   - **Azurite: debug** -디버그 로그를 Azurite 채널에 출력 합니다. 기본 값은 **false**입니다.
   - **Azurite: 키** -로컬에서 신뢰할 수 있는 pem 키 파일에 대 한 경로입니다. **Azurite: Cert** 가 PEM 파일을 가리킬 때 필요 합니다.
   - **Azurite: location** -작업 영역 위치 경로입니다. 기본값은 Visual Studio Code 작업 폴더입니다.
   - **Azurite:** 지원 되지 않는 헤더와 매개 변수를 무시 하는 느슨한 사용 느슨한 모드입니다.
   - **Azurite: oauth** -선택적 oauth 수준입니다.
   - **Azurite: Pwd** -PFX 파일의 암호입니다. **Azurite: Cert** 가 PFX 파일을 가리킬 때 필요 합니다.
   - **Azurite: Queue Host** -큐 서비스 수신 대기 끝점입니다. 기본 설정은 127.0.0.1입니다.
   - **Azurite: Queue port** -큐 서비스 수신 포트입니다. 기본 포트는 10001입니다.
   - **Azurite: 자동** -자동 모드에서 액세스 로그를 사용 하지 않도록 설정 합니다. 기본 값은 **false**입니다.
   - **Azurite: Api 버전 확인 건너뛰기** -요청 Api 버전 검사를 건너뜁니다. 기본 값은 **false**입니다.

## <a name="install-and-run-azurite-by-using-npm"></a>NPM를 사용 하 여 Azurite 설치 및 실행

이 설치 방법을 사용 하려면 [Node.js 버전 8.0 이상 버전이](https://nodejs.org) 설치 되어 있어야 합니다. Npm (Node Package Manager)는 모든 Node.js 설치에 포함 된 패키지 관리 도구입니다. Node.js를 설치한 후 다음 명령을 실행 `npm` 하 여 Azurite를 설치 합니다.

```console
npm install -g azurite
```

Azurite를 설치한 후 [명령줄에서 Azurite 실행](#run-azurite-from-a-command-line)을 참조 하세요.

## <a name="install-and-run-the-azurite-docker-image"></a>Azurite Docker 이미지를 설치 하 고 실행 합니다.

다음 명령을 사용 하 여 [Dockerhub](https://hub.docker.com/) 를 사용 하 여 [최신 Azurite 이미지](https://hub.docker.com/_/microsoft-azure-storage-azurite) 를 가져옵니다.

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite Docker 이미지를 실행 합니다**.

다음 명령은 Azurite Docker 이미지를 실행 합니다. `-p 10000:10000`매개 변수는 호스트 컴퓨터의 포트 1만에서 Docker 인스턴스로 요청을 리디렉션합니다.

```console
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**작업 영역 위치 지정**:

다음 예제에서 `-v c:/azurite:/data` 매개 변수는 azurite 지속형 데이터 위치로 *c:/azurite* 를 지정 합니다. Docker 명령을 실행 하기 전에 디렉터리 *c:/azurite*를 만들어야 합니다.

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Blob service만 실행**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

시작 시 Azurite를 구성 하는 방법에 대 한 자세한 내용은 [명령줄 옵션](#command-line-options)을 참조 하세요.

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>GitHub 리포지토리에서 Azurite 복제, 빌드 및 실행

이 설치 방법을 사용 하려면 [Git](https://git-scm.com/) 가 설치 되어 있어야 합니다. 다음 콘솔 명령을 사용 하 여 Azurite 프로젝트에 대 한 [GitHub 리포지토리](https://github.com/azure/azurite) 를 복제 합니다.

```console
git clone https://github.com/Azure/Azurite.git
```

소스 코드를 복제 한 후 복제 된 리포지토리의 루트에서 다음 명령을 실행 하 여 Azurite를 빌드하고 설치 합니다.

```console
npm install
npm run build
npm install -g
```

Azurite를 설치 하 고 빌드한 후 [명령줄에서 Azurite 실행](#run-azurite-from-a-command-line)을 참조 하세요.

## <a name="run-azurite-from-a-command-line"></a>명령줄에서 Azurite 실행

> [!NOTE]
> Visual Studio Code 확장명만 설치한 경우 명령줄에서 Azurite를 실행할 수 없습니다. 대신 Visual Studio Code 명령 팔레트를 사용 합니다. 자세한 내용은 [Azurite Visual Studio Code 확장 설치 및 실행](#install-and-run-the-azurite-visual-studio-code-extension)을 참조 하세요.

명령줄을 사용 하 여 즉시 시작 하려면 *c:\azurite*라는 디렉터리를 만든 후 다음 명령을 실행 하 여 azurite를 시작 합니다.

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

이 명령은 Azurite에 게 *c:\azurite*의 특정 디렉터리에 모든 데이터를 저장 하도록 지시 합니다. `--location`이 옵션을 생략 하면 현재 작업 디렉터리가 사용 됩니다.

## <a name="command-line-options"></a>명령줄 옵션

이 섹션에서는 Azurite를 시작할 때 사용할 수 있는 명령줄 스위치에 대해 자세히 설명 합니다.

### <a name="help"></a>도움말

**선택 사항** -또는 스위치를 사용 하 여 명령줄 도움말을 가져옵니다 `-h` `--help` .

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Blob 수신 대기 호스트

**선택 사항** -기본적으로 Azurite는 로컬 서버로 127.0.0.1을 수신 합니다. 스위치를 사용 `--blobHost` 하 여 요구 사항에 대 한 주소를 설정 합니다.

로컬 컴퓨터 에서만 요청 수락:

```console
azurite --blobHost 127.0.0.1
```

원격 요청 허용:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용 하면 시스템이 외부 공격에 취약 해질 수 있습니다.

### <a name="blob-listening-port-configuration"></a>Blob 수신 대기 포트 구성

**선택 사항** -기본적으로 Azurite는 포트 1만에서 Blob service를 수신 대기 합니다. 스위치를 사용 `--blobPort` 하 여 필요한 수신 대기 포트를 지정 합니다.

> [!NOTE]
> 사용자 지정 된 포트를 사용한 후 Azure Storage 도구나 Sdk에서 연결 문자열 또는 해당 구성을 업데이트 해야 합니다.

Blob service 수신 대기 포트를 사용자 지정 합니다.

```console
azurite --blobPort 8888
```

시스템에서 사용 가능한 포트를 자동으로 선택 하도록 합니다.

```console
azurite --blobPort 0
```

Azurite을 시작 하는 동안 사용 중인 포트가 표시 됩니다.

### <a name="queue-listening-host"></a>큐 수신 대기 호스트

**선택 사항** -기본적으로 Azurite는 로컬 서버로 127.0.0.1을 수신 합니다. 스위치를 사용 `--queueHost` 하 여 요구 사항에 대 한 주소를 설정 합니다.

로컬 컴퓨터 에서만 요청 수락:

```console
azurite --queueHost 127.0.0.1
```

원격 요청 허용:

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용 하면 시스템이 외부 공격에 취약 해질 수 있습니다.

### <a name="queue-listening-port-configuration"></a>큐 수신 대기 포트 구성

**선택 사항** -기본적으로 Azurite는 포트 10001에서 큐 서비스를 수신 대기 합니다. 스위치를 사용 `--queuePort` 하 여 필요한 수신 대기 포트를 지정 합니다.

> [!NOTE]
> 사용자 지정 된 포트를 사용한 후 Azure Storage 도구나 Sdk에서 연결 문자열 또는 해당 구성을 업데이트 해야 합니다.

큐 서비스 수신 대기 포트를 사용자 지정 합니다.

```console
azurite --queuePort 8888
```

시스템에서 사용 가능한 포트를 자동으로 선택 하도록 합니다.

```console
azurite --queuePort 0
```

Azurite을 시작 하는 동안 사용 중인 포트가 표시 됩니다.

### <a name="workspace-path"></a>작업 영역 경로

**선택 사항** -Azurite은 실행 중에 데이터를 로컬 디스크에 저장 합니다. `-l`또는 스위치를 사용 `--location` 하 여 경로를 작업 영역 위치로 지정 합니다. 기본적으로 현재 프로세스 작업 디렉터리가 사용 됩니다. 소문자 ' l '을 적어둡니다.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>액세스 로그

**선택 사항** -기본적으로 액세스 로그는 콘솔 창에 표시 됩니다. 또는 스위치를 사용 하 여 액세스 로그를 표시 하지 않습니다 `-s` `--silent` .

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>디버그 로그

**선택 사항** -디버그 로그에는 모든 요청 및 예외 스택 추적에 대 한 자세한 정보가 포함 됩니다. 또는 스위치에 올바른 로컬 파일 경로를 제공 하 여 디버그 로그를 사용 하도록 설정 합니다 `-d` `--debug` .

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>느슨한 모드

**선택 사항** -기본적으로 Azurite는 strict 모드를 적용 하 여 지원 되지 않는 요청 헤더와 매개 변수를 차단 합니다. 또는 스위치를 사용 하 여 strict 모드를 사용 하지 않도록 설정 `-L` `--loose` 합니다. 대문자 ' L '을 확인 합니다.

```console
azurite -L
azurite --loose
```
### <a name="version"></a>버전

**선택 사항** -또는 스위치를 사용 하 여 설치 된 Azurite 버전 번호를 표시 `-v` `--version` 합니다.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>인증서 구성 (HTTPS)

**선택 사항** -기본적으로 AZURITE는 HTTP 프로토콜을 사용 합니다. 스위치에 Privacy Enhanced Mail (pem) 또는 [개인 정보 교환 (.pfx)](https://docs.microsoft.com/windows-hardware/drivers/install/personal-information-exchange---pfx--files) 인증서 파일의 경로를 제공 하 여 HTTPS 모드를 사용 하도록 설정 합니다 `--cert` .

`--cert`PEM 파일에 대해가 제공 되는 경우 해당 스위치를 제공 해야 합니다 `--key` .

```console
azurite --cert path/server.pem --key path/key.pem
```

`--cert`PFX 파일에 대해가 제공 되는 경우 해당 스위치를 제공 해야 합니다 `--pwd` .

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

PEM 및 PFX 파일을 만드는 방법에 대 한 자세한 내용은 [HTTPS 설치](https://github.com/Azure/Azurite/blob/master/README.md#https-setup)를 참조 하세요.

### <a name="oauth-configuration"></a>OAuth 구성

**선택 사항** -스위치를 사용 하 여 Azurite에 대 한 OAuth 인증을 사용 하도록 설정 `--oauth` 합니다.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth에는 HTTPS 끝점이 필요 합니다. 스위치와 함께 스위치를 제공 하 여 HTTPS를 사용 하도록 설정 해야 `--cert` `--oauth` 합니다.

Azurite는 `basic` 스위치에 매개 변수를 지정 하 여 기본 인증을 지원 합니다 `--oauth` . Azurite는 들어오는 전달자 토큰의 유효성을 검사 하 고 발급자, 대상 그룹 및 만료를 확인 하는 등의 기본 인증을 수행 합니다. Azurite는 토큰 서명 또는 사용 권한을 확인 하지 않습니다.

### <a name="skip-api-version-check"></a>API 버전 확인 건너뛰기

**선택 사항** -시작할 때 Azurite는 요청 된 API 버전이 올바른지 확인 합니다. 다음 명령은 API 버전 검사를 건너뜁니다.

```console
azurite --skipApiVersionCheck
```


## <a name="authorization-for-tools-and-sdks"></a>도구 및 Sdk에 대 한 권한 부여

인증 전략을 사용 하 여 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 Azure Storage sdk 또는 도구에서 Azurite에 연결 합니다. 인증이 필요 합니다. Azurite는 OAuth, 공유 키 및 SAS (공유 액세스 서명)를 사용한 권한 부여를 지원 합니다. Azurite는 공용 컨테이너에 대 한 익명 액세스도 지원 합니다.

Azure Sdk를 사용 하는 경우 옵션을 사용 하 여 Azurite를 시작 `--oauth basic and --cert --key/--pwd` 합니다.

### <a name="well-known-storage-account-and-key"></a>잘 알려진 저장소 계정 및 키

Azurite은 레거시 Azure Storage 에뮬레이터에서 사용 하는 것과 동일한 잘 알려진 계정 및 키를 허용 합니다.

- 계정 이름: `devstoreaccount1`
- 계정 키: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>사용자 지정 저장소 계정 및 키

Azurite는 다음과 `AZURITE_ACCOUNTS` 같은 형식으로 환경 변수를 설정 하 여 사용자 지정 저장소 계정 이름 및 키를 지원 `account1:key1[:key2];account2:key1[:key2];...` 합니다.

예를 들어 하나의 키가 있는 사용자 지정 저장소 계정을 사용 합니다.

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

또는 각각 두 개의 키가 있는 여러 저장소 계정을 사용 합니다.

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite는 기본적으로 분 마다 사용자 지정 계정 이름과 키를 환경 변수에서 새로 고칩니다. 이 기능을 사용 하면 Azurite를 다시 시작 하지 않고 계정 키를 동적으로 회전 하거나 새 저장소 계정을 추가할 수 있습니다.

> [!NOTE]
> `devstoreaccount1`사용자 지정 저장소 계정을 설정 하는 경우 기본 저장소 계정이 사용 하지 않도록 설정 됩니다.

### <a name="connection-strings"></a>연결 문자열

응용 프로그램에서 Azurite에 연결 하는 가장 쉬운 방법은 *UseDevelopmentStorage = true*바로 가기를 참조 하는 응용 프로그램의 구성 파일에서 연결 문자열을 구성 하는 것입니다. *app.config* 파일에 있는 연결 문자열의 예는 다음과 같습니다.

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP 연결 문자열

Azure CLI 2.0 또는 Storage 탐색기와 같이 [Azure sdk](https://aka.ms/azsdk) 또는 도구에 다음 연결 문자열을 전달할 수 있습니다.

전체 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Blob service에만 연결 하려면 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

큐 서비스에만 연결 하려면 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS 연결 문자열

전체 HTTPS 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Blob 서비스만 사용 하려면 HTTPS 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

큐 서비스만 사용 하려면 HTTPS 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

자체 서명 된 인증서를 생성 하는 데를 사용한 경우에는 `dotnet dev-certs` 다음 연결 문자열을 사용 합니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

[사용자 지정 저장소 계정 및 키](#custom-storage-accounts-and-keys)를 사용 하는 경우 연결 문자열을 업데이트 합니다.

자세한 내용은 [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.

### <a name="azure-sdks"></a>Azure SDK

[Azure sdk](https://aka.ms/azsdk)와 함께 Azurite를 사용 하려면 OAUTH 및 HTTPS 옵션을 사용 합니다.

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

그런 다음 BlobContainerClient, BlobServiceClient 또는 BlobClient를 인스턴스화할 수 있습니다.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

QueueClient 또는 QueueServiceClient를 인스턴스화할 수도 있습니다.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Storage 탐색기를 사용 하 여 Azurite에 저장 된 데이터를 볼 수 있습니다.

#### <a name="connect-to-azurite-using-http"></a>HTTP를 사용 하 여 Azurite에 연결

Storage 탐색기에서 다음 단계를 수행 하 여 Azurite에 연결 합니다.

 1. **계정 관리** 아이콘을 선택 합니다.
 1. **계정 추가를 선택 합니다** .
 1. **로컬 에뮬레이터에 연결을 선택 합니다** .
 1. **다음**을 선택합니다.
 1. **표시 이름** 필드를 원하는 이름으로 편집 합니다.
 1. 다시 **다음** 을 선택 합니다.
 1. **연결** 선택

#### <a name="connect-to-azurite-using-https"></a>HTTPS를 사용 하 여 Azurite에 연결

기본적으로 Storage 탐색기 자체 서명 된 인증서를 사용 하는 HTTPS 끝점은 열리지 않습니다. HTTPS를 사용 하 여 Azurite를 실행 하는 경우 자체 서명 된 인증서를 사용할 가능성이 높습니다. Storage 탐색기에서 ssl 인증서 **편집**  ->  **SSL Certificates**  ->  **인증서 가져오기** 대화 상자를 통해 ssl 인증서를 가져옵니다.

##### <a name="import-certificate-to-storage-explorer"></a>인증서를 Storage 탐색기 가져오기

1. 로컬 컴퓨터에서 인증서를 찾습니다.
1. Storage 탐색기에서 **Edit**  ->  **SSL 인증서**편집  ->  **인증서 가져오기** 로 이동 하 여 인증서를 가져옵니다.

인증서를 가져오지 않으면 오류가 발생 합니다.

`unable to verify the first certificate` 또는 `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>HTTPS 연결 문자열을 통해 Azurite 추가

Storage 탐색기에 Azurite HTTPS를 추가 하려면 다음 단계를 수행 합니다.

1. **탐색기 설정/해제** 를 선택 합니다.
1. **로컬 & 연결** 됨을 선택 합니다.
1. **저장소 계정** 을 마우스 오른쪽 단추로 클릭 하 고 **Azure Storage에 연결을**선택 합니다.
1. **연결 문자열 사용을 선택 합니다** .
1. **다음**을 선택합니다.
1. **표시 이름** 필드에 값을 입력 합니다.
1. 이 문서의 이전 섹션에서 [HTTPS 연결 문자열](#https-connection-strings) 을 입력 합니다.
1. **다음**을 선택합니다.
1. **연결** 선택

## <a name="workspace-structure"></a>작업 영역 구조

Azurite를 초기화할 때 작업 영역 위치에서 다음 파일 및 폴더를 만들 수 있습니다.

- `__blobstorage__` -Azurite blob 서비스 지속형 이진 데이터를 포함 하는 디렉터리
- `__queuestorage__` -Azurite queue 서비스 지속형 이진 데이터를 포함 하는 디렉터리
- `__azurite_db_blob__.json` -Azurite blob service 메타 데이터 파일
- `__azurite_db_blob_extent__.json` -Azurite blob service 익스텐트 메타 데이터 파일
- `__azurite_db_queue__.json` -Azurite 큐 서비스 메타 데이터 파일
- `__azurite_db_queue_extent__.json` -Azurite 큐 서비스 익스텐트 메타 데이터 파일

Azurite를 정리 하려면 위의 파일과 폴더를 삭제 하 고 에뮬레이터를 다시 시작 합니다.

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite와 Azure Storage 간의 차이점

Azurite의 로컬 인스턴스와 클라우드의 Azure Storage 계정 사이에는 기능적인 차이점이 있습니다.

### <a name="endpoint-and-connection-url"></a>끝점 및 연결 URL

Azurite에 대 한 서비스 끝점은 Azure Storage 계정의 끝점과 다릅니다. 로컬 컴퓨터는 도메인 이름 확인을 수행 하지 않습니다. Azurite 끝점은 로컬 주소 여야 합니다.

Azure Storage 계정에서 리소스에 주소를 표시 하는 경우 계정 이름은 URI 호스트 이름의 일부입니다. 주소를 지정 하는 리소스는 URI 경로의 일부입니다.

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

다음 URI는 Azure Storage 계정의 blob에 대 한 올바른 주소입니다.

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

로컬 컴퓨터가 도메인 이름 확인을 수행 하지 않기 때문에 계정 이름은 호스트 이름이 아닌 URI 경로의 일부입니다. Azurite의 리소스에 대해 다음 URI 형식을 사용 합니다.

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Azurite에서 blob에 액세스 하는 데 사용할 수 있는 주소는 다음과 같습니다.

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>크기 조정 및 성능

Azurite는 다 수의 연결 된 클라이언트를 지원 하지 않습니다. 성능 보장은 없습니다. Azurite은 개발 및 테스트 목적으로 작성 되었습니다.

### <a name="error-handling"></a>오류 처리

Azurite는 Azure Storage 오류 처리 논리와 맞춰져 있지만 차이가 있습니다. 예를 들어 오류 메시지는 서로 다를 수 있으며, 오류 상태 코드는 정렬 됩니다.

### <a name="ra-grs"></a>RA-GRS

Azurite는 읽기 액세스 지역 중복 복제 (RA-GRS)를 지원 합니다. 저장소 리소스의 경우 계정 이름에를 추가 하 여 보조 위치에 액세스 합니다 `-secondary` . 예를 들어 Azurite의 읽기 전용 보조 데이터베이스를 사용 하 여 blob에 액세스 하기 위해 다음 주소를 사용할 수 있습니다.

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite는 오픈 소스입니다.

Azurite에 대 한 기여 및 제안은 환영 합니다. 예정 된 기능 및 버그 수정에 대해 추적 중인 마일스 톤 및 작업 항목에 대해서는 Azurite [github 프로젝트](https://github.com/Azure/Azurite/projects) 페이지 또는 [github 문제](https://github.com/Azure/Azurite/issues) 를 참조 하세요. 자세한 작업 항목은 GitHub 에서도 추적 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 에뮬레이터를 사용 하 여 개발 및 테스트 문서에](storage-use-emulator.md) 는 Azurite로 대체 되는 레거시 Azure Storage 에뮬레이터가 있습니다.
- [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md) 올바른 Azure Storage 연결 문자열을 조합 하는 방법을 설명 합니다.
