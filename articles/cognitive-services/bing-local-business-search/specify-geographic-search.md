---
title: 地理的境界を使用して Bing Local Business Search API からの結果をフィルター処理する
titleSuffix: Azure Cognitive Services
description: この記事を使用して、Bing Local Business Search API からの検索結果をフィルター処理する方法について学習します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2e72abd777ac4834385fd916b730d438b8540203
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490263"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>地理的境界を使用して Bing Local Business Search API からの結果をフィルター処理する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Local Business Search API では、`localCircularView` または `localMapView` クエリ パラメーターを使用して、検索する特定の地域に境界を設定することができます。 クエリでは、パラメーターを 1 つだけ使用するようにしてください。 

検索語句に明示的で地理的な場所が含まれている場合、Bing Local Business API では、検索結果の境界を設定するためにそれが自動的に使用されます。 たとえば、検索語句が `sailing in San Diego` である場合、`San Diego` は場所として使用され、クエリ パラメーターやユーザー ヘッダーで指定されたその他の場所は無視されます。 

検索語句で地理的な場所が検出されず、クエリ パラメーターを使用して地理的な場所が指定されていない場合、Bing Local Business Search API では、要求の `X-Search-ClientIP` または `X-Search-Location` ヘッダーからの場所の特定が試行されます。 いずれのヘッダーも指定されていない場合、API では、要求のクライアント IP、またはモバイル デバイスの GPS 座標から場所が特定されます。

## <a name="localcircularview"></a>localCircularView

`localCircularView` パラメーターでは、半径で定義された、緯度/経度の座標セットを中心に円形の地域が作成されます。 このパラメーターを使用する場合、検索領域より少し外側の場所が含まれる可能性のある `localMapView` パラメーターとは異なり、Bing Local Business Search API からの応答には、この円内の場所のみが含まれます。

円形の地理的検索領域を指定するには、円の中心として機能する緯度と経度、および半径 (メートル単位) を選択します。 その後、このパラメーターをクエリ文字列に追加できます (例: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`)。

完全なクエリ:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView` パラメーターでは、その南東と北西の角を指定するために 2 セットの座標を使用して、検索する四角形の地域を指定します。 このパラメーターを使用する場合、検索領域内の場所のみを含む、`localCircularView` パラメーターとは異なり、Bing Local Business Search API からの応答には、指定された領域内の場所と少し外側の場所が含まれる可能性があります。

四角形の検索領域を指定するには、境界の南東と北西の角として機能する緯度/経度の座標の 2 つのセットを選択します。 次の例のように、必ず、最初に南東の座標を定義してください。`localMapView=47.619987,-122.181671,47.6421,-122.13715`

完全なクエリ:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>次のステップ
- [Local Business Search (Java) のクイック スタート](quickstarts/local-search-java-quickstart.md)
- [Local Business Search (C#) のクイック スタート](quickstarts/local-quickstart.md)
- [Local Business Search (Node.js) のクイック スタート](quickstarts/local-search-node-quickstart.md)
- [Local Business Search (Python) のクイック スタート](quickstarts/local-search-python-quickstart.md)