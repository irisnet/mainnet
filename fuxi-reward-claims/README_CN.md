# 如何获得Fuxi测试网奖励

## 重要信息

**1 point = 100 IRIS** 

作为一个公有区块链网络，IRISnet网络的价值需要一组可靠的验证人来维护。Fuxi测试网的目的在于让验证人模拟主网上线后的真实情况，为将来做好准备。
Fuxi-8000是IRISnet Betanet上线前的最后一个测试网，Fuxi测试网的参与度很高。获得测试网积分奖励的同学可以按照以下步骤领取iris通证奖励。

1. 确认测试网积分奖励:

经过统计，社区一共获得了57820个积分奖励，平均每个人获得1100个积分。测试网积分奖励和iris的兑换比例为1 point = 100 IRIS。
测试网奖励积分汇总如下：

https://github.com/irisnet/testnets/issues/290

2. 准备PGP ID

发放给社区成员的iris代币奖励将写入Beta网络的genesis文件中。为了保障奖励发放的准确性，社区成员需要用GitHub账号认证的keybase签名一个irishub的账户地址。
Keybase使用教程如下： 

https://github.com/irisnet/testnets/blob/master/fuxi/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8keybase.md

3. 生成IRIShub账户地址：

* 安装IRIShub v0.12.0: https://github.com/irisnet/irishub/blob/master/docs/zh/get-started/Install-the-Software.md
请注意，若下载可执行文件请使用`irishub_0.12.0_mainnet_linux_amd64.zip`,若编译，请使用为betanet编译的命令。

* 生成账户: 若选增使用新的账户，可以参考以下教程：https://www.irisnet.org/docs/cli-client/keys/add.html
也可以使用已有的地址。该地址也对应了之后提交gentx文件中的`delegator-address` 


4. 使用Keybase签名

你可以使用keybase命令行工具： https://keybase.io/docs/command_line，
对该地址签名。例如：如果你的地址是: `iaa1mmsm487rqkgktl2qgrjad0z3yaf9n8t5pkp33m`,需要执行以下操作
```
keybase pgp sign -m "iaa1mmsm487rqkgktl2qgrjad0z3yaf9n8t5pkp33m"
```
也可以使用<https://keybase.io/sign> 完成签名操作


5. 提交签名信息

你需要将以上返回内容保存早 [github-user-name]-[keybase fingerprint].txt, 
例如,GitHub用户 [irisnetvalidator](https://github.com/irisnetvalidator) , 它的keybase id是 `6763B2C7947A9363`.
因此，它的提交文件名应该是：'irisnetvalidator-6763B2C7947A9363.txt'.

然后请以PR的方式提交你的`claim file`到`sig-files`文件夹下.

在团队公布genesis文件后确认账户余额是否与奖励一致