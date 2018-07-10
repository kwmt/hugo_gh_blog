+++
date = "2018-07-10T21:30:00+09:00"
title = "Wifiでadb接続する"
draft = false
categories = ["Android"]
tags = ["Android", "adb"]
+++

## Wifi経由でadb接続するには

1. 端末とUSBで接続する

2. 認識されてることを確認
    ```
    % adb devices
    List of devices attached
    <端末>	device
    ```

3. adb tcpip PORTを叩く

    ```
    % adb tcpip 5555
    restarting in TCP mode port: 5555
    ```
    adb serverがリスタートします。

4. adb connect <端末のIPアドレス>    
端末に接続します。

    ```
    % adb connect <端末のIPアドレス>
    connected to <端末のIPアドレス>
    ```
    このように`connected`と出たら、完了です。USB外してもadb devicesで端末が表示されるはずです。


## もし adb connectできなかったら

adb connectしたときに、下記のように接続できないことがありました。
```
% adb connect <端末のIPアドレス>
unable to connect to <端末のIPアドレス>:5555: Operation timed out
```

その場合は、PORTを変えてみてください。自分の場合はこれで繋がりました。

```
% adb tcpip 5556
restarting in TCP mode port: 5556
% adb connect <端末のIPアドレス>:5556
connected to <端末のIPアドレス>:5556
```
