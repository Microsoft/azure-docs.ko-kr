---
title: 심층 학습 및 AI 프레임워크 - Azure | Microsoft Docs
description: Data Science Virtual Machine에서 지원되는 딥러닝 프레임워크 및 도구에 대해 알아봅니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 59f88d54d3542738f1a500c8c476995eb1535ecf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130279"
---
# <a name="deep-learning-and-ai-frameworks"></a>심층 학습 및 AI 프레임워크
[데이터 과학 Virtual Machine](https://aka.ms/dsvm)(DSVM) 및 [심층 학습 VM](https://aka.ms/dsvm/deeplearning)은 예측 분석 및 이미지, 언어 이해 등의 인식 기능을 갖춘 인공 지능(AI) 애플리케이션을 빌드하는 데 유용한 여러 심층 학습 프레임워크를 지원합니다.

여기에 DSVM에서 사용할 수 있는 모든 심층 학습에 대한 자세한 내용이 있습니다.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive 도구 키트

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | Microsoft CNTK(Cognitive Toolkit)은 [Linux 및 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)의 Python 3.5에 설치되고, [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 Python 3.6에 설치됩니다.   |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다.     |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | * 터미널에서: 올바른 환경을 활성화한 다음, Python을 실행합니다. <br/> * Jupyter에서: [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플에 대한 CNTK 디렉터리를 엽니다. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | TensorFlow는 [Linux 및 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)의 Python 3.5에 설치되고, [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 Python 3.6에 설치됩니다.  |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다.     |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | * 터미널에서: 올바른 환경을 활성화한 다음, Python을 실행합니다. <br/> * Jupyter에서: [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플에 대한 TensorFlow 디렉터리를 엽니다.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | TensorFlow에 대한 분산된 딥 러닝 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Horovod는 [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에서 Python 3.5에 설치됩니다.  |
| 샘플에 대한 링크      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| DSVM의 관련 도구      | TensorFlow      |
| 사용/실행 방법    | 터미널에서: 올바른 환경을 활성화한 다음, Python을 실행합니다. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 고급 심화 학습 API      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | TensorFlow는 [Linux 및 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)의 Python 3.5에 설치되고, [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 Python 3.6에 설치됩니다. |
| 샘플에 대한 링크      | https://github.com/fchollet/keras/tree/master/examples      |
| DSVM의 관련 도구      | Microsoft Cognitive Toolkit, TensorFlow, Theano      |
| 사용/실행 방법    | * 터미널에서: 올바른 환경을 활성화한 다음, Python을 실행합니다. <br/> * Jupyter에서: GitHub 위치에서 샘플을 다운로드하고, [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플 디렉터리를 엽니다. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Caffe는 `/opt/caffe`에 설치됩니다.    |
| 파이썬 2.7로 전환하는 방법 | `source activate root` 실행 |
| 샘플에 대한 링크      | 샘플은 `/opt/caffe/examples`에 포함됩니다.      |
| DSVM의 관련 도구      | Caffe2      |

### <a name="how-to-use--run-it"></a>사용/실행 방법

X2Go를 사용하여 VM에 로그인한 다음 새 터미널을 시작하고 입력합니다

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

새 브라우저 창이 샘플 노트북과 함께 열립니다.

이진 파일은 /opt/caffe/build/install/bin에 설치됩니다.

설치된 Caffe 버전은 Python 2.7이 필요하며 기본적으로 활성화되는 Python 3.5에서는 작동하지 않습니다. Anaconda 환경을 전환하려면 `source activate root`을 실행합니다.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Caffe2는 [Python 2.7(루트) conda 환경](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에 설치됩니다. 원본은 `/opt/caffe2`에 있습니다. |
| 샘플에 대한 링크      | 샘플 노트북은 JupyterHub에 포함되어 있습니다. |
| DSVM의 관련 도구      | Caffe      |
| 사용/실행 방법    | * 터미널에서: [루트 Python 환경](dsvm-languages.md#python-linux-and-windows-server-2012-edition)을 활성화하고, Python을 시작하고, caffe2를 가져옵니다. <br/> * JupyterHub에서: [JupyterHub에 연결](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)한 다음, 샘플 노트북을 찾으려면 Caffe2 디렉터리로 이동합니다. 일부 노트북은 Python 코드에 Caffe2 루트를 설정해야 합니다; enter /opt/caffe2. |
| 빌드 정보 | Caffe2는 Linux의 원본에서 빌드되고 CUDA, cuDNN 및 Intel MKL을 포함합니다. 현재 커밋은 모든 GPU 및 테스트되는 샘플에서 안정성을 위해 선택된 0d9c0d48c6f20143d6404b99cc568efd29d5a4be입니다. |

## <a name="chainer"></a>Chainer

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | Chainer는 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에 설치됩니다. ChainerRL 및 ChainerCV도 설치됩니다.   |
| 샘플에 대한 링크      | 샘플 노트북은 JupyterHub에 포함되어 있습니다. |
| DSVM의 관련 도구      | Caffe      |
| 사용/실행 방법  | * 터미널에서: [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 환경을 활성화하고, _python_을 실행한 다음, Chainer를 가져옵니다. <br/> JupyterHub에서: [JupyterHub에 연결](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)한 다음, 샘플 노트북을 찾으려면 Chainer 디렉터리로 이동합니다.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | H2O에 대한 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Deep Water는 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에 설치되며 `/dsvm/tools/deep_water`에서도 사용할 수 있습니다.   |
| 샘플에 대한 링크      | 샘플 노트북은 JupyterHub에 포함되어 있습니다.      |
| DSVM의 관련 도구      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>사용/실행 방법

Deep Water는 cuDNN 5.1의 CUDA 8이 필요합니다. 다른 딥 러닝 프레임워크는 CUDA 9 및 cuDNN 7을 사용하므로 기본적으로 라이브러리 경로에 있지 않습니다. Deep Water에 CUDA 8 + cuDNN 5.1을 사용하려면:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Deep Water를 사용하려면:
* 터미널에서: [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) 환경을 활성화한 다음, _python_을 실행합니다. <br/>
* JupyterHub에서: [JupyterHub에 연결](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)한 다음, 샘플 노트북을 찾으려면 deep_water 디렉터리로 이동합니다.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | MXNet은 Windows에서 `C:\dsvm\tools\mxnet`에 그리고 Linux에서 `/dsvm/tools/mxnet`에 설치됩니다. Python 바인딩은 [Linux 및 Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition)의 Python 3.5에 설치되고, [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition)의 Python 3.6에 설치됩니다. 또한 R 바인딩도 Ubuntu에 설치됩니다.   |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함됩니다.    |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | * 터미널에서: 올바른 환경을 활성화한 다음, Python을 실행합니다. <br/> * Jupyter에서: [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) 또는 [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플에 대한 mxnet 디렉터리를 엽니다.  |
 | 빌드 정보 | MXNet는 Linux의 원본에서 빌드됩니다. 이 빌드는 CUDA, cuDNN, NCCL 및 MKL을 포함합니다. |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 모델을 빠르게 훈련하기 위한 NVIDIA의 심층 교육 시스템      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | DIGITS은 `/dsvm/tools/DIGITS`에 설치되며 _digits_이란 서비스로 사용할 수 있습니다.   |

### <a name="how-to-use--run-it"></a>사용/실행 방법

X2Go를 사용하여 VM에 로그인합니다. 터미널에서 서비스를 시작합니다.

    sudo systemctl start digits

서비스는 시작하는 데 약 1분이 걸립니다. 웹 브라우저를 시작하여 `http://localhost:5000` 로 이동합니다. DIGITS는 보안 로그인을 제공하지 않으며 VM 외부에 공개하면 안 됩니다.



## <a name="nvidia-smi"></a>nvidia-smi

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | GPU 작업을 쿼리하기 위한 NVIDIA 도구      |
| 지원되는 DSVM 버전      | Windows, Linux     |
| DSVM에 구성/설치 방법  | _nvidia smi_는 시스템 경로에서 사용할 수 있습니다.   |
| 사용/실행 방법 | 명령 프롬프트(Windows) 또는 터미널(Linux)을 시작한 후 _nvidia smi_를 실행합니다.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Theano는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다.   |
| DSVM의 관련 도구      | Keras      |
| 사용/실행 방법    | * 터미널에서 원하는 Python 버전(루트 또는 py35)을 활성화하고, python을 실행한 다음, theano를 가져옵니다. <br/> * Jupyter에서 Python 2.7 또는 3.5 커널을 선택한 다음, theano를 가져옵니다.  <br/>최근 MKL 버그를 해결하려면 먼저 MKL 스레딩 계층을 설정해야 합니다.<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | Torch는 `/dsvm/tools/torch`에 설치됩니다. PyTorch는 Python 2.7(_루트_)은 물론 Python 3.5(_py35_) 환경에 설치됩니다.   |
| 샘플에 대한 링크      | Torch 샘플은 `/dsvm/samples/torch`에 있습니다. PyTorch 샘플은 `/dsvm/samples/pytorch`에 있습니다.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 심층 학습 프레임워크      |
| 지원되는 DSVM 버전      | Linux     |
| DSVM에 구성/설치 방법  | PyTorch는 [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition)에 설치됩니다.  |
| 샘플에 대한 링크      | 샘플 Jupyter 노트북이 포함되며 /dsvm/samples/pytorch에도 샘플이 있습니다.      |
| DSVM의 관련 도구      | Torch      |
| 사용/실행 방법 |* 터미널에서: 올바른 환경을 활성화한 다음, Python을 실행합니다. <br/> * Jupyter에서: [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux)에 연결한 다음, 샘플에 대한 PyTorch 디렉터리를 엽니다.  |

## <a name="mxnet-model-server"></a>MXNet 모델 서버

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | MXNet 및 ONNX 모델에 대한 HTTP 엔드포인트를 만들 서버      |
| 지원되는 DSVM 버전      | Linux     |
| DSVM에 구성/설치 방법  | _mxnet-model-server_는 터미널에서 사용할 수 있습니다.   |
| 샘플에 대한 링크      | [MXNet 모델 서버 페이지](https://github.com/awslabs/mxnet-model-server)에서 최신 샘플을 확인할 수 있습니다.    |
| DSVM의 관련 도구      | MXNet      |

## <a name="tensorflow-serving"></a>TensorFlow 서비스

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | TensorFlow 모델에서 유추를 실행하는 서버      |
| 지원되는 DSVM 버전      | Linux     |
| DSVM에 구성/설치 방법  | _tensorflow_model_server_는 터미널에서 제공됩니다.   |
| 샘플에 대한 링크      | 샘플은 [온라인](https://www.tensorflow.org/serving/)으로 제공됩니다.      |
| DSVM의 관련 도구      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| 무엇인가요?   | NVIDIA의 딥 러닝 유추 서버입니다. |
| 지원되는 DSVM 버전      | Ubuntu     |
| DSVM에 구성/설치 방법  | TensorRT는 _apt_ 패키지로 설치됩니다.   |
| 샘플에 대한 링크      | 샘플은 [온라인](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples)으로 제공됩니다.      |
| DSVM의 관련 도구      | TensorFlow 서비스, MXNet 모델 서버  |



