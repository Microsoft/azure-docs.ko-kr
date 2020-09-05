---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 6705e6f1e988a836a3a9b7e7c4950510fcb2b228
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511362"
---
이 문서에서는 Node.js와 함께 Custom Vision 클라이언트 라이브러리를 사용하여 개체 검색 모델을 빌드하는 방법을 보여줍니다. 프로젝트를 만든 후에는 태그가 지정된 지역을 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 게시된 예측 엔드포인트 URL을 확보하고, 이 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. Node.js 애플리케이션을 빌드하기 위한 템플릿으로 이 예제를 사용하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Node.js 8](https://www.nodejs.org/en/download/) 이상이 설치되어 있어야 합니다.
- [npm](https://www.npmjs.com/)이 설치되어 있어야 합니다.
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [node-get-images](../../includes/node-get-images.md)]


## <a name="install-the-custom-vision-client-library"></a>Custom Vision 클라이언트 라이브러리 설치

프로젝트에 Node.js용 Custom Vision Service 클라이언트 라이브러리를 설치하려면 다음 명령을 실행합니다.

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

## <a name="add-the-code"></a>코드 추가

원하는 프로젝트 디렉터리에 *sample.js*라는 새 파일을 만듭니다.

### <a name="create-the-custom-vision-service-project"></a>Custom Vision Service 프로젝트 만들기

새 Custom Vision Service 프로젝트를 만드는 다음 코드를 스크립트에 추가합니다. 적절한 정의에 구독 키를 삽입하고 sampleDataRoot 경로 값을 이미지 폴더 경로로 설정합니다. endPoint 값이 [Customvision.ai](https://www.customvision.ai/)에서 만든 학습 및 예측 엔드포인트와 일치하는지 확인합니다. 개체 검색 프로젝트와 이미지 분류 프로젝트를 만드는 작업 간의 차이점은 **createProject** 호출에 지정되는 도메인입니다.

```javascript
const fs = require('fs');
const util = require('util');
const TrainingApi = require("@azure/cognitiveservices-customvision-training");
const PredictionApi = require("@azure/cognitiveservices-customvision-prediction");
const msRest = require("@azure/ms-rest-js");

const setTimeoutPromise = util.promisify(setTimeout);

const trainingKey = "<your training key>";
const predictionKey = "<your prediction key>";
const predictionResourceId = "<your prediction resource id>";
const sampleDataRoot = "<path to image files>";

const endPoint = "https://<my-resource-name>.cognitiveservices.azure.com/"

const publishIterationName = "detectModel";

const credentials = new msRest.ApiKeyCredentials({ inHeader: { "Training-key": trainingKey } });
const trainer = new TrainingApi.TrainingAPIClient(credentials, endPoint);

/* Helper function to let us use await inside a forEach loop.
 * This lets us insert delays between image uploads to accommodate the rate limit.
 */
async function asyncForEach (array, callback) {
    for (let i = 0; i < array.length; i++) {
        await callback(array[i], i, array);
    }
}

(async () => {
    console.log("Creating project...");
    const domains = await trainer.getDomains()
    const objDetectDomain = domains.find(domain => domain.type === "ObjectDetection");
    const sampleProject = await trainer.createProject("Sample Obj Detection Project", { domainId: objDetectDomain.id });
```

### <a name="create-tags-in-the-project"></a>프로젝트에서 태그 만들기

프로젝트의 분류 태그를 만들려면 *sample.js* 파일의 끝에 다음 코드를 추가합니다.

```javascript
    const forkTag = await trainer.createTag(sampleProject.id, "Fork");
    const scissorsTag = await trainer.createTag(sampleProject.id, "Scissors");
```

### <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

개체 검색 프로젝트의 이미지에 태그를 지정할 때 정규화된 좌표를 사용하여 태그가 지정된 각 개체의 지역을 지정해야 합니다. 

> [!NOTE]
> 영역의 좌표를 표시하는 클릭 및 끌기 유틸리티를 사용하지 않는 경우 [Customvision.ai](https://www.customvision.ai/)에서 웹 UI를 사용할 수 있습니다. 이 예제에서는 좌표가 이미 제공되어 있습니다.

프로젝트에 이미지, 태그 및 지역을 추가하려면 태그를 만든 후 다음 코드를 삽입합니다. 이 자습서에서 지역은 코드의 인라인에 하드 코드됩니다. 지역은 정규화된 좌표에서 경계 상자를 지정하며, 좌표는 왼쪽, 위쪽, 너비, 높이 순서대로 지정됩니다. 단일 일괄 처리에서 최대 64개의 이미지를 업로드할 수 있습니다.

```javascript
const forkImageRegions = {
    "fork_1.jpg": [0.145833328, 0.3509314, 0.5894608, 0.238562092],
    "fork_2.jpg": [0.294117659, 0.216944471, 0.534313738, 0.5980392],
    "fork_3.jpg": [0.09191177, 0.0682516545, 0.757352948, 0.6143791],
    "fork_4.jpg": [0.254901975, 0.185898721, 0.5232843, 0.594771266],
    "fork_5.jpg": [0.2365196, 0.128709182, 0.5845588, 0.71405226],
    "fork_6.jpg": [0.115196079, 0.133611143, 0.676470637, 0.6993464],
    "fork_7.jpg": [0.164215669, 0.31008172, 0.767156839, 0.410130739],
    "fork_8.jpg": [0.118872553, 0.318251669, 0.817401946, 0.225490168],
    "fork_9.jpg": [0.18259804, 0.2136765, 0.6335784, 0.643790841],
    "fork_10.jpg": [0.05269608, 0.282303959, 0.8088235, 0.452614367],
    "fork_11.jpg": [0.05759804, 0.0894935, 0.9007353, 0.3251634],
    "fork_12.jpg": [0.3345588, 0.07315363, 0.375, 0.9150327],
    "fork_13.jpg": [0.269607842, 0.194068655, 0.4093137, 0.6732026],
    "fork_14.jpg": [0.143382356, 0.218578458, 0.7977941, 0.295751631],
    "fork_15.jpg": [0.19240196, 0.0633497, 0.5710784, 0.8398692],
    "fork_16.jpg": [0.140931368, 0.480016381, 0.6838235, 0.240196079],
    "fork_17.jpg": [0.305147052, 0.2512582, 0.4791667, 0.5408496],
    "fork_18.jpg": [0.234068632, 0.445702642, 0.6127451, 0.344771236],
    "fork_19.jpg": [0.219362751, 0.141781077, 0.5919118, 0.6683006],
    "fork_20.jpg": [0.180147052, 0.239820287, 0.6887255, 0.235294119]
};

const scissorsImageRegions = {
    "scissors_1.jpg": [0.4007353, 0.194068655, 0.259803921, 0.6617647],
    "scissors_2.jpg": [0.426470578, 0.185898721, 0.172794119, 0.5539216],
    "scissors_3.jpg": [0.289215684, 0.259428144, 0.403186262, 0.421568632],
    "scissors_4.jpg": [0.343137264, 0.105833367, 0.332107842, 0.8055556],
    "scissors_5.jpg": [0.3125, 0.09766343, 0.435049027, 0.71405226],
    "scissors_6.jpg": [0.379901975, 0.24308826, 0.32107842, 0.5718954],
    "scissors_7.jpg": [0.341911763, 0.20714055, 0.3137255, 0.6356209],
    "scissors_8.jpg": [0.231617644, 0.08459154, 0.504901946, 0.8480392],
    "scissors_9.jpg": [0.170343131, 0.332957536, 0.767156839, 0.403594762],
    "scissors_10.jpg": [0.204656869, 0.120539248, 0.5245098, 0.743464053],
    "scissors_11.jpg": [0.05514706, 0.159754932, 0.799019635, 0.730392158],
    "scissors_12.jpg": [0.265931368, 0.169558853, 0.5061275, 0.606209159],
    "scissors_13.jpg": [0.241421565, 0.184264734, 0.448529422, 0.6830065],
    "scissors_14.jpg": [0.05759804, 0.05027781, 0.75, 0.882352948],
    "scissors_15.jpg": [0.191176474, 0.169558853, 0.6936275, 0.6748366],
    "scissors_16.jpg": [0.1004902, 0.279036, 0.6911765, 0.477124184],
    "scissors_17.jpg": [0.2720588, 0.131977156, 0.4987745, 0.6911765],
    "scissors_18.jpg": [0.180147052, 0.112369314, 0.6262255, 0.6666667],
    "scissors_19.jpg": [0.333333343, 0.0274019931, 0.443627447, 0.852941155],
    "scissors_20.jpg": [0.158088237, 0.04047389, 0.6691176, 0.843137264]
};

console.log("Adding images...");
let fileUploadPromises = [];

const forkDir = `${sampleDataRoot}/Fork`;
const forkFiles = fs.readdirSync(forkDir);

await asyncForEach(forkFiles, async (file) => {
    const region = { tagId : forkTag.id, left : forkImageRegions[file][0], top : forkImageRegions[file][1], width : forkImageRegions[file][2], height : forkImageRegions[file][3] };
    const entry = { name : file, contents : fs.readFileSync(`${forkDir}/${file}`), regions : [region] };
    const batch = { images : [entry] };
    // Wait one second to accommodate rate limit.
    await setTimeoutPromise(1000, null);
    fileUploadPromises.push(trainer.createImagesFromFiles(sampleProject.id, batch));
});

const scissorsDir = `${sampleDataRoot}/Scissors`;
const scissorsFiles = fs.readdirSync(scissorsDir);

await asyncForEach(scissorsFiles, async (file) => {
    const region = { tagId : scissorsTag.id, left : scissorsImageRegions[file][0], top : scissorsImageRegions[file][1], width : scissorsImageRegions[file][2], height : scissorsImageRegions[file][3] };
    const entry = { name : file, contents : fs.readFileSync(`${scissorsDir}/${file}`), regions : [region] };
    const batch = { images : [entry] };
    // Wait one second to accommodate rate limit.
    await setTimeoutPromise(1000, null);
    fileUploadPromises.push(trainer.createImagesFromFiles(sampleProject.id, batch));
});

await Promise.all(fileUploadPromises);
```

### <a name="train-the-project-and-publish"></a>프로젝트 학습 및 게시

이 코드는 예측 모델의 첫 번째 반복을 만든 다음, 해당 반복을 예측 엔드포인트에 게시합니다. 게시된 반복에 부여된 이름은 예측 요청을 보내는 데 사용할 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

```javascript
console.log("Training...");
let trainingIteration = await trainer.trainProject(sampleProject.id);

// Wait for training to complete
console.log("Training started...");
while (trainingIteration.status == "Training") {
    console.log("Training status: " + trainingIteration.status);
    // wait for one second
    await setTimeoutPromise(1000, null);
    trainingIteration = await trainer.getIteration(sampleProject.id, trainingIteration.id)
}
console.log("Training status: " + trainingIteration.status);

// Publish the iteration to the end point
await trainer.publishIteration(sampleProject.id, trainingIteration.id, publishIterationName, predictionResourceId);
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>게시된 반복을 예측 엔드포인트에서 가져와서 사용합니다.

예측 엔드포인트에 이미지를 보내고 예측을 검색하려면 파일의 끝에 다음 코드를 추가합니다.

```javascript
    const predictor_credentials = new msRest.ApiKeyCredentials({ inHeader: { "Prediction-key": predictionKey } });
    const predictor = new PredictionApi.PredictionAPIClient(predictor_credentials, endPoint);

    const testFile = fs.readFileSync(`${sampleDataRoot}/Test/test_od_image.jpg`);

    const results = await predictor.detectImage(sampleProject.id, publishIterationName, testFile)

    // Show results
    console.log("Results:");
    results.predictions.forEach(predictedResult => {
        console.log(`\t ${predictedResult.tagName}: ${(predictedResult.probability * 100.0).toFixed(2)}% ${predictedResult.boundingBox.left},${predictedResult.boundingBox.top},${predictedResult.boundingBox.width},${predictedResult.boundingBox.height}`);
    });
})()
```

## <a name="run-the-application"></a>애플리케이션 실행

*sample.js*를 실행합니다.

```shell
node sample.js
```

애플리케이션의 출력이 콘솔에 표시됩니다. 그러면 테스트 이미지(**samples/vision/images/Test**에 있음)에 태그가 적절하게 지정되는지, 검색 지역이 올바른지 확인할 수 있습니다.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>다음 단계

지금까지 개체 검색 프로세스의 모든 단계를 코드로 수행하는 방법을 살펴보았습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다. 다음 학습 가이드에서는 이미지 분류를 다루지만, 원칙은 개체 검색과 비슷합니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)
