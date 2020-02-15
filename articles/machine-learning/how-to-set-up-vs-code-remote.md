---
title: '대화형 디버그: ML 계산 인스턴스 & VS Code'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 코드를 대화형으로 디버깅 하려면 VS Code 원격을 설정 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169746"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>VS Code 원격을 사용 하 여 Azure Machine Learning 계산 인스턴스에서 대화형으로 디버그

이 문서에서는 VS Code에서 **코드를 대화형으로 디버그할** 수 있도록 Azure Machine Learning 계산 인스턴스에서 원격 Visual Studio Code를 설정 하는 방법에 대해 알아봅니다. 

+ [Azure Machine Learning 계산 인스턴스](concept-compute-instance.md) 는 데이터 과학자 완전히 관리 되는 클라우드 기반 워크스테이션 이며 IT 관리자에 게 관리 및 엔터프라이즈 준비 기능을 제공 합니다. 


+ [원격 Visual Studio Code](https://code.visualstudio.com/docs/remote/remote-overview) 개발을 통해 컨테이너, 원격 컴퓨터 또는 WSL (Linux 용 Windows 하위 시스템)을 모든 기능을 갖춘 개발 환경으로 사용할 수 있습니다. 

## <a name="prerequisite"></a>필수 요소  

Windows 플랫폼에서 아직 없는 경우 [OpenSSH 호환 SSH 클라이언트를 설치](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) 해야 합니다. 

> [!Note]
> Ssh 명령이 경로에 있어야 하므로 PuTTY는 Windows에서 지원 되지 않습니다. 

## <a name="get-ip-and-ssh-port"></a>IP 및 SSH 포트 가져오기 

1. https://ml.azure.com/에서 Azure Machine Learning studio로 이동 합니다.

2. [작업 영역](concept-workspace.md)을 선택 합니다.
1. **계산 인스턴스** 탭을 클릭 합니다.
1. **응용 프로그램 URI** 열에서 원격 계산으로 사용 하려는 계산 인스턴스의 **SSH** 링크를 클릭 합니다. 
1. 대화 상자에서 IP 주소와 SSH 포트를 적어둡니다. 
1. 로컬 컴퓨터의 ~/.ssh/디렉터리에 개인 키를 저장 합니다. 예를 들어 새 파일에 대 한 편집기를 열고 다음 키를 붙여넣습니다. 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   개인 키는 다음과 유사 하 게 표시 됩니다.
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. 파일에 대 한 사용 권한을 변경 하 여 파일을 읽을 수만 있는지 확인 합니다.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>인스턴스를 호스트로 추가 

편집기에서 파일 `~/.ssh/config` (Linux) 또는 `C:\Users<username>.ssh\config` (Windows)를 열고 다음과 같은 새 항목을 추가 합니다.

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

필드에 대 한 자세한 내용은 다음과 같습니다. 

|필드|Description|
|----|---------|
|호스트|계산 인스턴스에 대해 원하는 약어를 사용 합니다. |
|HostName|계산 인스턴스의 IP 주소입니다. |
|포트|위의 SSH 대화 상자에 표시 되는 포트입니다. |
|사용자|이를 `azureuser` 해야 합니다. |
|IdentityFile|는 개인 키를 저장 한 파일을 가리켜야 합니다. |

이제 위에서 사용한 약어를 사용 하 여 계산 인스턴스로 ssh를 `ssh azmlci1`수 있습니다. 

## <a name="connect-vs-code-to-the-instance"></a>인스턴스에 VS Code 연결 

1. [Visual Studio Code를 설치](https://code.visualstudio.com/)합니다.

1. [원격 SSH 확장을 설치](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)합니다. 

1. 왼쪽에 있는 원격-SSH 아이콘을 클릭 하 여 SSH 구성을 표시 합니다.

1. 방금 만든 SSH 호스트 구성을 마우스 오른쪽 단추로 클릭 합니다.

1. **현재 창에서 호스트에 연결을**선택 합니다. 

여기서는 계산 인스턴스를 완전히 작업 하며, 이제 로컬 Visual Studio Code와 마찬가지로 편집, 디버그, git 사용, 확장 등의 작업을 수행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 원격 Visual Studio Code 설정 했으므로 계산 인스턴스를 Visual Studio Code의 원격 계산으로 사용 하 여 코드를 대화형으로 디버그할 수 있습니다. 

[자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md) 은 통합 된 노트북으로 계산 인스턴스를 사용 하는 방법을 보여 줍니다.