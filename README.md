# SISO59 ビルドガイド
質問は[discord](https://discordapp.com/invite/zXCss8T) または作者Twitter(@_gonnoc)まで

## 使用上の注意
 - 乾電池の取扱いに当たっては各電池の注意事項を守ってください
 - 長時間使用しないとき、持ち運ぶ際には必ず電源を切ってください
 - 正常な動作中に電池が発熱することはありません。発熱を感じた際には直ちに使用を中断してください

## キット以外に必要な部品
本キットを完成させるには以下の部品が必要です

|部品|個数|
|---|---|
|MXスイッチ用PCBソケット|50|
|MX互換スイッチ(PCBマウント推奨)|50|
|MX用キーキャップ|50|
|AAサイズのリチウム電池(3.6V)|2| 

 (※リチウム電池は公称電圧3.6V程度のものを用意してください。アルカリ・マンガン・Ni-MH電池等は電圧が低いため動作しません)

## 本体組み立て

1. スイッチ用ソケットをはんだ付けする

	* ソケットが斜めにつくとスイッチを差し込むときにモゲる原因となるので注意してください
	
	* 2段目と3段目はレイアウトが選べるようになっています  
    	1.25Uのキーキャップを使う場合は外側、1Uのキーキャップを使う場合は内側のランドにソケットを取り付けてください

1. TRRSジャック、タクトスイッチをはんだ付けする

	* タクトスイッチの足を少し内側に折り曲げておくとボトムプレートと干渉しにくくなります
	
1. 実装面に気を付けて電源用スライドスイッチを実装する

	* ソケットを取り付けた面にスライドスイッチをはんだ付けしてください  
	   **右手スイッチの電源ONのシルクは誤りです。**　左手同様、右側につまみを寄せた状態が電源ONとなります。  
	  部品実装後の基板を横から見るとそれぞれ以下の画像のようになります。基板、電池ボックス、無線モジュール、スイッチの位置関係に注目してください
	![left-sideview](https://user-images.githubusercontent.com/43873124/57151078-0212e880-6e0b-11e9-9f34-f6d1cccb6be3.JPG)
	![right-sideview](https://user-images.githubusercontent.com/43873124/57151082-03dcac00-6e0b-11e9-9073-9f292519eb02.JPG)
	
1. 実装面に気を付けて単三用電池ボックスをはんだ付けする

	* 右手側は制御用モジュールが電池ボックスの下に隠れてしまい修正が難しくなるため、
	Modulo用有線ペンダントを持っている場合、電池ボックスを取り付ける前に有線ペンダントを繋いですべてのキーが動くことを確認したほうが無難です。  
	ファームウェア書き込みの項を参照してください
	
1. ボトムプレートを重ねてねじ止めする
	
	* 銅箔が抜けている部分(色が薄くなっている部分)が一致するように重ねてください。ネジを締めすぎると基板がたわみます。

1. スイッチ、キーキャップをつける

## ファームウェア書き込み

1. リポジトリを取得する
  nrf52対応のqmk_firmwareは[こちら](https://github.com/sekigon-gonnoc/qmk_firmware/tree/nrf52)

	- 既にqmk_firmwareを使っていて別のブランチとして用意したい場合
	```
        git remote add sekigon https://github.com/sekigon-gonnoc/qmk_firmware.git
        git fetch sekigon
        git checkout -b nrf52 sekigon/nrf52
	```
	
	- 既にqmk_firmwareを使っていて別のフォルダに置きたい場合
	  
	 ```
		git clone  -b nrf52 https://github.com/sekigon-gonnoc/qmk_firmware.git ble_micro_pro
	 ```
	  
	- 初めて使う場合
	
	```
        git clone --depth 1 -b nrf52 https://github.com/sekigon-gonnoc/qmk_firmware.git
	```

1. 必要サブモジュールを用意

		make git-submodule

1. NRFSDK v12.3.0を取得し、適当なディレクトリに展開する

	* BLE Micro Pro用とはバージョンが異なります。バージョン番号は厳守してください。

1. 展開したディレクトリをmsys2などビルド環境の環境変数に設定する

    ```
    export NRFSDK12_ROOT=<path to sdk> #例 /c/dev/nRF5_SDK_12.3.0_d7731ad
    ```    
    
### nrfutilのセットアップ(Windows(MSYS2))
[nrfutil.exe](https://github.com/NordicSemiconductor/pc-nrfutil/releases)をダウンロードし、~/qmk_utilに保存します。

### nrfutilのセットアップ(Mac, Linux)
pipでnrfutilを入れます。(python2.7)

```
pip install nrfutil
```

### 本体へのファームウェア書き込み
1. マスタ(デフォルトは左手・スレーブ(右手)それぞれのファームウェアをビルドする

	 - 有線接続に対応させる場合、それぞれのconfig.hにUSE_AS_I2C_SLAVEを定義してください

    ```
    make siso59/master:default:dfu_ble
    make siso59/slave:default:dfu_ble
    ```

1. 完成したzipファイルをスマートフォンに移動、あるいは共有フォルダに移動する

1. スマートフォン・タブレットに[nRF Toolbox](https://play.google.com/store/apps/details?id=no.nordicsemi.android.nrftoolbox&hl=ja)をインストール(現時点ではPC版からは書き込めないようです。)

1. 書き込みたい基板の電源をタクトスイッチを押しながら入れる

1. nRF ToolboxでDFUを選択し起動

1. SELECT FILEから先ほど用意したzipファイルを選択

1. SELECT DEVICEで**DFU Targ**を選択

1. UPLOADを押し、書き込みを開始

無線のみの動作であれば、これで書き込みは終了です。ペアリングの項を参照してください。

### BlackPillペンダントへのファームウェア書き込み

1. ファームウェアをビルドする

    ```
    make siso59/pendant:bp_pendant
    ```

1. dfu-utilを使って書きこむ

	  リセットスイッチを押して青色LEDの点滅がゆっくりになったら以下のコマンドを実行

    ```
    dfu-util -d 1eaf:0003 -a 2 -D <firmware.bin>
    ```


## 動作確認

1. スレーブ側のみを起動し、スマートフォン等からBluetoothデバイスのスキャンを実行

1. デバイス一覧に**Nordic UART**という表示があることを確認したら、マスタ側を起動。マスタとスレーブのペアリングが自動的に実行される

1. デバイス一覧から**Nordic UART**という表示が消え、代わりに**SISO59**が表示されることを確認後、ペアリングを実行

以上の手順により、無線キーボードとしての動作確認ができます。
これ以降、スレーブ側のみを起動したとしても**Nordic UART**はデバイス一覧に表示されません。

## トラブルシューティング
### ペアリングが出来ない・出来なくなった

端末とマスタのペアリング情報を削除してから再ペアリングを実行してみてください。
 - 特定のペアリング情報を削除するにはDEL_IDxをキーマップに割り当てて実行してください。
 - スレーブも含む全てのペアリングを削除するには最上段の中心3つのキーを押しながら電源を起動してください。スレーブ側も同様の手順で削除できます。

