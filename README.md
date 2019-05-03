# SISO59 ビルドガイド
## 本体組み立て

1. スイッチ用ソケットをはんだ付けする

	* ソケットが斜めにつくとスイッチを差し込むときにモゲる原因となるので注意してください
	
	* 2段目と3段目はレイアウトが選べるようになっています  
    	1.25Uのキーキャップを使う場合は外側、1Uのキーキャップを使う場合は内側のランドにソケットを取り付けてください

1. TRRSジャック、タクトスイッチをはんだ付けする

	* タクトスイッチの足を少し内側に折り曲げておくとボトムプレートと干渉しにくくなります
	
1. 実装面に気を付けて電源用スライドスイッチを実装する

	* ソケットを取り付けた面にスライドスイッチをはんだ付けしてください
	
1. 実装面に気を付けて単三用電池ボックスをはんだ付けする

	* 右手側は制御用モジュールが電池ボックスの下に隠れてしまい修正が難しくなるため、
	Modulo用有線ペンダントを持っている場合、電池ボックスを取り付ける前に有線ペンダントを繋いですべてのキーが動くことを確認したほうが無難です。  
	ファームウェア書き込みの項を参照してください
	
1. ボトムプレートを重ねてねじ止めする

1. スイッチ、キーキャップをつける

## ファームウェア書き込み

1. リポジトリを取得する
  nrf52対応のqmk_firmwareは[こちら](https://github.com/sekigon-gonnoc/qmk_firmware/tree/nrf52)

	- 既にqmk_firmwareを使っている場合

        git remote add sekigon https://github.com/sekigon-gonnoc/qmk_firmware.git
        git pull sekigon nrf52
        git checkout nrf52
	
	 または
	 
		git clone  -b nrf52 https://github.com/sekigon-gonnoc/qmk_firmware.git ble_micro_pro
	  
	- 初めて使う場合

        git clone --depth 1 -b nrf52 https://github.com/sekigon-gonnoc/qmk_firmware.git

1. 必要サブモジュールを用意

		make git-submodule

1. NRFSDK v12.3.0を取得し、適当なディレクトリに展開する

	* BLE Micro Pro用とはバージョンが異なります。バージョン番号は厳守してください。

1. 展開したディレクトリをmsys2などビルド環境の環境変数に設定する

    ```
    export NRFSDK12_ROOT=<path to sdk> #例 /c/dev/nRF5_SDK_12.3.0_d7731ad
    ```


### 本体へのファームウェア書き込み
1. マスタ・スレーブそれぞれのファームウェアをビルドする

    ```
    make siso59/master:default
    make siso59/slave:default
    ```

1. 完成したzipファイルをスマートフォンに移動、あるいは共有フォルダに移動する

1. スマートフォン・タブレットに[nRF Toolbox](https://play.google.com/store/apps/details?id=no.nordicsemi.android.nrftoolbox&hl=ja)をインストール(現時点ではPC版からは書き込めないようです。)

1. 書き込みたい基板の電源をタクトスイッチを押しながら入れる

1. nRF ToolboxでDFUを選択し起動

1. SELECT FILEから先ほど用意したzipファイルを選択

1. SELECT DEVICEで**DFU Targ**を選択

1. UPLOADを押し、書き込みを開始

### BlackPillペンダントへのファームウェア書き込み

1. ファームウェアをビルドする

    ```
    make siso59/bp_pendant:bp_pendant
    ```

1. dfu-utilを使って書きこむ

	  リセットスイッチを押して青色LEDの点滅がゆっくりになったら以下のコマンドを実行

    ```
    dfu-util -d 1eaf:0003 -a 2 -D <firmware.bin>
    ```

