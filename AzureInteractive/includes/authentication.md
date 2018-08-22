---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079490"
---
Azure App Service 認証を使用すると、Azure 関数アプリでターン キー認証がサポートされるようになります。 Facebook、Twitter、Microsoft アカウント、Google、および Azure Active Directory とシームレスに統合されます。 Web アプリのバックエンド API を保護するには、App Service 認証を追加します。

## <a name="enable-app-service-authentication"></a>App Service 認証を有効にする

1. Azure Portal で関数アプリを開きます。

1. **[プラットフォーム機能]** で、**[認証/承認]** を選択します。

    ![[認証/承認] を選択する](media/functions-first-serverless-web-app/6-authorization.jpg)

1. 次の値を選択します。
    
    | Setting      |  推奨値   | 説明                                        |
    | --- | --- | ---|
    | **App Service 認証** | On | 認証を有効にします。 |
    | **Action when request is not authenticated (要求が認証されない場合のアクション)** | Azure Active Directory でのログイン | 構成済みの認証方法 (下記) を選択します。 |
    | **認証プロバイダー** | 以下を参照 | 以下を参照 |
    | **トークン ストア** | On | App Service でトークンを格納および管理できるようになります。 |
    | **[許可される外部リダイレクト URL]** | アプリケーションの URL。例: https://firstserverlessweb.z4.web.core.windows.net/ | ユーザーが認証された後に App Service からリダイレクトできる URL。 |

1. **[Azure Active Directory]** を選択して **[Azure Active Directory の設定]** を表示します。

    1. **[管理モード]** として **[簡易]** を選択し、次の情報を入力します。
    
        | Setting      |  推奨値   | 説明                                        |
        | --- | --- | ---|
        | **管理モード** | [簡易]、[新しい AD アプリを作成する] | サービス プリンシパルと Azure Active Directory 認証が自動的に設定されます。 |
        | **アプリの作成** | my-serverless-webapp | 一意のアプリケーション名を入力します。 |
    
    1. **[OK]** をクリックして Azure Active Directory の設定を保存します。

    ![[認証/承認] と Azure Active Directory の設定](media/functions-first-serverless-web-app/6-create-aad.png)

1. **[Save]** をクリックします。


## <a name="modify-the-web-app-to-enable-authentication"></a>Web アプリを変更して認証を有効にする

1. Cloud Shell で、現在のディレクトリが **www/dist** フォルダーであることを確認します。

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. 関数アプリで認証を有効にするには、次のコード行を **settings.js** に追加します。

    `window.authEnabled = true`

    次のコマンドを使用するか、VIM などのコマンド ライン エディターを使用して、変更を加え結果を表示します。

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    ファイルが変更されたことを確認します。

    ```azurecli
    cat settings.js
    ```

1. このファイルを Blob Storage にアップロードします。

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>アプリケーションをテストする

1. ブラウザーでアプリケーションを開きます。 **[ログイン]** をクリックしてログインします。

1. 画像ファイルを選択してアップロードします。

    ![サインイン ページ](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>まとめ

このモジュールでは、Azure App Service 認証を使用してアプリケーションに認証を追加する方法について説明しました。
