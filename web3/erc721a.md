# ERC721A

## 什么是 ERC721A

以太坊上的 gas费一直居高不下，当流行的 NFT 项目开始铸造时，gas 价格往往会飙升，产生「 gas war 」现象，这直接导致整个生态系统需支付数百万的 gas 费用进行交易。

基于此背景，Azuki的开发团队在原有的 [ERC-721 ](https://eips.ethereum.org/EIPS/eip-721)标准上创建和发布了 [ERC-721A](https://www.erc721a.org/) 标准。它可达到铸造多个 NFT 的成本与铸造单个 NFT 的成本基本相同，即做到在单笔交易中铸造多个 NFT 可显著节省 gas 费用。

同时，ERC-721A 与外部安全顾问合作，确保了该标准的方法是合理的，Azuki 社区鼓励更广泛的社区采用此标准来为其用户节省 gas 成本。

以下是一些已经实施或计划使用 ERC-721A 的社区：

{% hint style="info" %}
请注意，这并不代表 ERC-721A 对这些项目的认可。
{% endhint %}

* [@](https://twitter.com/goblintownwtf)[moonbirds](https://twitter.com/moonbirds)
* [@doodles (dooplicator)](https://opensea.io/collection/the-dooplicator)
* [@rtfkt (forged token)](https://opensea.io/collection/rtfkt-nike-ar-hoodie)
* [@goblintown](https://twitter.com/goblintownwtf)
* [@adidas (airdrop)](https://opensea.io/collection/adidascapsule)

## 成本测算

Azuki 通过最常用的 NFT 智能合约启动代码（[OpenZeppelin 的 ERC721Enumerable 合约](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#ERC721Enumerable)）批量铸造 NFT 与使用新的 Azuki ERC-721A 测量了铸造的 gas 成本和价格。

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

此表显示了用于 ERC-721A 的气体如何以更小的常数因子缩放越来越多的铸币厂。

铸造 NFT 的气体成本增加：

* 使用 Azuki ERC-721A 合约**每增加一个薄荷大约增加 2k gas 费用；**
* 使用 OpenZeppelin ERC721Enumerable 合约，**每个额外铸币厂约增加 115k gas 费用；**

对于通过 ERC-721 Enumerable 合约铸造一个代币的价格，用户可以通过 ERC-721A 合约铸造多达 5 个代币或更多（可能）。

{% hint style="info" %}
假设当前以太为 3500 美元，拿 300 gwei 来进行计算。（在撰写本文时目前为 120 gwei）。

则最终节省的 gas 费如下：
{% endhint %}

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## 它是如何工作的？

{% hint style="info" %}
以下部分假设您了解 Solidity 和 OpenZeppelin 的 ERC721 / ERC721Enumerable 实现。
{% endhint %}

Azuki的开发团队主要做了以下三个优化：

> **优化 1 - 从 OpenZeppelin (OZ) ERC721Enumerable 中删除重复存储**
>
> 广泛使用的 ERC721Enumerable 的 OZ 实现包括每个令牌元数据的冗余存储。这种非规范化方法以编写函数的显着成本优化了读取函数，这并不理想，因为用户不太可能为读取函数付费。此外，我们的令牌从 0 开始连续编号这一事实使我们能够从基本实现中删除一些冗余存储。我们强烈建议所有新推出的产品在寻求大获成功时仔细检查此文件。

> **优化 2 - 每个批量铸造请求更新一次所有者的余额，而不是每个铸造的 NFT**
>
> 假设爱丽丝有 2 个代币，想再买 5 个。在 Solidity 中，更新存储值需要消耗 gas。因此，如果我们在存储中跟踪爱丽丝拥有多少代币，那么通过一次更新将爱丽丝的持有量从 2 直接更新到 7 会更便宜，而不是更新该值 5 次（每个额外的代币一次，例如从 2 到 3, 3到 4，等等）。
>
> 虽然这是一个相对简单的概念，但 NFT 领域的绝大多数批量铸币厂还没有采用它，因为 OZ 默认实现不包括批量铸币 API，并且很容易在不调整的情况下直接使用现成的解决方案. 我们强烈建议所有支持批量铸币的项目都考虑这个技巧。

> **优化 3 - 每个批量铸造请求更新一次所有者数据，而不是每个铸造的 NFT**
>
> 这在本质上类似于优化 2。假设爱丽丝想要购买 3 个代币——代币 #100、#101 和 #102。与其将 Alice 作为所有者保存 3 次（每次都花费我们的 gas），不如只保存所有者值一次，在语义上暗示 Alice 拥有所有这 3 个代币。
>
> 假设 Alice 铸造了代币 #100、#101 和 #102，Bob 铸造了代币 #103 和 #104。内部所有者跟踪器如下所示：

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

> 这里的关键是，如果我们想看看谁拥有#102，那么实际上我们不需要将 Alice 明确设置为#102 的明确所有者。我们只需要更改 ownerOf 函数来执行以下操作：

<figure><img src="https://www.azuki.com/erc721a/code7.png" alt=""><figcaption></figcaption></figure>

> 关键见解：如果我们将其实现更改为递减直到找到明确的所有者集，ownerOf 仍会按预期工作。
>
> 虽然如果代币没有被 HODL 化，这些延迟的所有者写入可能仍会在代币生命周期的后期发生，但我们仍然期望从整体上节省大量净成本，因为这减少了铸币时的气体消耗，从而降低了集中气体的严重性，减少整个生态系统在薄荷时间出现峰值。这种优化涉及一些额外的逻辑，尤其是涉及到传输时，但超出了本博客的范围。

