# シーケンス図

## ラズパイの電源が入った時

```mermaid
sequenceDiagram
    participant server as PHP Server
    participant python as Python Server
    participant client as Raspberry Pi

    client ->> python: ラズパイのIDを送信<br>(send_pi_id)
    alt 実行中のフラグがTrueだったら
        python ->> client: 適切な指示を出す
        python ->> server: 再接続があったことを通知<br>(reconnected_pi)
    else 実行中のフラグがFalseだったら
        python ->> server: 接続があったことを通知<br>(connected_pi)

        alt タスクがセットされていれば
            python ->> client: タスクの準備を指示<br>(ready_task)
            Note over client: 次のタスクを表示
        else タスクがセットされていなければ
            python ->> client: タスクセットのお願いの表示を指示<br>(req_ready_task)
            Note over client: タスクセットのお願いを表示
        end
    end
```

## Webでタスクをセットした時

```mermaid
sequenceDiagram
    participant server as PHP Server
    participant python as Python Server
    participant client as Raspberry Pi 

    server ->> python: やるタスクの一覧を送信<br>(send_tasks)
    Note over python: タスク情報を処理
    alt ラズパイとの接続があれば
        python ->> client: タスクの準備を指示<br>(ready_task)
        Note over client: 次のタスクを表示
    end
```

## ラズパイのボタンが押された時

```mermaid
sequenceDiagram
    participant server as PHP Server
    participant python as Python Server
    participant client as Raspberry Pi

    client ->> python: タスクの開始をリクエスト<br>(req_start_task)
    Note over python: 実行可能かどうかを調査する
    alt タスクが実行可能だったら
        Note over python: 実行中のフラグをTrueに
        python ->> client: タスクの開始を指示<br>(start_task)
        python ->> server: タスクが開始されたことを通知<br>(started_task)
    else タスクが実行不可だったら
        python ->> client: タスクが実行不可なことを通知<br>(cannot_start_task)
    end
```

## ラズパイのタイマーが終了した時

```mermaid
sequenceDiagram
    participant server as PHP Server
    participant python as Python Server
    participant client as Raspberry Pi

    Note over client: アラームの停止ボタンが押されるまで待機
    client ->>+ python: タスクの終了をリクエスト<br>(req_finish_task)
    Note over python: タイマーの終了を確認できるまで待機
    Note over python: 実行中のフラグをFalseに
    python ->>- client: タスクの終了を指示<br>(finish_task)
    python ->> server: タスクが終了されたことを通知<br>(finished_task)
    Note over client: アラームを停止
    Note over client: タスク表示を次に変更
```

## Web側からタスクを強制終了した時

```mermaid
sequenceDiagram
    participant server as PHP Server
    participant python as Python Server
    participant client as Raspberry Pi

    server ->> python: タスクの強制終了を指示<br>(stop_task)
    Note over python: 実行中のフラグをFalseに
    python ->> client: タスクの強制終了を指示<br>(stop_task)
```

## 次のタスクの時間が来た時

```mermaid
sequenceDiagram
    participant server as PHP Server
    participant python as Python Server
    participant client as Raspberry Pi

    Note over server: 次の時間が来たことをプッシュ通知
```
