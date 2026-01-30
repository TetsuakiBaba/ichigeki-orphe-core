# ORPHE CORE AI Agent Guidelines

ORPHE CORE（BLEモーションセンサ）を利用したアプリケーション開発のための最適化ガイドラインです。

## プロジェクト構成
- **メインライブラリ**: [src/ORPHE-CORE.js](src/ORPHE-CORE.js)
- **エントリポイント**: [index.html](index.html)
- **ビルドツール**: 非推奨。ブラウザでそのまま動作する HTML/CSS/JavaScript 構成を維持してください。

## ORPHE CORE について
ORPHE CORE は、BLE（Bluetooth Low Energy）を介してモーションデータを送信する小型センサデバイスです。取得できるデータは大きく分けて2種類あります。

- **センサ生データ (SENSOR_VALUES)**: 加速度、角速度、姿勢（クォータニオン、オイラー角）など
- **歩容解析データ (STEP_ANALYSIS)**: 歩数、歩幅、接地角度、足の方向、消費カロリーなど

接続時に `ble.begin()` の引数で取得するデータ種別を指定します。

### 取得可能なデータの詳細

#### 1. センサ生データ (`SENSOR_VALUES`)
- **加速度 (`gotAcc`)**: `{x, y, z}` [g]
- **正規化加速度 (`gotConvertedAcc`)**: `{x, y, z}` [g]
- **角速度 (`gotGyro`)**: `{x, y, z}` [deg/s]
- **正規化角速度 (`gotConvertedGyro`)**: `{x, y, z}` [deg/s]
- **クォータニオン (`gotQuat`)**: `{w, x, y, z}`
- **オイラー角 (`gotEuler`)**: `{pitch, roll, yaw}` [deg]

#### 2. 歩容解析データ (`STEP_ANALYSIS`)
- **累積歩数 (`gotStepsNumber`)**: `{value}`
- **歩容解析まとめ (`gotGait`)**: `{type, direction, calorie, distance, steps, standing_phase_duration, swing_phase_duration}`
- **ストライド/歩幅 (`gotStride`)**: `{x, y, z, steps_number}` [m]
- **接地角度 (`gotFootAngle`)**: `{value}` [deg]
- **プロネーション (`gotPronation`)**: `{x, y, z}`
- **着地衝撃 (`gotLandingImpact`)**: `{value}` [kgf]

加速度、および角速度は、ユーザから指示されない限り、原則的には正規化データを利用してください。

## 開発パターンと制約

### 1. BLE接続の作法
Web Bluetooth API のセキュリティ上、接続は必ずユーザーのジェスチャー（ボタンクリック等）を起点にする必要があります。
- ページロード時に `ble.setup()` を呼び出し、内部データを初期化します。
- クリックイベント内で `await ble.begin()` を呼び出して接続します。

```javascript
const ble = new Orphe(0); // 0 または 1

// 初期化（ページロード時）
window.onload = () => {
    ble.setup();
};

// 接続開始（ユーザージェスチャー時）
async function connect() {
    // 引数: 'SENSOR_VALUES', 'STEP_ANALYSIS', または 'STEP_ANALYSIS_AND_SENSOR_VALUES' (デフォルト)
    await ble.begin('STEP_ANALYSIS_AND_SENSOR_VALUES');
}
```

### 2. データ取得 (コールバック関数)
`Orphe` インスタンスのコールバックメンバを上書きしてデータを取得します。
```javascript
ble.gotEuler = (euler) => {
    console.log(euler.pitch, euler.roll, euler.yaw);
};
ble.gotStepsNumber = (steps) => {
    console.log(steps.value);
};
```

### 3. LED制御
コアモジュールのLEDを制御できます。
- `ble.setLED(on_off, pattern)`
    - `on_off`: 0 (消灯), 1 (点灯)
    - `pattern`: 0-4 (0:通常点灯、1-4:アニメーション)

### 4. データ欠損の処理
無線通信のためデータが欠損することがあります。その際に呼び出されるコールバックです。
- `ble.lostData = (num, num_prev) => { ... }`

### 5. 可視化・依存ライブラリ 
- 2D グラフィックスには **p5.js** を推奨します。
  - 加速度および角速度を利用する場合は正規化された `gotConvertedAcc`,  `gotConvertedGyro` コールバックを使用し、グラフは -1.0 〜 +1.0 の範囲で表示してください。
- 3D アプリケーションには **Three.js** を推奨します。
- インポートには `importmap` を活用し、CDN からの読み込みを基本とします。

## コーディングスタイル
- プロンプトから「一撃で」動作するアプリを生成するため、依存関係は最小限にし、HTML ファイル内に直接 JS/CSS を記述してください。
- ORPHE-CORE.js の改変は避けてください。
- インポートする際は、`src/ORPHE-CORE.js` をローカルパスで指定してください。
- 日本語でのコメントや UI 表示をデフォルトとします。
