# VLANプールの作成
VLANプールを作成します。この設定では以下のオブジェクトを対象とします。

- VLANプール(fvns:VlanInstP)
- VLANプールのカプセルブロック(fvns:EncapBlk)

## VLANプール(fvns:VlanInstP)
VLANプールは以下の形式で定義します。
```yml
aci_fvns_vlaninstp:
  - name: VLANプール名
    pool_allocation_mode: VLANのアロケーションモード(static|dynamic) 
    description: VLANプールの説明
    state: VLANプールの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略可能です。
- `pool_allocation_mode`: デフォルト値は`static`
- `description`: デフォルト値は空白

以下はVLANプールを2つ作成する定義の例です。
```yml
aci_fvns_vlaninstp:
  - name: NEW_VLAN_POOL 
    pool_allocation_mode: static
    description: 'New vlan pool.'
    state: present
  - name: NEW_VLAN_POOL_L3OUT
    pool_allocation_mode: static
    description: 'New vlan pool for L3Out.'
    state: present
```

## VLANプールのカプセルブロック(fvns:EncapBlk)
VLANプールのカプセルブロックは以下の形式で定義します。
```yml
aci_fvns_encapblk:
  - pool: VLANプール名
    pool_allocation_mode: VLANプールのアロケーションモード(inherit|static|dynamic) 
    block_start: VLANブロックの開始VLANID 
    block_end: VLANブロックの終了VLANID 
    state: ブロックの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略可能です。
- `pool_allocation_mode`: デフォルト値は`inherit`

以下はVLANプールのカプセルブロックを2つ作成する定義の例です。
```yml
aci_fvns_encapblk:
  - pool: NEW_VLAN_POOL
    block_start: 3791
    block_end: 3793
    state: present
  - pool: NEW_VLAN_POOL_L3OUT
    pool_allocation_mode: dynamic
    block_start: 3981
    block_end: 3983
    state: present
```

# ドメインの作成
物理ドメイン及びL3ドメインを作成します。この設定では以下のオブジェクトを対象とします。

- 物理ドメイン(phys:DomP)
- L3ドメイン(l3ext:DomP)
- VLANプールとドメインの関連付け(infra:RsVlanNs)

## 物理ドメイン(phys:DomP)
物理ドメインは以下の形式で定義します。
```yml
aci_phys_domp:
  - name: ドメイン名
    state: 物理ドメインの作成/削除(作成:present|削除:absent)
```

以下は物理ドメインを1つ作成する定義の例です。
```yml
aci_phys_domp:
  - name: NEW_PHYS_DOM
    state: present
```

## L3ドメイン(l3ext:DomP)
L3ドメインは以下の形式で定義します。
```yml
aci_l3ext_domp:
  - name: ドメイン名
    state: L3ドメインの作成/削除(作成:present|削除:absent)
```

以下はL3ドメインを1つ作成する定義の例です。
```yml
aci_l3ext_domp:
  - name: NEW_L3EXT_DOM
    state: present
```

## VLANプールとドメインの関連付け(infra:RsVlanNs)
VLANプールとドメインの関連付けは以下の形式で定義します。
```yml
aci_infra_rsvlanns:
  - domain: ドメイン名
    domain_type: ドメインのタイプ 
    pool: VLANプール名
    pool_allocation_mode: VLANプールのアロケーションモード
    state: 関連付けの作成/削除(作成:present|削除:absent)
```

`domain_type`は`(phys|l3dom|l2dom|fc|vmm)`のいずれかを指定します。

`pool_allocation_mode`は設定対象のVLANプールに対するアロケーションモードを指定します。

以下はVLANプールとドメインの関連付けを2つ作成する定義の例です。
```yml
aci_infra_rsvlanns:
  - domain: NEW_PHYS_DOM
    domain_type: phys
    pool: NEW_VLAN_POOL
    pool_allocation_mode: static
    state: present
  - domain: NEW_L3EXT_DOM
    domain_type: l3dom
    pool: NEW_VLAN_POOL_L3OUT
    pool_allocation_mode: static
    state: present
```

# AEPの作成
AEPとドメインへの関連付けを作成します。この設定は以下のオブジェクトを対象とします。
- AEP(infra:AttEntityP)
- AEPとドメインの関連付け(infra:RsDomP)

## AEP(infra:AttEntityP)
AEPは以下の形式で定義します。
```yml
aci_infra_attentityp:
  - name: AEP名
    description: AEPの説明
    infra_vlan: Infrastructure VLANの有効化 
    state: AEPの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略することが出来ます。
- `description`: デフォルトは空白
- `infra_vlan`: デフォルトは`false`

以下はAEPを1つ作成する定義の例です。
```yml
aci_infra_attentityp:
  - name: NEW_AEP
    description: 'My new AEP'
    state: present
```

## AEPとドメインの関連付け(infra:RsDomP)
AEPとドメインの関連付けは以下の形式で定義します。
```yml
aci_infra_rsdomp:
  - name: AEP
    domain: 関連付けるドメイン
    domain_type: 関連付けるドメインのタイプ
    state: 関連付けの作成/削除(作成:present|削除:absent)
```

`domain_type`は`(phys|l3dom|l2dom|fc|vmm)`のいずれかを指定します。

以下はAEPとドメインの関連付けを1つ作成する定義の例です。
```yml
aci_infra_rsdomp:
  - aep: NEW_AEP
    domain: NEW_PHYS_DOM
    domain_type: phys
    state: present
```

# インターフェースポリシーグループの作成
インターフェースポリシー及びインターフェースポリシーグループを作成します。
この設定は以下のオブジェクトを対象とします。

- リンクレベルインターフェースポリシー(fabric:HIfPol)
- CDPインターフェースポリシー(cdp:IfPol)
- LLDPインターフェースポリシー(lldp:IfPol)
- ポートチャンネルインターフェースポリシー(lacp:LagPol)
- インターフェースポリシーグループ(infra:AccPortGrp)

## リンクレベルインターフェースポリシー(fabric:HIfPol)
リンクレベルのインターフェースポリシーは以下の形式で定義します。
```yml
aci_fabric_hifpol:
  - name: ポリシー名
    auto_negotiation: オートネゴシエーション(true|false)
    speed: リンクスピード([100]<|[1-400]G) 
    link_debounce_interval: デバウンスタイマー
    forwarding_error_correction: FECモード
    state: ポリシーの作成/削除(作成:present|削除:absent)
```

以下の設定は省略することが出来ます。
- `auto_negotiation`: デフォルトは`true`
- `link_debounce_interval`: デフォルトは100
- `forwarding_error_correction`: デフォルトは`inherit`

以下はリンクスピード1Gbpsのポリシーを1つ作成する定義の例です。
```yml
aci_fabric_hifpol:
  - name: NEW_LINK_LEVEL_POL
    auto_negotiation: true
    speed: 1G
    state: present
```

## CDPインターフェースポリシー(cdp:IfPol)
CDPインターフェースポリシーは以下の形式で定義します。
```yml
aci_cdp_ifpol:
  - name: ポリシー名
    admin_state: CDPの有効化(true|false) 
    state: ポリシーの作成/削除(作成:present|削除:absent)
```

以下はCDP有効のポリシーを1つ作成する定義の例です。
```yml
aci_cdp_ifpol:
  - name: NEW_CDP_POL 
    admin_state: true
    state: present
```

## LLDPインターフェースポリシー(lldp:IfPol)
LLDPインターフェースポリシーは以下の形式で定義します。
```yml
aci_lldp_ifpol:
  - name: ポリシー名
    receive_state: LLDP受信の有効化(true|false)
    transmit_state: LLDP送信の有効化(true|false)
    state: ポリシーの作成/削除(作成:present|削除:absent)
```

以下はLLDP無効のポリシーを1つ作成する定義の例です。
```yml
aci_lldp_ifpol:
  - name: NEW_LLDP_POL
    receive_state: false
    transmit_state: false
    state: present
```

## ポートチャンネルインターフェースポリシー(lacp:LagPol)
ポートチャンネルインターフェースポリシーは以下の形式で定義します。
```yml
aci_lacp_lagpol:
  - name: ポリシー名
    mode: LACPのモード
    min_links: 最小リンク数
    max_links: 最大リンク数
    load_defer: ロードの遅延(true|false)
    graceful_convergence: LACP graceful-convergenceの有効化(true|false)
    fast_select: LACP高速セレクトの有効化(true|false) 
    state: ポリシーの作成/削除(作成:present|削除:absent)
```

`mode`は以下いずれかのモードを指定します。
- active
- mac-pin
- mac-pin-nicload
- off
- passive

`min_links`は最小1、`max_links`は最大16の範囲で指定します。

以下の設定は省略することが出来ます。
- `min_links`: デフォルトは1
- `max_links`: デフォルトは16
- `load_defer`: デフォルトは`false`
- `graceful_convergence`: デフォルトは`true`
- `fast_select`: デフォルトは`true`

以下はLACP-Activeのポートチャンネルポリシーを1つ作成する定義の例です。
```yml
aci_lacp_lagpol:
  - name: NEW_LACP_ACTIVE_POL
    mode: active
    min_links: 1
    max_links: 16
    load_defer: false
    graceful_convergence: true
    fast_select: true
    state: present
```

## インターフェースポリシーグループ(infra:AccPortGrp)
インターフェースポリシーグループは以下の形式で定義します。
```yml
aci_infra_accportgrp:
  - name: インターフェースポリシーグループ名
    lag_type: LAGのタイプ
    aep: 関連するAEP名
    description: ポリシーグループの説明
    link_level_policy: 適用するリンクレベルポリシー
    cdp_policy: 適用するCDPポリシー
    lldp_policy: 適用するLLDPポリシー
    # lag_typeがlinkまたはnodeの場合
    port_channel_policy: 適用するポートチャンネルポリシー
    state: ポリシーグループの作成/削除(作成:present|削除:absent)
```

`lag_type`は以下のいずれかを指定します。
- leaf: アクセスポート
- link: ポートチャンネル
- node: VPCポート

`lag_type`が`link|vpc`の場合、`port_channel_policy`オプションが追加で必要です。

以下のオプションは省略することが出来ます。
- `description`: デフォルトは空白

以下はアクセスポート及びVPCポートのインターフェースポリシーグループを作成する定義の例です。
```yml
aci_infra_accportgrp:
  - name: NEW_ACCESS_PORT_IPG
    lag_type: leaf
    aep: NEW_AEP
    description: 'Access Port'
    link_level_policy: NEW_LINK_LEVEL_POL 
    cdp_policy: NEW_CDP_POL
    lldp_policy: NEW_LLDP_POL
    state: present
  - name: NEW_VPC_PORT_IPG 
    lag_type: node
    aep: NEW_AEP
    description: 'VPC Port'
    link_level_policy: NEW_LINK_LEVEL_POL 
    cdp_policy: NEW_CDP_POL
    lldp_policy: NEW_LLDP_POL
    port_channel_policy: NEW_LACP_ACTIVE_POL
    state: present
```

# インターフェースプロファイルの作成
インターフェースプロファイルを作成します。この設定は以下のオブジェクトを対象とします。

- インターフェースプロファイル(infra:AccPortP)
- インターフェースセレクター(infra:HPorts)
- インターフェースセレクターのポートブロック(infra:PortBlk)

## インターフェースプロファイル(infra:AccPortP)
インターフェースプロファイルは以下の形式で定義します。
```yml
aci_infra_accportp:
  - name: プロファイル名
    description: プロファイルの説明 
    type: プロファイルのタイプ(leaf|fex)
    state: プロファイルの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略することが出来ます。
- `description`: デフォルトは空白
- `type`: デフォルトは`leaf`

以下はLEAFインターフェースプロファイルを1つ作成する定義の例です。
```yml
aci_infra_accportp:
  - name: NEW_IFP 
    type: leaf
    state: present
```
## インターフェースセレクター(infra:HPorts)
インターフェースセレクターは以下の形式で定義します。
```yml
aci_infra_hports:
  - type: セレクター(プロファイル)のタイプ(leaf|fex)
    interface_type: インターフェースのタイプ
    name: セレクター名
    interface_profile: 所属するインターフェースプロファイル
    policy_group: 適用するインターフェースポリシーグループ
    state: セレクターの作成/削除(作成:present|削除:absent)
    # type: fexの場合、以下のオプションを追加します。
    fex_profile: 所属するFEXプロファイル名
    fex_id: 所属するFEXのID
```

`interface_type`は以下のいずれかを設定します。
- breakout
- fex
- port_channel
- switch_port
- vpc
- fex_port_channel
- fex_vpc
- fex_profile

以下はアクセスポート及びVPCのインターフェースプロファイルを作成する定義の例です。
```yml
aci_infra_hports:
  - type: leaf
    interface_type: switch_port
    name: NEW_SELECTOR_ACC
    interface_profile: NEW_IFP
    policy_group: NEW_ACCESS_PORT_IPG
    state: present
  - type: leaf
    interface_type: vpc
    name: NEW_SELECTOR_VPC
    interface_profile: NEW_IFP
    policy_group: NEW_VPC_PORT_IPG
    state: present
```

## インターフェースセレクターのポートブロック(infra:PortBlk)
ポートブロックは以下の形式で定義します。
```yml
aci_infra_portblk:
  - type: セレクター(プロファイル)のタイプ(leaf|fex)
    name: ポートブロック名
    interface_profile: インターフェースプロファイル名
    access_port_selector: インターフェースセレクター名
    from_card: セレクタの開始カード番号
    to_card: セレクタの終了カード番号
    from_port: セレクタの開始ポート番号 
    to_port: セレクタの終了ポート番号 
    state: セレクターの作成/削除(作成:present|削除:absent)
```

`from_card`及び`to_card`は、ラインカード番号を整数値で指定します。

`form_port`及び`to_port`は、インターフェースのポート番号を整数値で指定します。

以下はポートブロックを1つ作成する定義の例です。
```yml
aci_infra_portblk:
  - type: leaf
    name: NEW_PORT_BLK
    interface_profile: NEW_IFP
    access_port_selector: NEW_SELECTOR_ACC
    from_card: 1
    to_card: 1
    from_port: 13
    to_port: 15
    state: present
```

# スイッチポリシーグループの作成
スイッチポリシーグループを作成します。この設定は以下のオブジェクトを対象とします。

- スイッチポリシーグループ(fabric:LeNodePGrp)

## スイッチポリシーグループ(fabric:LeNodePGrp)
スイッチポリシーグループは以下の形式で定義します。
```yml
aci_fabric_lenodepgrp:
  - name: ポリシーグループ名
    monitoring_policy: モニタリングポリシー
    inventory_policy: インベントリポリシー
    state: ポリシーグループの作成/削除(作成:present|削除:absent)
```

以下のオプションは省略することが出来ます。
- `monitoring_policy`: デフォルトは`default`
- `inventory_policy`: デフォルトは`default`

以下はデフォルト設定のポリシーグループを1つ作成する定義の例です。
```yml
aci_fabric_lenodepgrp:
  - name: NEW_DEFAULT_SPG 
```

# スイッチプロファイルの作成
スイッチプロファイルを作成します。この設定は以下のオブジェクトを対象とします。

- スイッチプロファイル(infra:NodeP)
- インターフェースプロファイルの関連付け(infra:RsAccPortP)
- LEAFセレクター(infra:LeafS)

## スイッチプロファイル(infra:NodeP)
スイッチプロファイルは以下の形式で定義します。
```yml
aci_infra_nodep:
  - name: スイッチプロファイル名
    state: スイッチプロファイルの作成/削除(作成:present|削除:absent)
```

以下はスイッチプロファイルを1つ作成する定義の例です。
```yml
aci_infra_nodep:
  - name: NEW_SP
    state: present
```

## インターフェースプロファイルの関連付け(infra:RsAccPortP)
スイッチプロファイルのインターフェースプロファイル関連付けは以下の形式で定義します。
```yml
aci_infra_rsaccportp:
  - switch_profile: スイッチプロファイル名
    interface_profile: インターフェースプロファイル名
    state: 関連付けの作成/削除(作成:present|削除:absent)
```

以下はインターフェースプロファイルを1つ関連付けする定義の例です。
```yml
aci_infra_rsaccportp:
  - switch_profile: NEW_SP
    interface_profile: NEW_IFP
    state: present 
```
## LEAFセレクター(infra:LeafS)
スイッチプロファイルのLEAFセレクターは以下の形式で定義します。
```yml
aci_infra_leafs:
  - name: LEAFセレクター名
    leaf_profile: スイッチプロファイル名
    policy_group: 適用するスイッチポリシーグループ名
    leaf_node_blk_name: ノードブロック名
    from: ノードブロックの開始LeafID 
    to: ノードブロックの終了LeafID 
    state: LEAFセレクターの作成/削除(作成:present|削除:absent)
```

以下はLEAFセレクターを1つ作成する定義の例です。
```yml
aci_infra_leafs:
  - name: NEW_SELECTOR
    leaf_profile: NEW_SP 
    policy_group: NEW_DEFAULT_SPG
    leaf_node_blk_name: NEW_SELECTOR_BLK1
    from: 101
    to: 102
    state: present
```