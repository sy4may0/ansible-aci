# テナントの作成
テナントを作成します。この設定では以下のオブジェクトを対象とします。

- テナント(fv:Tenant)
- VRF(fv:Ctx)
- ブリッジドメイン(fv:BD)
- サブネット(fv:Subnet)
- アプリケーションプロファイル(fv:AP)
- AEPG(fv:AEPg)
- EPGのスタティックバインディング(fv:RsPathAtt)

## テナント(fv:Tenant)
テナントは以下の形式で定義します。
```yml
aci_fv_tenant:
  - name: テナント名
    description: テナントの説明
    state: テナントの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略することが出来ます。
- `description`: デフォルトは空白

以下はテナントを1つ作成する定義の例です。
```yml
aci_fv_tenant:
  - name: NEW_TENANT
    description: new tenant 
    state: present
```

## VRF(fv:Ctx)
VRFは以下の形式で定義します。
```yml
aci_fv_ctx:
  - name: VRF名 
    tenant: テナント名
    description: VRFの説明 
    policy_control_preference: 優先ポリシー制御(enforced|unenforced)
    policy_control_direction: 優先ポリシー制御の方向(ingress|egress)
    state: VRFの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略することができます。
- `description`: デフォルトは空白
- `policy_control_preference`: デフォルトは`enforced`
- `policy_control_direction`: デフォルトは`ingress`

以下はVRFを1つ作成する定義の例です。
```yml
aci_fv_ctx:
  - name: NEW_VRF
    tenant: NEW_TENANT
    description: IK
    policy_control_preference: enforced
    policy_control_direction: ingress
    state: present
```
## ブリッジドメイン(fv:BD)
ブリッジドメインは以下の形式で定義します。
```yml
aci_fv_bd:
  - name: ブリッジドメイン名
    vrf: VRF名
    tenant: テナント名
    bd_type: ドメインタイプ(ethernet|fc)
    mac_address: MACアドレス
    arp_flooding: ARPフラッディング(true|false)
    l2_unknown_unicast: 宛先不明L2ユニキャストパケットの送信(proxy|flood) 
    enable_routing: L3ルーティングの有効化(true|false)
    ip_learning: エンドポイントのIPラーニング有効化(true|false) 
    state: BDの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略することができます。
- `bd_type`: デフォルトは`ethernet`
- `mac_address`: デフォルトは`00:22:BD:F8:19:FF`
- `arp_flooding`: デフォルトは`false`
- `l2_unknown_unicast`: デフォルトは`proxy`
- `enable_routing`: デフォルトは`true`
- `ip_learning`: デフォルトは`true`

以下はブリッジドメインを1つ作成する定義の例です。

```yml
aci_fv_bd:
  - name: NEW_BD
    vrf: NEW_VRF
    tenant: NEW_TENANT
    bd_type: 'ethernet'
    mac_address: '00:22:BD:F8:19:AF'
    arp_flooding: true
    l2_unknown_unicast: 'proxy'
    state: present
```

## サブネット(fv:Subnet)
サブネットは以下の形式で定義します。
```yml
aci_fv_subnet:
  - name: サブネット名
    bd: ブリッジドメイン名 
    tenant: テナント名
    gateway: ゲートウェイIPアドレス
    mask: サブネットマスク(PREFIX)
    description: サブネットの説明
    scope: サブネットのスコープ
    state: BDの作成/削除(作成:present|削除:absent)
```

scopeは以下のいずれかを定義します。
- private
- public
- [private, shared]
- [public, shared]

以下のオプションは省略することができます。
- `description`: デフォルトは空白

以下はサブネットを1つ作成する定義の例です。
```yml
aci_fv_subnet:
  - name: NEW_SUBNET1
    bd: NEW_BD
    tenant: NEW_TENANT
    gateway: 192.168.19.1
    mask: 24
    description: new bd
    scope: [private, shared]
    state: present
```

## アプリケーションプロファイル(fv:AP)
APは以下の形式で定義します。
```yml
aci_fv_ap:
  - name: AP名
    tenant: テナント名
    description: APの説明
    monitoring_policy: 監視ポリシー
    state: APの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略することが出来ます。
- `description`: デフォルトは空白
- `monitoring_policy`: デフォルトは`default`

以下はAPを1つ作成する定義の例です。
```yml
aci_fv_ap:
  - name: NEW_AP 
    tenant: NEW_TENANT
    description: new ap
    monitoring_policy: default
    state: present
```

## AEPG(fv:AEPg)
AEPGは以下の形式で定義します。
```yml
aci_fv_aepg:
  - name: AEPG名
    ap: AP名
    bd: BD名
    tenant: テナント名
    description: AEPGの説明
    monitoring_policy: 監視ポリシー
    preffered_group: Prefferd Group設定 
    intra_epg_isolation: EPG Isolation設定 
    priority: QoSクラス設定(level[1-6]|unspecified)
    state: AEPGの作成/削除(作成:present|削除:absent)
```

`preffered_group`は以下の通り設定します。
- `true`: コントラクトを使用しない(include)
- `false`: コントラクトを使用する(exclude)

`intra_epg_isolation`は以下の通り設定します。
- `enforced`: Isolationを有効化
- `inenforced`: Isolationを無効化

以下のオプションは省略することが出来ます。
- `description`: デフォルトは空白
- `monitoring_policy`: デフォルトは`default`
- `preffered_group`: デフォルトは`false`
- `intra_epg_isolation`: デフォルトは`unenforced`
- `priority`: デフォルトは`level3`

以下はAEPGを1つ作成する定義の例です。
```yml
aci_fv_aepg:
  - name: NEW_AEPG
    ap: NEW_AP
    bd: NEW_BD
    tenant: NEW_TENANT
    description: new aepg
    monitoring_policy: default
    preffered_group: false
    intra_epg_isolation: enforced
    state: present
```

## EPGのスタティックバインディング(fv:RsPathAtt)
スタティックバインディングは以下の形式で定義します。

```yml
aci_fv_rspathatt:
  - ap: AP名
    epg: EPG名
    tenant: テナント名
    encap_id: VLANID
    interface_mode: インターフェースモード
    interface_type: インターフェースタイプ
    deploy_immediacy: データプレーンへの即時反映
    description: スタティックバインディングの説明
    interface_configs: インターフェース設定のリスト
    state: スタティックバインディングの作成/削除(作成:present|削除:absent)
```

`interface_mode`は以下の値を指定します。
- native
- untagged
- trunk

`interface_type`は以下いずれかの値を指定します。
- fex
- port_channel
- switch_port
- vpc
- fex_port_channel
- fex_vpc

`deploy_immediacy`は以下いずれかの値を指定します。
- immediate: 即時反映
- lazy: アクセス時に反映

`interface_configs`は以下のオプションを指定した辞書型のリストを指定します。
```yml
interface_configs:
  - interface: インターフェース番号(X/X)
    leafs: LeafIDのリスト
    pod: ポッドID
```

VPCポートの場合、`leafs`に複数のLeafIDを指定します。
```yml
leafs:
  - 101
  - 102
```

また、以下の通りインターフェースごとに親の設定値を上書きすることが出来ます。
```yml
interface_configs:
  - interface: インターフェース番号(X/X)
    leafs: LeafIDのリスト
    pod: ポッドID
    encap_id: 特定のインターフェースでVLANIDを上書き指定
    interface_mode: 特定のインターフェースでインターフェースモードを上書き指定
    interface_type: 特定のインターフェースでインターフェースタイプを上書き指定
    deploy_immediacy: 特定のインターフェースで即時反映を上書き指定
    description: 特定のインターフェースで説明を上書き指定
```

以下はスイッチポート及びVPCポートのスタティックバインディングを設定する定義の例です。
```yml
aci_fv_rspathatt:
  - ap: NEW_AP
    epg: NEW_AEPG
    tenant: NEW_TENANT
    encap_id: 3791
    interface_mode: untagged
    interface_type: switch_port
    deploy_immediacy: immediate
    description: '3791 Access'
    interface_configs:
      - interface: 1/13
        leafs: 101
        pod: 1
      - interface: 1/14
        leafs: 101
        pod: 1
      - interface: 1/15
        leafs: 101
        pod: 1
    state: present
  - ap: NEW_AP
    epg: NEW_AEPG
    tenant: NEW_TENANT
    encap_id: 3792
    interface_mode: trunk
    interface_type: vpc
    deploy_immediacy: immediate
    description: '3792 VPC trunk'
    interface_configs:
      - interface: 1/16
        leafs: [101,102]
        pod: 1
    state: present
```