---
title: Azure Service Fabric CLI 시작
description: Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다. 클러스터에 연결하는 방법과 애플리케이션을 관리하는 방법을 알아봅니다.
services: service-fabric
author: Christina-Kang
manager: chackdan
ms.service: service-fabric
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: d5b6f183a59e3f47aa5867b5e09e06541a6a67db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803233"
---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI

Azure Service Fabric CLI(명령줄 인터페이스)는 Azure Service Fabric 엔터티와 상호 작용하고 이를 관리하기 위한 명령줄 유틸리티입니다. Service Fabric CLI는 Windows 또는 Linux 클러스터에서 사용할 수 있으며, Python을 지원하는 모든 플랫폼에서 실행됩니다.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="prerequisites"></a>필수 조건

설치하기 전에 환경에 Python과 pip가 모두 설치되어 있는지 확인합니다. 자세한 내용은 [pip 빠른 시작 설명서(영문)](https://pip.pypa.io/en/latest/quickstart/) 및 공식 [Python 설치 설명서(영문)](https://wiki.python.org/moin/BeginnersGuide/Download)를 참조하세요.

CLI는 Python 버전 2.7, 3.5, 3.6, 3.7을 지원합니다. Python 2.7의 지원이 곧 끝나기 때문에 Python 3.x가 권장되는 버전입니다.

### <a name="service-fabric-target-runtime"></a>Service Fabric 대상 런타임

Service Fabric CLI는 Service Fabric SDK의 최신 런타임 버전을 지원하기 위한 것입니다. 다음 표를 사용하여 설치할 CLI의 버전을 확인합니다.

| CLI 버전   | 지원되는 런타임 버전 |
|---------------|---------------------------|
| 최신(~=7)  | 최신(~=6.4)            |
| 6.0.0         | 6.3                       |
| 5.0.0         | 6.2                       |
| 4.0.0         | 6.1                       |
| 3.0.0         | 6.0                       |
| 1.1.0         | 5.6, 5.7                  |

필요에 따라 `pip install` 명령에 `==<version>`을 접미사로 붙여 설치하기 위해 CLI의 대상 버전을 지정할 수 있습니다. 예를 들어 1.1.0 버전에서 구문은 다음과 같습니다.

```
pip install -I sfctl==1.1.0
```

필요한 경우 다음 `pip install` 명령을 앞에서 언급한 명령으로 바꿉니다.

Service Fabric CLI 릴리스에 대한 자세한 내용은 [GitHub 설명서](https://github.com/Azure/service-fabric-cli/releases)를 참조하세요.

## <a name="install-pip-python-and-the-service-fabric-cli"></a>pip, Python 및 Service Fabric CLI 설치

플랫폼에 pip와 Python을 설치하는 방법은 여러 가지가 있습니다. Python 3 및 pip를 사용하여 주요 운영 체제를 빠르게 설정하는 몇 가지 단계는 다음과 같습니다.

### <a name="windows"></a>Windows

Windows 10, Windows Server 2016 및 Windows Server 2012 R2의 경우 표준 공식 설치 지침을 사용합니다. 또한 Python 설치 관리자는 기본적으로 pip를 설치합니다.

1. 공식 [Python 다운로드 페이지](https://www.python.org/downloads/)로 이동하여 Python 3.7의 최신 릴리스를 다운로드합니다.

2. 설치 관리자를 시작합니다.

3. 프롬프트의 아래쪽에 있는 **PATH에 Python 3.7 추가**를 선택합니다.

4. **지금 설치**를 선택하고 설치를 완료합니다.

이제 새 명령 창을 열고 Python과 pip의 버전을 모두 가져올 수 있습니다.

```bat
python --version
pip --version
```

그런 다음, 다음 명령을 실행하여 Azure Service Fabric CLI(sfctl)를 설치하고 CLI 도움말 페이지를 봅니다.

```bat
pip install sfctl
sfctl -h
```

### <a name="ubuntu-and-windows-subsystem-for-linux"></a>Linux용 Ubuntu 및 Windows 하위 시스템

Service Fabric CLI를 설치하려면 그러면 다음 명령을 실행합니다.

```bash
sudo apt-get install python3
sudo apt-get install python3-pip
pip3 install sfctl
```

다음을 사용하여 설치를 테스트할 수 있습니다.

```bash
sfctl -h
```

다음과 같은 명령을 찾을 수 없음 오류가 표시되면:

`sfctl: command not found`

`$PATH`에서 `~/.local/bin`에 액세스할 수 있도록 합니다.

```bash
export PATH=$PATH:~/.local/bin
echo "export PATH=$PATH:~/.local/bin" >> .bashrc
```

Linux용 Windows 하위 시스템에 대한 설치가 잘못된 폴더 사용 권한으로 인해 실패하는 경우 승격된 사용 권한으로 다시 시도해야 합니다.

```bash
sudo pip3 install sfctl
```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4(Service Fabric 미리 보기 지원)

Red Hat에 Service Fabric CLI를 설치하려면 다음 명령을 실행합니다.

```bash
sudo yum install -y python34
sudo yum install python34-setuptools
sudo easy_install-3.4 pip
sudo pip3 install sfctl
```

설치를 테스트하려면 **Linux용 Ubuntu 및 Windows 하위 시스템** 섹션에서 설명한 단계를 참조하세요.

<a name = "cli-mac"></a>
### <a name="macos"></a>MacOS

MacOS의 경우 [HomeBrew 패키지 관리자](https://brew.sh)를 사용하는 것이 좋습니다. HomeBrew가 아직 설치되지 않은 경우 다음 명령을 실행하여 설치합니다.

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

그런 다음, 터미널에서 다음 명령을 실행하여 Python 3.7, pip 및 Service Fabric CLI를 설치합니다.

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

## <a name="cli-syntax"></a>CLI 구문

명령에는 항상 `sfctl`이라는 접두사가 지정됩니다. 사용할 수 있는 모든 명령에 대한 일반적인 정보를 얻으려면 `sfctl -h`를 사용합니다. 단일 명령의 도움말은 `sfctl <command> -h`를 사용합니다.

명령은 동사 또는 작업 앞에 명령의 대상이 포함된 반복 구조를 따릅니다.

```azurecli
sfctl <object> <action>
```

이 예제에서는 `<object>`는 `<action>`의 대상입니다.

## <a name="select-a-cluster"></a>클러스터 선택

모든 작업을 수행하기 전에 연결할 클러스터를 선택해야 합니다. 예를 들어 다음 명령을 실행하여 `testcluster.com`이라는 클러스터를 선택하고 연결합니다.

> [!WARNING]
> 프로덕션 환경에는 보안되지 않은 Service Fabric 클러스터를 사용하지 않습니다.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

클러스터 엔드포인트는 접두사로 `http` 또는 `https`를 사용해야 합니다. HTTP 게이트웨이에 대한 포트를 포함해야 합니다. 포트 및 주소는 Service Fabric Explorer URL와 동일합니다.

인증서로 보호되는 클러스터에는 PEM으로 인코딩된 인증서를 지정할 수 있습니다. 인증서는 단일 파일 또는 인증서와 키 쌍으로 지정할 수 있습니다. CA 서명되지 않은 자체 서명된 인증서인 경우 `--no-verify` 옵션을 전달하여 CA 확인을 건너뛸 수 있습니다.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

자세한 내용은 [안전한 Azure Service Fabric 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요.

## <a name="basic-operations"></a>기본 작업

클러스터 연결 정보는 여러 Service Fabric CLI 세션에서 유지됩니다. Service Fabric 클러스터를 선택한 후에 클러스터에서 모든 Service Fabric 명령을 실행할 수 있습니다.

예를 들어, Service Fabric 클러스터 상태를 가져오려면 다음 명령을 실행합니다.

```azurecli
sfctl cluster health
```

이 명령은 다음과 같이 출력됩니다.

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>팁 및 문제 해결

일반적인 문제를 해결하기 위한 몇 가지 제안 및 팁은 다음과 같습니다.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>인증서를 PFX에서 PEM 형식으로 변환

Service Fabric CLI는 PEM(확장명 .pem) 파일의 클라이언트 쪽 인증서를 지원합니다. Windows에서 PFX 파일을 사용하는 경우 해당 인증서를 PEM 형식으로 변환해야 합니다. PFX 파일을 PEM 파일로 변환하려면 다음 명령을 사용합니다.

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

마찬가지로, PEM 파일에서 PFX 파일로 변환하려면 다음 명령을 사용할 수 있습니다(여기에서 암호가 제공되지 않음).

```bash
openssl  pkcs12 -export -out Certificates.pfx -inkey Certificates.pem -in Certificates.pem -passout pass:'' 
```

자세한 내용은 [OpenSSL 설명서](https://www.openssl.org/docs/)를 참조하세요.

### <a name="connection-problems"></a>연결 문제

일부 작업은 다음과 같은 메시지를 생성할 수 있습니다.

`Failed to establish a new connection`

지정된 클러스터 엔드포인트를 사용 가능하고 수신 대기하는지를 확인합니다. Service Fabric Explorer UI가 해당 호스트 및 포트에서 사용 가능한지도 확인합니다. 엔드포인트를 업데이트하려면 `sfctl cluster select`를 사용합니다.

### <a name="detailed-logs"></a>자세한 로그

자세한 로그는 문제를 디버그하거나 보고하는 경우에 유용합니다. `--debug` 플래그는 출력의 자세한 표시 수준을 늘립니다.

### <a name="command-help-and-syntax"></a>명령 도움말 및 구문

특정 명령 또는 명령 그룹에 대한 도움말은 얻으려면 `-h` 플래그를 사용합니다.

```azurecli
sfctl application -h
```

다른 예제입니다.

```azurecli
sfctl application create -h
```

## <a name="updating-the-service-fabric-cli"></a>Service Fabric CLI 업데이트 

Service Fabric CLI를 업데이트하려면 다음 명령을 실행합니다(원래 설치 중 선택한 항목에 따라 `pip`를 `pip3`으로 대체).

```bash
pip uninstall sfctl
pip install sfctl
```

## <a name="next-steps"></a>다음 단계

* [Azure Service Fabric CLI에서 애플리케이션 배포](service-fabric-application-lifecycle-sfctl.md)
* [Linux에서 Service Fabric 시작](service-fabric-get-started-linux.md)
