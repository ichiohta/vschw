# Visual Studio Codeで始めるASP.Net Core

この記事は [Visual Studio Code Advent Calendar 2016](http://qiita.com/advent-calendar/2016/vscode) に参加しています。

## はじめに

以前から興味をもっていたASP.NET Coreについて、最近いろいろと試す機会がありました。この記事は、その際の環境のセットアップのメモ書きをまとめたものです。「来年はASP.NET Coreをちょっとまじめにやってみようかな」というような（都合のいい）方を読者に想定していますが、その割に前提環境がLinuxやmacOSでなくてWindowsだったりするのは上記のような理由によるものです。

_重要_ .NET Coreというプロダクトは、2016年12月時点でまだプレビューという段階です。ビルド周りのツーリングがまだ流動的な状況にあるようで、下記の記事の内容はWindows環境でないとうまく実行できません。あしからずご了承ください。

## 環境のセットアップ

ASP.NET Coreを始めるにあたって、まずは以下のものをインストールしておきましょう。

* [Visual Studio Code] (https://code.visualstudio.com/Download)
* [.NET Core 1.1 SDK] (https://www.microsoft.com/net/core#windowscmd)

### 動作確認

インストールが正しくできていればdotnetというコマンドが利用できるようになっているはずです。
下記のように実行してバージョン番号を確認してみてください。

    dotnet --version

ついでなので、Hello Worldプログラムも実行してみましょう。
適当にディレクトリを作って、そこで作業します。

    mkdir helloworld
    cd helloworld

dotnetにはプロジェクトのひな形を作成する機能があります。

    dotnet new

上記を実行すると、以下の2本のファイルが作成されているはずです。

    helloworld.csproj
    Program.cs

このプログラムをコンパイルして実行してみます。

    dotnet restore
    dotnet run

"Hello World!"と表示されたのなら、ここまでの作業はうまく完了していることになります。

## ASP.NET Coreプロジェクトのひな形の作成

ではさっそくASP.NET Coreのプロジェクトを作成しましょう。
ここでもdotnetコマンドを用いるのが簡単です。

    mkdir vschw
    dotnet new -t web

先ほどと同じくdotnet newでプロジェクトのひな形を作成しています。
少し異なるのは引数-t webを指定して、ASP.NET Coreを用いるプロジェクトのひな形作成を指定しているところです。

作成されたファイルの一覧を確認してください。

    Controllers/
    Data/
    Models/
    Services/
    Views/
    wwwroot/
    aspsettings.json
    .bower.rc
    .gitignore
    appsettings.json
    bower.json
    gulpfile.js
    package.json
    Program.cs
    project.json
    readme.md
    Startup.cs
    web.config

Controllersやwwwrootはディレクトリで、その下にもいろいろとファイルが作成されています。これらのファイルがどういうものかについては後ほど説明するとして、まずは試しに実行してみましょう。

まずはプロジェクトが依存している各種のライブラリをダウンロードします。

    dotnet restore

依存しているライブラリについての情報は、project.jsonに保存されています。依存しているライブラリを新たに追加したりしない限り、このdotnet restoreというコマンドをビルドの度に再度実行する必要はありません。

先ほどの動作確認では省略しましたが、ここでは明示的にビルドを行っておきましょう。

    dotnet build

bin\Debug\netcoreapp1.1以下にvschw.dll他がビルドされているはずです。以下のコマンドでこのdllをロードして実行します。

    dotnet run

"Now listening on: http://localhost:5000"というメッセージが表示されましたか？
WebブラウザでこのURLを表示することで、作成されたひな形ASP.NETアプリケーションの動作を確認できます。

![サンプルアプリケーション](figs/fig-dotnet-run.png?raw=true)

## Visual Studio Codeとの連携
### C# Extensionのインストール

C#の開発を行うためのエクステンションをまだインストールしていないならば、最初にまずそちらをインストールしておきましょう。

![エクステンションのインストール](figs/fig-install-extension.png?raw=true)

(Extensionsの一覧はアイコンをクリックするかCtrl-Shift-Xで表示できます)

### launch.jsonおよびtasks.jsonの生成

Visual Studio Codeから、プロジェクトのひな形を作成したディレクトリを開くと、以下のようなメッセージが表示されます。

![ファイルの生成](figs/fig-generate-files.png?raw=true)

Visual Studio Codeからビルドおよびデバッグを行うためには、それぞれ設定ファイルが必要です。このメッセージは、そのファイルを自動で生成すべきかどうかを確認しています。

ここでは自動で生成しておきましょう。.vscodeというディレクトリ、さらにその下にlauncher.jsonとtasks.jsonというファイルが生成されます。

### Visual Studio Codeから起動

Visual Studio Codeからデバッグモードでこのアプリケーションを起動してみましょう。F5を押してみてください。デバッグコンソール他が表示されてデバッグビューに切り替わるとともに、Webブラウザが起動します。

![デバッガーから起動](figs/fig-seemingly-broken-layout.png?raw=true)

先ほどと違って画面のレイアウトが崩れていますね。これにはもちろん理由がありますが、そちらについては後ほど解説するとして、少しデバッガの挙動を確認してみましょう。

Controllerディレクトリーの下にあるHomeController.csというファイルを開いてください（ウィンドウ左端上部に配置されているアイコン（![](figs/icon-explorer.png?raw=true)）をクリックすると、エクスプローラービューに切り替えることができます）。

このファイルの中からIndex()というメソッドの定義を探しだしてください。

    public IActionResult Index()
    {
        return View();
    }

returnの行にカーソルを移動し、F9を押してください。行番号の横に赤丸が表示されたでしょうか。これは、この行にブレークポイントが設定された印です。

この状態で、Webブラウザに戻り、先ほどのレイアウトが崩れたページをリフレッシュ表示してみましょう（F5を押してください）。

Visual Studio Codeにフォーカスが切り替わり、設定したブレークポイントのところで実行が一時停止されたことがわかります。

![ブレークポイント](figs/fig-hit-breakpoint.png?raw=true)

画面左端中ほどに配置されているアイコン（![](figs/icon-debug.png?raw=true)）をクリックして、左側のペーンをデバッグビューに切り替えてください。

VARIABLES以下に現在のスコープからみえる変数の一覧が表示されています。thisを展開していけば、このメソッドが定義されているHomeControllerクラスのインスタンスの各種メンバー変数の現在値を確認できるはずです。

![変数の一覧](figs/fig-variable-values.png?raw=true)

Visual Studioをご使用の方にはおなじみですが、以下のキー操作で実行を再開できます。

* F10でステップオーバー（次の行に進んで停止）
* F11でステップイン（コールスタックの一段深いところに進んで停止）
* F5で停止状態を抜けて実行を再開

デバッガの動作を簡単に確認できたところで、アプリケーションの挙動について簡単に見ておきましょう。先に進む前に、Shift+F5を押してデバッガを終了させておいてください。

## プログラムの解説

### プロジェクトファイル

project.jsonには、アプリケーションプログラムを構成するソースファイルや、依存している外部ライブラリについての情報が定義されています。ソースファイルやライブラリを新しくプロジェクトに追加する場合は、このファイルを編集することなります。

ところで、このプロジェクトファイルをよくみると確認できるのですが、プロジェクト名そのものは定義されていません。
dotnet buildの出力を注意ぶかくみると、このファイルが存在するディレクトリーの名前がプロジェクト名として使用されていることがわかります。

    Project vschw (.NETCoreApp,Version=v1.1) will be compiled because expected outputs are missing 
    Compiling vschw for .NETCoreApp,Version=v1.1
    ...

このプロジェクト名は最終的に生成されるバイナリ（アセンブリ）のファイル名としても用いられます。
dotnet build実行後、binディレクトリー以下を検索すると "_プロジェクト名_.dll" というファイルが生成されているはずです。

dotnet runを実行するとこのアセンブリがロードされ、アプリケーションの実行が開始されます。

_注記_ 将来のバージョンにおいて、プロジェクトファイルのフォーマット変更が予定されています。また.NET Core SDKのLinux版ではすでに変更されています。

### Program

アプリケーションのアセンブリがロードされた後、最初に呼び出されるのはProgram.csに定義されているProgramクラスのMainメソッドになります。

    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory())
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }

ASP.NET Coreアプリケーションを実行するための環境はIWebHostというインターフェイスで抽象化されています。このインスタンスを生成して初期化するために用いるのが[WebHostBuilder](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.hosting.webhostbuilder)です。

WebHostBuilderに対する様々なUse...のメソッド呼び出しは、生成されるIWebHostに対する設定を行っています。ここで抑えておきたいのはUseContentRootとUseStartupです。

IWebHostはASP.NET Coreのアプリケーション実行環境だと先ほど述べました。これをもう少し具体的にいうと、ASP.NET Coreのプログラムを実行するためだけの、非常にコンパクトなHTTPサーバーの心臓部分のようなものになります。

UseContentRootは、そのHTTPサーバーを通じてアクセスできる静的ファイル（静的なHTML、CSSやJavaScriptなどのファイル）の格納場所を指定するものです。

UseStartupは、アプリケーションが起動したときに実行される初期化用のスタートアップコードを指定するためのものです。ここではStartup.csに定義されているStartupクラスが指定されています。

WebHostBuilderのBuildを呼び出すことで、上記の設定を施したIWebHostのインスタンスが生成されます。この生成されたインスタンスのRunを呼び出すことにより、ASP.NET Coreの実行環境（ホスト）が起動されるというわけです。

_補足_ IWebHostとWebHostBuilderの関係は、デザインパターンでいうところの[Builderパターン](https://ja.wikipedia.org/wiki/Builder_%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B3)に相当します。ASP.NET Coreではほかの場所でもこのBuilderパターンがよく用いられています。

上述したように、初期化用のスタートアップコードとしてStartupクラスのコードが呼び出されます。Startup.csを開いてください。このクラスにはみっつのメソッドが定義されており、これらはASP.NET Coreのホストから以下の順で呼び出されます。

1. Startup
1. ConfigureServices
3. Configure

### Startup

Starupでは、アプリケーションの設定情報を格納するプロパティConfigurationを初期化しています。

ここでもBuilderパターンが用いられていて、[ConfigurationBuilder](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.configuration.configurationbuilder)を使って、設定情報としてロードするファイルの指定（AddJsonFile）等を行い、最終的にBuildを読んで[IConfigurationRoot](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.configuration.iconfigurationroot)のインスタンスを生成しています。

    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true);
    ....
    Configuration = builder.Build();

さて、ここで環境名について簡単に解説しておきたいと思います。アプリケーションが稼働する環境を「開発環境」「結合テスト環境」「本番環境」として切り分け、環境に応じて設定ファイルの内容を変えたい、といった要求は開発現場においては一般的でしょう。

ASP.NET Coreでは、この環境名をASPNETCORE_ENVIRONMENTというOSの環境変数を用いて指定することになっており、この情報はStartupの引数として渡される[IHostingEnvironment](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.hosting.ihostingenvironment)のインスタンスから入手可能です（プロパティEnvironmentName）。

上記のコードをもう一度みてください。appsetings.jsonというファイルに加え、オプションでappsettings._環境名_.jsonというファイルも設定情報として読み込む指定をしていますね。

### ConfigureServices

ASP.NET Coreのホストのアーキテクチャは徹底したモジュラー構造になっています。どれくらい徹底しているかというと、デフォルトではほぼ何の機能も持たず、必要に応じていろいろな機能、たとえばユーザーの認証機能等を追加する仕組みになっています。

これらの必要な機能を構成するコードを記述する場所が、Startupの次に呼び出されるConfigureServicesメソッドになります。サンプルのコードでもいろいろ設定されているのですが、注目すべきは以下の一行です。

    services.AddMvc();

これによって、ASP.NETのMVCフレームワークを利用するために必要なコンポーネントがホストに組み込まれます。

### Configure

ConfigureServicesの次に呼び出されるConfigureでは、HTTPリクエストのパイプラインに関する設定を行いますが、ここでもまたBuilderパターンが使われていることに気づかれたでしょうか。

ConfigureServicesの引数として渡される[IApplicationBuilder](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder)のインスタンスに対して、各種のUse...メソッドで設定を行いますが、先ほどまでのパターンと違って最終的にBuildを呼び出していない点に注意してください。

さて、ここでまず注目したいのはUseStaticFilesです。先ほどASP.NET Coreのホストは徹底したモジュラー構造になっていると書きました。どれくらい徹底しているかというと、静的ファイルへのHTTPアクセスもデフォルトではまったく処理されないほどです。このUseStaticFilesを呼び出すことで、リクエストパイプラインにおいて静的ファイルへのアクセスが処理されるようになります。

そして一番重要なのが、UseMVCの呼び出しです。

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });

MVCフレームワークでは、コントローラよ呼ばれるアプリケーションコードがリクエストの処理を行います。このUseMvcにより、リクエストパイプライン中におけるMVCコントローラへの処理の振り分けを指定しているのです。この振り分けのことをルーティングと呼びます。

### ルーティングの仕組み

ここでは以下のようなルーティングのルールをひとつ定義しています。

* ルール名は"default"
* URLのパスの第1要素がコントローラ名、指定されていない時はHome
* 同じくパスの第2要素がアクション（メソッド）名、指定されていない時はIndex
* 同じくパスの第3要素をidというリクエストへの引数（オプション）として取り扱う

デバッガを起動した際、最初に表示されるWebページを例にとって考えてみましょう。このページのURLは以下の値です。

    http://localhost:5000
    
この場合、パス部分は何も指定されていません。上記のルールを当てはめると以下のようにコントローラ名とアクション名が決定されます。

* パスの第1要素はなし → コントローラ名はHome
* パスの第2要素はなし → アクション名はIndex

つまりこれは、以下のようなURLが指定された場合と同様といえます。

    http://localhost:5000/Home/Index

このようなルーティングルールは複数定義でき、リクエストに対してこれらのルーティングルールが適用されることなります。適用の結果、最終的なコントローラ名とアクション名が決まれば、それに対応するコントローラのクラス（コントローラ名+Controller）のメソッド（アクション名）が決まって呼び出しが行われます。

さて例に戻って、HomeControllerのIndexメソッドに軽く目をとおしておきましょう。HomeControllerクラスは、Controllerディレクトリ以下のHomeController.csに定義されています。

    public IActionResult Index()
    {
        return View();
    }

ASP.NET MVCフレームワークにおいてアクションの処理結果は[IActionResult](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.iactionresult)のインスタンスとして抽象化されおり、処理結果の型に応じてその値を格納するために適切なIActionResultの実装を用いることができます。たとえば処理結果としてJsonを返したいのであれば、[JsonResult](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.jsonresult)が利用可能です。

ViewResultもそのような処理結果のタイプのひとつで、テンプレートに基づいて動的に生成されるHTMLページ（ビュー）をレスポンスとして返すためのものです。ここでは、HomeControllerの基底クラス[Controller](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.controller)に定義されているヘルパーメソッド[View](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.aspnetcore.mvc.controller#Microsoft_AspNetCore_Mvc_Controller_View)を呼び出して、ViewResultを生成しています。

このView()メソッドを呼び出すことで、HomeコントローラのアクションIndexに対応するテンプレート（Views\Home\Index.cshtml）を自動的に選択し、ViewResultのインスタンスを生成することができるのです。

_補足_ このテンプレートに埋め込む具体的な値をもったオブジェクトがビューモデルになるのですが、今日はそこまで触れません。

Index.cshtmlを開いてみてください。完全なHTMLページを定義しておらず、ページの断片を定義しているように見えます。実は共通のレイアウトを指定するマスターページの定義があり、その内部に埋め込まれる想定になっているのです。この共通のレイアウトはデフォルトで_Layout.cshtmlになっています。

## レイアウトの乱れを修正
### 共通レイアウト

さて、_Layout.cshtmlの名前が出てきたところで、ようやくレイアウトが乱れている理由と解決法について説明することができます。

_Layout.cshtmlを開き、以下の部分に注目してください。

    <environment names="Development">
        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </environment>
    <environment names="Staging,Production">
        <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.6/css/bootstrap.min.css"
              asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
              asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
        <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
    </environment>

<environment>要素は、namesの値が環境名に一致するものだけが評価されます。
dotnet runから起動した場合、環境名はProductionになり、Visual Studio Codeから起動した場合はDevelopmentになります。

それぞれ<link>要素でbootstrapのスタイルシートをロードしようとしていますが、Production環境ではCDNからbootstrap.min.cssをロードする設定になっているのに対し、Development環境ではローカルのbootstarp.cssをロードする設定になっています。

ここで、静的ファイルが格納されているwwwrootディレクトリ以下の内容を確認してください。そもそもlibディレクトリがありませんね。

dotnet restoreで、依存している.NETのライブラリをダウンロードしたのと同じように、bootstrapをはじめとするフロントエンド開発用のライブラリもlibディレクトリ以下にダウンロードする必要があるのです。

### 依存ファイルのダウンロード

手動でbootstrapをダウンロードしてlibディレクトリ以下の適切な場所にコピーしてやれば、一応動くことは動くのですが少々面倒です、というか実運用ではやってられません。実のところ、この自動生成されるサンプルのプロジェクトでは、bowerを利用してこれらのファイルを簡単にダウンロードできるようになっています。

bowerは[Node.js](https://nodejs.org)上で動くツールなので、まずNode.jsをインストールします。インストール完了後、コンソールを開いてnpmコマンドでbowerをインストールします。

    npm install -g bower

インストールできたらbowerを実行してください。
    
    bower install

プロジェクトのディレクトリにある.bower.rcおよびbower.jsonの内容に基づいてファイルが自動的にダウンロードされます。bowerの出力を注意深く見れば、bootstrapの他にjqueryもダウンロードされることもわかるでしょう。

ダウンロードが完了したら、Visual Studio CodeからF5を押して再度アプリケーションを起動してください。

レイアウトの乱れは直っていることが確認できたでしょうか。

## 終わりに

というわけで、ASP.NET Coreをセットアップしてひな形アプリを動作させ、簡単に概要を紹介してみました。なんとなく面白そうだ、と思ったらぜひ手を動かしていろいろ試してみてください :D 

なお、この記事の内容に関してのフィードバックは[@ichiohta](https://twitter.com/ichiohta)までいただけると幸いです。