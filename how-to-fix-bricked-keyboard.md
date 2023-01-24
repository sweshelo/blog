# How to fix bricked System settings app and keyboard ?
カスタムキーボードをインストールした状態でCFWをアンインストールすると、キーボードが使えなくなり、本体設定が起動できなくなります。この記事では、この状態から再度CFWを導入し、復旧する方法を解説します。

## 本体設定が使えないということ
本体設定が起動できなくなる上で障壁となるのは、大きく2つです。

### インターネット接続の設定が出来ない
まず、この記事の内容を編纂するにあたって、私もカスタムキーボードを導入した状態でCFWのアンインストールを実施しました。すると、正常に繋がっていたインターネットへの接続が失われてしまいました。仮にインターネットへ接続できていたとしても、設定内容が変更できないのは痛手となり得ます。

### セーフモードが使えない
セーフモードは、3DSを復旧するための標準機能で、電源を投入する際、`↑`+`A`+`L`+`R`をホールドし続けることで起動できる、本体を更新するためのウィザードです。現在最も主流なCFWのインストール方法では、このセーフモードのexploitを利用するため、必然的に別の方法を選ばざるを得なくなります。

## 用意するもの
まず、本記事内容を実施するに当たり必要なものを紹介します。

- 対象の3DS
- **上記とは別の** 3DS(CFW導入済み) 1台
- 『マリオカート7』1本
  * ダウンロードソフトの場合、CFW導入済みの本体で動作している必要があります
- GPUを搭載しているPC 1台
- Python3
  * PCにインストールしておきます
  * Windowsの場合、以下のPowerShellコマンドでインストールできます
  ```
  iwr get.scoop.sh | iex ; scoop update ; scoop install python3
  ```

# 手順
以下のように進めます。
1. kartdlphax
1. seedminer
1. frogtool

# kartdlphax
[kartdlphax](https://github.com/PabloMK7/kartdlphax)は、CTGP-7 Projectで知られるPabloMK7氏によるセミプライマリ[^1]Exploitです。『マリオカート7』の『みんなで(ローカル通信プレイ)』を利用してコードを送信します。

1. [plugin.3gx](https://github.com/PabloMK7/kartdlphax/releases/download/v1.2/plugin.3gx)をダウンロードし、CFW機のSDカード `SD:/luma/titles/0004000000030600/` に配置する。

  > **Note**
  > 
  > `0004000000030600` は、JPN版マリオカート7のTitleIDです。他リージョンのタイトルを利用する場合、そちらに合わせます。

2. 3GXに対応している[boot.firm](https://github.com/Nanquitas/Luma3DS/releases/download/v11.0/boot.firm)のビルドをダウンロードし、CFW機のSDカード `SD:/` に配置する。

3. [boot.3dsx](https://github.com/devkitPro/3ds-hbmenu/releases/download/v2.4.0/boot.3dsx)をダウンロードし、故障機のSDカード `SD:/` に配置する。

1. [menuhax67](https://github.com/zoogie/menuhax67/releases/download/v1.1/menuhax67_v1.1.zip)をダウンロードしたのち展開し、故障機の3DSのファミリとリージョンに応じた`Launcher.bat`を、故障機のSDカード `SD:/` に配置する。

1. CFW機でRosalina menuを開き、`Plugin Loader: `の項目でAボタンを押下し `Enabled`の状態にする。この状態でマリオカート7を起動する。正常に3gxを用意できれば、起動時に一瞬スクリーンの色が変化する。

1. 「みんなで」を選択すると、kartdlphaxのメニューが開く。この画面で「Change Settings」を選択する。
- 画面の指示に従って対象の3DSファミリを選択する。
- `3DS ROP xPloit Injector`を選択する。
- 上画面で設定内容を確認し直し、`Use settings`を選択する。

7. 通常のプレイと同様にプレイヤーを募集し、50ccのキノコカップを選択する。この際、プレイヤーとカートは問わない。レースを開始すると、haxの送信が開始される。完了すると、CFW機は自動でHOMEメニューに戻り、故障機は`3DS ROP xPloit Injector`が起動する。

1. 故障機で、`Y: menuhax67`を実行する。成功すると、下画面がグリーンバックになりHOMEメニューに回帰した後、電源が切断される。

1. 再度電源を投入し、下画面左上のHOMEメニューの設定ボタンを押下し、HBLが起動することを確認する。

以降は、全て故障機における作業を記します。

## Seedminer
Nintendo3DSには、全てのユーザセーブデータに影響を及ぼす128bitの暗号化キー`movable.sed`が存在します。セーブデータにインジェクトを実施する場合、この鍵を使ってデータを復号し、また暗号化する必要があります。[Seedminer](https://github.com/zoogie/seedminer)は、その暗号化キーを、総当たりによって算出するための仕組みです。より詳しい情報を得るには、[ここ](https://zoogie.github.io/web/34%E2%85%95c3/)を訪れると良いでしょう。

1. [Seedminer](https://github.com/zoogie/seedminer/releases/download/v2.1.6/Seedminer_v2.1.6-x86_64.zip)をダウンロードしたのち展開する。これはPC上で動かすためのソフトウェアである。

1. [Seedstarter](https://github.com/zoogie/seedminer/releases/download/v2.1/RELEASE_v2.1.zip)をダウンロードしたのち展開する。これはSeedminerが必要とするファイルをダンプするhomebrewである。取り出した`seedstarter.3dsx`をSDカードの任意の場所に保存する。

1. HBLを開き、`seedstarter`を起動する。`A dump LFCS from GetConfigInboBlk2` を実施する。完了したら、終了し、3DSからSDカードを取り外す。`SD:/seedstarter/movable_part1.sed`をPC上のSeedminerディレクトリ(Windows/Linux/Mac)にコピーする。

1. コピーしたディレクトリでShellを開き、以下のコマンドを実行する。
```
python3 ./seedminer_launcher3.py gpu
```

成功すると、以下のような出力とともに、`movable.sed`が生成される。
```
GPU selected
Old3DS msed - this can happen on a New3DS
LFCS      : 0x2d9a0ca
msed3 est : 0x91e028
Error est : 3277
ID0 hash 0: b385a906c822c3d993618c9d0123abcd
Hash total: 1
movable_part2.sed generation success
bfcl msky caa0d902000000000000000028e09100 b385a906c822c3d993618c9d0123abcd 00000000 00400000
selected device NVIDIA GeForce RTX 3060 on platform NVIDIA CUDA
0.012 seconds for OpenCL compiling
local work size: 256
got a hit: abcdef01234567890123456789e09100 at offset: 1
movable.sed dumped to file!
msed_data will also be written to
msed_data_000033BE.bin
just keep it handy if you don't know what to do with it!

done.
1.66 seconds, 2741.32 M/s
```

# frogtool
[frogtool](https://github.com/zoogie/Frogtool)は、「うごくメモ帳」にハック用のデータをパッチしたり、またパッチ後のアプリを起動するためのユーティリティです。B9sInstallerをパッチして起動することで、故障機に再度CFWを構築し直すことが可能です。

1. Seedminerで算出した`movable.sed`を `SD:/`に配置する。
1. [frogcert.bin](magnet:?xt=urn:btih:d12278ea50bb3574f1fbd327f3d0e2292c70941f&dn=frogcert.bin&tr=https%3a%2f%2ftracker.fastdownload.xyz%3a443%2fannounce&tr=https%3a%2f%2fopentracker.xyz%3a443%2fannounce&tr=http%3a%2f%2fopen.trackerlist.xyz%3a80%2fannounce&tr=http%3a%2f%2ft.nyaatracker.com%3a80%2fannounce&tr=udp%3a%2f%2ftracker.tiny-vps.com%3a6969%2fannounce&tr=udp%3a%2f%2fopen.demonii.si%3a1337%2fannounce&tr=udp%3a%2f%2ftracker.port443.xyz%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.vanitycore.co%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.torrent.eu.org%3a451%2fannounce&tr=udp%3a%2f%2fretracker.lanta-net.ru%3a2710%2fannounce&tr=udp%3a%2f%2fthetracker.org%3a80%2fannounce&tr=http%3a%2f%2ftorrent.nwps.ws%3a80%2fannounce&tr=udp%3a%2f%2ftracker.coppersurfer.tk%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.iamhansen.xyz%3a2000%2fannounce&tr=udp%3a%2f%2fbt.xxx-tracker.com%3a2710%2fannounce&tr=http%3a%2f%2f0d.kebhana.mx%3a443%2fannounce&tr=udp%3a%2f%2fexodus.desync.com%3a6969%2fannounce&tr=udp%3a%2f%2ftracker.opentrackr.org%3a1337%2fannounce&tr=udp%3a%2f%2ftracker4.itzmx.com%3a2710%2fannounce&tr=udp%3a%2f%2ftracker.justseed.it%3a1337%2fannounce&tr=http%3a%2f%2ftherightsize.net%3a1337%2fannounce&tr=udp%3a%2f%2fretracker.hotplug.ru%3a2710%2fannounce&tr=udp%3a%2f%2ftracker.internetwarriors.net%3a1337%2fannounce&tr=udp%3a%2f%2f9.rarbg.com%3a2800%2fannounce&tr=https%3a%2f%2f2.track.ga%3a443%2fannounce&tr=udp%3a%2f%2fbigfoot1942.sektori.org%3a6969%2fannounce)(magnet) をダウンロードし、`SD:/` に配置する。
1. [frogtool](https://github.com/zoogie/Frogtool/releases/download/v2.3/Frogtool_v2.3.zip)をダウンロードしたのち展開する。`frogtool.3dsx`をSDカードの任意の場所に保存する。
1. [b9sTool](https://github.com/zoogie/b9sTool/releases/download/v6.1.1/release_6.1.1.zip)をダウンロードしたのち展開する。`boot.nds`を `SD:/` に配置する。
1. [Luma3DS](https://github.com/LumaTeam/Luma3DS/releases/download/v11.0/Luma3DSv11.0.zip)をダウンロードしたのち展開する。`boot.firm`を `SD:/` に配置する。
1. HBLを開き、`frogtool`を起動する。`INJECT`を選択し、完了したら同様に`BOOT`を選択する。
1. 「うごくメモ帳」が起動する。初回の案内を飛ばし、メインメニューまで遷移させる。
1. 「メモを見る」「SDカード」「(顔文字のメモ)」「書く」の順に選択する。
1. 方向キー上または`X`でエディタ画面を開き、「終了」ボタン隣のフィルムアイコンをクリックする。
1. 3枚目のコマを1枚目のコマへコピーする。b9sToolが起動する。画面の指示に従って、インストールを完了する。
1. ソフトを終了すると、Luma3DSの設定画面が起動する。

# 最後に
カスタムキーボードを削除、またはNANDイメージをリストアして、CFWインストール操作を完了する。

## 脚注
[^1]: プライマリExploitは、他にHomebrewを実行する手段がない中でHomebrewを実行可能にするExploitを指します。例えば、`SoundHax`はプライマリです。一方、手順の簡略化を目的とするなど、既にHomebrewを実行できる環境でのみ導入可能なものをセカンダリExploitと呼び、`BaseHaxx`などがこれにあたります。
