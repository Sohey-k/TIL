<details>
  <summary>Upstartの特徴</summary>

  ***
  
  - サービスやタスクを「ジョブ」という単位で扱う
  - サービスの並列起動処理が可能
  - イベント駆動型
    
</details>
<details>
  <summary>systemd</summary>
  
  ***
  
  - systemdはSysVinitを置き換える新しいinitの仕組み。
  以下のデーモンプロセスが連携して動作。

  |プロセス名      |機能                                            |
  |:----------------|:---------------------------------------------|
  |systemd         |systemdの本体となるプロセス。initの代わり          |
  |systemd-journald|各サービスが出力するログを扱う                    |
  |systemd-logind  |システムへのログイン、起動停止、電源ボタン操作を管理|
  |systemd-udevd    |従来のudevの代替え機能                          |
</details>
<details>
  <summary>systemdで扱うUnit</summary>
  
  ***

  - Unitは設定ファイルであり、Unitの設定に従ってsystemd自体が処理を実行する。
  SysVinitのようにスクリプトを実行するわけではありません。
  Unitには各機能ごとに拡張子が割り当てられており、
  拡張子を見ることでどういった機能のためのUnitかが判別できるようになっています。
  
  ***
  
  以下主な拡張子
  
  |拡張子|機能|
  |:----|:----|
  |device|各種デバイスを管理|
  |mount|ファイルシステムをマウント|
  |service|サービス制御|
  |swap|スワップ領域を管理|
  |target|複数のサービスを一つのグループにする|
  
</details>
<details>
  <summary>SysVinitのランレベルとsystemdのターゲット</summary>

  ***
  |ランレベル|ターゲット|
  |:--------|:---------|
  |0|poweroff.target|
  |1|rescue.target|
  |2,3,4|multi-user.target|
  |5|graphical.target|
  |6|reboot.target|
  
</details>
<details>
  <summary>systemctlコマンド一覧</summary>

  ***
  |サブコマンド|用途|
  |:----------|:----|
  |disable|サービス自動起動無効|
  |enable|サービス自動起動有効|
  |get-default|次回起動時ターゲット表示|
  |halt|システムを停止し、halt状態|
  |is-active|サービスが稼働してるか表示|
  |list-unit-files|全てのUnit定義ファイル一覧表示|
  |reboot|システム再起動|
  |reload|サービス設定ファイル再読み込み|
  |restart|サービス再起動|
  |set-default|次回起動時ターゲット設定|
  |start|サービス起動|
  |status|サービス状態表示|
  |stop|サービス停止|
  |poweroff|システム停止して電源停止|
  
</details>
<details>
  <summary>dmesg --clear</summary>
  
  ***
  
  - dmesgコマンドはカーネルが出力するメッセージが格納されるリングバッファの内容を出力。
  リングバッファはマシンの再起動やシャットダウンによってリセットされるが、
  運用中に一杯になると古い情報から上書きされる。
  手動でリングバッファの内容をクリアするには、rootユーザで「dmesg --clear」を実行する。

  

</details>
<details>
  <summary>システムのブートプロセス</summary>

  ***

  - 一般的なLinuxシステムでは、BIOS、ブートローダ、カーネル、initの順にブートプロセスが進行する。
  - BIOS（Basic I/O System）では、まずBIOSが起動し、記憶装置（HDD）等に関して最低限の認識をして起動デバイスの優先順位を決定。
  その後、各デバイスの先頭セクタにあるMBRを読み込み、得られたブートローダーに制御を移す。
  最近のシステムではUEFI（Unified Extensible Firmware Interface）への移行が進んでいる。
  - LinuxシステムではGRUBがブートローダーを担当する。第一段階のブートローダはMBRの先頭446バイトの領域にインストールされる。
  ブートローダは記憶装置内のカーネルをロードし、カーネルに制御を移す役割を果たす。
  - カーネルは起動されると、高度にハードウェアを認識・制御し、ルートファイルシステムのマウントなど様々な初期化処理を行う。
  ブートローダはカーネルと初期RAMディスク（initramfs）の内容をメモリ上に展開し、カーネルはメモリ上に展開された初期RAMディスク内の、
  ファイルシステムへアクセスするために必要なドライバやスクリプトを使用してルートファイルシステムをマウントする。
  その後、initという特別な最初のプロセスをルートファイルシステムから起動。
  カーネルイメージと、カーネルのバージョンに対応する初期RAMディスクは「/boot」ディレクトリに格納される。
  初期RAMディスクは展開してイメージ内のディレクトリ、ファイルを参照することができる。
 - initは最初に起動されるプロセスで、PID（プロセス ID）は必ず1である。「SysVinit」と呼ばれる従来のinitプログラムを採用しているシステムでは、「/sbin/init」が起動される。
  initプロセスは設定ファイル「/etc/inittab」の記述に基づいて、自動起動するべきプロセスを立ちあげるなど、アプリケーションレベルの初期化を行う。
  以降、initプロセスは全てのプロセスの先祖（直接・間接的な呼び出し元）として存在し続ける。
  なお、最近のシステムではinitプログラムとして、初期化処理を高速化したUpstartやsystemdを採用している場合がある。その場合は基本的に「/etc/inittab」ファイルは使用されない。
    

  
</details>
