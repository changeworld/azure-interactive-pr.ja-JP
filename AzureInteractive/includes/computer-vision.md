---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460002"
---
現段階では、このアプリケーションは画像をアップロードして表示できる機能ギャラリーです。 このモジュールでは、Microsoft Cognitive Services の Computer Vision API を使用して、アップロードした画像のキャプションを生成し、Cosmos DB 内の画像メタデータと共にそのキャプションを保存する方法について説明します。

## <a name="create-a-computer-vision-account"></a>Computer Vision アカウントを作成する

Microsoft Cognitive Services は、開発者がよりインテリジェントなアプリケーションを開発するためのサービスのコレクションです。 Computer Vision API は、高度なアルゴリズムを使用して画像を処理し、各画像に関する情報を返す、サーバーレス サービスです。 Free レベルでは、1 か月あたり最大 5,000 回 API 呼び出しを実行できます。

1. Cloud Shell に引き続きサインインしていることを確認します。 そうでない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択して、Cloud Shell ウィンドウを開きます。 

1. リソース グループ内で一意の名前を持つ、**ComputerVision** という種類の新しい Cognitive Services アカウントを作成します。 Free レベルの場合は、**F0** を SKU として使用します。 既に Computer Vision のアカウントがある場合は、Standard アカウント (S1) の作成が必要になる場合があります。ただし、これによってコストが発生することがあります。

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Computer Vision の URL およびキーの Function App 設定を作成する

Computer Vision API を呼び出すには、URL とキーが必要です。

1. Computer Vision API のキーと URL を取得し、bash 変数に保存します。

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. それぞれ **COMP_VISION_KEY** と **COMP_VISION_URL** という名前のアプリ設定を、関数アプリで作成します。

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>ResizeImage 関数から Computer Vision API を呼び出す

次の手順では、**ResizeImage** 関数を変更して Computer Vision API を呼び出し、アップロードした各画像について説明してその説明を Cosmos DB に保存します。

1. Azure Portal で関数アプリを開きます。

1. **C# を選択した場合**

    1. (C#) 左側のナビゲーションを使用して、**ResizeImage** 関数を見つけ、コード ウィンドウを開きます。

    1. (C#) このコードを [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx) の内容で置き換えます。 このコードでは、`HttpClient` を使用して Computer Vision API を呼び出し、その結果を Cosmos DB に保存しています。

1. **JavaScript を選択した場合**

    1. (JavaScript) この関数では、Computer Vision API への HTTP 呼び出しを行うために、npm の `axios` パッケージが必要です。 npm パッケージをインストールするには、左側のナビゲーションで関数アプリの名前をクリックし、**[プラットフォーム機能]** をクリックします。

    1. (JavaScript) **[コンソール]** をクリックしてコンソール ウィンドウを表示します。

    1. (JavaScript) コンソールで `npm install axios` コマンドを実行します。 操作が完了するまでに 1 分から 2 分かかる場合があります。

    1. (JavaScript) 左側のナビゲーションで関数名 (**ResizeImage**) をクリックして関数を表示し、**index.js** のすべてを [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js) の内容で置き換えます。

1. コード ウィンドウ下部の **[ログ]** をクリックして、ログ パネルを展開します。

1. **[保存]** をクリックします。 関数が正常に保存され、エラーがないことを、ログ パネルで確認します


## <a name="test-the-application"></a>アプリケーションをテストする

1. ブラウザーでアプリケーションを開きます。 画像ファイルを選択してアップロードします。

1. 数秒後に、新しい画像のサムネイルがこのページに表示されます。 画像上にポインターを合わせると、Computer Vision によって生成された説明が表示されます。


## <a name="summary"></a>まとめ

このモジュールでは、Microsoft Cognitive Services の Computer Vision API を使用して、アップロードした各画像のキャプションを自動的に生成する方法を説明しました。 次に、Azure App Service 認証を使用して、アプリケーションに認証を追加する方法を説明します。
