# ETHEREUM PET SHOP

本文翻译至：[https://truffleframework.com/tutorials/pet-shop](https://truffleframework.com/tutorials/pet-shop) 版本为：2017-07-20

本教程将构建一个名为 “宠物商店追踪系统” 的 dapp

本教程需要对以太坊和智能合约有一定的基础，了解 Html 和 JavaScript

> 注意：关于以太坊基本内容，可以阅读 [Ethereum Overview](https://truffleframework.com/tutorials/ethereum-overview)

本教程将会覆盖：

1. 设置开发环境

2. 使用 Truffle Box 创建 Truffle 项目

3. 编写智能合约

4. 编译和移植智能合约

5. 测试智能合约

6. 创建与智能合约交互的用户界面

7. 在浏览器中与dapp交互

## 1. 背景

pete 的宠物商店想要用以太坊记录他们宠物的收养人。商店在一次可以容纳16只宠物，并且他们有一个宠物数据库。 pete 想要一个将已领养的宠物同以太坊地址联系起来的dapp。

网站结构和风格已经给出。我们的工作是编写智能合约和前端逻辑进行使用。

## 2. 设置开发环境

在开始之前需要一些技术依赖。请安装如下：

- [Node.js v6+ LTS and npm](https://nodejs.org/en/)

- [Git](https://git-scm.com/)

之后，输入命令安装Truffle：

```shell
npm install -g truffle
```

为了验证truffle是否正确安装，终端输入 `truffle version`。如果有错，请确保npm模块已添加至路径

我们也将使用 [Ganache](https://truffleframework.com/ganache) 。Ganache是用于以太坊开发的个人区块链，可以使用它部署合约、开发应用和运行测试。下载链接为 [http://truffleframework.com/ganache](http://truffleframework.com/ganache)

> 注意：如果你的开发环境中没有图形界面，你也可以使用Truffle Develop。Truffle内嵌有个人区块链。你需要修改一些设置，如区块链运行的端口，以适应Truffle Develop

## 3. 使用Truffle Box创建Truffle项目

1. 初始化当前目录。首先在你当前文件夹下创建一个目录，然后移动到该目录

  ```shell
  mkdir pet-shop-tutorial

  cd pet-shop-tutorial
  ```

2. 我们为了本教程已经创建了一个叫 `pet-shop` 的 [Truffle Box](https://truffleframework.com/boxes), 它包括基本的项目结构以及用户界面代码。使用 `truffle unbox` 命令解包 Truffle Box

```shell
truffle unbox pet-shop
```

> 注意：Truffle有几种不同的初始化方法。另外一种使用命令 `truffle init`，它将创建一个空的没有样例合约在内的 Truffle 项目。获取更多消息，请参考 [Creating a project](https://truffleframework.com/docs/getting_started/project)

### 3.1 目录结构

Truffle默认目录结构如下：

- `contracts/`：包含用于智能合约的 [Solidity](https://solidity.readthedocs.io/) 资源文件。其中有一个非常重要的合约 `Migrations.sol`，我们之后会谈论到

- `migrations/`：Truffle 使用移植系统来处理智能合约部署。移植是一个特殊的智能合约，用于追踪智能合约的变化

- `test/`：包含用于测试智能合约的 JavaScript 和 Solidity 文件

- `truffle-config.js`：Truffle 配置文件

`pet-shop` Truffle Box 中有其它的文件和文件夹，但是我们现阶段不用担心

## 4. 编写智能合约

我们将编写智能合约，用于扮演dapp的后端逻辑和存储

1. 在 `contracts/` 目录中创建 `Adoption.sol` 文件

2. 文件中添加如下内容：

```Solidity
pragma solidity ^0.5.0;

contract Adoption {

}
```

代码解读：

- `pragma solidity ^0.5.0;` 表示所需 Solidity 的最小版本。 `pragma` 指的是 *"additional information that only the compiler cares about"*，而符号 `^` 指的是 *"the version indicated or higher"*

- 类似 JavaScript 或者 PHP，以分号作为结尾

## 4.1 变量设置

Solidity 是静态类型语言，所以像 strings 、integers 和 arrays 等数据类型必须定义。 Solidity 有一个特殊的数据类型 **address** 。address 指的是以太坊地址，长度为20字节。以太坊区块链上的每一个帐号和智能合约都有一个地址，可以用来发送和接收以太。

1. 在 `contract Adoption {` 后一行添加如下变量

```solidity
address[16] public adopters;
```

代码解读：

- 我们定义单个变量：adopters。这是一个以太坊地址数组。数组类型 `address` ，长度 `16` ，

- 你可以发现 `adopters` 有 public属性 。 **Public** 属性对应的变量有自带的 getter 方法，但是在数组的情况下需要传入一个关键值(数组索引下标)，并且只能返回一个值。我们将写一个函数用于返回整个数组，以便在 UI 中使用。

## 4.2 第一个函数：Adopting a pet

用户可以提出收养宠物的请求

1. 在变量声明的下面添加如下函数

```solidity
// Adopting a pet
function adopt(uint petId) public returns (uint) {
  require(petId >= 0 && petId <= 15);

  adopters[petId] = msg.sender;

  return petId;
}
```

代码解读：

- 在 Solidity 中，函数参数和输出参数的类型都必须事先被指定。本例中，我们传入一个整型参数 `petId` ，并且返回一个整型数字。

- 我们需要确保 `petId` 在我们 `adopters` 数组的范围内。Solidity 中，数组索引从 0 开始，所以 ID 值为 0 — 15 。我们使用 `require()` 确保 `ID` 在范围内。

- 如果 `ID` 在范围内，我们使用 `adopters` 数组。调用当前函数的个人或者智能合约的地址将被作为 `msg.sender` 保存。

- 最后，我们返回 `petId` 作为确认。

## 4.3 第二个函数：Retrieving the adopters

从 4.1 部分我们可知，数组的 getters 方法将通过给定的数组下标返回数组的单个值。我们 UI 需要更新所有的宠物收养状态，但是进行 16 次 API 调用又不理想。所以我们下一步是编写返回整个数组的函数

1. 在 `adopt()` 方法后添加 `getAdopters` 函数

```solidity
// Retrieving the adopters
function getAdopters() public view returns (address[16] memory) {
  return adopters;
}
```

2. 因为 `adopters` 已经声明过，所以我们可以直接返回它。注意明确返回类型为 `address[16] memory` 。`memory` 指定变量的数据位置。

3. 函数中 `view` 关键字表明该函数不会修改当前合约的状态。关于 `view` 更多的详细信息可以参考 [此处](https://solidity.readthedocs.io/en/latest/contracts.html#view-functions)

## 5. 编译和移植智能合约

当前我们已经编写完智能合约，下一步将对其进行编译和移植

Truffle 有一个叫 Truffle Develop 的内嵌式开发者控制台 (developer console) ，它可以生成一条用于开发的区块链，我们使用它进行测试和部署合约。它支持在控制台直接运行 Truffle 命令。本教程中，我们将使用 Truffle Develop 在合约上执行操作。

### 5.1 编译

Solidity 是一门编译型语言，意味着我们需要编译 Solidity 为字节码以供以太坊虚拟机执行 (EVM)。可以把它想象成将人类可读的 Solidity 翻译成 EVM 理解的东西

1. 打开终端，并确保当前路径为 dapp 项目根路径：

```shell
truffle compile
```

> 注意：如果你在 Windows 中运行命令时出现问题，请查看文档 [resolving naming conflicts on Windows](https://truffleframework.com/docs/advanced/configuration#resolving-naming-conflicts-on-windows)

你应该会看到和下面相似的输出：

```shell
Compiling ./contracts/Migrations.sol...
Compiling ./contracts/Adoption.sol...
Writing artifacts to ./build/contracts
```

### 5.2 移植

现在我们已经成功的编译了合约，是时候将它们移植至区块链了

**移植是一个部署脚本，用于更改应用合约的状态**，将合约从一个状态移动至另一个状态。对于第一次移植，你或许只是部署新代码，但是随着时间的推移，其它移植可能会移动数据或用新代码替换合约

> 注意：阅读更多有关移植 [Truffle documentation](https://truffleframework.com/docs/getting_started/migrations)

当前 `migrations/` 目录中已经有一个 JavaScript 文件：`1_initial_migration.js`。它用于部署 `Migrations.sol` 合约来观察随后其它智能合约的部署，并且确保未来我们不会二次移植无变化修改的合约。

现在我们准备创建我们自己的移植脚本。

1. 在 `migtations/` 目录中创建 `2_deploy_contracts.js` 文件

2. 在 `2_deploy_contracts.js` 文件中添加如下内容：

  ```javascript
  var Adoption = artifacts.require("Adoption");

  module.exports = function(deployer) {
    deployer.deploy(Adoption);
  };
  ```

3. 在我们移植合约至区块链之前，我们需要运行一个区块链。本教程我们会使用 [Ganache](https://truffleframework.com/ganache)，一个用于以太坊开发的个人区块链，利用它部署合约，开发应用和运行测试。如果你还没有安装，可以 [下载](https://truffleframework.com/ganache)，然后双击图标运行软件。它将生成区块链，并运行在本地 7545 端口。

> 注意：阅读更多关于 Ganache [Truffle documentation](https://truffleframework.com/docs/ganache/using)

第一次运行 Ganache 如下图：

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzme31nhn7j31580khtgf.jpg)

4. 返回终端，移植合约至区块链

```shell
truffle migrate
```

你应该看到如下相似输出：

```shell
1_initial_migration.js
  ======================

     Deploying 'Migrations'
     ----------------------
     > transaction hash:    0x3b558e9cdf1231d8ffb3445cb2f9fb01de9d0363e0b97a17f9517da318c2e5af
     > Blocks: 0            Seconds: 0
     > contract address:    0x5ccb4dc04600cffA8a67197d5b644ae71856aEE4
     > account:             0x8d9606F90B6CA5D856A9f0867a82a645e2DfFf37
     > balance:             99.99430184
     > gas used:            284908
     > gas price:           20 gwei
     > value sent:          0 ETH
     > total cost:          0.00569816 ETH


     > Saving migration to chain.
     > Saving artifacts
     -------------------------------------
     > Total cost:          0.00569816 ETH


  2_deploy_contracts.js
  =====================

     Deploying 'Adoption'
     .............................
     .............................
```

你可以看到移植按顺序被执行，以及每一个移植的信息

5. 在 Ganache中，区块链的状态已经改变。现在区块链显示当前区块是 `4` ，但之前是 `0`。此外，第一个账户初始化时有 100 个 ether，但是现在只有 99.99 。因为移植需要开销。我们将在之后谈论更多关于交易开销。

移植之后的 Ganache

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzmelqmzozj30x40cwgnx.jpg)

当前，你已经编写了第一个智能合约，并且将其部署至本地运行的区块链。现在是时候与智能合约进行交互以确保它是我们所想要的。

## 6. 测试智能合约

Truffle 可以非常灵活的进行智能合约测试，支持 JavaScript 和 Solidity 。本教程，我们使用 Solidity 编写测试。

1. 在 `test/` 目录中创建 `TestAdoption.sol` 文件。

2. 在 `TestAdoption.sol` 文件中添加如下内容：

```solidity
pragma solidity ^0.5.0;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/Adoption.sol";

contract TestAdoption {
 // The address of the adoption contract to be tested
 Adoption adoption = Adoption(DeployedAddresses.Adoption());

 // The id of the pet that will be used for testing
 uint expectedPetId = 8;

 //The expected owner of adopted pet is this contract
 address expectedAdopter = address(this);

}
```

我们用三个 import 开始合约：

- `Assert.sol` ：提供各种测试断言。在测试中，**断言检查诸如相等、不相等或空来返回通过/失败**。 [这里](https://github.com/trufflesuite/truffle/tree/master/packages/truffle-core/lib/testing/Assert.sol) 是 Truffle 中包含的断言完整列表。

- `DeployedAddresses.sol` ：当运行测试时，Truffle 将在被测试的区块链中部署新的合约实例。智能合约获得部署合约的地址。

- `Adoption.sol` ：我们想测试的智能合约

> 注意：前两个输入是引入的全局 Truflle 文件，不是 `truffle` 目录。 `test/` 目录中没有 `truffle` 目录。

然后我们定义三个合约范围的变量：

- 首先，一个包含要测试的智能合约，调用 DeployedAddresses 智能合约来获取其地址

- 第二，设置测试 adoption 函数的宠物 ID

- 第三，由于 TestAdoption 合约将发送交易，我们设置 expectedAdoption (预期的收养人) 地址为 **This** (就是 TestAdoption 合约部署时分配的地址)，一个合约范围的变量获取当前合约的地址

### 6.1 测试 adopt() 函数

为了测试 `adopt()` 函数，我们需要知道的是一旦它执行成功，它将返回给定的 `petId` 。我们可以比较 `adopt()` 方法的返回值与我们传入的 ID 值是否相等

1. 在 `TestAdoption.sol` 智能合约的 `adoption` 变量声明之后添加如下函数

```solidity
// Testing the adopt() function
function testUserCanAdoptPet() public {
  uint returnedId = adoption.adopt(expectedPetId);

  Assert.equal(returnedId, expectedPetId, "Adoption of the expected pet should match what is returned.");
}
```

代码讲解：

- 我们用 `expectedPetId` 调用函数

- 最后，我们传递真实值、预期值和一条失败信息至 `Assert.equal()`。其中失败信息是当真实值和预期值不相等时输出到控制台的消息。

### 6.2 测试检索单个宠物主人

之前我们说过 public 变量有自带的 getter 方法，我们可以检索 6.1 中 adoption 测试所存储的地址。在测试阶段已存储的数据都是有效的，所以宠物 `expectedPetId` 的收养数据可用于其它测试。

1. `TestAdoption.sol` 文件中，在 6.1 添加的函数后面添加如下函数

```solidity
// Testing retrieval of a single pet's owner
function testGetAdopterAddressByPetId() public {
  address adopter = adoption.adopters(expectedPetId);

  Assert.equal(adopter, expectedAdopter, "Owner of the expected pet should be this contract");
}
```

获得 adoption 合约中存储的收养人地址后，我们按照 6.1 中同样的方法进行相等断言测试

### 6.3 测试检索所有宠物主人

因为数组可以由给定的单个键返回单个值，所以我们创建了获得整个数组的getter方法

1. 在 6.2 添加的函数后面写入下面函数

```solidity
// Testing retrieval of all pet owners
function testGetAdopterAddressByPetIdInArray() public {
  // Store adopters in memory rather than contract's storage
  address[16] memory adopters = adoption.getAdopters();

  Assert.equal(adopters[expectedPetId], expectedAdopter, "Owner of the expected pet should be this contract");
}
```

注意 `adopters` 变量的 **memory** 属性。 memory 属性告诉 Solidity 将值暂时存储在内存中，而不是将它保存至合约的存储中。因为 `adopters` 是一个数组，我们知道宠物 `expectedPetId` 的收养人地址，所以我们用宠物 `expectedPetId` 的收养人地址和数组中对应 `expectedPetId` 下标的值进行对比。

### 6.4 运行测试

1. 回到终端，运行测试：

```shell
truffle test
```

2. 如果所有测试通过，你将看到终端输出如下相似内容：

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzmirz9nnjj30dm06m3yt.jpg)

此时 `ganache` 的相关状态变为：

![3](http://ww1.sinaimg.cn/large/006alGmrgy1fzmittfesij30ww0cracd.jpg)

## 7. 创建与智能合约交互的用户界面

现在我们已经创建了智能合约，并在本地的测试链上进行了部署，而且通过控制台验证了可以和它进行交互，是时候创建一个 UI ，以便于 Pete 来使用他的宠物商店。

pet-shop Truffle Box 包含应用程序的前端代码。代码在 `src/` 目录下

前端无法使用编译系统 (webpack，grunt 等) 来尽可能简单的启动。应用程序的结构已经存在，我们将补充特定于以太坊的函数。这样，你就可以掌握这些知识并将其应用到你自己的前端开发中。

### 7.1 实例化web3

1. 在文本编辑器中打开 `/src/js/app.js`

2. 检查该文件。注意这里有一个全局 `App` 对象管理我们的应用，在 `init()` 中加载 pet 数据，然后调用函数 `initweb3()`。[web3 JavaScript library](https://github.com/ethereum/web3.js/) 同以太坊区块链进行交互。它可以检索用户账户、发送交易、同智能合约交互以及其它。

3. 从 `initweb3` 中移除多行内容，并且替换成如下：

```js
// Modern dapp browsers...
if (window.ethereum) {
  App.web3Provider = window.ethereum;
  try {
    // Request account access
    await window.ethereum.enable();
  } catch (error) {
    // User denied account access...
    console.error("User denied account access")
  }
}
// Legacy dapp browsers...
else if (window.web3) {
  App.web3Provider = window.web3.currentProvider;
}
// If no injected web3 instance is detected, fall back to Ganache
else {
  App.web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
}
web3 = new Web3(App.web3Provider);
```

代码解读：

- 首先，我们检查是否使用新版的 dapp 浏览器或 [MetaMask](https://github.com/MetaMask)，其中将 `ethereum` Provider 注入至 `window` 对象。如果是的话，我们使用它来创建我们 web3 对象，此外我们也需要 `ethereum.enable()` 显式请求访问帐户

- 如果 `ethereum` 对象不存在，我们将检查注入的 `web3` 实例。如果存在，则表示我们正在使用老版本的dapp 浏览器 (如 [Mist](https://github.com/ethereum/mist) 或 MetaMask 的老版本)。如果是这样的话，我们获得它的 provoder 并且使用它创建我们的 web3 对象。

- 如果没有注入的 web3 实例，我们可以基于本地的 provider 创建 web3 对象。(这种备用方案使用于开发环境，但不安全且不适合生产)

### 7.2 实例化合约

现在我们可以通过 web3 和以太坊交互，我们需要实例化智能合约以便于 web3 可以发现和调用它。Truffle 有一个 `truffle-contract` 可以帮助做到这一步。它使合同信息与移植保持同步，因此您无需手动更改合同的部署地址。

1. `/src/js/app.js` 文件，移除 `initContract` 中的多行内容并且替换成如下内容：

```js
$.getJSON('Adoption.json', function(data) {
  // Get the necessary contract artifact file and instantiate it with truffle-contract
  var AdoptionArtifact = data;
  App.contracts.Adoption = TruffleContract(AdoptionArtifact);

  // Set the provider for our contract
  App.contracts.Adoption.setProvider(App.web3Provider);

  // Use our contract to retrieve and mark the adopted pets
  return App.markAdopted();
});
```

代码解读：

- 首先为智能合约检索生成文件。**生成文件有大量关于合约的信息，如部署地址和程序应用二进制接口(ABI)。 ABI 是一个 JavaScript 对象，它定义如何同合约进行交互，包括合约变量、函数和相关参数**

- 一旦我们回调中有生成文件，我们就将它传递给 `TruffleContract()` 函数。这会创建一个我们可以与之交互的合约实例。

- 合约实例化之后，我们使用 `App.web3Provider` 值设置它的 web3 provider。

- 然后调用应用程序的 `markAdopted()` 函数，以防宠物在之前已被领养。我们已经将它封装为一个独立的函数，因为我们需要在修改智能约合数据后更新 UI 。

### 7.3 获取已领养宠物，更新 UI

1. `/src/js/app.js/` 文件，移除 `markAdopted` 文件中多行内容，替换成如下：

```js
var adoptionInstance;

App.contracts.Adoption.deployed().then(function(instance) {
  adoptionInstance = instance;

  return adoptionInstance.getAdopters.call();
}).then(function(adopters) {
  for (i = 0; i < adopters.length; i++) {
    if (adopters[i] !== '0x0000000000000000000000000000000000000000') {
      $('.panel-pet').eq(i).find('button').text('Success').attr('disabled', true);
    }
  }
}).catch(function(err) {
  console.log(err.message);
});
```

代码解读：

- 我们访问已部署的 `Adoption` 合约，然后在这个实例上调用 `getAdopters()` 方法

- 首先我们在智能合约调用之外声明变量 `adoptionInstance` 变量。以便在最初检索它之后可以访问该实例

- call() 方法允许不用发送完整交易就可以从区块链上读取数据，意味着我们不需要花费任何 ether

- 调用 `getAdopters()` 之后，我们循环遍历 adopters ，检查每个宠物是否已被领养。因为数组是 address 类型，以太坊初始化数组为16个空的 addresses。这就是为什么我们检查空地址串而不是 null 或其它 falsey 值

- 一旦发现 `petId` 对应的地址不为空地址，我们就 disable 它的收养按钮，并且修改按钮 text 为 "Success" ，以便于用户得到反馈

- 出现的任何错误会显示在控制台

### 7.4 处理 adopt() 函数

1. `/src/js/app.js/` 文件，移除 `handleAdopt` 文件中多行内容，替换成如下：

```js
var adoptionInstance;

web3.eth.getAccounts(function(error, accounts) {
  if (error) {
    console.log(error);
  }

  var account = accounts[0];

  App.contracts.Adoption.deployed().then(function(instance) {
    adoptionInstance = instance;

    // Execute adopt as a transaction by sending account
    return adoptionInstance.adopt(petId, {from: account});
  }).then(function(result) {
    return App.markAdopted();
  }).catch(function(err) {
    console.log(err.message);
  });
});
```

代码解读：

- 我们使用 web3 获取用户账号。在错误检测之后，我们选择账户的第一个账号

- 这里，我们像之前一样获取已部署的合约，并且将实例存储为 `adoptionInstance`。接下来，我们将发送一个交易而不是调用。交易需要一个 from" 地址，并且需要 cost 。 cost 就是指用 ether 交付，称作为 gas 。 gas 花费是在智能合约上执行计算或存储数据所需的费用。我们通过执行 `adopt()` 函数发送交易，该函数需要 宠物 ID 和包含账号地址的对象，这个对象就是 `account`。

- 发送交易的结果是交易对象。如果没有错误，我们会调用 `markAdopted()` 函数将最新的存储数据同步至 UI

## 8. 在浏览器中和 dapp 交互

现在我们准备使用 dapp

### 8.1 安装和配置 MetaMask

浏览器中和 dapp 交互最简单的方式是通过 [MetaMask](https://metamask.io/)，适用于 Chrome 和 Firefox 的扩展插件

1. 在浏览器中安装 MetaMask

2. 安好后，你可以在地址栏看见 MetaMask 狐狸图标。点击图标，出现如下：

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzn73x330hj30es0kugnt.jpg)

3. 点击 accept

4. 然后你将看见使用细则。阅读之后，滚动至底端，然后点击 Accept

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzn766xw8rj30eq0ktabp.jpg)

5. 现在你将看见最初的 MetaMask 。点击 Import Existing DEN

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzn77x42whj30eq0kpab8.jpg)

6. 在方框中标记 Wallet Seed，输入 Ganache 中出现的助记符

助记符如下：

![3](http://ww1.sinaimg.cn/large/006alGmrgy1fznjkn2rftj30s90dmdmt.jpg)

> 警告：不要在 main Ethreum network 上使用此助记符。如果你发送 ETH 给任何由此助记符生成的账号，你将会失去它。

在下面输入密码，点击 OK

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzn7de8dqtj30em0kf75j.jpg)

7. 现在我们需要将 MetaMask 链接至由 Ganache 生成的区块链上。点击显示 "Main Network" 的菜单，选择 Custom RPC

![2](http://ww1.sinaimg.cn/large/006alGmrgy1fzn7g2tpu0j30em0kdwh5.jpg)

8. 在标题为 “New RPC URL” 的框中输入 `http://127.0.0.1:7545` 并单击 Sava

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzn7kmb8s5j30es0krgoa.jpg)

顶部的网络名将转换为 "Private Network"

9. 点击 "Setting" 旁边的向左箭头关闭当前页面，返回到 Accounts 页面

每一个由 Ganache 创建的账号都有 100 ether。 你会发现第一个账号略微少于 100 ，因为当合约部署和测试时消耗了一部分 gas

![3](http://ww1.sinaimg.cn/large/006alGmrgy1fznjmq9mdkj309q0gbaay.jpg)

配置完成

### 8.3 安装和配置 lite-server

我们现在可以启动本地 web 服务器，使用 dapp。 我们将使用 `lite-server` 包来服务我们的静态文件。

1. 在文本编辑器中打开 `bs-config.json` ，检查内容：

```json
{
  "server": {
    "baseDir": ["./src", "./build/contracts"]
  }
}
```

这告诉 `lite-server` 那些文件包含在我们基目录中。我们为我们网站文件添加 `./src` 目录，为合约文件添加 `./build/contracts` 目录

我们也在 `package.json` 文件中对 `script` 对象添加了 `dev` 命令。`script` 对象允许我们将控制台命令别名为单个 npm 命令。在这种情况下，我们只是执行一个命令，但可能有更复杂的配置。 你应该看到如下：

```json
"scripts": {
  "dev": "lite-server",
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

这告诉 npm 运行我们本地安装的 `lite-server` ，当我们从终端执行 `npm run dev` 时

### 8.4 使用 dapp

1. 启动本地 web 服务：

```shell
npm run dev
```

dev 服务器将运行，自动打开包含 dapp 的新浏览器页面

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzn9kespdij30l30iogur.jpg)

2. 使用 dapp，点击你所选择宠物对应的 Adopt 按钮

3. MetaMask 将会自动提示你批准该交易。点击 Submit 批准该交易

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzn9o70yj7j30eq0kkju9.jpg)

4. 你将发现之前的 adopted 按钮变成了 "Success"，并且无法继续点击，因为该宠物已经被领养。

![3](http://ww1.sinaimg.cn/large/006alGmrly1fzn9q9jwwhj30dq0hon1n.jpg)

> 注意：如果按钮没有自动变为 "Success" ，请刷新浏览器

在 MetaMask 中，你将发现交易列表：

![2](http://ww1.sinaimg.cn/large/006alGmrly1fzn9rweajpj30eq0km3zt.jpg)

你也可以发现 Ganache 中有同样的交易列表

恭喜！你在成为全栈 dapp 开发师的路上迈出了一大步。对于本地开发，您拥有开始制作更高级 dapp 所需的所有工具。如果您想让其他人使用 dapp ，请继续关注我们将来部署到 Ropsten testnet 的教程。