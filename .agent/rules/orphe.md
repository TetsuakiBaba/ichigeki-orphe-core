---
trigger: always_on
---

このプロジェクトでは ORPHE COREというBLE接続モーションセンサをブラウザで利用するライブラリを用いてアプリケーション開発を行います。使い方は以下がgetting startedなhtml, jsコードになります。
```

            <!doctype html>
            <html lang="en">
              <head>
                <meta charset="utf-8">
                <title>ORPHE CORE JS DEMO</title>
              </head>
              <body>
                <h1>Hello, ORPHE!</h1>
                <button onclick="ble.setLED(1,0);">connect</button>
                <script src="https://cdn.jsdelivr.net/gh/Orphe-OSS/ORPHE-CORE.js/js/ORPHE-CORE.js"
                crossorigin="anonymous"></script>
                <script>
                var ble = new Orphe(0);
                window.onload = function () {
                  // ORPHE CORE Init
                  ble.setup();
                }
                </script>
              </body>
            </html>
          
```
このライブラリのソースコードは　/src/ORPHE-CORE.js に保存されています。モーションセンサには加速度センサや各速度の他、step analysisという歩様解析結果も取得することが可能です。ソースコードを参照して、ユーザの要望に合わせたアプリケーションを作成してください。ただしアプリケーションは HTML, CSS, JavaScript で実装してください。
