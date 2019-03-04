# GETTING STARTED WITH DRIZZLE AND REACT

本文翻译至：[https://truffleframework.com/tutorials/getting-started-with-drizzle-and-react](https://truffleframework.com/tutorials/getting-started-with-drizzle-and-react) 版本为：2018-08-28

Drizzle 是 Truffle Suite 的最新成员，也是我们的第一个前端开发工具。 Drizzle 的核心是将合约数据和交易数据等内容从区块链同步至 Redux store。 `drizzle` 基础库顶部有更高级别的抽象; 用于React兼容性 （[drizzle-react](https://truffleframework.com/docs/drizzle/react/react-integration)） 和一组即用型 React 组件 （[drizzle-react-components](https://truffleframework.com/docs/drizzle/react/react-components)） 的工具。

今天我们专注于初级应用，带你从头开始用 React 和 Drizzle 设置 Truffle 项目。通过这种方式，我们可以更好地了解 Drizzle 在应用中如何使用。有了这些知识，您可以利用您选择的任何前端框架充分利用 Drizzle，或者放心使用更高级别的React抽象

这将是一个非常初级的教程，专注于设置和获取存储在合约中的简单字符串。它适用于具有 Truffle 基础知识的人，此外还需要对 JavaScript 和 React.js 有一定的了解，但是不太熟悉 Drizzle 。

> 注意：了解 Truffle 基础，可以参考 [Pet Shop](https://truffleframework.com/tutorials/pet-shop) 教程

本教程将覆盖：

1. 设置开发环境

2. 从头开始创建 Truffle 项目

3. 编写智能合约

4. 编译和移植智能合约

5. 测试智能合约

6. 创建 React.js 项目

7. 设置前端客户端

8. 用 Drizzle 连接 React 应用程序

9. 写一个从 Drizzle 中读取的组件

10. 写一个写入智能合约的组件

## 1. 设置开发环境

开始之前有一些技术要求。请安装以下内容：

- [Node.js v8+ LTS and npm](https://nodejs.org/en/) (comes with Node)

### 1.1 Truffle

安装如上内容后，安装 Truffle：

```shell
npm install -g truffle
```

验证 Truffle 是否已正确安装，在终端上输入 `truffle version` 。如果发现错误，请检查是否已将npm模块添加到路径中。

### 1.2 Ganache-CLI

我们还将使用 Ganache-CLI ，这是一个用于以太坊开发的个人区块链，可用于部署合约、开发应用程序和运行测试。 可以使用以下命令全局安装：

```shell
npm install -g ganache-cli
```

这里如果出现非全局安装问题，可使用 `ln -s` 将 ganache-cli 链接至 `/usr/local/bin/` 。详细使用方法请 google

### 1.3 Create-React-App

最后，由于这是一个 React.js 教程，我们将使用 [Create-React-App](https://github.com/facebook/create-react-app/) 创建我们的React项目

如果你拥有 NPM 5.2 或更高版本，则无需执行任何操作。你可以通过运行 `npm --version` 来检查 `NPM` 版本。如果没有，则需要使用以下命令全局安装该工具：

```shell
npm install -g create-react-app
```

## 2. 创建 Truffle 项目

1. Truffle 在当前目录中初始化，因此首先在你选择的开发文件夹中创建一个目录，然后移动至目录

    ```shell
    mkdir drizzle-react-tutorial
    cd drizzle-react-tutorial
    ```

2. 运行如下命令生成空 Truffle 项目

    ```shell
    truffle init
    ```

简单看一下生成的目录结构

### 2.1 目录结构

默认的目录结构包含如下：

- `contracts/` : 包含我们智能合约的 [Solidity](https://solidity.readthedocs.io/) 源文件。 这里有一个名为 `Migrations.sol` 的重要合约，我们稍后会讨论。

- `migrations/` ：`Truffle` 使用移植系统来处理智能合约部署。 移植是一种特殊的智能合约，可以跟踪合约变化。

- `test/` ：包含智能合约 JavaScript 和 Solidity 测试

- `truffle-config.js` ：Truffle 配置文件

## 3. 编写智能合约

我们将添加一个名为 MyStringStore 的简单智能合约。

1. 在 `contracts/` 目录中创建名为 `MyStringStore.sol` 的新文件。

2. 文件中添加如下内容：

    ```solidity
    pragma solidity ^0.5.0;

    contract MyStringStore {
    string public myString = "Hello World";

    function set(string memory x) public {
        myString = x;
    }
    }
    ```

由于这不是Solidity教程，所以您需要了解的是：

- 我们创建了一个名为 `myString` 的公共字符串变量，并将其初始化为 “Hello World” 。 这会自动创建一个 getter方法（因为它是一个公共变量），所以我们不必自己编写一个。

- 我们创建了一个 setter 方法，将 `myString` 变量设置为传入的任何字符串。

**注意：**

这里需要在 set 函数传入参数 x 中添加 memory 属性，不然可能会在 4.1 编译部分出现如下错误（应该是 solidity 版本的问题）：

![3](http://ww1.sinaimg.cn/large/006alGmrgy1fzojk8nckuj30k405maaq.jpg)

### 3.1 运行测试链

在我们继续操作之前，让我们首先使用 Ganache-CLI 启动我们的测试区块链。

打开一个新终端并运行以下命令：

```shell
ganache-cli -b 3
```

这将生成一个新的区块链，默认情况下会侦听127.0.0.1:8545，并将每3秒进行一次挖矿。 如果我们没有指定这个，Ganache会立即挖矿，我们将无法模拟真正的区块链开采所需的延迟。

保持终端窗口打开，您可以观察稍后与之交互时发生的情况

### 3.2 指定网络

我们需要让我们的 Truffle 项目知道如何连接到这个区块链。 要做到这一点，我们需要将以下内容放在 `truffle-config.js中` ：

```js
module.exports = {
  networks: {
    development: {
      host: "localhost",
      port: 8545,
      network_id: "*" // Match any network id
    }
  }
};
```

## 4. 编译和移植智能合约

现在我们准备编译和移植合约

### 4.1 编译

1. 在终端中，确保您位于项目目录的根目录中并输入：

    ```shell
    truffle compile
    ```

> 如果您使用的是 Windows 并且在运行此命令时遇到问题，请参阅文档 [resolving naming conflicts on Windows](https://truffleframework.com/docs/advanced/configuration#resolving-naming-conflicts-on-windows)

您应该看到类似于以下输出内容：

```shell
Compiling ./contracts/Migrations.sol...
Compiling ./contracts/MyStringStore.sol...
Writing artifacts to ./build/contracts
```

### 4.2 移植

现在我们已经成功编译了合约，是时候将它们移植到区块链了！

> 注意：阅读更多关于移植 [Truffle documentation](https://truffleframework.com/docs/getting_started/migrations)

创建移植脚本

1. 在 `migrations/` 目录中创建名为 `2_deploy_contracts.js` 的新文件

2. 文件中添加如下内容：

    ```js
    const MyStringStore = artifacts.require("MyStringStore");

    module.exports = function(deployer) {
    deployer.deploy(MyStringStore);
    };
    ```

3. 返回终端，移植合约到区块链

    ```shell
    truffle migrate
    ```

你可以看到如下类似输出：

```shell
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xcc1a5aea7c0a8257ba3ae366b83af2d257d73a5772e84393b0576065bf24aedf
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying MyStringStore...
  ... 0x43b6a6888c90c38568d4f9ea494b9e2a22f55e506a8197938fb1bb6e5eaa5d34
  MyStringStore: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
Saving successful migration to network...
  ... 0xf36163615f41ef7ed8f4a8f192149a0bf633fe1a2398ce001bf44c43dc7bdda0
Saving artifacts...
```

您可以按顺序查看正在执行的移植，然后是每个已部署合同的区块链地址（您的地址将有所不同）。

## 5. 测试智能合约

在我们继续之前，我们应该编写几个测试来确保合约按预期工作。

1. 在 `test/` 目录中创建一个名为 `MyStringStore.js` 的新文件。

2. 将以下内容添加到 `MyStringStore.js` 文件：

    ```js
    const MyStringStore = artifacts.require("./MyStringStore.sol");

    contract("MyStringStore", accounts => {
    it("should store the string 'Hey there!'", async () => {
        const myStringStore = await MyStringStore.deployed();

        // Set myString to "Hey there!"
        await myStringStore.set("Hey there!", { from: accounts[0] });

        // Get myString from public variable getter
        const storedString = await myStringStore.myString.call();

        assert.equal(storedString, "Hey there!", "The string was not stored");
    });
    });
    ```

### 5.1 运行测试

1. 返回终端，运行测试：

    ```shell
    truffle test
    ```
2. 如果所有测试都通过，您将在控制台看到与此类似的输出：

    ```shell
    Using network 'development'.

    Contract: MyStringStore
        ✓ should store the value 'Hey there!' (3085ms)

    1 passing (3s)
    ```

真棒！ 现在我们知道合同确实有效。

## 6. 创建 React.js 项目

现在我们完成了智能合约，我们可以用 React.js 编写我们的前端客户端！ 为此，只需运行此命令（如果您有NPM 5.2或更高版本）：

```shell
npx create-react-app client
```

如果您有较旧版本的 NPM ，请确保按照 [Setting up the development environment](https://truffleframework.com/tutorials/getting-started-with-drizzle-and-react#create-react-app) 部分中的说明全局安装 Create-React-App ，然后运行以下命令：

```shell
create-react-app client
```

这应该在您的 Truffle 项目中创建一个 `client` 目录，并生成一个裸的  React.js 项目，让你开始构建您的前端。

## 7. 设置前端客户端

现在我们在 `client` 目录中有一个前端客户端，使用命令 `cd client` 切换到该目录，然后继续执行以下步骤进行设置。

### 7.1 链接编译文件

由于 Create-React-App 默认不允许从 `src` 文件夹外部导入文件，因此我们需要将我们 `build` 文件夹中的 contracts 放入 `src` 中。 我们可以在每次编译合约时复制和粘贴一次，但更好的方法是创建符号链接。

如果你之前没有创建过符号链接，请将其视为文件系统中的一个 magical portal 

切换到 `src` 目录，然后创建符号链接文件夹：

```shell
// For MacOS and Linux

cd src
ln -s ../../build/contracts contracts

// For Windows 7, 8 and 10
// Using a Command Prompt as Admin

cd src
mklink /D contracts ..\..\build\contracts
```

实际上，这应该在 `src` 中创建看起来像 `contract` 文件夹的内容，但它实际上指向我们的 Truffle 项目的 `build/contracts` 文件夹中的文件。

### 7.2 安装 Drizzle

这是最有趣的部分，我们安装 Drizzle。 使用命令 `cd ..` 更改回 `client` 目录，然后运行以下命令：

```shell
npm install drizzle
```

这就是依赖关系！ 请注意，我们不需要自己安装 Web3.js 或 Truffle-Contract 。 Drizzle 包含我们与智能合约交互所需的一切。

## 8. 用 Drizzle 连接 React 应用程序

在我们进一步讨论之前，让我们通过在 `client` 目录中运行如下命令来启动我们的 React 应用程序：

```shell
npm start
```

这将在 `localhost：3000` 下启动前端服务，因此请在浏览器中打开它

> 注意：如果已经安装了 MetaMask，请确保使用隐身窗口（或暂时禁用 MetaMask ）。 否则，应用程序将尝试使用 MetaMask 中指定的网络，而不是 localhost：8545 下的开发网络。

如果加载的默认 Create-React-App 页面没有任何问题，你可以继续。

### 8.1 设置商店

我们需要做的第一件事是设置和实例化Drizzle商店。我们把以下5行添加到 `client/src/index.js` ：

```js
// import drizzle functions and contract artifact
import { Drizzle, generateStore } from "drizzle";
import MyStringStore from "./contracts/MyStringStore.json";

// let drizzle know what contracts we want
const options = { contracts: [MyStringStore] };

// setup the drizzle store and drizzle
const drizzleStore = generateStore(options);
const drizzle = new Drizzle(options, drizzleStore);
```

首先，我们从 Drizzle 和软连接的合约定义中导入工具

然后我们为 Drizzle 构建了我们的选项对象，在这种情况下，它只是通过传入 JSON 构建工件来指定我们想要加载的特定合约。

最后，我们创建了 `drizzleStore` 并使用它来创建我们的 `drizzle` 实例，我们将其作为支持传递给我们的 `App` 组件。

完成后，`index.js` 应如下所示：

```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import registerServiceWorker from "./registerServiceWorker";

// import drizzle functions and contract artifact
import { Drizzle, generateStore } from "drizzle";
import MyStringStore from "./contracts/MyStringStore.json";

// let drizzle know what contracts we want
const options = { contracts: [MyStringStore] };

// setup the drizzle store and drizzle
const drizzleStore = generateStore(options);
const drizzle = new Drizzle(options, drizzleStore);

// pass in the drizzle instance
ReactDOM.render(<App drizzle={drizzle} />, document.getElementById("root"));
registerServiceWorker();
```

再次注意， `drizzle` 实例作为 `props` 传递到 `App` 组件。

### 8.2 连接 App 组件

现在我们有一个 `drizzle` 实例，我们可以进入 `client/src/App.js` 开始使用 React API。

#### 8.2.1 添加状态变量

我们要做的第一件事是在我们的 App 组件中添加以下行

```js
state = { loading: true, drizzleState: null };
```

我们将在这里使用两个状态变量：

1. `loading` — 指示 Drizzle 是否已完成初始化并且应用程序已准备就绪。 初始化过程包括实例化 `web3` 和我们的智能合约，获取任何可用的以太坊帐户并监听（或者，在不支持订阅的情况下：轮询）新块。

2. `drizzleState` — 这是我们将 Drizzle 商店的状态存储在顶级组件中的位置。 如果我们可以保持这个状态变量是最新的，那么我们可以简单地使用简单的 `props` 和 `state` 来使用 Drizzle（即你不必使用任何 Redux 或高级 React 模式）。

#### 8.2.2 添加一些初始化逻辑

接下来，我们将 `componentDidMount` 方法添加到组件类中，以便我们可以运行一些初始化逻辑。

```js
componentDidMount() {
  const { drizzle } = this.props;

  // subscribe to changes in the store
  this.unsubscribe = drizzle.store.subscribe(() => {

    // every time the store updates, grab the state from drizzle
    const drizzleState = drizzle.store.getState();

    // check to see if it's ready, if so, update local component state
    if (drizzleState.drizzleStatus.initialized) {
      this.setState({ loading: false, drizzleState });
    }
  });
}
```

首先，我们从 props 中获取 `drizzle` 实例，然后我们调用`drizzle.store.subscribe` 并传入一个回调函数。 每当更新 Drizzle 存储时，都会调用此回调函数。 请注意，这个商店实际上是一个 Redux 商店，所以如果您之前使用过 Redux，这可能看起来很熟悉。

每当更新商店时，我们将尝试使用 `drizzle.store.getState（）` 获取状态，然后如果初始化并准备好 Drizzle，我们将 `loading` 设置为false，并更新 `drizzleState` 状态变量。

通过这样做，`drizzleState` 将始终是最新的，我们也确切知道 Drizzle 何时准备就绪，因此我们可以使用加载组件让用户知道。

#### 8.2.3 取消订阅商店

请注意，我们将 `subscribe（）` 的返回值赋给类变量 `this.unsubscribe` 。 这是因为取消订阅组件卸载时的任何订阅始终是一种好习惯。 为了做到这一点，我们保存对该订阅的引用（即 `this.unsubscribe` ），并在 `componentWillUnmount` 中，我们有以下内容：

```js
componentWillUnmount() {
  this.unsubscribe();
}
```

当 App 组件卸载时，这将安全取消订阅，因此我们可以防止任何内存泄漏。

#### 8.2.4 替换 `render` 方法

最后，我们可以用更适合我们的东西替换 boilerplate render 方法：

```js
render() {
  if (this.state.loading) return "Loading Drizzle...";
  return <div className="App">Drizzle is ready</div>;
}
```

在下一节中，我们将使用从商店中读取的实际组件替换 “Drizzle ready”。 如果您现在刷新浏览器并运行此应用程序，您应该会在屏幕上看到 “Loading Drizzle...”，然后 “Drizzle is ready”。

### 8.3 完整的组件代码

完成此部分后，您的 App 组件应如下所示：

```js
class App extends Component {
  state = { loading: true, drizzleState: null };

  componentDidMount() {
    const { drizzle } = this.props;

    // subscribe to changes in the store
    this.unsubscribe = drizzle.store.subscribe(() => {

      // every time the store updates, grab the state from drizzle
      const drizzleState = drizzle.store.getState();

      // check to see if it's ready, if so, update local component state
      if (drizzleState.drizzleStatus.initialized) {
        this.setState({ loading: false, drizzleState });
      }
    });
  }

  componentWillUnmount() {
    this.unsubscribe();
  }

  render() {
    if (this.state.loading) return "Loading Drizzle...";
    return <div className="App">Drizzle is ready</div>;
  }
}
```

## 9. 写一个从 Drizzle 中读取的组件

首先，让我们在 `client/src/ReadString.js` 创建一个新文件并粘贴如下：

```js
import React from "react";

class ReadString extends React.Component {
  componentDidMount() {
    const { drizzle, drizzleState } = this.props;
    console.log(drizzle);
    console.log(drizzleState);
  }

  render() {
    return <div>ReadString Component</div>;
  }
}

export default ReadString;
```

然后在 `App.js` 中，使用以下语句导入新组件：

```js
import ReadString from "./ReadString";
```

现在修改你的 `App.js` 的 render 方法，以便我们从 props 中传递 `drizzle` 实例以及从组件状态传递 `drizzleState`：

```js
render() {
  if (this.state.loading) return "Loading Drizzle...";
  return (
    <div className="App">
      <ReadString
        drizzle={this.props.drizzle}
        drizzleState={this.state.drizzleState}
      />
    </div>
  );
}
```

返回浏览器并打开控制台。 您应该看到两个 `console.log` 语句正在运行，它们同时显示 `drizzle` 实例以及完全初始化的 `drizzleState`。

这告诉我们的是，一旦这个组件安装完毕，我们在这个组件中得到的 `drizzleState` 将始终完全就绪。 此时，您可以花一些时间来探索 `drizzle` 实例对象以及 `drizzleState` 对象。

### 9.1 `drizzle` 实例和 `drizzleState`

在大多数情况下，`drizzleState` 是您从中读取信息（即合同状态变量，返回值，交易状态，帐户数据等），而 `drizzle` 实例是您将用来实际完成任务（即调用） 合同方法， Web3 实例等）。

### 9.2 连接 `ReadString` 组件

现在我们可以访问我们的 `drizzle` 实例和 `drizzleState` ，我们可以输入允许我们读取我们感兴趣的智能合约变量的逻辑。以下是 `ReadString.js` 的完整代码：

```js
import React from "react";

class ReadString extends React.Component {
  state = { dataKey: null };

  componentDidMount() {
    const { drizzle } = this.props;
    const contract = drizzle.contracts.MyStringStore;

    // let drizzle know we want to watch the `myString` method
    const dataKey = contract.methods["myString"].cacheCall();

    // save the `dataKey` to local component state for later reference
    this.setState({ dataKey });
  }

  render() {
    // get the contract state from drizzleState
    const { MyStringStore } = this.props.drizzleState.contracts;

    // using the saved `dataKey`, get the variable we're interested in
    const myString = MyStringStore.myString[this.state.dataKey];

    // if it exists, then we display its value
    return <p>My stored string: {myString && myString.value}</p>;
  }
}

export default ReadString;
```

如果一切正常，您的应用应显示 “Hello World” 。 但首先，让我们来看看我们在这里所做的事情。

### 9.2.1 组件安装时

```js
componentDidMount() {
  const { drizzle } = this.props;
  const contract = drizzle.contracts.MyStringStore;

  // let drizzle know we want to watch the `myString` method
  const dataKey = contract.methods["myString"].cacheCall();

  // save the `dataKey` to local component state for later reference
  this.setState({ dataKey });
}
```

当组件安装时，我们首先获取对我们感兴趣的合同的引用并将其分配给 `contract` 。

然后我们需要告诉 Drizzle 跟踪我们感兴趣的变量。为了做到这一点，我们在`myString` getter 方法上调用 `.cacheCall（）` 函数。

我们得到的返回是 `dataKey` ，它允许我们引用这个变量。 我们将其保存到组件的状态，以便稍后使用。

### 9.2.2 `render` 方法

```js
render() {
  // get the contract state from drizzleState
  const { MyStringStore } = this.props.drizzleState.contracts;

  // using the saved `dataKey`, get the variable we're interested in
  const myString = MyStringStore.myString[this.state.dataKey];

  // if it exists, then we display its value
  return <p>My stored string: {myString && myString.value}</p>;
}
```

从 `drizzleState` 中，我们获取了我们感兴趣的状态片段，在本例中是 `MyStringStore` 合约。 从那里，我们使用之前保存的 `dataKey` 来访问`myString` 变量。

最后，我们编写 `myString && myString.value` 来显示变量的值（如果存在），否则不显示。 在这种情况下，它应该显示 “Hello World” ，因为这是合同初始化的字符串。

### 9.3 快速回顾

这一部分最重要的是，使用 Drizzle 读取值有两个步骤：

1. 首先，你需要让 Drizzle 了解你想要注意的变量。 Drizzle 会给你一个 `dataKey` 作为回报，你需要保存它以供以后参考。

2. 其次，由于Drizzle工作方式的异步性，你应该注意 `drizzleState` 的变化。 一旦 `dataKey` 访问的变量存在，您将能够获得您感兴趣的值。

## 10. 写一个写入智能合约的组件

当然，简单地阅读预先初始化的变量根本就没有乐趣; 我们想要一些我们可以互动的东西。 在本节中，我们将创建一个输入框，您可以在其中键入您选择的字符串，并将其永久保存到区块链中！

首先，让我们创建一个新文件 `client/src/SetString.js` 并粘贴如下：

```js
import React from "react";

class SetString extends React.Component {
  state = { stackId: null };

  handleKeyDown = e => {
    // if the enter key is pressed, set the value with the string
    if (e.keyCode === 13) {
      this.setValue(e.target.value);
    }
  };

  setValue = value => {
    const { drizzle, drizzleState } = this.props;
    const contract = drizzle.contracts.MyStringStore;

    // let drizzle know we want to call the `set` method with `value`
    const stackId = contract.methods["set"].cacheSend(value, {
      from: drizzleState.accounts[0]
    });

    // save the `stackId` for later reference
    this.setState({ stackId });
  };

  getTxStatus = () => {
    // get the transaction states from the drizzle state
    const { transactions, transactionStack } = this.props.drizzleState;

    // get the transaction hash using our saved `stackId`
    const txHash = transactionStack[this.state.stackId];

    // if transaction hash does not exist, don't display anything
    if (!txHash) return null;

    // otherwise, return the transaction status
    return `Transaction status: ${transactions[txHash].status}`;
  };

  render() {
    return (
      <div>
        <input type="text" onKeyDown={this.handleKeyDown} />
        <div>{this.getTxStatus()}</div>
      </div>
    );
  }
}

export default SetString;
```

此时，导入并将其包含在 `App.js` 中，就像使用 `ReadString` 组件一样：

```js
import SetString from "./SetString";

// ...

  render() {
    if (this.state.loading) return "Loading Drizzle...";
    return (
      <div className="App">
        <ReadString
          drizzle={this.props.drizzle}
          drizzleState={this.state.drizzleState}
        />
        <SetString
          drizzle={this.props.drizzle}
          drizzleState={this.state.drizzleState}
        />
      </div>
    );
  }
```

此时，应用程序应该工作，你应该尝试一下。 您应该能够在输入文本框中键入内容，按 Enter 键，Drizzle 的 react store 将自动显示新字符串。

接下来，让我们一步一步地浏览 `SetString.js` 。

### 10.1 一般结构

首先让我们看看我们需要的一般 `React.js` 样板。

```js
class SetString extends React.Component {
  state = { stackId: null };

  handleKeyDown = e => {
    // if the enter key is pressed, set the value with the string
    if (e.keyCode === 13) {
      this.setValue(e.target.value);
    }
  };

  setValue = value => { ... };

  getTxStatus = () => { ... };

  render() {
    return (
      <div>
        <input type="text" onKeyDown={this.handleKeyDown} />
        <div>{this.getTxStatus()}</div>
      </div>
    );
  }
}
```

在这个组件中，我们将有一个输入文本框供用户输入一个字符串，当按下 Enter 键时，将调用 `setValue` 方法，并将字符串作为参数。

此外，我们将显示交易的状态。 `getTxStatus` 方法将通过引用 `stackId` 状态变量返回显示事务状态的字符串（稍后将详细介绍）。

### 10.2 提交交易

```js
setValue = value => {
  const { drizzle, drizzleState } = this.props;
  const contract = drizzle.contracts.MyStringStore;

  // let drizzle know we want to call the `set` method with `value`
  const stackId = contract.methods["set"].cacheSend(value, {
    from: drizzleState.accounts[0]
  });

  // save the `stackId` for later reference
  this.setState({ stackId });
};
```

我们首先将 `drizzle` 实例中的 `contract` 分配给合约，然后我们在我们感兴趣的方法上调用 `cacheSend（）`（即 `set` ）。 然后我们传入我们想要设置的字符串（即 `value`）以及我们的事务选项（在这种情况下，只是 `from` 字段）。 请注意，我们可以从 `drizzleState.accounts [0]` 获取当前帐户地址。

我们得到的是 `stackId` ，它是我们想要执行的事务的引用。 以太坊交易在广播到网络之前不会收到哈希值。 如果在广播之前发生错误， Drizzle 会通过提供每个自己的 ID 来跟踪这些事务。 成功广播后， `stackId` 将指向事务哈希，因此我们将其保存在本地组件状态以供以后使用。

### 10.3 跟踪交易状态

```js
getTxStatus = () => {
  // get the transaction states from the drizzle state
  const { transactions, transactionStack } = this.props.drizzleState;

  // get the transaction hash using our saved `stackId`
  const txHash = transactionStack[this.state.stackId];

  // if transaction hash does not exist, don't display anything
  if (!txHash) return null;

  // otherwise, return the transaction status
  return `Transaction status: ${transactions[txHash].status}`;
};
```

现在我们已将 `stackId` 保存到本地组件状态，我们可以使用它来检查事务的状态。 首先，我们需要来自 `drizzleState` 的 `transaction` 和`transactionStack` 切片状态。

然后，我们可以通过 `transactionStack [stackId]` 获取事务哈希（分配给 `txHash` ）。 如果哈希不存在，那么我们知道事务尚未广播，我们返回 null 。

否则，我们会显示一个字符串以显示我们的交易状态。 通常，这将是“待定”或“成功”。

## 结束

恭喜！ 您已经迈出了一大步，了解 Drizzle 的工作原理。 当然，这只是一个开始，您可以使用像 [drizzle-react](https://github.com/trufflesuite/drizzle-react) 这样的工具来帮助您将 Drizzle 集成到您的 dapp 中，从而减少您必须编写的必要样板。

或者，您也可以使用我们的 [Truffle box](https://github.com/truffle-box/drizzle-box) 来引导您的 Drizzle dapp 。