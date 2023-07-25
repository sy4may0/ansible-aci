ACI
=========

ACIのファブリック及びテナント設定を行います。

Requirements
------------

None.

Role Variables
--------------

変数のデフォルト値はdefaultsディレクトリを確認してください。必要に応じて、パラメーターをデフォルト値から変更する場合、group_vars等、他の変数定義ファイルを作成し値を上書きしてください。

変数の詳細な説明は以下のページを確認してください。
- [接続設定](documents/Variables.md)
- [ACIファブリック設定](documents/Variables_Fabric.md)
- [テナント設定](documents/Variables_Tenant.md)
- [L3Out設定](documents/Variables_L3Out.md)
- [コントラクト設定](documents/Variables_Fabric.md)

Dependencies
------------

None.

Example Playbook
----------------

ローカルから実行するため、`become: no`及び`gather_facts: no`を設定します。
```
- hosts: apic
  become: no
  gather_facts: no
  roles:
    - aci
```

License
-------

MIT

Author Information
------------------

This role was created in 2023 by [sy4may0](https://github.com/sy4may0).