# Magicodes.Pay

## 简介

Magicodes.Pay，是心莱科技团队提供的统一支付库，相关库均使用.NET标准库编写，支持.NET Framework以及.NET Core。目前已提供Abp模块的封装，支持开箱即用。

## Nuget

### 新的包

| 名称     |      说明      |      Nuget      |
|----------|:-------------:|:-------------:|
| Magicodes.Pay.Alipay  |支付宝支付库|  [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Alipay)](https://www.nuget.org/packages/Magicodes.Pay.Alipay) |
| Magicodes.Pay.Notify  |支付通用回调库|   [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Notify)](https://www.nuget.org/packages/Magicodes.Pay.Notify) |
| Magicodes.Pay.Wxpay  |微信支付库|  [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Wxpay)](https://www.nuget.org/packages/Magicodes.Pay.Wxpay) |
| Magicodes.Pay.Alipay.Global |国际支付宝支付库 |  [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Alipay.Global)](https://www.nuget.org/packages/Magicodes.Pay.Alipay.Global) |
| Magicodes.Pay.Allinpay  | 通联支付库 | [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Allinpay)](https://www.nuget.org/packages/Magicodes.Pay.Allinpay) |
| Magicodes.Pay.Abp  | ABP 支付通用封装库| [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Abp)](https://www.nuget.org/packages/Magicodes.Pay.Abp) |
| Magicodes.Pay.Abp.Allinpay  |ABP 通联支付模块|  [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Abp.Allinpay)](https://www.nuget.org/packages/Magicodes.Pay.Abp.Allinpay) |
| Magicodes.Pay.Abp.Wxpay  | ABP 微信支付模块| [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Abp.Wxpay)](https://www.nuget.org/packages/Magicodes.Pay.Abp.Wxpay) |
| Magicodes.Pay.Abp.Alipay.Global  | ABP 国际支付宝模块 |  [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Abp.Alipay.Global)](https://www.nuget.org/packages/Magicodes.Pay.Abp.Alipay.Global) |

### 已弃用的包，不再更新

| 名称     |      Nuget      |
|----------|:-------------:|
| Magicodes.Alipay  |  [![NuGet](https://buildstats.info/nuget/Magicodes.Alipay)](https://www.nuget.org/packages/Magicodes.Alipay) |
| Magicodes.Alipay.Global |    [![NuGet](https://buildstats.info/nuget/Magicodes.Alipay.Global)](https://www.nuget.org/packages/Magicodes.Alipay.Global)   |
| Magicodes.Pay.WeChat | [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.WeChat)](https://www.nuget.org/packages/Magicodes.Pay.WeChat) |
| Magicodes.PayNotify | [![NuGet](https://buildstats.info/nuget/Magicodes.PayNotify)](https://www.nuget.org/packages/Magicodes.PayNotify) |

## 主要功能
Magicodes.Pay，是心莱科技团队提供的统一支付库，相关库均使用.NET标准库编写，支持.NET Framework以及.NET Core。目前已提供Abp模块的封装，支持开箱即用。目前支持以下支付方式和功能：
* 支付宝支付
  * APP支付
  * Wap支付
* 支付宝国际支付
  * 支持分账
* 微信支付
  * 小程序支付
  * APP支付
  * 订单查询
  * 企业付款（提现）
  * 退款申请
  * 普通红包
* 通联支付
  * 小程序支付
* 统一支付回调处理
* 支持日志函数注入（不依赖支付库）
* 支持支付配置函数注入，以便于支持自定义配置获取逻辑，以应用于不同的场景（比如从配置文件、用户设置获取配置，或者多租户支持）
* 针对ABP提供模块封装，添加模块依赖即可立即使用。主要包括：
  * 统一支付服务封装（见IPayAppService）
  * 支付管理器封装（IPaymentManager），包含：
    * 支付渠道注册（IPaymentRegister）
    * 支付回调逻辑处理（IPaymentCallbackAction）
    * 统一支付服务实现（IToPayService）
  * 交易日志封装，自动记录客户端信息以及自动异常处理和记录
  * 仅需编写一次回调逻辑，即可支持多个支付渠道
  * 业务参数支持更大长度（500）

## 开始使用

如果使用Abp相关模块，则使用起来比较简单，具体您可以参考相关单元测试的编写。主要有以下步骤：

1. 引用对应的Abp支付的Nuget包
如果仅需某个支付，仅需引用该支付的包。下面以通联支付为例，我们需要在工程中引用此包：

| 名称     |      说明      |      Nuget      |
|----------|:-------------:|:-------------:|
| Magicodes.Pay.Abp.Allinpay  |ABP 通联支付模块|  [![NuGet](https://buildstats.info/nuget/Magicodes.Pay.Abp.Allinpay)](https://www.nuget.org/packages/Magicodes.Pay.Abp.Allinpay) |

2. 添加模块依赖
在对应工程的Abp的模块（AbpModule）中，添加对“AbpAllinpayModule”的依赖，如：

````C#
    [DependsOn(typeof(AbpAllinpayModule))]
````

3. 在DbContext中添加名为“TransactionLogs”的DbSet
整个支付过程中（无论是支付成功还是出现异常），均会记录交易日志。交易日志会记录交易过程中的一些信息，比如客户端信息、交易参数、自定义参数以及异常信息。因此我们需要针对EF添加对TransactionLog的支持。需要在DbContext中添加的完整代码如下所示：

````C#
public DbSet<TransactionLog> TransactionLogs { get; set; }
````

4. 注册回调逻辑
我们需要实现“IPaymentCallbackAction”接口来编写自定义的回调逻辑。如以下示例所示：

````C#
    public class TestPaymentCallbackAction : IPaymentCallbackAction
    {
        /// <summary>
        /// 业务Key
        /// </summary>
        public string Key { get; set; } = "缴费支付";

        /// <summary>
        /// 执行回调
        /// </summary>
        /// <returns></returns>
        public async Task Process(IUnitOfWorkManager unitOfWork, TransactionLog transactionLog)
        {
            var data = transactionLog.CustomData.FromJsonString<JObject>();
            //业务处理

            await Task.FromResult(0);
        }
    }
````
注意Key不要重复。

5. 向容器中注册回调逻辑

我们可以将回调逻辑写在一个公共的程序集，然后使用以下代码进行注册：

````C#
            IocManager.IocContainer.Register(
                //注册自定义支付回调逻辑
                Classes.FromAssembly(typeof(ApplicationCoreModule).GetAssembly())
                    .BasedOn<IPaymentCallbackAction>()
                    .LifestyleTransient()
                    .Configure(component => component.Named(component.Implementation.FullName))
                    .WithServiceFromInterface()
            );
````

除了上面的方式，我们还可以通过注入IPaymentManager对象，通过其RegisterCallbackAction方法来注册自定义的回调逻辑。

6. 发起支付

通过容器获得IPayAppService，然后调用Pay方法即可。也可以自行封装：

````C#
        public async Task<object> Payment(PaymentInput input)
        {
            return await _payAppService.Pay(new PayInputBase()
            {
                Body = $"{input.Name} {input.ChargeProjectName}",
                CustomData = input.ToJsonString(),
                Key = "缴费支付",
                OpenId = input.OpenId,
                Subject = input.ChargeProjectName,
                TotalAmount = input.Amount,
                PayChannel = input.PayChannel
            });
        }
````

通过IPayAppService统一支付有如下好处：
- 统一支付（无论支付宝还是微信各种端的支付，均可统一）
- 自动记录交易日志以及进行相关逻辑处理
- 自定义数据依赖交易日志进行存储，而不依赖支付渠道，因此支持无业务参数的支付渠道，也支持存储更多自定义数据

## 非ABP集成

请参考Abp相关模块的封装或者历史代码。

## 官方订阅号

关注“麦扣聊技术”订阅号免费获取：

* 最新文章、教程、文档
* 视频教程
* 基础版免费授权
* 模板
* 解决方案
* 编程心得和理念

![官方订阅号](res/wechat.jpg)

## 相关QQ群

编程交流群<85318032>

产品交流群<897857351>

## 官方博客/文档站

- <http://www.cnblogs.com/codelove/>
- <https://docs.xin-lai.com/>

## 其他开源库地址

- <https://gitee.com/magicodes/Magicodes.Admin.Core>
- <https://github.com/xin-lai>

