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
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460085"
---
前のモジュールでは、サーバーレス関数を使用して、Web アプリケーションから Blob Storage に画像をアップロードするときにセキュリティで保護する方法について説明しました。 このモジュールでは、アップロードされた画像を監視し、そこからサムネイルを作成するために、別のサーバーレス関数を作成します。

## <a name="create-a-blob-storage-container-for-thumbnails"></a>サムネイル用の Blob Storage コンテナーを作成する

フル サイズの画像は、**images** という名前のコンテナーに格納されます。 これらの画像のサムネイルを格納するには、別のコンテナーが必要になります。

1. Cloud Shell (bash) に現在もサインインしていることを確認してください。  サインインしていない場合は、**[Enter focus mode]\(フォーカス モードにする\)** を選択し、 Cloud Shell ウィンドウを開きます。 

1. すべての BLOB へのパブリック アクセスを持つストレージ アカウント内に、**thumbnails** という名前の新しいコンテナーを作成します。

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>BLOB によってトリガーされるサーバーレス関数を作成する

トリガーは、関数を呼び出す方法を定義します。 次に作成する関数では、BLOB トリガーを使用します。この関数は、BLOB (画像ファイル) が **images** コンテナーにアップロードされるときに自動的に呼び出されます。 1 つの関数には 1 つのトリガーしか含められません。 トリガーにはデータが関連付けられていて、通常そのデータは、その関数をトリガーしたペイロードです。

バインディングは、関数が Azure またはサード パーティのサービスでデータを読み書きする方法を定義します。 この関数では、関数をトリガーする画像のサムネイル バージョンを作成し、そのサムネイルを *thumbnails* コンテナーに保存します。

1. Azure Portal で関数アプリを開きます。

1. 関数アプリ ウィンドウの左側のナビゲーションで、**[Functions]** にポインターを合わせて **+** をクリックし、新しいサーバーレス関数の作成を開始します。 クイック スタート ページが表示されたら、**[カスタム関数]** をクリックして、関数テンプレートの一覧を表示します。

1. **BlobTrigger** テンプレートを検索し、選択します。

1. 以下の値を使用して、画像がアップロードされるときにサムネイルを作成する関数を作成します。

    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **言語** | C# または JavaScript | 使用したい言語を選択。 |
    | **関数名の指定** | ResizeImage | アプリケーションが関数を検出できるように、この名前を正確に表示されているとおりに入力します。 |
    | **パス** | images/{name} | **images** コンテナーにファイルが格納されると関数が実行されます。 |
    | **ストレージ アカウント情報** | AZURE_STORAGE_CONNECTION_STRING | 接続文字列を使用して前に作成した環境変数を使用します。 |

    ![新しい関数の設定を入力する](media/functions-first-serverless-web-app/3-new-function.png)

1. **[作成]** をクリックして、関数を作成します。

1. 関数が作成されたら、**[統合]** をクリックして、そのトリガー、入力、および出力バインディングを表示します。

1. **[新規出力]** をクリックし、新しい出力バインディングを作成します。

    ![[統合] タブで新しい出力を選択する](media/functions-first-serverless-web-app/3-new-output.jpg)

1. **[Azure Blob Storage]** を選択し、**[選択]** をクリックします。 **[選択]** ボタンを見つけるために、下へスクロールする必要がある場合があります。

    ![[Azure Blob Storage] を選択する](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. 次の値を入力します。

    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **BLOB パラメーター名** | thumbnail | この関数では、この名前のパラメーターを使用してサムネイルを書き込みます。 |
    | **関数の戻り値を使用する** | いいえ  |  |
    | **パス** | thumbnails/{name} | サムネイルは、**thumbnails** という名前のコンテナーに書き込まれます。 |
    | **ストレージ アカウント接続** | AZURE_STORAGE_CONNECTION_STRING | 接続文字列を使用して前に作成した環境変数を使用します。 |

    ![BLOB 出力バインディングの設定を入力する](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript を選択した場合**

    1. (JavaScript) ウィンドウの右上隅にある **[詳細エディター]** をクリックして、バインディングを表す JSON を表示します。

    1. (JavaScript) `blobTrigger` バインディングで、`binary` の値を持つ `dataType` という名前のプロパティを追加します。 これによって、BLOB コンテンツをバイナリ データとして関数に渡すように、バインディングが構成されます。

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. **[保存]** をクリックし、新しいバインディングを作成します。

1. **C# を選択した場合**

    1. (C#) 左側のナビゲーションで、関数名 **ResizeImage** を選択して、この関数のソース コードを開きます。

    1. (C#) この関数でサムネイルを生成するには、**ImageResizer** という NuGet パッケージが必要です。 NuGet パッケージは、**project.json** ファイルを使用して C# 関数に追加します。 このファイルを作成するには、右にある **[ファイルの表示]** をクリックして、関数を構成するファイルを表示します。
    
    1. (C#) **[追加]** をクリックして、**project.json** という名前の新しいファイルを追加します。
    
    1. (C#) [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) の内容を、新しく作成したファイルにコピーします。 ファイルを保存します。 ファイルが更新されると、パッケージが自動的に復元されます。
    
        ![project.json ファイルと ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) **[ファイルの表示]** で **run.csx** を選択し、その内容を [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx) の内容で置き換えます。

1. **JavaScript を選択した場合** 

    1. (JavaScript) この関数で画像のサイズを変更するには、npm の `jimp` パッケージが必要です。 npm パッケージをインストールするには、左側のナビゲーションで関数アプリの名前をクリックし、**[プラットフォーム機能]** をクリックします。

    1. (JavaScript) **[コンソール]** をクリックしてコンソール ウィンドウを表示します。

    1. (JavaScript) コンソールで `npm install jimp` コマンドを実行します。 操作が完了するまでに 1 分から 2 分かかる場合があります。

    1. (JavaScript) 左側のナビゲーションで関数名 **ResizeImage** をクリックして関数を表示し、**index.js** のすべてを [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js) の内容で置き換えます。

1. コード ウィンドウの下の **[ログ]** をクリックして、ログ パネルを展開します。

1. **[保存]** をクリックします。 関数が正常に保存され、エラーがないことを、ログ パネルで確認します


## <a name="test-the-serverless-function"></a>サーバーレス関数をテストする

1. ブラウザーでアプリケーションを開きます。 画像ファイルを選択してアップロードします。 アップロードが完了しますが、画像を表示する機能はまだ追加していないため、アップロードした写真はアプリに表示されません。

1. Cloud Shell で、**images** コンテナーに画像がアップロードされていることを確認します。

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. **thumbnails** いう名前のコンテナー内に、サムネイルが作成されていることを確認します。

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. サムネイルの URL を取得します。

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    ブラウザーでこの URL を開き、サムネイルが正常に作成されたことを確認します。

1. 次のチュートリアルに進む前に、**images** と **thumbnails** の各コンテナー内のすべてのファイルを削除します。

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>まとめ

このモジュールでは、画像が Blob Storage コンテナーにアップロードされるたびにサムネイルを作成するサーバーレス関数を作成しました。 次に、Azure Cosmos DB を使用して、画像のメタデータの格納および一覧表示を行う方法について説明します。
