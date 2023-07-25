# APIC接続設定

APICのWebAPI接続設定を定義します。デフォルト設定は`defaults/main.yml`に記述されています。

接続設定は以下のパラメーターを定義します。
```yml
aci_apic_address: APICのIPアドレスまたはホスト名
aci_apic_username: APICのログインユーザー名
aci_apic_password: APICのログインパスワード
aci_apic_validate_certs: 接続時に証明書チェックを行う(true|false)
```

以下はCisco DevNetの[APICサンドボックス](sandboxapicdc.cisco.com)に接続する際の定義例です。
```yml
aci_apic_address: sandboxapicdc.cisco.com
aci_apic_username: admin
aci_apic_password: '!v3G@!4@Y'
aci_apic_validate_certs: false
```
