---
title: FPGA 정의-배포 하는 방법
titleSuffix: Azure Machine Learning
description: 매우 낮은 대기 시간 유추를 위해 Azure Machine Learning를 사용 하 여 FPGA에서 실행 되는 모델을 사용 하 여 웹 서비스를 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 06/03/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 263038ee1be8b31d493282cd920f386132420156
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648788"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>FPGA (필드 프로그래밍 가능 게이트 배열) 및 배포 방법

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 FPGA (필드 프로그래밍 가능 게이트 배열)에 대해 소개 하 고 Azure FPGA에 [Azure Machine Learning](overview-what-is-azure-ml.md) 를 사용 하 여 모델을 배포 하는 방법을 보여 줍니다.

## <a name="what-are-fpgas"></a>FPGAs

FPGA는 프로그래밍 가능한 논리 블록 배열과 재구성 가능한 상호 연결 계층 구조를 포함하고 있습니다. 제조 후 상호 연결을 통해 이러한 블록을 다양한 방법으로 구성할 수 있습니다. 다른 칩과 비교해서, FPGA는 프로그래밍 기능 및 성능 조합을 제공합니다. 

![Azure Machine Learning FPGA 비교 다이어그램](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|프로세서| 약어 |설명|
|---|:-------:|------|
|애플리케이션 관련 집적 회로|ASIC|Google의 TPU(TensorFlow Processor Units) 같은 사용자 지정 회로는 가장 높은 효율성을 제공합니다. 이러한 회로는 변하는 요구 사항에 따라 재구성할 수 없습니다.|
|Field-programmable Gate Arrays|FPGA|Azure에서 사용할 수 있는 것과 같은 FPGA는 ASIC에 가까운 성능을 제공합니다. 또한 유연하고, 시간 경과에 따라 새 논리를 구현하기 위해 다시 구성할 수 있습니다.|
|그래픽 처리 장치|GPU|GPU는 AI 계산에 널리 사용되며, CPU보다 이미지 렌더링 속도가 빠른 병렬 처리 기능을 제공합니다.|
|중앙 처리 장치|CPU|범용 프로세서이며, 성능이 그래픽 및 비디오 처리에 적합하지 않습니다.|


FPGAs는 실시간 유추 (또는 모델 점수 매기기) 요청에 대해 짧은 대기 시간을 달성할 수 있도록 합니다. 비동기 요청(일괄 처리)이 필요하지 않습니다. 일괄 처리를 사용하면 더 많은 데이터를 처리해야 하므로 대기 시간이 길어질 수 있습니다. 신경망을 구현 하려면 일괄 처리를 수행 하지 않아도 됩니다. 따라서 CPU 및 GPU 프로세서에 비해 대기 시간이 더 낮을 수 있습니다.

여러 형식의 기계 학습 모델에 대해 FPGA를 다시 구성할 수 있습니다. 이러한 유연성 덕분에 사용되는 최적의 숫자 정밀도 및 메모리 모델에 따라 애플리케이션을 가속화할 수 있습니다. FPGA는 다시 구성할 수 있으므로 빠르게 변화하는 AI 알고리즘의 요구 사항에 맞게 최신 상태를 유지할 수 있습니다.

### <a name="fpga-support-in-azure"></a>Azure의 FPGA 지원

Microsoft Azure는 FPGA 부문에서 세계 최대의 클라우드 투자 규모를 자랑합니다. Microsoft에서는 DNN 평가, Bing 검색 순위, SDN(소프트웨어 정의 네트워킹) 가속화에 FPGA를 사용하여 대기 시간을 줄이는 한편, CPU 리소스를 회수하여 다른 작업에 사용합니다.

Azure의 FPGAs는 데이터 과학자와 개발자가 실시간 AI 계산을 가속화 하기 위해 사용 하는 Intel의 FPGA 장치를 기반으로 합니다. 이 FPGA 지원 아키텍처는 강력한 성능, 유연성 및 확장성을 제공하며 Azure에서 사용할 수 있습니다.

Azure FPGAs는 Azure Machine Learning와 통합 됩니다. Azure는 FPGAs 사전 학습 된 DNN (심층 신경망)를 병렬화 하 여 서비스를 확장할 수 있습니다. DNN은 전송 학습을 위한 심층 기능 기능화기로 미리 학습하거나 업데이트된 가중치를 사용하여 미세 조정할 수 있습니다.

Azure의 FPGAs는 다음을 지원 합니다.

+ 이미지 분류 및 인식 시나리오
+ TensorFlow 배포 (Tensorflow 1.x 필요)
+ Intel FPGA 하드웨어

이러한 DNN 모델을 현재 사용할 수 있습니다.

  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

  
FPGAs는 다음 Azure 지역에서 사용할 수 있습니다.
  - 미국 동부
  - 동남아시아
  - 서유럽
  - 미국 서부 2

대기 시간 및 처리량을 최적화 하기 위해 FPGA 모델에 데이터를 보내는 클라이언트는 위의 영역 중 하나 (모델을 배포한 항목)에 있어야 합니다.

**Azure vm의 PBS 패밀리** 에는 Intel Arria 10 fpgas 포함 됩니다. Azure 할당량 할당을 확인 하는 경우 "표준 PBS 제품군 vCPUs"로 표시 됩니다. PB6 VM에는 6 개의 vCPUs와 하나의 FPGA가 있으며, FPGA에 모델을 배포 하는 과정에서 Azure ML에 의해 자동으로 프로 비전 됩니다. Azure ML 에서만 사용 되며 임의의 bitstreams을 실행할 수 없습니다. 예를 들어 bitstreams을 사용 하 여 FPGA을 플래시 하 여 암호화, 인코딩 등을 수행할 수 없습니다.


## <a name="deploy-models-on-fpgas"></a>FPGA에 모델 배포

[Azure Machine Learning 하드웨어 가속 모델](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py)를 사용 하 여 모델을 fpgas에서 웹 서비스로 배포할 수 있습니다. FPGAs를 사용 하면 단일 일괄 처리 크기를 사용 하는 경우에도 매우 짧은 대기 시간 유추가 가능 합니다. 유추 또는 모델 점수 매기기는 배포 된 모델이 예측에 사용 되는 단계 이며 가장 일반적으로 프로덕션 데이터에 사용 됩니다.

FPGA에 모델을 배포 하려면 다음 단계를 수행 해야 합니다.

1. TensorFlow 모델 정의
1. 모델을 ONNX로 변환
1. 클라우드 또는에 지 장치에 모델 배포
1. 배포된 모델 사용

이 샘플에서는 TensorFlow 그래프를 만들어 입력 이미지를 전처리 하 고, FPGA에서 ResNet 50을 사용 하 여 featurizer 만든 다음, ImageNet 데이터 집합에 대해 학습 한 분류자를 통해 기능을 실행 합니다. 그런 다음 AKS 클러스터에 모델을 배포 합니다.

### <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 계정이 없는 경우 [종 량](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) 제 계정을 만들어야 합니다 (무료 AZURE 계정은 FPGA 할당량에 적합 하지 않음).
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- FPGA 할당량입니다. Azure CLI를 사용 하 여 할당량이 있는지 여부를 확인 합니다.

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```


    명령은 다음과 유사한 텍스트를 반환 합니다.

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    __Currentvalue__아래에 6 개 이상의 vcpus가 있는지 확인 합니다.

    할당량이 없는 경우에서 요청을 제출 [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI) 합니다.

- Azure Machine Learning 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
 
- 하드웨어 가속 모델용 Python SDK:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```
### <a name="1-define-the-tensorflow-model"></a>1. TensorFlow 모델을 정의 합니다.

[Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용하여 서비스 정의를 만듭니다. 서비스 정의는 TensorFlow를 기반으로 그래프(입력, 기능화기 및 분류자) 파이프라인을 설명하는 파일입니다. 배포 명령은 자동으로 정의 및 그래프를 ZIP 파일로 압축하고, ZIP 파일을 Azure Blob Storage에 업로드합니다. DNN가 FPGA에서 실행 되도록 이미 배포 되었습니다.

1. Azure Machine Learning 작업 영역 로드

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. 이미지를 전처리 합니다. 웹 서비스에 대 한 입력은 JPEG 이미지입니다.  첫 번째 단계는 JPEG 이미지를 디코딩하고 전처리 하는 것입니다.  JPEG 이미지는 문자열로 처리 되 고 결과는 tensors 이며,이는 ResNet 50 모델에 대 한 입력입니다.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Load featurizer. 모델을 초기화하고 기능화기로 사용할 ResNet50의 양자화 버전에 대한 TensorFlow 검사점을 다운로드합니다.  다른 심층 신경망을 가져오면 아래 코드 조각에서 "QuantizedResnet50"를으로 바꿀 수 있습니다.

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. 분류자를 추가 합니다. 이 분류자는 ImageNet 데이터 집합을 기반으로 학습되었습니다.  사용자 지정 가중치에 대 한 전송 학습 및 학습 예제는 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks)집합에서 사용할 수 있습니다.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. 모델을 저장합니다. 이제 전처리기, ResNet 50 featurizer 및 분류자가 로드 되었으므로 그래프 및 연관 된 변수를 모델로 저장 합니다.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. 입력 및 출력 tensors을 저장 합니다. 전처리 및 분류자 단계 중에 만들어진 입력 및 출력 tensors 모델 변환 및 유추에 필요 합니다.

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   > [!IMPORTANT]
   > 모델 변환 및 유추 요청에 필요 하므로 입력 및 출력 tensors을 저장 합니다.

   사용 가능한 모델 및 해당 기본 분류자 출력 tensors은 아래와 같습니다. 기본 분류자를 사용 하는 경우 유추에 사용 합니다.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="2-convert-the-model"></a>2. 모델 변환

FPGAs에 모델을 배포 하기 전에이를 ONNX 형식으로 변환 해야 합니다.

1. SDK를 사용 하 여 Azure Blob storage에서 ZIP 파일을 사용 하 여 모델을 [등록](concept-model-management-and-deployment.md) 합니다. 모델에 대 한 태그 및 기타 메타 데이터를 추가 하면 학습 된 모델을 추적 하는 데 도움이 됩니다.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   이미 모델을 등록 하 고 로드 하려면 모델을 검색할 수 있습니다.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. TensorFlow graph를[Onnx](https://onnx.ai/)(Open 신경망 Exchange format)로 변환 합니다.  입력 및 출력 tensors의 이름을 제공 해야 하며,이 이름은 웹 서비스를 사용할 때 클라이언트에서 사용 됩니다.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="3-containerize-and-deploy-the-model"></a>3. 모델 컨테이너 화 및 배포

변환 된 모델 및 모든 종속성에서 Docker 이미지를 만듭니다.  그런 다음이 Docker 이미지를 배포 하 고 인스턴스화할 수 있습니다.  지원 되는 배포 대상에는 클라우드 또는 [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)와 같은에 지 장치에 있는 AKS 포함 됩니다.  등록 된 Docker 이미지에 대 한 태그 및 설명을 추가할 수도 있습니다.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   태그로 이미지를 나열 하 고 디버깅에 대 한 자세한 로그를 가져옵니다.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-aks-cluster"></a>AKS 클러스터에 배포

1. 모델을 확장성이 뛰어난 프로덕션 웹 서비스로 배포하려면 AKS(Azure Kubernetes Service)를 사용합니다. Azure Machine Learning SDK, CLI 또는 [Azure Machine Learning studio](https://ml.azure.com)를 사용 하 여 새 항목을 만들 수 있습니다.

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    AKS 배포는 약 15 분 정도 걸릴 수 있습니다.  배포가 성공 했는지 확인 합니다.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. AKS 클러스터에 컨테이너를 배포 합니다.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>로컬에 지 서버에 배포

모든 [Azure Data Box Edge 장치](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) 에는 모델을 실행 하기 위한 FPGA이 포함 되어 있습니다.  한 번에 하나의 모델만 FPGA에서 실행할 수 있습니다.  다른 모델을 실행 하려면 새 컨테이너를 배포 하기만 하면 됩니다. 지침 및 샘플 코드는 [이 Azure 샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models)에서 찾을 수 있습니다.

### <a name="4-consume-the-deployed-model"></a>4. 배포 된 모델 사용

Docker 이미지는 gRPC와 "predict" API를 제공 하는 TensorFlow을 지원 합니다.  샘플 클라이언트를 사용 하 여 Docker 이미지를 호출 하 여 모델에서 예측을 가져옵니다.  샘플 클라이언트 코드를 사용할 수 있습니다.
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

TensorFlow 서비스를 사용 하려는 경우 [샘플 클라이언트를 다운로드할](https://www.tensorflow.org/serving/setup)수 있습니다.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

이 분류자는 [Imagenet](http://www.image-net.org/) 데이터 집합에 대해 학습 되었으므로 클래스를 사람이 읽을 수 있는 레이블에 매핑합니다.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

## <a name="clean-up-resources"></a>리소스 정리

웹 서비스, 이미지 및 모델을 삭제 합니다 (종속성이 있으므로이 순서 대로 수행 해야 함).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>다음 단계

이러한 노트북, 비디오 및 블로그를 확인 하세요.

+ 여러 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/accelerated-models)
+ FPGA 웹 서비스를 보호 하려면 [보안 웹 서비스](how-to-secure-web-service.md) 문서를 참조 하세요.
+ [하이퍼 크기 조정 하드웨어: Azure + FPGA의 맨 위에 있는 ML: 빌드 2018 (비디오)](https://channel9.msdn.com/events/Build/2018/BRK3202)
+ [Microsoft FPGA 기반 구성 가능 클라우드 살펴보기(비디오)](https://channel9.msdn.com/Events/Build/2017/B8063)
+ [실시간 AI용 Project Brainwave: 프로젝트 홈페이지](https://www.microsoft.com/research/project/project-brainwave/)
+ [자동 광학 검사 시스템](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
+ [육지 커버 매핑](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)
