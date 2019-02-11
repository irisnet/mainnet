# 参与Betanet的Genesis文件生成

## 前提条件

每个希望成为验证人的参与者确保按照以下[文档](https://github.com/irisnet/irishub/blob/master/docs/zh/get-started/Install-the-Software.md) 安装了mainnet版本的软件：**iris**


## Step 1: 创建账户  


首先如果你没有现成的账户，或者忘记了原有账户的密码，那么你就需要为自己创建对应的验证人账户。该账户也是你用来[获取测试网奖励](https://github.com/irisnet/betanet/blob/master/fuxi-reward-claims/README_CN.md)的账户。


## Step 2: 初始化节点 

初始化节点的`chain-id=irishub`，`moniker`字段可以自定义
```bash
iris init --home={path_to_iris_home} --chain-id=irishub --moniker=node-name
```
该命令会在home目录下创建相应genesis&config文件

## Step 3: 执行gentx交易

执行gentx交易，并使用刚才创建的验证人账户对交易进行签名
```bash
iris gentx --amount=XXXiris --home={path_to_iris_home} --name={key_name} --ip={sentry_node_ip}
```
这个命令将把交易的结果存储在如下目录：{path_to_your_home}/config/gentx
gentx包含一个签名后的 `CreateValidator` 交易，这个交易将为验证人设置如下默认参数： 
*	delegation amount:           XXXiris
*	commission rate:             0.1
*	commission max rate:         0.2
*	commission max change rate:  0.01

commission相关参数也可根据需要进行修改。

> 请注意此处的amount为self-delegate的数量, 该数目不应该大于你的账户在genesis文件中的账户余额
> 请把IP字段填为哨兵节点的IP 

生成的gentx.json文件存放在目录下：{path_to_your_home}/config/gentx

## Step 4: 提交gentx文件

将上述提到的json文件保存为[github-user-name]-[keybase fingerprint].json

例如,GitHub用户 [irisnetvalidator](https://github.com/irisnetvalidator),它的keybase fingerprint是 `6763B2C7947A9363`.
因此，它的提交文件名应该是：'irisnetvalidator-6763B2C7947A9363.json'.


通过提交pull request将json文件提交到`https://github.com/irisnet/betanet/gentx/betanet-gentx` 目录下

在确认所有的gentx文件有效后，团队将公布最终的genesis文件

