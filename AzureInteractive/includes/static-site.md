Azure Blob Storage は、静的ファイルをホストする際に使用できる、低コストで非常にスケーラブルなサービスです。 このチュートリアルでは、Blob Storage を使用して、構築した Web アプリの静的コンテンツ (HTML、JavaScript、CSS など) を提供します。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントは、テーブル、キュー、ファイル、BLOB (オブジェクト)、および仮想マシン ディスクを格納できる Azure リソースです。

1. **[Enter focus mode]\(フォーカス モードにする\)** ボタンをクリックして、Cloud Shell (Bash) にログインします。 このボタンは、ブラウザー ウィンドウサイズに応じて、ページの右上または下部に表示されます。 フォーカス モードでは、ブラウザー ウィンドウの右側に Cloud Shell ウィンドウがドッキングされるので、このチュートリアルで示すコマンドを簡単に実行できます。

1. Azure では、リソース グループは、管理を容易にするために関連する Azure リソースをまとめるコンテナーです。 **first-serverless-app** という名前の新しいリソース グループを作成します。

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. このチュートリアルの静的コンテンツ (HTML、CSS、および JavaScript ファイル) は Blob Storage でホストされます。 Blob Storage にはストレージ アカウントが必要です。 リソース グループにストレージ アカウント (汎用 V2) を作成します。 `<storage account name>` を一意の名前に置き換えます。

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. [Azure portal](https://portal.azure.com) の上部にある検索バーを使用して、先ほど作成したストレージ アカウントを検索し、開きます。

1. 左側のナビゲーションで、**[静的な Web サイト (プレビュー)]** を選択し、静的な Web サイトを有効にします。
    - **[有効]** を選択して、静的な Web サイトを有効にします。
    - インデックス ドキュメント名に「*index.html*」と入力します。 このフィールドには、グレースケールフォントで *index.html* が既に入力されていますが、これはサンプル テキストに過ぎません。フィールドに値を入力する必要があります。
    - **[保存]** をクリックします。
    
    ![静的な Web サイトの設定を入力する](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. このチュートリアルの作業中に簡単にコピーできる場所に**プライマリ エンドポイント**を保存します。 これは作成する Web アプリケーションの URL です。

## <a name="upload-the-web-application"></a>Web アプリケーションをアップロードする

1. このチュートリアルで構築するアプリケーションのソース ファイルは、[GitHub リポジトリ](https://github.com/Azure-Samples/functions-first-serverless-web-application)にあります。 Cloud Shell のホーム ディレクトリから、このリポジトリをクローンします。

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    リポジトリは `/home/<username>/functions-first-serverless-web-application` にクローンされます。

1. クライアント側の Web アプリケーションは **www** フォルダーにあり、 JavaScript の Vue.js フレームワークを使用して構築されています。 このフォルダーに移動し、 npm コマンドを実行してアプリケーションの依存関係をインストールし、アプリケーションをビルドします。 最後のコマンドは、完了に数分かかることがあります。

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    アプリケーションが **dist** フォルダーに生成されます。

1. カレントディレクトリを **dist** に変更し、アプリケーションを **$web** Blob コンテナーにアップロードします。

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Web ブラウザーで Storage の静的 Web サイトのプライマリ エンドポイント URL を開き、アプリケーションを表示します。

    ![最初のサーバーレス Web アプリのホーム ページ](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>まとめ

本章では、ストレージ アカウントを含む **first-serverless-app** という名前のリソース グループを作成しました。 ストレージ アカウント内の **$web** という名前の Blob コンテナーでは、 Web アプリケーションの静的コンテンツを格納し、コンテンツを公開します。 次に、サーバレスである Azure Functions を使用し、この Web アプリケーションから Blob Storage に画像をアップロードする方法を学習します。
