1. #### 以太坊的智能合约运行在 EVM（以太坊虚拟机）上，这个虚拟机是由以太坊区块链网络支持的

2. #### 所有的智能合约都是开源的

3. #### 计算机中的位和字节的理解

   计算机中最基本的储存单位：位（bit）和字节（byte）,那么什么是位和字节：

   - 位：最小的储存单位，值只能是 0 或者 1

   - 字节：通常由 8 个 bit 组成

   所以，1 字节（byte）= 8 位（bit）

   例如：一个字节的数据：十六进制 0x61 = 二进制 01100001 = 共 8 位

4. #### uint 和 int

   1. `uint`

      - `uint` 是 **无符号整数（unsigned integer）**的缩写，它表示只存储非负整数（0和正整数），不包含负数

      - `uint` 本质上等同于 `uint256`，即占用 256 位（32 字节）的无符号整数，取值范围是从 0 ~ 2的256次方减1

      - Solidity 允许声明不同位数长度的无符号整数，分别是 `uint8, uint16, uint24, ..., uint256`，步长是 8 位

        1. `uint8`：8 位无符号整数，范围 0~255
        2. `uint16`：16 位无符号整数，范围 0~65535
        3. `uint24`：24 位无符号整数，范围 0~2的24次方减1
        4. ...
        5. `uint256`：256 位无符号整数，范围 0~2的256次方减1

        共计 **32 种**

      > 举例，有 3 个灯泡，每个灯泡只能是 **亮（1）或灭（0）**，你最多能组合出几种不同的状态？
      >
      > 答案是：
      >
      > 每个灯泡 2 种状态 → 总共就是 2 * 2 * 2 = 2^3 = 8 种状态。
      >
      > 
      >
      > 现在，我们再看8个二进制位的情况，`uint8` 是 **8 位的二进制数**，每一位可以是 0 或 1，所以一共可以组合出 **2^8=256** 种状态？
      >
      > 这 256 种组合，如果按数字从 **小到大**排列，会是这样的：
      >
      > | 二进制     | 十进制  | 说明          |
      > | ---------- | ------- | ------------- |
      > | `00000000` | 0       | 第 1 个组合   |
      > | `00000001` | 1       | 第 2 个组合   |
      > | `00000010` | 2       | 第 3 个组合   |
      > | `00000011` | 3       | 第 4 个组合   |
      > | ...        | ...     | ...           |
      > | `11111111` | **255** | 第 256 个组合 |
      >
      > 这 256 种组合，正好能表示从 `0` 到 `255`，总共 256 个数。

   2. `int`

      - `int` 是 **有符号整数（signed integer）**的缩写，它既可以表示正数，也可以表示负数

      - `int` 本质上等同于 `int256`，即占用 256 位（32 字节）的有符号整数，取值范围是 -2的255次方 ~ 2的255次方减1

      - `int` 使用的是 **补码（two's complement）** 表示方式

      - 数据范围是对称的，但负数比正数多一个，因为要包含 **0**

      - Solidity 允许声明不同位数长度的有符号整数，分别是 `int8, int16, int24, ..., int256`，步长是 8 位

        1. `int8`：8 位有符号整数，范围 -128~127
        2. `int16`：16 位有符号整数，范围 -2的15次方 ~ 2的15次方减1
        3. `int24`：24 位有符号整数，范围 -2的23次方 ~ 2的23次方减1
        4. ...
        5. `int256`：256 位有无符号整数，范围 -2的255次方 ~ 2的255次方减1

        共计 **32 种**

      > 1. ##### 为什么 `int8` 是 -128 到 127？
      >
      >    我们来一步一步拆解：
      >
      >    - `int8` 表示的是 **8 位有符号整数**
      >    - 使用 **补码** 表示
      >    - 8 位可以组合出 2^8 = 256 种不同的二进制值
      >    - 这些值平均分配为：
      >      - 正数（包括 0）：从 `00000000` 到 `01111111`（十进制 0 到 127）
      >      - 负数：从 `10000000` 到 `11111111`（十进制 -128 到 -1）
      >
      >    所以：
      >
      >    | 范围                     | 十进制表示 | 说明               |
      >    | ------------------------ | ---------- | ------------------ |
      >    | `00000000` ～ `01111111` | 0 到 127   | 正整数             |
      >    | `10000000` ～ `11111111` | -128 到 -1 | 负整数（补码表示） |
      >
      >    **总数仍然是 256 个：**
      >    $$
      >    128（负数）+ 1（0）+ 127（正数） = 256
      >    $$
      >    
      >
      > 2. ##### 什么是补码？
      >
      > 补码是一种用于表示负数的方式，在计算机中是标准做法。
      >
      > **例子：**
      >
      > - 对于 `int8` 类型：
      >   - `+1`：`00000001`
      >   - `-1`：先取 `1` 的反码：`11111110`，再加 `1` 得到补码：`11111111`
      >   - `-128`：是补码里能表示的最小值，写作：`10000000`
      >
      > 这种设计的好处：
      >
      > - 运算更快（加减统一用加法电路）
      > - 比较大小、加减法操作更简单高效

5. #### 智能合约的基本结构

   ```solidity
   contract ContractStructure {
   	// 成员变量，又叫状态变量
   	uint256 public balance;
   
     // 构造函数，构造函数当合约在区块链上被部署的时候执行；constructor 与普通成员函数不一样，它的可见性说明符默认为：public
     constructor (uint256 initBalance) {
     	balance = initBalance;
     }
   
   	 // 事件
     event balanceLog (uint256 oldBalance, uint256 increment);
     
   	// 修饰器，修饰器是对函数的输入或者输出进行条件约束
     modifier incrementLimit (uint256 increment) {
       require(increment > 500, "The increment must be greater than or equal to 500");
       _; // 执行被修饰函数的函数体
     }
   
   	// 成员函数，其中的 public 用来定义函数的可见性
     function addBalance (uint256 increment) public incrementLimit(increment) { 
       emit balanceLog(balance, increment);
       balance += increment;
     }
     
     // 成员函数，其中的 view 用来说明函数是读操作，而后的 uint256 定义返回值的数据类型，返回值是可以有多个的
     function getBalance () public view returns (uint256) {
       return balance;
     }
   }
   ```

6. #### 智能合约的部署其实是要在区块链执行一个交易，这个交易带着合约的代码发送给区块链，而后区块链来处理这个交易，在链上产生一个新的合约实例，部署合约需要耗费资源，耗费资源意味着需要花钱，在以太坊上则花费以太币；合约实例拥有一个地址，称为合约地址（一串十六进制字符串）。  

7. #### 成员变量

   1. 成员变量是存储合约状态的变量，声明方法：`类型 [修饰符] 变量名称`
   2. 成员变量的**可见性说明符**（默认值：`internal`）

   - public：合约外部、本合约、子合约可见
   - private：本合约可见
   - internal：本合约、子合约可见

8. #### 成员函数（TODO：returns(param)）

   1. 成员函数的声明方法：`function fName([param1, param2, ...]) [可见性] [交易相关] [...] [retruns([param]) {}]`

   2. 成员函数的结构分解：

      - 函数签名：`fName([param1,param2,...])`
      - 返回值：`retruns([param])`
      - 修饰符：`[可见性] [交易相关] [...]`

   3. 成员函数的**可见性说明符**（默认值：无默认值，必须显式定义）

      - public：合约外部、本合约、子合约可见
      - private：本合约可见
      - internal：本合约、子合约可见
      - external：仅合约外部

   4. 成员函数的**状态可变性说明符**(交易相关)

      - 无描述符（默认）：可以读、写区块链状态*（它会引发向区块链发送一个交易，而后区块链来处理这个交易；gas 消耗较高）*

      - view：只读，不能修改状态

      - pure：纯函数，既不读取也不修改状态

      - payable：允许接收 ETH

9. #### ABI 信息描述合约的外部行为，而不是描述合约的内部实现

10. #### 数据类型

    - **值类型（Value Types）**

      存储的是**值本身**，赋值或传参时会**拷贝一份**，互不影响。

      主要包括：

      1. 布尔类型（`bool`）

         - 取值：`true` / `false`

      2. 整数类型

         - **无符号整数**（`uint` / `uint8` ~ `uint256`，步长 8）
         - **有符号整数**（`int` / `int8` ~ `int256`，步长 8）

      3. 定长字节数组

         - `bytes1` ~ `bytes32`（定长，按字节存储）

      4. 枚举类型（`enum`）

         - 用来定义一组常量值，例如：

           ```solidity
           enum Status { Pending, Shipped, Delivered }
           ```

      5. 地址类型

         - `address`：固定占用 20 字节，储存以太坊地址
         - `address payable`：固定占用 20 字节，可接收 ETH，有`.transfer()`/`.send()`/`.call{value:...}()`方法

    - **引用类型**（Reference Types）

      存储的是**数据位置的引用**，赋值或传参时指向同一份数据，修改会互相影响。在 Solidity 中，这类变量必须指定存储位置（`storage`/`memory`/`calldata`）。

      主要包括：

      1. 动态字节数组与动态长度字符串

         - **动态字节数组**：`bytes`（长度可变，按字节存储）
         - **动态长度字符串**：`string`（UTF-8 编码的动态字节序列）

      2. 数组

         - 定长数组（如 `uint[5] arr`）

         - 动态数组（如 `uint[] arr`）

           元素存储在 `keccak256(abi.encode(slot))` 起始位置，元素位置：`keccak256(abi.encode(slot)) + index`

      3. 结构体（`struct`）

         - 自定义的复合数据类型，例如：
      
           ```solidity
           struct Person {
             string name;
             uint age;
           }
           ```
           
           > `Struct` 不能“直接递归包含自身”（值递归），但允许通过 array / mapping 实现递归（引用递归）

      4. 映射（`mapping`）
      
         - 类似哈希表（Key → Value），例如：
      
           ```solidity
           mapping(address => uint256) balances;
           ```
      
         - `mapping` 的本质不是数据结构，而是一个如何在 `storage` 里定位数据的规则，`mapping` 计算 `storage` 位置的公式：
      
           ```solidity
           keccak256(abi.encode(key, slot))
           ```
      
           当我们访问一个 `mapping`：
      
           ```solidity
           contract Test {
           	uint256 a; // slot 0
           	mapping(address => uint256) balance; // slot 1；mapping 不直接占用数据空间，所以 slot 1 不储存数据
           	
           	function getBalance (address addr) public returns(uint256) {
           		// 访问 mapping 时，实际定位的储存位置是：keccak256(abi.encode(addr, 1))
           		uint256 currentBalance = balance[addr];
           		
           		return currentBalance;
           	}
           }
           ```
      
           如果是访问嵌套 `mapping`:
      
           ```solidity
           contract Test {
           	mapping(address => mapping(uint256 => uint256)) data; // slot 0
           	
           	function getFraction (address addr, uint256 id) public returns (uint256) {
           		uint256 currentFraction = data[addr][id];
           	}
           }
           ```
      
           实际定位的储存位置计算方法：
      
           第一步：
      
           ```solidity
           InnerSlot = keccak256(abi.encode(addr, 0))
           ```
      
           第二步：
      
           ```solidity
           FinalSlot = keccak256(abi.encode(id, InnerSlot))
           ```
      
         - 如何用 `string` 当 key？
      
           ```solidity
           mapping(bytes32 => uint256) m
           
           // 设置
           function set(string memory key, uint256 value) public {
               bytes32 k = keccak256(abi.encodePacked(key));
               m[k] = value;
           }
           
           // 读取
           function get(string memory key) public view returns (uint256) {
               return m[keccak256(abi.encodePacked(key))];
           }
           ```
      
         > - `mapping` 类型只能存在于 `storage` 中，不能存在于 `memory` 或 `calldata`
         > - `mapping` 的 `key` 必须是值类型（value type），不能是引用类型（reference type）
         > - `mapping` 不能作为 `public`、`external` 函数的参数，但可以作为 `internal`、`private`、`library` 函数的 `storage` 参数
         > - `mapping` 不能作为任何函数的返回值
         > - `mapping` 不能作为数组成员
         > - `mapping` 不能被遍历，因此也不支持拷贝

11. #### Address 账户地址

    账户地址分为外部账户和内部账户，都是20字节长度，`address payable` 与 `address` 之间可以进行显式互相转换

    - 外部账户指的是资产持有人的钱包地址
    - 内部账户指的是合约部署后形成的地址

12. #### Contract 合约类型

    - 合约可以隐式转换为它的父合约，父合约需要显式转换为它的子合约 
    - Contract 与 Address 之间可以进行显式互相转换
    - 合约不支持任何运算符
    - 可以从合约变量（合约实例）调用合约函数
    - 合约可以使用 new 操作符部署另一合约

13. #### 所有引用类型（reference types）的局部 `storage` 变量，都必须绑定到已有的 storage 数据

14. #### 引用类型的拷贝

    1. 判定算法：

       > [!NOTE]
       >
       > - 合约的成员变量中引用类型的特殊性
       >
       >   与一般虚拟机或物理机不同的是,EVM 的机器模型中引入了 `storage`,合约成员变量指向固定的持久化(storage)数据块,它并不能像一般引用类型变量一样切换它所指向的数据块,由于这个限制,对成员变量的赋值,引用拷贝从技术上变成不可能。
       >
       > - Location 对数据空间的分割
       >
       >   数据块有三个储存位置（`storage`、`memory`、`calldata`），概念上储存空间被分割成三个子空间，引用类型变量被 location 属性限定，不可能跨越子空间进行指向切换，只能在子空间内部切换。

       一个赋值操作：

       ```solidity
       x = a;
       ```

       其中 x 是被赋值的变量，在赋值操作符的左侧，a 是赋值变量，在赋值操作符的右侧。

       如果 x 是成员变量（意味着 x 会指向一个数据块，这种指向关系不会发生改变），则是值拷贝；如果 x 是局部变量，x 与 a 的 location 相同，则是引用拷贝，如果 x 与 a 的 location 不相同，则是值拷贝。

    2. 检查算法：

       > [!NOTE]
       >
       > - `calldata` 是只读的，它是 `message` 的数据字段

       判定算法有三个输出：引用拷贝、值拷贝。如果输出引用拷贝则不必检查；如果输出值拷贝则执行检查算法：

       - 检查 x 和 a 的类型中是否有 `mapping` 元素（本身是 mapping 或者嵌入了 mapping 成分），如果有则报错
       - 检查 x 是否是 `calldata`，如果是则报错
       - 执行值拷贝

15. #### 合约之间的函数调用

    - EOA（Externally Owned Account）指外部账户，本质上是以太坊网络上的一种账户，由使用私钥的人控制。换句话说，它是您用于发送交易或管理以太币 (ETH) 的账户。与仅由代码控制的智能合约账户不同，外部拥有者账户由人员直接管理
    - 合约的调用最初是由 EOA 发起的
    - EOA 使用的 ABI 数据与合约间调用时调用者持有的接口是等价的，都是对合约函数签名的完整描述
    - Solidity 不要求被调用的合约语法上必须实现一个接口(is 关键字)

16. #### 合约的调用链

    ![contractCallChain](/Users/ethereal/Documents/PersonalProject/NoteLibrary/images/contractCallChain.svg)

    ![eoa-contractA-contractB](/Users/ethereal/Documents/PersonalProject/NoteLibrary/images/eoa-contractA-contractB.png)

    `calldata` 的本质是 EVM 调用函数时输入的数据载体

    - 对于 EOA 调用合约，`calldata` 存在于 transaction 中的 data，可以通过 `msg.data` 读取
    - 对于合约调用合约，`calldata` 存在于 EVM 内部 `message call`，可以通过 `msg.data` 读取

    ```solidity
    calldata = 函数选择器（前 4 字节）+ 参数编码（按 ABI 规则）
    ```

    **参数的编码**是完整的 ABI 编码，**函数的编码**是：**函数签名 -> keccak256 -> 取前 4 字节**

    ```solidity
    // 函数签名
    transfer(address, uint256)
    
    // keccak256
    keccak256("transfer(address, uint256)") // 0xa9059cbb...
    
    // 取前 4 字节
    0xa9059cbb // 这个就是函数的编码结果（函数选择器）,可以通过 msg.sig 获取函数选择器
    ```

     

    手动构造 `calldata`（主要用于 call 方法使用）：

    ```solidity
    bytes memory data = abi.encodeWithSignature(
    	"transfer(address,uint256)", // 函数签名(不能有空格、去掉参数名称只保留参数类型)
    	0x000000..., // 参数1
    	1000 // 参数2
    )
    ```

    

    一些全局变量的使用

    ```solidity
    block.timestamp // 获取当前区块的时间戳（Unix 秒）
    
    tx.origin // 获取最初发起这笔 transaction 的 EOA 地址
    
    msg.sender // 获取谁调用了当前合约
    msg.sig // 获取函数选择器
    msg.data // 获取 calldata
    msg.value // 获取转账金额，单位为 wei，1ETH = 10¹⁸wei
    ```

    > 1. 在以太坊中，函数调用（无论来自 EOA 还是合约的调用）会构造一段 calldata（原始字节数据），通常按照 ABI 规范组织（函数选择器 + 参数编码），并通过 transaction 或 message call 传递给目标合约，在合约内部通过 msg.data 被解析执行。
    > 2. ABI 不是数据类型，而是一套编码/解码规则；calldata 不是 ABI，本质只是 bytes，只是通常“按 ABI 规则组织”。

17. #### 函数可见性为 external 的理解

    - 可见性为 `external` 的函数应该只被外部函数调用

    - `external` 的函数虽然可以被合约内部调用，但是采用外部调用的机制，会产生新的 `message`，耗费的资源也会增多

18. #### Call 的使用语法

    ```solidity
    (bool status, bytes memory data) = <address>.call(bytes <calldata>)
    ```

    请务必判断 `call` 调用后返回的 `status`，并进行相应逻辑处理，否则会造成严重问题。

    这里的 data 是经过编码后的动态字节数组，需要使用 `abi.decode(bytes)` 进行解码。

19. #### 合约中的 gas、gas fee、gas price 的区别

    - gas 是完全由执行逻辑决定的，一个逻辑没有变化的合约函数执行，gas 没有变化
    - gas price 由市场定价，可以由交易发起者在 transaction 中设定
    - gas fee = gas * gas price

20. #### Gaslimit 与 Gasleft() 函数

    Gaslimit：

    - 交易的发起者可以设定 `gaslimit`
    - 合约之间的调用，调用者可以设定 `gaslimit`
    - 区块本身设定了一个 `gaslimit`

    Gasleft 是由以上三个 `gaslimit` 作为限定，与当前 gas 的消耗一起计算的结果，可以理解为：
    $$
    gasleft() = gaslimit - gas
    $$

21. #### Gas 退款规则

    - 交易进行中 gas 被消耗完后，交易会被终止执行
    - 交易完成时，没有被消耗的 gas 会被退回

    - 交易失败时，已经被消耗的 gas 不会被退回

22. #### 转账

    - `send` 和 `transfer` 是旧的转账设计，它们可以理解为 `gaslimit` 为 2300 的 `calldata` 为空的 `call`，区别在于 `transfer` 处理了 `call` 的返回值

    - 新的转账设计没有专门的转账函数，而是普通函数调用的伴生物

23. #### send 和 transfer 函数的底层实现

    - send 函数的实现：

      ```solidity
      function send (address addr, uint256 amount) public returns (bool) {
      	(bool status, bytes memory data) = addr.call{gas: 2300, value: amount}("");
      	return status;
      }
      ```

    - transfer 函数的实现：

      ```solidity
      function transfer (address addr, uint256 amount) public {
      	(bool status, bytes memory data) = addr.call{gas: 2300, value: amount}("");
      	require(status, "Call failed!");
      }
      ```

24. #### 边界性问题

    - 调用非合约地址的合约函数总是能成功的
    - 非合约地址并非一定是外部账号，它可能仅仅是形式上是合法的地址

25. #### Storage Layout

    - 值类型的存储规则：

      1. `storage` 以 32 字节（slot）为最小单位进行存储，小于 32 字节的**值类型**会按声明顺序进行**紧凑打包（packing）**

      2. **值类型**按顺序尝试放入当前 slot，如果剩余空间不足或不满足对齐规则，则会使用新的 slot

      3. **静态数组**会参与外部变量 packing，但具体取决于：

         - 若数组元素小于 32 字节，则数组元素会进行紧凑打包，可能与外部变量共享 slot

         - 若数组元素等于 32 字节，则每个元素独占一个 slot

    - 引用类型的堆叠规则

      1. **结构类型**不会与外部变量 packing，但会从某个起始 slot 连续占用多个 slot（取决于内部字段布局）
      2. **动态数组**在 slot 中存储长度，元素存储在 `keccak256(abi.encode(slot))` 起始位置
      3. **mapping** 在 slot 中不存储实际数据，仅作为哈希计算基准

    > 真正的 packing 边界类型：`struct`、`mapping`、`动态数组`

26. #### delegatecall 的使用语法

    ```solidity
    (bool status, bytes memory data) = <address>.delegatecall(bytes <calldata>)
    ```

    - `delegatecall` 用于执行其他合约的代码，但使用当前合约的储存（`storage`）

    - `delegatecall` 是一种 `message call`（EVM **调用类型**之一），但它不会创建新的调用上下文，而是复用当前上下文

    - `delegatecall` 的调用选项不能设置 `value`，但可以设置 `gas`

27. #### 代理模式

    - 结构化储存代理模式

      `Proxy` 合约负责数据储存，`Logic` 合约负责数据的逻辑处理

    - 非结构化储存代理模式

      `Proxy` 合约负责代理处理，`Logic` 合约负责数据和逻辑处理

    > **合约升级**是指在不改变合约地址和储存数据的前提下，替换执行逻辑。
    >
    > 其结构是这样的：`用户  →  Proxy（地址固定）  →  Implementation（逻辑可变）`

28. #### library 合约的特点

    - `library` 可以继承普通 `contract` 和其他 `library`
    - `library` 可以实现 `interface`
    - `library` 支持修饰器（`modifier`）
    - `library` 的函数可见性通常是 `public`、`internal`（`external` 比较少见）
    - `library` 不能有状态变量（`storage`）
    - `library` 不支持构造函数，`constructor` 不会执行
    - `library` 不能接收 ETH，因此也不支持 `fallback` 和 `receive`

29. #### library 合约的调用方式

    - `library` 合约包含 `public` 或 `external` 函数时，必须单独部署为一个合约，调用代码由编译器生成，并在运行时通过 `delegatecall` 执行，其地址是在部署时通过链接（linking）填充到调用合约的字节码中，而不是通过构造函数参数传入
    - `library` 合约可见性为 `internal` 函数是通过编译时代码内联实现

    > 库函数调用在语义上表现为内部调用（上下文不变），但在实现上：
    >
    > - `public` / `external` 函数通过 `delegatecall` 实现，会发生一次外部调用（`message call`）
    >
    > - `internal` 函数通过代码 `inline` 方式实现
    >

30. #### 合约继承（TODO 与课程中有个地方最后一共部署了几个合约）

    Solidity 的合约继承是在编译阶段展开的，编译器会将父合约的逻辑合并到子合约中，生成一个完整的字节码。因此，如果只部署子合约，链上只存在一个合约实例，但其逻辑包含了父合约的实现。

31. #### 抽象合约与接口

    | 特性                   | 抽象合约 (abstract contract) | 接口 (interface) |
    | ---------------------- | ---------------------------- | ---------------- |
    | 能否直接部署           | ❌ 不能                       | ❌ 不能           |
    | 能否包含状态变量       | ✅ 可以                       | ❌ 不能           |
    | 能否包含**未实现函数** | ✅ 可以                       | ✅ 全是未实现函数 |
    | 能否包含**已实现函数** | ✅ 可以                       | ❌ 不能           |
    | 能否被继承/实现        | ✅ 可以被继承                 | ✅ 可以被实现     |

    > - 如果合约中有未实现的函数，该合约必须被标记为 `abstract`，且这些未实现的函数必须标记为 `virtual`，派生合约继承后必须实现这些函数，除非派生合约也是 `abstract`
    >
    > - `interface` 的本质是一个全是未实现函数的 `abstract` 合约
    >
    >   ```solidity
    >   interface Base {
    >   	function foo () external; // 默认就是 virtual 函数
    >   }
    >   // 等价于
    >   abstract contract Base {
    >   	function foo () external virtual // 显式标明 virtual 函数
    >   }
    >   ```
    >
    > - 接收 `storage` 参数的 `constructor`，该合约必须设置成 `abstract` 合约
    >
    >   ```solidity
    >   abstract contract Base {
    >   	constructor (string storage name) {}
    >   }
    >   contract A is Base {
    >   	string public name;
    >   	constructor () Base(name) {}
    >   }
    >   ```

32. #### 合约继承中 Visibility 和 Mutability 的覆盖规则

    - Visibility 规则

      子合约函数覆盖父合约函数，子函数需要与父函数兼容

      | 父函数     | 子函数允许            |
      | ---------- | --------------------- |
      | `private`  | ❌ 不可以 override     |
      | `internal` | `internal`            |
      | `external` | `external` / `public` |
      | `public`   | `public`              |

    - Mutability 规则

      子合约函数覆盖父合约函数，子函数可以更严格，但不能更宽松

      | 父函数             | 子函数允许             |
      | ------------------ | ---------------------- |
      | `payable`          | `payable`              |
      | `pure`             | `pure`                 |
      | `view`             | `view` / `pure`        |
      | 普通（可修改状态） | 普通 / `view` / `pure` |

33. 

34. 

35. 

36. 

37. 

38. 

    

    
