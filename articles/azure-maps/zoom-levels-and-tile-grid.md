---
title: 확대/축소 수준 및 타일 그리드 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure 맵의 확대/축소 수준 및 타일 그리드에 대해 알아봅니다.
author: jingjing-z
ms.author: jinzh
ms.date: 01/22/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 6ee697ac9b7849a0231d9916c6fa8bc73ef7f9b7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76765850"
---
# <a name="zoom-levels-and-tile-grid"></a>확대/축소 수준 및 타일 그리드

Azure Maps에서는 구면 메르카토르 도법 구면좌표계를 사용합니다(EPSG: 3857). 프로젝션은 구형을 플랫 맵으로 변환 하는 데 사용 되는 수치 모델입니다. 구면 Mercator 프로젝션은 극 지방에서 지도를 확장 하 여 사각형 지도를 만듭니다. 이 프로젝션을 지도의 크기와 영역을 크게 왜곡 하지만 두 가지 중요 한 속성은이 왜곡 보다 큽니다.

- 이는 상대적으로 작은 개체의 모양을 유지 하는 conformal 프로젝션입니다. 항공 이미지를 표시할 때 작은 개체의 모양을 유지 하는 것이 특히 중요 합니다. 예를 들어 빌딩의 모양이 왜곡 되지 않도록 하려고 합니다. 정사각형 빌딩은 사각형이 아닌 사각형을 표시 해야 합니다.
- 원통 프로젝션입니다. 북쪽 및 남부는 항상 위쪽 및 아래쪽 이며 서 부 및 동부는 항상 왼쪽과 오른쪽입니다. 

지도 검색 및 표시의 성능을 최적화 하기 위해 지도는 사각형 타일로 나뉩니다. Azure Maps SDK는도로 지도에 512 x 512 픽셀 크기를 가지 며 위성 이미지에는 작은 256 x 256 픽셀을 사용 하는 타일을 사용 합니다. Azure Maps는 23 확대/축소 수준에 대한 래스터 및 벡터 타일을 제공 하며 0부터 22까지 번호가 지정 됩니다. 확대/축소 수준 0에서 전 세계는 단일 타일로 맞춰집니다.

<center>

![세계 지도 타일](./media/zoom-levels-and-tile-grid/world0.png)</center>

확대/축소 수준 1은 4개의 타일을 사용하여 전 세계를 2x2 사각형으로 렌더링합니다.

<center>

![2x2 지도 타일 레이아웃](media/zoom-levels-and-tile-grid/map-2x2-tile-layout.png)</center>

각 추가 확대/축소 수준 4는 이전 타일의 타일을 분할 하 여 2<sup>줌</sup> x 2<sup>확대/축소</sup>그리드를 만듭니다. 확대/축소 수준 22는 2<sup>22</sup> x 2<sup>22</sup> 그리드 또는 4,194,304 x 4,194,304 타일(총 17,592,186,044,416개)입니다.

웹 및 Android에 대한 대화형 맵 컨트롤 Azure Maps 25 개의 확대/축소 수준 (0-24)을 지원 합니다. 타일을 사용할 수 있는 경우의 확대/축소 수준 에서만도로 데이터를 사용할 수 있습니다.

다음 표에서는 타일 크기가 512 픽셀 정사각형 인 확대/축소 수준에 대한 값의 전체 목록을 제공 합니다.

|확대/축소 수준|미터/픽셀|미터/타일 측|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0.075|19.1|
|22|0.0375|9.55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

## <a name="pixel-coordinates"></a>픽셀 좌표

각 확대/축소 수준에서 사용할 투영 및 배율을 선택한 경우 지리적 좌표를 픽셀 좌표로 변환할 수 있습니다. 특정 확대/축소 수준에 대한 전 세계 지도 이미지의 전체 픽셀 너비와 높이는 다음과 같이 계산 됩니다.

```javascript
var mapWidth = tileSize * Math.pow(2, zoom);

var mapHeight = mapWidth;
```

각 확대/축소 수준에서 지도 너비와 높이가 다르기 때문에 픽셀 좌표가 됩니다. 맵의 왼쪽 위 모퉁이에 있는 픽셀에는 항상 픽셀 좌표 (0, 0)가 있습니다. 지도의 오른쪽 아래 모퉁이에 있는 픽셀의 픽셀 좌표 *(width-1, height-1)* 또는 이전 섹션의 방정식 *(tileSize \* 2<sup>Zoom</sup>– 1, tileSize \* 2<sup>zoom</sup>– 1)* 을 참조 합니다. 예를 들어 수준 2에서 512 정사각형 타일을 사용 하는 경우 픽셀 좌표는 (0, 0)에서 (2047, 2047)와 같이 범위를 조정 합니다.

<center>

![픽셀 차원을 표시 하는 맵](media/zoom-levels-and-tile-grid/map-width-height.png)

</center>

지정 된 위도 및 경도 (도) 및 세부 정보 수준에서 픽셀 XY 좌표는 다음과 같이 계산 됩니다.

```javascript
var sinLatitude = Math.sin(latitude * Math.PI/180);

var pixelX = ((longitude + 180) / 360) * tileSize * Math.pow(2, zoom);

var pixelY = (0.5 – Math.log((1 + sinLatitude) / (1 – sinLatitude)) / (4 * Math.PI)) * tileSize * Math.pow(2, zoom);
```

위도 및 경도 값은 WGS 84 데이텀에 있는 것으로 간주 됩니다. Azure Maps에서 구형 프로젝션을 사용 하는 경우에도 모든 지리적 좌표를 공통 데이텀으로 변환 하는 것이 중요 합니다. 선택한 데이텀이 WGS 84입니다. 경도 값은-180도에서 + 180까지로 간주 되 고, 위도 값은-85.05112878에서 85.05112878 사이의 범위로 잘라내야 합니다. 이러한 값을 준수 하면 극 지방에서 특이성가 방지 되 고 투영 된 맵이 제곱 된 모양이 됩니다.

## <a name="tile-coordinates"></a>타일 좌표

지도 검색 및 표시의 성능을 최적화 하기 위해 렌더링 된 맵은 타일로 잘립니다. 각 확대/축소 수준 마다 픽셀 수와 타일 수가 다릅니다.

```javascript
var numberOfTilesWide = Math.pow(2, zoom);

var numberOfTilesHigh = numberOfTilesWide;
```

각 타일에는 오른쪽 아래에서 (0, 0) 왼쪽 위에서 ( *2<sup>확대/축소</sup>– 1, 2<sup>줌</sup>– 1)* 까지의 XY 좌표가 지정 됩니다. 예를 들어 확대/축소 수준 2에서 타일은 (0, 0)에서 (7, 7) 사이의 범위를 다음과 같이 조정 합니다.

<center>

타일 좌표의 ![맵](media/zoom-levels-and-tile-grid/map-tiles-x-y-coordinates-7x7.png)</center>

픽셀 XY 좌표 쌍이 지정 된 경우 해당 픽셀이 포함 된 타일의 타일 XY 좌표를 쉽게 확인할 수 있습니다.

```javascript
var tileX = Math.floor(pixelX / tileSize);

var tileY = Math.floor(pixelY / tileSize);
```

타일은 확대/축소 수준에 의해 호출 됩니다. X 및 y 좌표는 해당 확대/축소 수준에 대한 표의 타일 위치에 해당 합니다.

사용할 확대/축소 수준을 결정 하는 경우 각 위치는 해당 타일의 고정 위치에 있습니다. 결과적으로, 지정 된 expanse 영토를 표시 하는 데 필요한 타일 수는 세계 지도의 확대/축소 그리드의 특정 배치에 따라 달라 집니다. 예를 들어 900미터 떨어진 두 개의 지점이 있는 경우 확대/축소 수준 17에서 서로 간에 경로를 표시하기 위해 세 개의 타일만을 사용할 *수* 있습니다. 그러나 서쪽 지점이 해당 타일의 오른쪽에 있고 동쪽 지점이 해당 타일의 왼쪽에 있는 경우 4개의 타일이 필요할 수 있습니다.

<center>

![확대/축소 데모 크기 조정](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png)</center>

확대/축소 수준이 결정되면 x 및 y 값을 계산할 수 있습니다. 각 확대/축소 모눈의 왼쪽 위 타일은 x = 0, y = 0;입니다. 오른쪽 아래에 있는 타일은 x = 2<sup>확대/축소-1</sup>, y = 2<sup>확대/축소-1</sup>입니다.

확대/축소 수준 1에서 확대/축소 그리드는 다음과 같습니다.

<center>

확대/축소 수준 1에 대한 확대/축소 그리드 ![](media/zoom-levels-and-tile-grid/api_x_y.png)</center>

## <a name="quadkey-indices"></a>Quadkey 인덱스

일부 매핑 플랫폼에서는 `quadkey` 인덱싱 명명 규칙을 사용 합니다 .이 규칙은 타일 ZY 좌표를 `quadtree` 키 라고 하는 1 차원 문자열 또는 짧은 `quadkeys` 결합 합니다. 각 `quadkey`는 특정 세부 수준에서 단일 타일을 고유 하 게 식별 하며 일반적인 데이터베이스 B-트리 인덱스에서 키로 사용할 수 있습니다. Azure Maps Sdk는 [타일 계층 추가](map-add-tile-layer.md) 문서에 설명 된 대로 다른 명명 규칙 외에도 `quadkey` 명명 규칙을 사용 하는 타일 계층의 오버레이를 지원 합니다.

> [!NOTE]
> `quadkeys` 명명 규칙은 하나 이상의 확대/축소 수준 에서만 작동 합니다. Azure Maps SDK의 지원 확대/축소 수준 0은 전 세계의 단일 지도 타일입니다. 

타일 좌표를 `quadkey`변환 하기 위해 Y 및 X 좌표의 비트는 인터리브 되며, 결과는 기본-4 숫자로 해석 되 고 (앞에 오는 0이 유지 됨) 문자열로 변환 됩니다. 예를 들어 수준 3에서 (3, 5)의 타일 XY 좌표를 지정 하는 경우 `quadkey`은 다음과 같이 결정 됩니다.

```
tileX = 3 = 011 (base 2)

tileY = 5 = 1012 (base 2)

quadkey = 100111 (base 2) = 213 (base 4) = "213"
```

`Qquadkeys`에는 여러 가지 흥미로운 속성이 있습니다. 첫째, `quadkey` (자릿수)의 길이는 해당 타일의 확대/축소 수준과 같습니다. 둘째, 모든 타일의 `quadkey` 부모 타일 (이전 수준의 포함 하는 타일)의 `quadkey` 시작 합니다. 아래 예제와 같이 타일 2는 20부터 23까지 타일의 부모입니다.

<center>

![Quadkey 타일 피라미드](media/zoom-levels-and-tile-grid/quadkey-tile-pyramid.png)</center>

마지막으로, 일반적으로 XY 공간에서 타일의 근접성을 유지 하는 1 차원 인덱스 키를 제공 `quadkeys` 합니다. 즉, XY 좌표가 가까운 두 타일에는 일반적으로 서로 가까운 `quadkeys` 있습니다. 이는 데이터베이스 성능을 최적화 하는 데 중요 합니다. 인접 한 타일은 종종 그룹에서 요청 되므로 디스크 읽기 수를 최소화 하기 위해 이러한 타일을 동일한 디스크 블록에 보관 하는 것이 좋습니다.

## <a name="tile-math-source-code"></a>수식 소스 코드 바둑판식 배열

다음 샘플 코드에서는이 문서에서 설명 하는 함수를 구현 하는 방법을 보여 줍니다. 이러한 함수는 필요에 따라 다른 프로그래밍 언어로 쉽게 변환할 수 있습니다.

#### <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Text;

namespace AzureMaps
{
    /// <summary>
    /// Tile System math for the Spherical Mercator projection coordinate system (EPSG:3857)
    /// </summary>
    public static class TileMath
    {
        //Earth radius in meters.
        private const double EarthRadius = 6378137;

        private const double MinLatitude = -85.05112878;
        private const double MaxLatitude = 85.05112878;
        private const double MinLongitude = -180;
        private const double MaxLongitude = 180;

        /// <summary>
        /// Clips a number to the specified minimum and maximum values.
        /// </summary>
        /// <param name="n">The number to clip.</param>
        /// <param name="minValue">Minimum allowable value.</param>
        /// <param name="maxValue">Maximum allowable value.</param>
        /// <returns>The clipped value.</returns>
        private static double Clip(double n, double minValue, double maxValue)
        {
            return Math.Min(Math.Max(n, minValue), maxValue);
        }

        /// <summary>
        /// Calculates width and height of the map in pixels at a specific zoom level from -180 degrees to 180 degrees.
        /// </summary>
        /// <param name="zoom">Zoom Level to calculate width at</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>Width and height of the map in pixels</returns>
        public static double MapSize(double zoom, int tileSize)
        {
            return Math.Ceiling(tileSize * Math.Pow(2, zoom));
        }

        /// <summary>
        /// Calculates the Ground resolution at a specific degree of latitude in meters per pixel.
        /// </summary>
        /// <param name="latitude">Degree of latitude to calculate resolution at</param>
        /// <param name="zoom">Zoom level to calculate resolution at</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>Ground resolution in meters per pixels</returns>
        public static double GroundResolution(double latitude, double zoom, int tileSize)
        {
            latitude = Clip(latitude, MinLatitude, MaxLatitude);
            return Math.Cos(latitude * Math.PI / 180) * 2 * Math.PI * EarthRadius / MapSize(zoom, tileSize);
        }

        /// <summary>
        /// Determines the map scale at a specified latitude, level of detail, and screen resolution.
        /// </summary>
        /// <param name="latitude">Latitude (in degrees) at which to measure the map scale.</param>
        /// <param name="zoom">Level of detail, from 1 (lowest detail) to 23 (highest detail).</param>
        /// <param name="screenDpi">Resolution of the screen, in dots per inch.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>The map scale, expressed as the denominator N of the ratio 1 : N.</returns>
        public static double MapScale(double latitude, double zoom, int screenDpi, int tileSize)
        {
            return GroundResolution(latitude, zoom, tileSize) * screenDpi / 0.0254;
        }

        /// <summary>
        /// Global Converts a Pixel coordinate into a geospatial coordinate at a specified zoom level. 
        /// Global Pixel coordinates are relative to the top left corner of the map (90, -180)
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A position value in the format [longitude, latitude].</returns>
        public static double[] GlobalPixelToPosition(double[] pixel, double zoom, int tileSize)
        {
            var mapSize = MapSize(zoom, tileSize);

            var x = (Clip(pixel[0], 0, mapSize - 1) / mapSize) - 0.5;
            var y = 0.5 - (Clip(pixel[1], 0, mapSize - 1) / mapSize);

            return new double[] {
                360 * x,    //Longitude
                90 - 360 * Math.Atan(Math.Exp(-y * 2 * Math.PI)) / Math.PI  //Latitude
            };
        }

        /// <summary>
        /// Converts a point from latitude/longitude WGS-84 coordinates (in degrees) into pixel XY coordinates at a specified level of detail.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param> 
        /// <returns>A global pixel coordinate.</returns>
        public static double[] PositionToGlobalPixel(double[] position, int zoom, int tileSize)
        {
            var latitude = Clip(position[1], MinLatitude, MaxLatitude);
            var longitude = Clip(position[0], MinLongitude, MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.Sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.Log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            var mapSize = MapSize(zoom, tileSize);

            return new double[] {
                 Clip(x * mapSize + 0.5, 0, mapSize - 1),
                 Clip(y * mapSize + 0.5, 0, mapSize - 1)
            };
        }

        /// <summary>
        /// Converts pixel XY coordinates into tile XY coordinates of the tile containing the specified pixel.
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="tileX">Output parameter receiving the tile X coordinate.</param>
        /// <param name="tileY">Output parameter receiving the tile Y coordinate.</param>
        public static void GlobalPixelToTileXY(double[] pixel, int tileSize, out int tileX, out int tileY)
        {
            tileX = (int)(pixel[0] / tileSize);
            tileY = (int)(pixel[1] / tileSize);
        }

        /// <summary>
        /// Performs a scale transform on a global pixel value from one zoom level to another.
        /// </summary>
        /// <param name="pixel">Pixel coordinates in the format of [x, y].</param>  
        /// <param name="oldZoom">The zoom level in which the input global pixel value is from.</param>  
        /// <returns>A scale pixel coordinate.</returns>
        public static double[] ScaleGlobalPixel(double[] pixel, double oldZoom, double newZoom)
        {
            var scale = Math.Pow(2, oldZoom - newZoom);

            return new double[] { pixel[0] * scale, pixel[1] * scale };
        }

        /// <summary>
        /// Performs a scale transform on a set of global pixel values from one zoom level to another.
        /// </summary>
        /// <param name="pixels">A set of global pixel value from the old zoom level. Points are in the format [x,y].</param>
        /// <param name="oldZoom">The zoom level in which the input global pixel values is from.</param>
        /// <param name="newZoom">The new zoom level in which the output global pixel values should be aligned with.</param>
        /// <returns>A set of global pixel values that has been scaled for the new zoom level.</returns>
        public static double[][] ScaleGlobalPixels(double[][] pixels, double oldZoom, double newZoom)
        {
            var scale = Math.Pow(2, oldZoom - newZoom);

            var output = new System.Collections.Generic.List<double[]>();
            foreach (var p in pixels)
            {
                output.Add(new double[] { p[0] * scale, p[1] * scale });
            }

            return output.ToArray();
        }

        /// <summary>
        /// Converts tile XY coordinates into a global pixel XY coordinates of the upper-left pixel of the specified tile.
        /// </summary>
        /// <param name="tileX">Tile X coordinate.</param>
        /// <param name="tileY">Tile Y coordinate.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="pixelX">Output parameter receiving the X coordinate of the point, in pixels.</param>  
        /// <param name="pixelY">Output parameter receiving the Y coordinate of the point, in pixels.</param>  
        public static double[] TileXYToGlobalPixel(int tileX, int tileY, int tileSize)
        {
            return new double[] { tileX * tileSize, tileY * tileSize };
        }

        /// <summary>
        /// Converts tile XY coordinates into a quadkey at a specified level of detail.
        /// </summary>
        /// <param name="tileX">Tile X coordinate.</param>
        /// <param name="tileY">Tile Y coordinate.</param>
        /// <param name="zoom">Zoom level</param>
        /// <returns>A string containing the quadkey.</returns>
        public static string TileXYToQuadKey(int tileX, int tileY, int zoom)
        {
            var quadKey = new StringBuilder();
            for (int i = zoom; i > 0; i--)
            {
                char digit = '0';
                int mask = 1 << (i - 1);
                if ((tileX & mask) != 0)
                {
                    digit++;
                }
                if ((tileY & mask) != 0)
                {
                    digit++;
                    digit++;
                }
                quadKey.Append(digit);
            }
            return quadKey.ToString();
        }

        /// <summary>
        /// Converts a quadkey into tile XY coordinates.
        /// </summary>
        /// <param name="quadKey">Quadkey of the tile.</param>
        /// <param name="tileX">Output parameter receiving the tile X coordinate.</param>
        /// <param name="tileY">Output parameter receiving the tile Y coordinate.</param>
        /// <param name="zoom">Output parameter receiving the zoom level.</param>
        public static void QuadKeyToTileXY(string quadKey, out int tileX, out int tileY, out int zoom)
        {
            tileX = tileY = 0;
            zoom = quadKey.Length;
            for (int i = zoom; i > 0; i--)
            {
                int mask = 1 << (i - 1);
                switch (quadKey[zoom - i])
                {
                    case '0':
                        break;

                    case '1':
                        tileX |= mask;
                        break;

                    case '2':
                        tileY |= mask;
                        break;

                    case '3':
                        tileX |= mask;
                        tileY |= mask;
                        break;

                    default:
                        throw new ArgumentException("Invalid QuadKey digit sequence.");
                }
            }
        }

        /// <summary>
        /// Calculates the XY tile coordinates that a coordinate falls into for a specific zoom level.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="tileX">Output parameter receiving the tile X position.</param>
        /// <param name="tileY">Output parameter receiving the tile Y position.</param>
        public static void PositionToTileXY(double[] position, int zoom, int tileSize, out int tileX, out int tileY)
        {
            var latitude = Clip(position[1], MinLatitude, MaxLatitude);
            var longitude = Clip(position[0], MinLongitude, MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.Sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.Log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            //tileSize needed in calculations as in rare cases the multiplying/rounding/dividing can make the difference of a pixel which can result in a completely different tile. 
            var mapSize = MapSize(zoom, tileSize);
            tileX = (int)Math.Floor(Clip(x * mapSize + 0.5, 0, mapSize - 1) / tileSize);
            tileY = (int)Math.Floor(Clip(y * mapSize + 0.5, 0, mapSize - 1) / tileSize);
        }

        /// <summary>
        /// Calculates the tile quadkey strings that are within a specified viewport.
        /// </summary>
        /// <param name="position">Position coordinate in the format [longitude, latitude]</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="width">The width of the map viewport in pixels.</param>
        /// <param name="height">The height of the map viewport in pixels.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A list of quadkey strings that are within the specified viewport.</returns>
        public static string[] GetQuadkeysInView(double[] position, int zoom, int width, int height, int tileSize)
        {
            var p = PositionToGlobalPixel(position, zoom, tileSize);

            var top = p[1] - height * 0.5;
            var left = p[0] - width * 0.5;

            var bottom = p[1] + height * 0.5;
            var right = p[0] + width * 0.5;

            var tl = GlobalPixelToPosition(new double[] { left, top }, zoom, tileSize);
            var br = GlobalPixelToPosition(new double[] { right, bottom }, zoom, tileSize);

            //Boudning box in the format: [west, south, east, north];
            var bounds = new double[] { tl[0], br[1], br[0], tl[1] };

            return GetQuadkeysInBoundingBox(bounds, zoom, tileSize);
        }

        /// <summary>
        /// Calculates the tile quadkey strings that are within a bounding box at a specific zoom level.
        /// </summary>
        /// <param name="bounds">A bounding box defined as an array of numbers in the format of [west, south, east, north].</param>
        /// <param name="zoom">Zoom level to calculate tiles for.</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A list of quadkey strings.</returns>
        public static string[] GetQuadkeysInBoundingBox(double[] bounds, int zoom, int tileSize)
        {
            var keys = new System.Collections.Generic.List<string>();

            if (bounds != null && bounds.Length >= 4)
            {
                PositionToTileXY(new double[] { bounds[3], bounds[0] }, zoom, tileSize, out int tlX, out int tlY);
                PositionToTileXY(new double[] { bounds[1], bounds[2] }, zoom, tileSize, out int brX, out int brY);

                for (int x = tlX; x <= brX; x++)
                {
                    for (int y = tlY; y <= brY; y++)
                    {
                        keys.Add(TileXYToQuadKey(x, y, zoom));
                    }
                }
            }

            return keys.ToArray();
        }

        /// <summary>
        /// Calculates the bounding box of a tile.
        /// </summary>
        /// <param name="tileX">Tile X coordinate</param>
        /// <param name="tileY">Tile Y coordinate</param>
        /// <param name="zoom">Zoom level</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <returns>A bounding box of the tile defined as an array of numbers in the format of [west, south, east, north].</returns>
        public static double[] TileXYToBoundingBox(int tileX, int tileY, double zoom, int tileSize)
        {
            //Top left corner pixel coordinates
            var x1 = (double)(tileX * tileSize);
            var y1 = (double)(tileY * tileSize);

            //Bottom right corner pixel coordinates
            var x2 = (double)(x1 + tileSize);
            var y2 = (double)(y1 + tileSize);

            var nw = GlobalPixelToPosition(new double[] { x1, y1 }, zoom, tileSize);
            var se = GlobalPixelToPosition(new double[] { x2, y2 }, zoom, tileSize);

            return new double[] { nw[0], se[1], se[0], nw[1] };
        }

        /// <summary>
        /// Calculates the best map view (center, zoom) for a bounding box on a map.
        /// </summary>
        /// <param name="bounds">A bounding box defined as an array of numbers in the format of [west, south, east, north].</param>
        /// <param name="mapWidth">Map width in pixels.</param>
        /// <param name="mapHeight">Map height in pixels.</param>
        /// <param name="padding">Width in pixels to use to create a buffer around the map. This is to keep markers from being cut off on the edge</param>
        /// <param name="tileSize">The size of the tiles in the tile pyramid.</param>
        /// <param name="latitude">Output parameter receiving the center latitude coordinate.</param>
        /// <param name="longitude">Output parameter receiving the center longitude coordinate.</param>
        /// <param name="zoom">Output parameter receiving the zoom level</param>
        public static void BestMapView(double[] bounds, double mapWidth, double mapHeight, int padding, int tileSize, out double centerLat, out double centerLon, out double zoom)
        {
            if (bounds == null || bounds.Length < 4)
            {
                centerLat = 0;
                centerLon = 0;
                zoom = 1;
                return;
            }

            double boundsDeltaX;

            //Check if east value is greater than west value which would indicate that bounding box crosses the antimeridian.
            if (bounds[2] > bounds[0])
            {
                boundsDeltaX = bounds[2] - bounds[0];
                centerLon = (bounds[2] + bounds[0]) / 2;
            }
            else
            {
                boundsDeltaX = 360 - (bounds[0] - bounds[2]);
                centerLon = ((bounds[2] + bounds[0]) / 2 + 360) % 360 - 180;
            }

            var ry1 = Math.Log((Math.Sin(bounds[1] * Math.PI / 180) + 1) / Math.Cos(bounds[1] * Math.PI / 180));
            var ry2 = Math.Log((Math.Sin(bounds[3] * Math.PI / 180) + 1) / Math.Cos(bounds[3] * Math.PI / 180));
            var ryc = (ry1 + ry2) / 2;

            centerLat = Math.Atan(Math.Sinh(ryc)) * 180 / Math.PI;

            var resolutionHorizontal = boundsDeltaX / (mapWidth - padding * 2);

            var vy0 = Math.Log(Math.Tan(Math.PI * (0.25 + centerLat / 360)));
            var vy1 = Math.Log(Math.Tan(Math.PI * (0.25 + bounds[3] / 360)));
            var zoomFactorPowered = (mapHeight * 0.5 - padding) / (40.7436654315252 * (vy1 - vy0));
            var resolutionVertical = 360.0 / (zoomFactorPowered * tileSize);

            var resolution = Math.Max(resolutionHorizontal, resolutionVertical);

            zoom = Math.Log(360 / (resolution * tileSize), 2);
        }
    }
}
```

#### <a name="typescripttabtypescript"></a>[TypeScript](#tab/typescript)

```typescript
module AzureMaps {

    /** Tile System math for the Spherical Mercator projection coordinate system (EPSG:3857) */
    export class TileMath {
        //Earth radius in meters.
        private static EarthRadius = 6378137;

        private static MinLatitude = -85.05112878;
        private static MaxLatitude = 85.05112878;
        private static MinLongitude = -180;
        private static MaxLongitude = 180;

        /**
         * Clips a number to the specified minimum and maximum values.
         * @param n The number to clip.
         * @param minValue Minimum allowable value.
         * @param maxValue Maximum allowable value.
         * @returns The clipped value.
         */
        private static Clip(n: number, minValue: number, maxValue: number): number {
            return Math.min(Math.max(n, minValue), maxValue);
        }

        /**
         * Calculates width and height of the map in pixels at a specific zoom level from -180 degrees to 180 degrees.
         * @param zoom Zoom Level to calculate width at.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Width and height of the map in pixels.
         */
        public static MapSize(zoom: number, tileSize: number): number {
            return Math.ceil(tileSize * Math.pow(2, zoom));
        }

        /**
         * Calculates the Ground resolution at a specific degree of latitude in the meters per pixel.
         * @param latitude Degree of latitude to calculate resolution at.
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Ground resolution in meters per pixels.
         */
        public static GroundResolution(latitude: number, zoom: number, tileSize: number): number {
            latitude = this.Clip(latitude, this.MinLatitude, this.MaxLatitude);
            return Math.cos(latitude * Math.PI / 180) * 2 * Math.PI * this.EarthRadius / this.MapSize(zoom, tileSize);
        }

        /**
         * Determines the map scale at a specified latitude, level of detail, and screen resolution.
         * @param latitude Latitude (in degrees) at which to measure the map scale.
         * @param zoom Zoom level.
         * @param screenDpi Resolution of the screen, in dots per inch.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns The map scale, expressed as the denominator N of the ratio 1 : N.
         */
        public static MapScale(latitude: number, zoom: number, screenDpi: number, tileSize: number): number {
            return this.GroundResolution(latitude, zoom, tileSize) * screenDpi / 0.0254;
        }

        /**
         * Global Converts a Pixel coordinate into a geospatial coordinate at a specified zoom level.
         * Global Pixel coordinates are relative to the top left corner of the map (90, -180).
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A position value in the format [longitude, latitude].
         */
        public static GlobalPixelToPosition(pixel: number[], zoom: number, tileSize: number): number[] {
            var mapSize = this.MapSize(zoom, tileSize);

            var x = (this.Clip(pixel[0], 0, mapSize - 1) / mapSize) - 0.5;
            var y = 0.5 - (this.Clip(pixel[1], 0, mapSize - 1) / mapSize);

            return [
                360 * x,    //Longitude
                90 - 360 * Math.atan(Math.exp(-y * 2 * Math.PI)) / Math.PI  //Latitude
            ];
        }

        /**
         * Converts a point from latitude/longitude WGS-84 coordinates (in degrees) into pixel XY coordinates at a specified level of detail.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A pixel coordinate 
         */
        public static PositionToGlobalPixel(position: number[], zoom: number, tileSize: number): number[] {
            var latitude = this.Clip(position[1], this.MinLatitude, this.MaxLatitude);
            var longitude = this.Clip(position[0], this.MinLongitude, this.MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            var mapSize = this.MapSize(zoom, tileSize);

            return [
                this.Clip(x * mapSize + 0.5, 0, mapSize - 1),
                this.Clip(y * mapSize + 0.5, 0, mapSize - 1)
            ];
        }

        /**
         * Converts pixel XY coordinates into tile XY coordinates of the tile containing the specified pixel.
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Tile XY coordinates.
         */
        public static GlobalPixelToTileXY(pixel: number[], tileSize: number): { tileX: number, tileY: number } {
            return {
                tileX: Math.round(pixel[0] / tileSize),
                tileY: Math.round(pixel[1] / tileSize)
            };
        }

        /**
         * Performs a scale transform on a global pixel value from one zoom level to another.
         * @param pixel Pixel coordinates in the format of [x, y].
         * @param oldZoom The zoom level in which the input global pixel value is from.
         * @param newZoom The new zoom level in which the output global pixel value should be aligned with.
         */
        public static ScaleGlobalPixel(pixel: number[], oldZoom: number, newZoom: number): number[] {
            var scale = Math.pow(2, oldZoom - newZoom);

            return [pixel[0] * scale, pixel[1] * scale];
        }

        /// <summary>
        /// Performs a scale transform on a set of global pixel values from one zoom level to another.
        /// </summary>
        /// <param name="points">A set of global pixel value from the old zoom level. Points are in the format [x,y].</param>
        /// <param name="oldZoom">The zoom level in which the input global pixel values is from.</param>
        /// <param name="newZoom">The new zoom level in which the output global pixel values should be aligned with.</param>
        /// <returns>A set of global pixel values that has been scaled for the new zoom level.</returns>
        public static ScaleGlobalPixels(pixels: number[][], oldZoom: number, newZoom: number): number[][] {
            var scale = Math.pow(2, oldZoom - newZoom);

            var output: number[][] = [];
            for (var i = 0, len = pixels.length; i < len; i++) {
                output.push([pixels[i][0] * scale, pixels[i][1] * scale]);
            }

            return output;
        }

        /**
         * Converts tile XY coordinates into a global pixel XY coordinates of the upper-left pixel of the specified tile.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Pixel coordinates in the format of [x, y].
         */
        public static TileXYToGlobalPixel(tileX, tileY, tileSize): number[] {
            return [tileX * tileSize, tileY * tileSize];
        }

        /**
         * Converts tile XY coordinates into a quadkey at a specified level of detail.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param zoom Zoom level.
         * @returns A string containing the quadkey.
         */
        public static TileXYToQuadKey(tileX: number, tileY: number, zoom: number): string {
            var quadKey: number[] = [];
            for (var i = zoom; i > 0; i--) {
                var digit = 0;
                var mask = 1 << (i - 1);

                if ((tileX & mask) != 0) {
                    digit++;
                }

                if ((tileY & mask) != 0) {
                    digit += 2
                }

                quadKey.push(digit);
            }
            return quadKey.join('');
        }

        /**
         * Converts a quadkey into tile XY coordinates.
         * @param quadKey Quadkey of the tile.
         * @returns Tile XY cocorindates and zoom level for the specified quadkey.
         */
        public static QuadKeyToTileXY(quadKey: string): { tileX: number, tileY: number, zoom: number } {
            var tileX = 0;
            var tileY = 0;
            var zoom = quadKey.length;

            for (var i = zoom; i > 0; i--) {
                var mask = 1 << (i - 1);
                switch (quadKey[zoom - i]) {
                    case '0':
                        break;

                    case '1':
                        tileX |= mask;
                        break;

                    case '2':
                        tileY |= mask;
                        break;

                    case '3':
                        tileX |= mask;
                        tileY |= mask;
                        break;

                    default:
                        throw "Invalid QuadKey digit sequence.";
                }
            }

            return {
                tileX: tileX,
                tileY: tileY,
                zoom: zoom
            };
        }

        /**
         * Calculates the XY tile coordinates that a coordinate falls into for a specific zoom level.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns Tiel XY coordinates.
         */
        public static PositionToTileXY(position: number[], zoom: number, tileSize: number): { tileX: number, tileY: number } {
            var latitude = this.Clip(position[1], this.MinLatitude, this.MaxLatitude);
            var longitude = this.Clip(position[0], this.MinLongitude, this.MaxLongitude);

            var x = (longitude + 180) / 360;
            var sinLatitude = Math.sin(latitude * Math.PI / 180);
            var y = 0.5 - Math.log((1 + sinLatitude) / (1 - sinLatitude)) / (4 * Math.PI);

            //tileSize needed in calculations as in rare cases the multiplying/rounding/dividing can make the difference of a pixel which can result in a completely different tile. 
            var mapSize = this.MapSize(zoom, tileSize);

            return {
                tileX: Math.floor(this.Clip(x * mapSize + 0.5, 0, mapSize - 1) / tileSize),
                tileY: Math.floor(this.Clip(y * mapSize + 0.5, 0, mapSize - 1) / tileSize)
            };
        }

        /**
         * Calculates the tile quadkey strings that are within a specified viewport.
         * @param position Position coordinate in the format [longitude, latitude].
         * @param zoom Zoom level.
         * @param width The width of the map viewport in pixels.
         * @param height The height of the map viewport in pixels.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A list of quadkey strings that are within the specified viewport.
         */
        public static GetQuadkeysInView(position: number[], zoom: number, width: number, height: number, tileSize: number): string[] {
            var p = this.PositionToGlobalPixel(position, zoom, tileSize);

            var top = p[1] - height * 0.5;
            var left = p[0] - width * 0.5;

            var bottom = p[1] + height * 0.5;
            var right = p[0] + width * 0.5;

            var tl = this.GlobalPixelToPosition([left, top], zoom, tileSize);
            var br = this.GlobalPixelToPosition([right, bottom], zoom, tileSize);

            //Boudning box in the format: [west, south, east, north];
            var bounds = [tl[0], br[1], br[0], tl[1]];

            return this.GetQuadkeysInBoundingBox(bounds, zoom, tileSize);
        }

        /**
         * Calculates the tile quadkey strings that are within a bounding box at a specific zoom level.
         * @param bounds A bounding box defined as an array of numbers in the format of [west, south, east, north].
         * @param zoom Zoom level to calculate tiles for.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A list of quadkey strings.
         */
        public static GetQuadkeysInBoundingBox(bounds: number[], zoom: number, tileSize: number): string[] {
            var keys: string[] = [];

            if (bounds != null && bounds.length >= 4) {
                var tl = this.PositionToTileXY([bounds[0], bounds[3]], zoom, tileSize);
                var br = this.PositionToTileXY([bounds[2], bounds[1]], zoom, tileSize);

                for (var x = tl[0]; x <= br[0]; x++) {
                    for (var y = tl[1]; y <= br[1]; y++) {
                        keys.push(this.TileXYToQuadKey(x, y, zoom));
                    }
                }
            }

            return keys;
        }

        /**
         * Calculates the bounding box of a tile.
         * @param tileX Tile X coordinate.
         * @param tileY Tile Y coordinate.
         * @param zoom Zoom level.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns A bounding box of the tile defined as an array of numbers in the format of [west, south, east, north].
         */
        public static TileXYToBoundingBox(tileX: number, tileY: number, zoom: number, tileSize: number): number[] {
            //Top left corner pixel coordinates
            var x1 = tileX * tileSize;
            var y1 = tileY * tileSize;

            //Bottom right corner pixel coordinates
            var x2 = x1 + tileSize;
            var y2 = y1 + tileSize;

            var nw = this.GlobalPixelToPosition([x1, y1], zoom, tileSize);
            var se = this.GlobalPixelToPosition([x2, y2], zoom, tileSize);

            return [nw[0], se[1], se[0], nw[1]];
        }

        /**
         * Calculates the best map view (center, zoom) for a bounding box on a map.
         * @param bounds A bounding box defined as an array of numbers in the format of [west, south, east, north]. 
         * @param mapWidth Map width in pixels.
         * @param mapHeight Map height in pixels.
         * @param padding Width in pixels to use to create a buffer around the map. This is to keep markers from being cut off on the edge.
         * @param tileSize The size of the tiles in the tile pyramid.
         * @returns The center and zoom level to best position the map view over the provided bounding box.
         */
        public static BestMapView(bounds: number[], mapWidth: number, mapHeight: number, padding: number, tileSize: number): { center: number[], zoom: number } {
            if (bounds == null || bounds.length < 4) {
                return {
                    center: [0, 0],
                    zoom: 1
                };
            }

            var boundsDeltaX: number;
            var centerLat: number;
            var centerLon: number;

            //Check if east value is greater than west value which would indicate that bounding box crosses the antimeridian.
            if (bounds[2] > bounds[0]) {
                boundsDeltaX = bounds[2] - bounds[0];
                centerLon = (bounds[2] + bounds[0]) / 2;
            }
            else {
                boundsDeltaX = 360 - (bounds[0] - bounds[2]);
                centerLon = ((bounds[2] + bounds[0]) / 2 + 360) % 360 - 180;
            }

            var ry1 = Math.log((Math.sin(bounds[1] * Math.PI / 180) + 1) / Math.cos(bounds[1] * Math.PI / 180));
            var ry2 = Math.log((Math.sin(bounds[3] * Math.PI / 180) + 1) / Math.cos(bounds[3] * Math.PI / 180));
            var ryc = (ry1 + ry2) / 2;

            centerLat = Math.atan(Math.sinh(ryc)) * 180 / Math.PI;

            var resolutionHorizontal = boundsDeltaX / (mapWidth - padding * 2);

            var vy0 = Math.log(Math.tan(Math.PI * (0.25 + centerLat / 360)));
            var vy1 = Math.log(Math.tan(Math.PI * (0.25 + bounds[3] / 360)));
            var zoomFactorPowered = (mapHeight * 0.5 - padding) / (40.7436654315252 * (vy1 - vy0));
            var resolutionVertical = 360.0 / (zoomFactorPowered * tileSize);

            var resolution = Math.max(resolutionHorizontal, resolutionVertical);

            var zoom = Math.log2(360 / (resolution * tileSize));

            return {
                center: [centerLon, centerLat],
                zoom: zoom
            };
        }
    }
}
```

* * *

> [!NOTE]
> Azure Maps SDK의 대화형 맵 컨트롤에는 지리 공간적 위치와 뷰포트 픽셀 간을 변환 하기 위한 도우미 함수가 있습니다. 
> - [웹 SDK: 지도 픽셀 및 위치 계산](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#pixelstopositions-pixel---)

## <a name="next-steps"></a>다음 단계

Azure Maps REST 서비스에서 지도 타일에 직접 액세스 합니다.

> [!div class="nextstepaction"]
> [지도 타일 가져오기](https://docs.microsoft.com/rest/api/maps/render/getmaptile)

> [!div class="nextstepaction"]
> [트래픽 흐름 타일 가져오기](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)

> [!div class="nextstepaction"]
> [트래픽 인시던트 타일 가져오기](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)

지리 공간적 개념에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Maps 용어집](glossary.md)
