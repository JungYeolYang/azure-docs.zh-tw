---
title: 快速入門：使用 Ruby 來執行新聞搜尋 - Bing 新聞搜尋 REST API
titlesuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Ruby 來傳送要求給「Bing 新聞搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9ed658c6613d270ab3de67a8800263d677d70bac
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867013"
---
# <a name="quickstart-perform-a-news-search-using-ruby-and-the-bing-news-search-rest-api"></a>快速入門：使用 Ruby 和 Bing 新聞搜尋 REST API 來執行新聞搜尋

使用本快速入門以第一次呼叫 Bing 新聞搜尋 API，並接收 JSON 回應。 這個簡單的 JavaScript 應用程式會將搜尋查詢傳送給 API，並處理結果。

雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingNewsSearchv7.rb) 上有此範例的原始程式碼。

## <a name="prerequisites"></a>必要條件

* [Ruby 2.4 或更新版本](https://www.ruby-lang.org/en/downloads/) (英文)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

另請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 將下列套件匯入至您的程式碼檔案中。

    ```ruby
    require 'net/https'
    require 'uri'
    require 'json'
    ```

2. 建立適用於 API 端點、新聞搜尋 URL、您訂用帳戶金鑰及搜尋字詞的變數。

    ```ruby
    accessKey = "enter key here"
    uri  = "https://api.cognitive.microsoft.com"
    path = "/bing/v7.0/news/search"
    term = "Microsoft"
    ```

## <a name="format-and-make-an-api-request"></a>製作 API 要求格式並提出 API 要求

使用上一個步驟中的變數來製作適用於 API 要求的搜尋 URL 格式。 然後傳送要求。

```ruby
uri = URI(uri + path + "?q=" + URI.escape(term))
request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = accessKey
response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
   http.request(request)
end
```

## <a name="process-and-print-the-json-response"></a>處理及列印 JSON 回應

在收到回應之後，您可以剖析 JSON，並列印回應主體和其標頭：

```ruby
puts "\nRelevant Headers:\n\n"
response.each_header do |key, value|
   # header names are coerced to lowercase
   if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
      puts key + ": " + value
   end
end
puts "\nJSON Response:\n\n"
puts JSON::pretty_generate(JSON(response.body))
```

## <a name="json-response"></a>JSON 回應

如以下範例所示，成功的回應會以 JSON 格式來傳回：

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

 
## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁應用程式](tutorial-bing-news-search-single-page-app.md)
