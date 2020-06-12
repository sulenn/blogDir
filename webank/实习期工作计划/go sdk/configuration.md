# 配置说明

Go SDK的配置文件为一个TOML文件，主要包括**网络配置**、**账户配置**以及**链配置**。配置文件示例如下：

```toml
[Network]                                                 ### 网络基础配置
Type="channel"                                    # 网络连接模式，支持channel和rpc
CAFile="ca.crt"                                      # ca证书文件
Cert="sdk.crt"                                        # sdk证书文件
Key="sdk.key"                                       # sdk私钥文件
[[Network.Connection]]                    ### 网络连接配置
NodeURL="127.0.0.1:20200"           # 节点URL地址
GroupID=1                                               # 群组ID
# [[Network.Connection]]
# NodeURL="127.0.0.1:20200"
# GroupID=2

[Account]                                                  ### 账户配置
KeyFile=".ci/0x83309d045a19c44dc3722d15a6abd472f95866ac.pem"             #私钥文件路径，目前仅支持PEM格式

[Chain]                                                       ### 链配置
ChainID=1                                                 # 链ID
SMCrypto=false                                      # 国密选项，true为支持国密、false为不支持国密
```



## 网络配置

### 网络基础配置

网络基础配置部分主要用于设置`网络连接模式`以及建立连接所需要的`证书文件`。

- `Type`：是Go SDK与区块链节点建立连接的模式，支持channel和rpc两种方式；
  - `channel`：使用ssl协议建立连接，需要提供ca.crt、sdk.crt、sdk.key这三个证书；
  - `rpc`：使用http协议建立连接，不需要提供证书；
- `CAfile`：CA根证书文件路径。用于验证待连接节点的合法性；
- `Cert`：SDK证书文件路径。用于待连接节点验证SDK的合法性；
- `Key`：SDK私钥文件路径。Cert证书对应的私钥，用于解密和签名。

### 网络连接配置

网络连接配置部分主要用于设置待连接的`节点信息`，支持设置多个节点。

- `NodeURL`：待连接节点的URL地址，由IP和port两部分组成；
- `GroupID`：待连接节点所属的群组ID。

## 账户配置

- `KeyFile`：外部账户的私钥文件路径，目前只支持pem格式的私钥文件。国密和非国密账户脚本可从[get_account.sh](https://github.com/FISCO-BCOS/console/blob/master/tools/get_account.sh)和[get_gm_account.sh](https://github.com/FISCO-BCOS/console/blob/master/tools/get_gm_account.sh)下载，使用方式可参考[账户管理](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/account.html)。此外，Go SDK代码也支持生成账号，[参考这里](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/account.html)了解更多。

## 链配置

- `ChainID`：待连接节点所属的链ID，可通过查看节点config.ini配置文件中chain.id配置项获得；
- `SMCrypto`：待连接节点所属链使用的签名算法，ture表示使用国密SM2，false表示使用普通ECDSA。