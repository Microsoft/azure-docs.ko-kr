---
ms.openlocfilehash: 46e8609be2ec57600e666ef9dab33eae2900e1ce
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691121"
---
1. Visual Studio Code에서 *src/edge*로 이동합니다. *.env* 파일 및 몇 가지 배포 템플릿 파일이 표시됩니다.

    배포 템플릿은 변수가 일부 속성에 사용된 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. *.env* 파일에는 이러한 변수에 대한 값이 포함되어 있습니다.
1. *src/cloud-to-device-console-app* 폴더로 이동합니다. 여기에는 *appsettings.json* 파일 및 몇 가지 다른 파일이 표시됩니다.

    * ***operations.json*** - 프로그램을 실행하려는 작업의 목록입니다.
    * **main.py** - 샘플 프로그램 코드입니다. 이 코드에서는 다음을 수행합니다.
    
      * 앱 설정을 로드합니다.
      * Live Video Analytics on IoT Edge 모듈에서 노출되는 직접 메서드를 호출합니다. 모듈을 사용하면 [직접 메서드](../../../direct-methods.md)를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
      * **터미널** 창에서 프로그램의 출력을 검사하고, **출력** 창에서 모듈에서 생성된 이벤트를 검사할 수 있도록 일시 중지합니다.
      * 리소스를 정리하는 직접 메서드를 호출합니다.   

