**简体中文** · [English](README.en.md)

![Hummingbot](https://github.com/user-attachments/assets/3213d7f8-414b-4df8-8c1b-a0cd142a82d8)

----
[![License](https://img.shields.io/badge/License-Apache%202.0-informational.svg)](https://github.com/hummingbot/hummingbot/blob/master/LICENSE)
[![Twitter](https://img.shields.io/twitter/url?url=https://twitter.com/_hummingbot?style=social&label=_hummingbot)](https://twitter.com/_hummingbot)
[![Youtube](https://img.shields.io/youtube/channel/subscribers/UCxzzdEnDRbylLMWmaMjywOA)](https://www.youtube.com/@hummingbot)
[![Discord](https://img.shields.io/discord/530578568154054663?logo=discord&logoColor=white&style=flat-square)](https://discord.gg/hummingbot)

Hummingbot 是一个开源框架，帮助你设计和部署自动化交易策略（即**机器人**），可在众多中心化或去中心化交易所上运行。过去一年里，Hummingbot 用户在 140 多个不同的交易场所累计产生了超过 340 亿美元的交易量。

Hummingbot 代码库以 Apache 2.0 开源许可证免费公开。我们的使命是通过建立一个由算法交易者和开发者组成、共享知识并共同贡献代码的全球社区，来**让高频交易大众化**。

## 快速链接

* [官网与文档](https://hummingbot.org）：Hummingbot 官方网站和文档
* [安装](https://hummingbot.org/installation/)：在各种平台上安装 Hummingbot
* [Discord](https://discord.gg/hummingbot)：全球 Hummingbot 社区的主要聚集地
* [YouTube](https://www.youtube.com/c/hummingbot)：教你充分利用 Hummingbot 的视频
* [Twitter](https://twitter.com/_hummingbot)：获取 Hummingbot 的最新公告
* [上报交易量](https://reporting.hummingbot.org/)：所有 Hummingbot 实例上报的交易量
* [通讯](https://hummingbot.substack.com)：每次发布新版本时都会收到我们的通讯

## 快速上手

### Condor（AI 框架）

**[Condor](https://github.com/hummingbot/condor)** 是用于构建和运行智能体策略及机器人实例的 AI 框架。它通过 Hummingbot API 将由 LLM 驱动的决策与确定性的交易执行连接起来，可通过 Telegram 或其 Web 仪表盘控制。请访问 **[condor.hummingbot.org](https://condor.hummingbot.org/)** 开始使用。

### `hbot` CLI

直接运行 Hummingbot 客户端的推荐方式是从源码安装的 **`hbot` 命令行界面**。`hbot` 以非交互方式运行、控制和监控交易机器人：启动/停止机器人、编写和调整配置，以及读取成交记录、盈亏（PnL）、日志和状态——全部可脚本化，输出为紧凑的 Markdown，并带有稳定的退出码。完整参考请参阅 **[hbot CLI 指南](hummingbot/cli/README.md)**。

需要 [Anaconda 或 Miniconda](https://www.anaconda.com/download)。

```bash
# Clone the repository
git clone https://github.com/hummingbot/hummingbot.git
cd hummingbot

# Create the conda environment, build extensions, and expose the `hbot` CLI
make install

# Activate the environment
conda activate hummingbot
hbot --help
```

如需在 conda 环境之外使用 `hbot`，请运行 `make link-cli` 将其加入主机的 PATH。

首次使用时，`hbot` 会提示你设置一个密钥库密码，用于加密你的交易所 API 密钥——设置 `HBOT_PASSWORD` 或传入 `--password-stdin` 即可以非交互方式运行（例如在脚本或智能体工作流中）。

然后创建配置并运行 `simple_pmm` **模拟交易脚本**——它基于 Binance 实时行情数据模拟交易，因此不需要 API 密钥：

```bash
hbot create simple_pmm --name conf_paper_bot.yml \
     --set exchange=binance_paper_trade --set trading_pair=BTC-USDT
hbot start conf_paper_bot.yml                          # run it (one bot per install)
hbot status                                            # check on it
hbot stop                                              # stop gracefully
```

如需**实盘**交易，请连接你的交易所 API 密钥，并运行 `pmm_mister` 这样的**策略控制器**——这是一个可复用的 V2 策略，可在机器人运行时实时调整其设置：

```bash
hbot connect binance                                   # store API keys (encrypted)
hbot create pmm_mister --name conf_my_bot.yml \
     --set connector_name=binance --set trading_pair=BTC-USDT --set total_amount_quote=100
hbot start conf_my_bot.yml                             # run it (one bot per install)
```

完整的命令参考和概念体系：**[hbot CLI 指南](hummingbot/cli/README.md)**。

### Docker

更喜欢容器？`hbot` 的用法完全相同——安装 [Docker Compose](https://docs.docker.com/compose/install/)，然后：

```bash
git clone https://github.com/hummingbot/hummingbot.git
cd hummingbot
make setup            # answer `y` to "Include Gateway?" to add the DEX middleware
make deploy           # start the container (interactive client by default)
make link-cli         # put the `hbot` command on your host PATH (dispatches into the container)

hbot --help           # same commands as the source install above
```

`make link-cli` 会安装一个在容器内运行 `hbot` 的小型包装器，因此无论你是从源码还是用 Docker 安装，上面的每条命令都完全相同。（也可以跳过这一步，使用 `docker exec -it hummingbot hbot <command>`。）如果想让容器专门运行 `hbot` 而不是交互式客户端，请在 `make deploy` 之前取消 `docker-compose.yml` 中 `command: tail -f /dev/null` 的注释——参见[在 Docker 中运行](hummingbot/cli/README.md#running-in-docker)。

### 交互式客户端（TUI）

经典的全屏客户端是 Docker 的默认方式：先 `make deploy`，再 `docker attach hummingbot`——也可以从源码通过 `make install && make run` 运行。包含 Gateway 时，它会以开发模式启动（未加密的 HTTP）；生产环境如需 HTTPS，请使用 `DEV=false` 参数并运行 `gateway generate-certs`。参见[开发模式与生产模式](https://hummingbot.org/gateway/installation/#development-vs-production-modes)。

---

完整的安装说明和故障排除，请访问我们的[安装](https://hummingbot.org/installation/)文档。

## 策略

Hummingbot 提供多种用于构建和运行算法交易策略的框架——完整概览请参阅[策略文档](https://hummingbot.org/strategies/)：

* **[脚本（Scripts）](./scripts)**：单文件 Python 策略——构建和定制自己的机器人最简单的方式。示例：[`simple_pmm.py`](./scripts/simple_pmm.py)，一个基础的做市脚本。
* **[控制器（Controllers）](./controllers)**：可复用的 V2 策略，其配置可以回测、部署，并在运行时实时调整。示例：[`pmm_mister.py`](./controllers/generic/pmm_mister.py)，一个功能完整的做市控制器。
* **[执行器（Executors）](./hummingbot/strategy_v2/executors)**：自包含的构建模块，为常见模式管理订单生命周期——持仓、DCA、网格、套利、XEMM、TWAP 和 LP。示例：[`position_executor`](./hummingbot/strategy_v2/executors/position_executor)，使用三重屏障风控管理方向性持仓。
* **[V1 策略](./hummingbot/strategy)**：经典的旧版策略，如纯做市（Pure Market Making）、Avellaneda 做市和跨交易所做市。示例：[`cross_exchange_market_making`](./hummingbot/strategy/cross_exchange_market_making)，在一个交易所做市，并在另一个交易所对冲成交。

## 交易所连接器

Hummingbot 连接器将不同类型交易所的 REST 和 WebSocket API 接口标准化，使你构建的复杂交易策略只需极少改动即可部署到众多交易所。

### 连接器类型

我们将交易所连接器分为三大类：

* **CLOB CEX**：采用中央限价订单簿、托管你资金的中心化交易所。通过 API 密钥连接。
  - **Spot（现货）**：交易现货市场
  - **Perpetual（永续）**：交易永续合约市场

* **CLOB DEX**：采用链上中央限价订单簿的去中心化交易所。非托管，通过钱包密钥连接。
  - **Spot（现货）**：在链上交易现货市场
  - **Perpetual（永续）**：在链上交易永续合约

* **AMM DEX**：使用自动做市商（AMM）协议的去中心化交易所。非托管，通过 Gateway 中间件连接。
  - **Router（路由）**：寻找最优兑换路径的 DEX 聚合器
  - **AMM**：传统的恒定乘积（x*y=k）池
  - **CLMM**：可自定义价格区间的集中流动性做市商池

### 交易所赞助商

感谢以下交易所通过经纪商合作和赞助支持 Hummingbot 的开发与维护。

| 交易所 | 类型 | 子类型 | 连接器 ID | 折扣 |
|------|------|------|-------|----------|
| [Backpack](https://hummingbot.org/exchanges/backpack/) | CLOB CEX | Spot, Perpetual | `backpack`, `backpack_perpetual` | [![Sign up for Backpack using Hummingbot's referral link!](https://img.shields.io/static/v1?label=Sponsor&message=Link&color=orange)](https://backpack.exchange/join/1tvdqfkk) |
| [Binance](https://hummingbot.org/exchanges/binance/) | CLOB CEX | Spot, Perpetual | `binance`, `binance_perpetual` | [![Sign up for Binance using Hummingbot's referral link for a 10% discount!](https://img.shields.io/static/v1?label=Fee&message=%2d10%25&color=orange)](https://accounts.binance.com/register?ref=CBWO4LU6) |
| [Bitget](https://hummingbot.org/exchanges/bitget/) | CLOB CEX | Spot, Perpetual | `bitget`, `bitget_perpetual` | [![Sign up for Bitget using Hummingbot's referral link!](https://img.shields.io/static/v1?label=Sponsor&message=Link&color=orange)](https://www.bitget.com/expressly?channelCode=v9cb&vipCode=26rr&languageType=0) |
| [Derive](https://hummingbot.org/exchanges/derive/) | CLOB DEX | Spot, Perpetual | `derive`, `derive_perpetual` | [![Sign up for Derive using Hummingbot's referral link!](https://img.shields.io/static/v1?label=Sponsor&message=Link&color=orange)](https://www.derive.xyz/invite/7SA0V) |
| [Gate.io](https://hummingbot.org/exchanges/gate-io/) | CLOB CEX | Spot, Perpetual | `gate_io`, `gate_io_perpetual` | [![Sign up for Gate.io using Hummingbot's referral link for a 20% discount!](https://img.shields.io/static/v1?label=Fee&message=%2d20%25&color=orange)](https://www.gate.io/referral/invite/HBOTGATE_0_103) |
| [Hyperliquid](https://hummingbot.org/exchanges/hyperliquid/) | CLOB DEX | Spot, Perpetual | `hyperliquid`, `hyperliquid_perpetual` | - |
| [KuCoin](https://hummingbot.org/exchanges/kucoin/) | CLOB CEX | Spot, Perpetual | `kucoin`, `kucoin_perpetual` | [![Sign up for Kucoin using Hummingbot's referral link for a 20% discount!](https://img.shields.io/static/v1?label=Fee&message=%2d20%25&color=orange)](https://www.kucoin.com/r/af/hummingbot) |
| [Meteora](https://hummingbot.org/exchanges/gateway/meteora/) | AMM DEX | CLMM | `meteora` | - |
| [OKX](https://hummingbot.org/exchanges/okx/) | CLOB CEX | Spot, Perpetual | `okx`, `okx_perpetual` | [![Sign up for OKX using Hummingbot's referral link for a 20% discount!](https://img.shields.io/static/v1?label=Fee&message=%2d20%25&color=orange)](https://www.okx.com/join/1931920269) |
| [Orca](https://hummingbot.org/exchanges/gateway/orca/) | AMM DEX | CLMM | `orca` | - |
| [XRP Ledger](https://hummingbot.org/exchanges/xrpl/) | CLOB DEX | Spot | `xrpl` | - |

### 其他交易所连接器

目前，Hummingbot 的 master 分支还包含以下交易所连接器，它们通过 Hummingbot 基金会的治理流程进行维护和更新。更多信息请参阅[治理](https://hummingbot.org/about/governance/)。

| 交易所 | 类型 | 子类型 | 连接器 ID | 折扣 |
|------|------|------|-------|----------|
| [0x Protocol](https://hummingbot.org/gateway/connectors/) | AMM DEX | Router | `0x` | - |
| [Aevo](https://hummingbot.org/exchanges/aevo/) | CLOB CEX | Perpetual | `aevo_perpetual` | - |
| [Architect](https://hummingbot.org/exchanges/architect/) | CLOB CEX | Perpetual | `architect_perpetual` | - |
| [Balancer](https://hummingbot.org/exchanges/gateway/balancer/) | AMM DEX | AMM | `balancer` | - |
| [BingX](https://hummingbot.org/exchanges/bing_x/) | CLOB CEX | Spot | `bing_x` | - |
| [Bitrue](https://hummingbot.org/exchanges/bitrue/) | CLOB CEX | Spot | `bitrue` | - |
| [Bitstamp](https://hummingbot.org/exchanges/bitstamp/) | CLOB CEX | Spot | `bitstamp` | - |
| [BTC Markets](https://hummingbot.org/exchanges/btc-markets/) | CLOB CEX | Spot | `btc_markets` | - |
| [Bybit](https://hummingbot.org/exchanges/bybit/) | CLOB CEX | Spot, Perpetual | `bybit`, `bybit_perpetual` | - |
| [Coinbase](https://hummingbot.org/exchanges/coinbase/) | CLOB CEX | Spot | `coinbase_advanced_trade` | - |
| [Curve](https://hummingbot.org/exchanges/gateway/curve/) | AMM DEX | AMM | `curve` | - |
| [Decibel](https://hummingbot.org/exchanges/decibel/) | CLOB CEX | Perpetual | `decibel_perpetual` | - |
| [Dexalot](https://hummingbot.org/exchanges/dexalot/) | CLOB DEX | Spot | `dexalot` | - |
| [DFlow](https://hummingbot.org/exchanges/gateway/jupiter/#other-solana-routers) | AMM DEX | Router | `dflow` | - |
| [dYdX](https://hummingbot.org/exchanges/dydx/) | CLOB DEX | Perpetual | `dydx_v4_perpetual` | - |
| [EVEDEX](https://hummingbot.org/exchanges/evedex/) | CLOB CEX | Perpetual | `evedex_perpetual` | - |
| [Foxbit](https://hummingbot.org/exchanges/foxbit/) | CLOB CEX | Spot | `foxbit` | - |
| [Gemini](https://hummingbot.org/exchanges/gemini/) | CLOB CEX | Spot | `gemini` | - |
| [GRVT](https://hummingbot.org/exchanges/grvt/) | CLOB CEX | Perpetual | `grvt_perpetual` | - |
| [HTX (Huobi)](https://hummingbot.org/exchanges/htx/) | CLOB CEX | Spot | `htx` | - |
| [Injective Helix](https://hummingbot.org/exchanges/injective/) | CLOB DEX | Spot, Perpetual | `injective_v2`, `injective_v2_perpetual` | - |
| [Jupiter](https://hummingbot.org/exchanges/gateway/jupiter/) | AMM DEX | Router | `jupiter` | - |
| [Kraken](https://hummingbot.org/exchanges/kraken/) | CLOB CEX | Spot | `kraken` | - |
| [Lambdaplex](https://hummingbot.org/exchanges/lambdaplex/) | CLOB DEX | Spot | `lambdaplex` | - |
| [Lighter](https://hummingbot.org/exchanges/lighter/) | CLOB DEX | Spot, Perpetual | `lighter`, `lighter_perpetual` | - |
| [MEXC](https://hummingbot.org/exchanges/mexc/) | CLOB CEX | Spot | `mexc` | - |
| [NDAX](https://hummingbot.org/exchanges/ndax/) | CLOB CEX | Spot | `ndax` | - |
| [OKX DEX](https://hummingbot.org/exchanges/gateway/jupiter/#other-solana-routers) | AMM DEX | Router | `okx` | - |
| [Pacifica](https://hummingbot.org/exchanges/pacifica/) | CLOB CEX | Perpetual | `pacifica_perpetual` | - |
| [PancakeSwap](https://hummingbot.org/exchanges/gateway/pancakeswap/) | AMM DEX | AMM | `pancakeswap` | - |
| [Raydium](https://hummingbot.org/exchanges/gateway/raydium/) | AMM DEX | AMM, CLMM | `raydium` | - |
| [Titan](https://hummingbot.org/exchanges/gateway/jupiter/#other-solana-routers) | AMM DEX | Router | `titan` | - |
| [Uniswap](https://hummingbot.org/exchanges/gateway/uniswap/) | AMM DEX | Router, AMM, CLMM | `uniswap` | - |

## 其他 Hummingbot 仓库

* [Condor](https://github.com/hummingbot/condor)：用于构建和运行智能体策略及机器人实例的 AI 框架
* [Hummingbot API](https://github.com/hummingbot/hummingbot-api)：运行 Hummingbot 交易机器人的中枢
* [Gateway](https://github.com/hummingbot/gateway)：基于 TypeScript 的 DEX 连接器 API 客户端
* [Hummingbot Site](https://github.com/hummingbot/hummingbot-site)：Hummingbot 官方文档——也欢迎在这里贡献！

## 获取帮助

如果你遇到问题或有疑问，可以通过以下方式获得帮助：

* 查阅我们的[常见问题](https://hummingbot.org/faq/)、[故障排除指南](https://hummingbot.org/troubleshooting/)或[术语表](https://hummingbot.org/glossary/)
* 报告 bug 或建议新功能，请提交 [GitHub issue](https://github.com/hummingbot/hummingbot/issues)
* 加入我们的 [Discord 社区](https://discord.gg/hummingbot)，在 #support 频道提问

我们承诺不会将你提供的信息/数据用于交易目的，也不会与第三方共享。

## 参与贡献

Hummingbot 的架构由模块化组件构成，可由社区成员各自维护和扩展。

欢迎社区贡献！提交 pull request 之前，请先阅读这些[指南](./CONTRIBUTING.md)。

如果你代表某个希望拥有官方 Hummingbot 连接器的交易所，请参阅[如何添加 Hummingbot 连接器](https://hummingbot.org/exchanges/#how-to-add-a-hummingbot-connector)了解可用的集成方式。

## 法律声明

* **许可证**：Hummingbot 是开源软件，采用 [Apache 2.0](./LICENSE) 许可证。
* **数据收集**：关于 Hummingbot 中匿名数据收集和上报的信息，请参阅[上报](https://hummingbot.org/reporting/)。
