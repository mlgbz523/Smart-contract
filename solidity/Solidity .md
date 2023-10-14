# Solidity  ^0.8.4





## 变量类型



>- 数值类型（Value 
>- 引用类型 （Reference
>- 映射类型（Mapping
>- 函数类型（Function



### 数值类型

---



#### 布尔型

（常规逻辑运算符）

初值：`false`

#### 整型（常用部分）

- `int`
- `uint` （uint类型变量不允许计算过程中小于0==【下溢】==）
- `uint256`

#### 地址类型

地址类型(address)存储一个 20 字节的值（以太坊地址的大小）

- `address`

##### 	address 的成员变量:

- `balance`: 便是地址的以太币余额（只读）
- `code`：表示地址的代码
	- 合约地址（包含合约的代码）
	- 用户地址（code为空）

通过 ==payable== 修饰的 address 将变为可转账ETH的地址，

拥有额外的成员变量：

- `transfer` ：用于安全地发送以太币。它==会检查==接收==地址==是否==有效==，并确保发送方有==足够的以太币==。

- `send`：用于发送以太币，但它==不会检查==接收地址是否有效，也不会检查发送方是否有足够的以太币。

	

|          |                      |                |
| -------- | -------------------- | -------------- |
| transfer | 检查地址和以太币数量 | 更安全         |
| send     | 不检查               | 更灵活，更便捷 |

#### 定长字节数组（数值类型）

`byte`, `bytes8`, `bytes32`

定长`bytes`可以存一些数据，==消耗`gas`比较少==。

代码：

```solidity
    // 固定长度的字节数组
    bytes32 public _byte32 = "MiniSolidity"; 
    bytes1 public _byte = _byte32[0]; 
```



MiniSolidity==变量以字节的方式存储进变量==`_byte32`，转换成`16进制`为：``

```
0x4d696e69536f6c69646974790000000000000000000000000000000000000000
```



`_byte`变量存储`_byte32`的第一个字节，为`0x4d`。

>
>
>1 字节 = 8 二进制位 = 2 十六进制位



- [ ] 不定长字节数组（引用类型）





#### 枚举 enum

主要用与为uint分配名称

可以显式的与uint互转

```solidity
// 用enum将uint 0， 1， 2表示为Buy, Hold, Sell
enum ActionSet { Buy, Hold, Sell }
// 创建enum变量 action
ActionSet action = ActionSet.Buy;
```



### 引用类型

---



#### 数组 array

用来存储一组数据（整数，字节，地址等等）



##### 常数组

在solidity中值没有指定value 的type时，默认是最小单位的该type  如 int的默认最小类型是 uint 8

常数组 [uint(1),2,3] 后边的元素type 以第一个元素为准

如果没有 uint(1), 这个数组的元素都会是 uint8



##### 	固定长度数组

​	用`T[k]` xx 的格式声明  ( T为元素类型  x为数组名)

```solidity
  // 固定长度 Array
    uint[8] array1;
    bytes1[5] array2;
    address[100] array3;
```





##### 	可变长度数组（动态数组）

​	用`T[]`的格式声明

````solidity
    // 可变长度 Array
    uint[] array4;
    bytes1[] array5;
    address[] array6;
    bytes array7;
````

###### 		memory 修饰的动态数组

- 用 new 创建，必须声明长度，声明后长度不能改变

	```solidity
	// memory动态数组(需要在函数内声明)
	uint[] memory array8 = new uint[](5);
	bytes memory array9 = new bytes(9);
	```

- 动态数组必须一个元素一个元素的赋值

	```solidity
	    uint[] memory x = new uint[](3);
	    x[0] = 1;
	    x[1] = 3;
	    x[2] = 4;
	```

	| 特性     | 一般动态数组         | memory 修饰的动态数组  |
	| :------- | :------------------- | :--------------------- |
	| 存储位置 | storage              | memory                 |
	| 长度     | 在编译时确定         | 在运行时动态变化       |
	| 性能     | 较低                 | 较高                   |
	| 安全性   | 较高                 | 较低                   |
	| 用途     | 存储需要持久化的数据 | 存储需要临时使用的数据 |
	
	

###### 		动态数组  成员

- `length`  :(memory数组创建后 length是固定的 )

######         动态数组，bytes 成员

- `push( )` :可以在数组最后添加一个`0`元素

- `push( x )`: 可以在数组最后添加==一个`x`元素==

- `pop( )`: 可以移除数组最后一个元素

	

##### 	特殊数组 bytes

- 是数组，但是不用加`[]`
- 不能用`byte[]`声明单字节数组，可以使用`bytes`或`bytes1[]`
- `bytes`可以用来存储任意数量的字节数
- `byte_ [] `表示固定大小的字节数组，长度必须是 ==1、2、3、4、8 或 32==
- ? ...

| 类型     | memory              | storage                |
| :------- | :------------------ | :--------------------- |
| bytes    | 可变                | 可变                   |
| bytes1[] | 固定为 32 字节      | 可变                   |
| gas 消耗 | bytes1[] 高于 bytes | bytes 和 bytes1[] 相同 |

**解释**

>在 memory 中，bytes1[] 的长度是 32 字节，而 bytes 的长度是可变的。因此，bytes1[] 在 memory 中要增加 31 个字节进行填充，会产生额外的 gas。





#### 结构体 struct

>结构体，用于定义新的复合类型

>给结构体赋值的两种方法
>
>- 引用赋值
>- 直接赋值



```solidity
        // 结构体
    struct Student{
        uint256 id;
        uint256 score; 
    }
    Student student; // 初始一个student结构体
    
   
    // 方法1:在函数中创建一个storage的struct引用
    function initStudent1() external{
        Student storage _student = student; // assign a copy of student
        _student.id = 11;
        _student.score = 100;
    }
    // 方法2:直接引用状态变量的struct
    function initStudent2() external{
        student.id = 1;
        student.score = 80;
    }
```







### 映射类型 Mapping

---



`mapping(key_type => value_type )` public someone;

#### 映射的规则

>- **规则1**：``key`` 不能使用自定义类型（struct）大部分数组 （也就是大部分的引用类型不适用）, ``value``没有使用约束
>
>	
>
>- **规则2：**映射``mapping`的存储位置必须是`storage` (状态变量 ，`storage`变量，`library`函数参数)
>
>	
>
>- **规则3：**如果映射声明为`public`，那么`solidity`会自动给你创建一个`getter`函数，可以通过`Key`来查询对应的`Value`
>
>	
>
>- 规则**4**：给映射新增的键值对的语法为`_Var[_Key] = _Value`，其中`_Var`是映射变量名，`_Key`和`_Value`对应新增的键值对





#### getter函数

- 在合约内部访问mapping的值：	

    ```solidity
    # 可以直接使用balances[address]

    mapping(address => uint) public balances;
    uint mp1 = balances[address]
    ```

- **通过合约地址和 key 调用 getter 函数**

    x = `target_contract(contract_address).mapping_name.get(key)`

    ```solidity
    contract Example {
    
      mapping(address => uint256) myMapping;
    
      function foo() public {
        // 创建映射
        myMapping[address(0)] = 10;
      }
    }
    
    contract Other {
    
      function test() public {
        // 通过合约地址和 key 调用 getter 函数
        uint256 balance = Example(0x1234567890abcdef).myMapping.get(address(0));
    
        // 输出映射中的值
        console.log(balance); // 10
      }
    }
    ```

    

#### 	映射的原理：

| 引用类型 可以通过编译的key | 原因：被都被哈希转换为bytes32                  |
| -------------------------- | ---------------------------------------------- |
| string                     | 不同的bytes或string可能会被哈希为相同的bytes32 |
| bytes                      | 都有可能产生哈希冲突，不推荐使用               |

`映射的实质就是哈希表，将key哈希后映射到value,哈希函数需要接受一个固定大小的输入，因为大部分引用类型的大小不是固定的，因此不能作为键 `

>- **原理1**: 映射不储存任何键（`Key`）的资讯，也没有length的资讯。
>- **原理2**: 映射使用`keccak256(key)`当成offset存取value。
>- **原理3**: 因为Ethereum会定义所有未使用的空间为0，所以未赋值（`Value`）的键（`Key`）初始值都是各个type的默认值，如uint的默认值是0。







## 函数



### 函数关键字以及函数格式

![image-20231011205021822](C:\Users\01\Desktop\智能合约工程师\Smart-contract\img\image-20231011205021822.png)

```solidity
    function <function name>(<parameter types>) {internal|external|public|private} [pure|view|payable] [returns (<return types>)]
```

> function 函数名（参数类型  参数）{ 函数可见性说明 （合约内必填，合约外默认internal）}  【函数权限（默认可读可写）】【函数返回类型和变量名称】{ 函数体 【返回值】}



>[  ] 中是可写可不写关键字
>
>1. `function`：声明函数时的固定用法，想写函数，就要以function关键字开头。
>
>2. `<function name>`：函数名。
>
>3. `(<parameter types>)`：圆括号里写函数的参数，也就是要输入到函数的变量类型和名字。
>
>4. `{internal|external|public|private}`：函数可见性说明符，一共4种。没标明函数类型的，默认`public`。合约之外的函数，即"自由函数"，始终具有隐含`internal`可见性。
>
>	- `public`: 内部外部均可见。
>	- `private`: 只能从本合约内部访问，继承的合约也不能用。
>	- `external`: 只能从合约外部访问（但是可以用`this.f()`来调用，`f`是函数名）。
>	- `internal`: 只能从合约内部访问，继承的合约可以用。
>
>	**Note 1**: 没有标明可见性类型的函数，默认为`public`。
>
>	**Note 2**: `public|private|internal` 也可用于修饰状态变量。 `public`变量会自动生成同名的`getter`函数，用于查询数值。
>
>	**Note 3**: 没有标明可见性类型的状态变量，默认为`internal`。
>
>5. `[pure|view|payable]`：决定函数权限/功能的关键字。`payable`（可支付的）很好理解，带着它的函数，运行的时候可以给合约转入`ETH`。
>
>6. `[returns ()]`：函数返回的变量类型和名称。





### 函数输出

Solidity 中控制函数输出的两个关键字

#### return 与 returns 组合

- returns加在函数名后面，用于声明返回的变量类型及变量名。

- return用于函数主体中，返回指定的变量。

	```solidity
	// 返回多个变量
	function returResult() public pure returns( uint, bool, unit256 memory){
		return(1,true,[uint256(1),2,5]);
	}
	```

#### returns() 命名式返回

- 命名式返回：我们可以在returns中标明返回变量的名称，这样solidity会自动给这些变量初始化，并且自动返回这些函数的值，不需要加return。
- 也可以在命名式返回中用return来返回变量

```solidity
function returnNamed() public pure returns(unit256 _number, bool _bool, unit256[3] memory _array){
	_number = 2;
	_bool = false;
	_array = [unit256(3),2,1]
}
```

#### 函数返回数组

当需要返回常数组的时候，需要注意returns声明的数组type，对返回的数组进行必要的强转





### 函数可见性说明

**`{internal|external|public|private}`** 是函数可见性说明符，用于指定函数在合约中的可见性。

| 可见性     | 描述                 | 适用场景             |
| :--------- | :------------------- | :------------------- |
| `public`   | 内部外部均可见       | 合约的公共 API       |
| `private`  | 只能从本合约内部访问 | 合约的私有方法       |
| `external` | 只能从合约外部访问   | 合约与外部交互的接口 |
| `internal` | 只能从合约内部访问   | 合约的内部方法       |



**`{internal|public|private}`** 也可以被用作==变量可见性说明==

| 可见性     |                |
| :--------- | :------------- |
| `public`   |                |
| `private`  |                |
| `internal` | 合约内默认说明 |

在Solidity中，只有常量（constant）变量和类型别名（type alias）可以在文件级别（也就是不在合约或库内部）声明。



### 函数权限 / 功能说明

**`[pure|view|payable]`** 是决定函数权限/功能的关键字。

| 关键字    | 描述                                     | 适用场景            |
| :-------- | :--------------------------------------- | :------------------ |
| `pure`    | 函数不会修改合约的状态（不能看也不能读） | 只读函数            |
| `view`    | 函数不会修改合约的状态，但可以读取状态   | 读写函数            |
| `payable` | 函数可以接收 ETH 的转账                  | 需要接收 ETH 的函数 |



`pure` 因为不配读也不配写，

### getter 函数

> Solidity中的特殊函数

> 用于读取合约中状态变量的值

> 当声明一个public的状态变量时 solidity编译器会自动创建一个同名的getter函数



```solidity
// 例如，如果你有一个如下的状态变量声明：
contract MyContract {

  uint256 public myVariable;

}

//	Solidity编译器会自动为你创建一个名为myVariable的getter函数，你可以像调用普通函数一样调用它来获取myVariable的值：
//	这个getter函数不需要你手动编写，它会在编译时自动创建。
// 这里的contracrInstance 指的是合约实例 的变量名


uint256 value = contractInstance.myVariable();

```



### 链上状态改变 

`导致链上状态改变需要支付gas`



以下行为被视作改变链上状态

>1. 写入状态变量。
>2. 释放事件。
>3. 创建其他合约。
>4. 使用`selfdestruct`.
>5. 通过调用发送以太币。
>6. 调用任何未标记`view`或`pure`的函数。
>7. 使用低级调用（low-level calls）。
>8. 使用包含某些操作码的内联汇编。







## 变量数据存储和作用域





### 三类存储位置

>- storage：
>	- 合约里的状态变量默认都是`storage`，存储在链上
>- memory
>	- 函数里的参数和临时变量一般用`memory`，存储在内存中，不上链
>- calldata
>	- 和`memory`类似，存储在内存中，不上链。与`memory`的不同点在于`calldata`变量不能修改（`immutable`），一般用于函数的参数  



| 存储位置 | 适用于                 |
| -------- | ---------------------- |
| storage  | 合约状态变量默认存储   |
| memory   | 函数参数和临时变量     |
| calldata | 函数的参数（不可修改） |

```solidity
    function fCalldata(uint[] calldata _x) public pure returns(uint[] calldata){
        //参数为calldata数组，不能被修改
        // _x[0] = 0 //这样修改会报错
        return(_x);
    }
```



### 数据位置与赋值规则



>有两种情况：
>
>- 创建引用（修改新变量影响原变量）（指针）
>- 创建副本（相互不影响，只复制值）



`一般理解：`

`同类之间赋值产生引用`

`不同类间赋值产生副本`



| 存储类型 | 赋值给  | 结果     |
| :------- | :------ | :------- |
| memory   | memory  | 引用     |
| memory   | storage | 独立副本 |
| storage  | memory  | 独立副本 |
| storage  | storage | 引用     |
| calldata | storage | 独立副本 |



### 变量类型



>- 状态变量
>- 局部变量
>- 全局变量



| 类型     | 访问                               | gas消耗        |
| -------- | ---------------------------------- | -------------- |
| 状态变量 | 合约内部函数都可以访问（存在链上） | 高             |
| 局部变量 | 函数执行过程中有效（内存中）       | 低             |
| 全局变量 | 全局可调用                         | 取决于如何使用 |



#### 全局变量

`读取` - `200 gas`

`修改` - 全局变量的gas消耗较高。在以太坊中，将存储中的一个位置从零值改为非零值需要消耗`20,000 gas`，将非零值改为另一个非零值需要消耗`5,000 gas`，将非零值改为零值会得到`15,000 gas`的`==退款==。

常用全局变量

>- `blockhash(uint blockNumber)`:
>	-  (`bytes32`)给定区块的哈希值 – 只适用于256最近区块, 不包含当前区块。
>- `block.coinbase`: 
>	- (`address payable`) 当前区块矿工的地址
>- `block.gaslimit`: 
>	- (`uint`) 当前区块的gaslimit
>- `block.number`: (
>	- `uint`) 当前区块的number
>- `block.timestamp`: 
>	- (`uint`) 当前区块的时间戳，为unix纪元以来的秒
>- `gasleft()`: 
>	- (`uint256`) 剩余 gas
>- `msg.data`:
>	-  (`bytes calldata`) 完整call data
>- `msg.sender`: 
>	- (`address payable`) 消息发送者 (当前 caller)
>- `msg.sig`: 
>	- (`bytes4`) calldata的前四个字节 (function identifier)
>- `msg.value`: 
>	- (`uint`) 当前交易发送的`wei`值





## 变量初始值





### 值类型初始值



>- `boolean`: `false`
>- `string`: `""`
>- `int`: `0`
>- `uint`: `0`
>- `enum`: 枚举中的第一个元素
>- `address`: `0x0000000000000000000000000000000000000000` (或 `address(0)`)
>- ``function`
>	- `internal`: 空白方程
>	- `external`: 空白方程



### 引用类型初始值



>- 映射`mapping`: 所有元素都为其默认值的`mapping`
>- 结构体`struct`: 所有成员设为其默认值的结构体
>- 数组`array`
>	- 动态数组: `[]`
>	- 静态数组（定长）: 所有成员设为其默认值的静态数组



### `delete`操作符



- `delete a`会让变量`a`的值变为初始值。

```solidity
// delete操作符
bool public _bool2 = true; 
function d() external {
    delete _bool2; // delete 会让_bool2变为默认值，false
}
```







## 常数





>`constant`（常量）和`immutable`（不变量）。状态变量声明这个两个关键字之后，不能在合约后更改数值；并且还可以节省`gas`。另外，只有数值变量可以声明`constant`和`immutable`；`string`和`bytes`可以声明为`constant`，但不能为`immutable`。





| 属性     | 常量             | 不可变变量         |
| :------- | :--------------- | :----------------- |
| 声明位置 | 任何作用域       | 构造函数           |
| 初始化值 | 必须在编译时确定 | 可以是在运行时确定 |
| gas 消耗 | 高               | 低                 |

### ``constant``



> `constant`变量必须在声明的时候初始化，之后再也不能改变。尝试改变的话，编译不通过。

```solidity
// constant变量必须在声明的时候初始化，之后不能改变
uint256 constant CONSTANT_NUM = 10;
string constant CONSTANT_STRING = "0xAA";
bytes constant CONSTANT_BYTES = "WTF";
address constant CONSTANT_ADDRESS = 0x0000000000000000000000000000000000000000;
```



### ``immutable``

>`immutable`变量可以在声明时或构造函数中初始化，因此更加灵活。



```solidity
// immutable变量可以在constructor里初始化，之后不能改变
uint256 public immutable IMMUTABLE_NUM = 9999999999;
address public immutable IMMUTABLE_ADDRESS;
uint256 public immutable IMMUTABLE_BLOCK;
uint256 public immutable IMMUTABLE_TEST;
```





> 可以使用全局变量例如`address(this)`，`block.number` ，或者自定义的函数给`immutable`变量初始化。在下面这个例子，我们利用了`test()`函数给`IMMUTABLE_TEST`初始化为`9`：



```solidity
// 利用constructor初始化immutable变量，因此可以利用
constructor(){
    IMMUTABLE_ADDRESS = address(this);
    IMMUTABLE_BLOCK = block.number;
    IMMUTABLE_TEST = test();
}

function test() public pure returns(uint256){
    uint256 what = 9;
    return(what);
}
```







## # 控制流



>- `if-else`
>- `for循环`
>- `while循环`
>- `do-while循环`
>- `三元运算符`(solidity 唯一支持3个操作数的运算符)
>- `continue`
>- `break`







## 构造函数和修饰器



> - `constructor`
> - `modifier`



### 构造函数 `constructor`



>每个合约可以定义一个，并在部署合约的时候自动运行一次。它可以用来初始化合约的一些参数



### 修饰器 `modifier`



> 它修饰的函数会带有某些特定的行为。`modifier`的主要使用场景是运行函数前的检查，例如地址，变量，余额等



下面定义了一个`changeOwner`函数，运行他可以改变合约的`owner`，但是由于`onlyOwner`修饰符的存在，只有原先的`owner`可以调用，别人调用就会报错。这也是最常用的控制智能合约权限的方法。

```solidity
 // 定义modifier
   modifier onlyOwner {
      require(msg.sender == owner); // 检查调用者是否为owner地址
      _; // 如果是的话，继续运行函数主体；否则报错并revert交易
   }


   function changeOwner(address _newOwner) external onlyOwner{
      owner = _newOwner; // 只有owner地址运行这个函数，并改变owner
   }
```





## 事件



`Solidity`中的事件（`event`）是`EVM`上日志的抽象，它具有两个特点：

- 响应：应用程序（[`ethers.js`](https://learnblockchain.cn/docs/ethers.js/api-contract.html#id18)）可以通过`RPC`接口订阅和监听这些事件，并在前端做响应。
- 经济：事件是`EVM`上比较经济的存储数据的方式，每个大概消耗2,000 `gas`；相比之下，链上存储一个新变量至少需要20,000 `gas`。



==``ERC20代币``==

### 声明事件



>事件的声明由`event`关键字开头，接着是事件名称，括号里面写好事件需要记录的变量类型和变量名。以`ERC20`代币合约的`Transfer`事件为例：

```solidity
event Transfer(address indexed from, address indexed to, uint256 value);
```

`Transfer`事件共记录了3个变量`from`，`to`和`value`，分别对应代币的转账地址，接收地址和转账数量，其中`from`和`to`前面带有`indexed`关键字，他们会保存在以太坊虚拟机日志的`topics`中，方便之后检索。



### 释放事件



> 我们可以在函数里释放事件。在下面的例子中，每次用`_transfer()`函数进行转账操作的时候，都会释放`Transfer`事件，并记录相应的变量。

```solidity
    // 定义_transfer函数，执行转账逻辑
    function _transfer(
        address from,
        address to,
        uint256 amount
    ) external {

        _balances[from] = 10000000; // 给转账地址一些初始代币

        _balances[from] -=  amount; // from地址减去转账数量
        _balances[to] += amount; // to地址加上转账数量

        // 释放事件
        emit Transfer(from, to, amount);
    }
```



### EVM日志 `Log`



> 以太坊虚拟机（EVM）用日志`Log`来存储`Solidity`事件，每条日志记录都包含主题`topics`和数据`data`两部分。



```
Address Ox6b175474e89094c44da98b954eedeac495271dof

Name Transfer (index_topic_1 address src， index_topic_2address dst， uint256 wad ) View Source

Topicso 

0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef

Dec v →0×8c855186f3f3dda4fa8fd08cbe1525914835eb8e

Dec v →0x0000000000000000000000000000000000000000

Data
0x000000000000000000000000000000000000000000005adcee912a2548b65cd0

```



### 主题 `Topics`

> 日志的第一部分是主题数组，用于描述事件，长度不能超过`4`。它的第一个元素是事件的签名（哈希）。对于上面的`Transfer`事件，它的签名就是：

```solidity
keccak256("Transfer(addrses,address,uint256)")

//0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef
```



> 除了事件签名，主题还可以包含至多`3`个`indexed`参数，也就是`Transfer`事件中的`from`和`to`。

> `indexed`标记的参数可以理解为检索事件的索引“键”，方便之后搜索。每个 `indexed` 参数的大小为固定的256比特，如果参数太大了（比如字符串），就会自动计算哈希存储在主题中。



### 数据 `Data`

> 事件中不带 `indexed`的参数会被存储在 `data` 部分中，可以理解为事件的“值”。`data` 部分的变量不能被直接检索，但可以存储任意大小的数据。因此一般 `data` 部分可以用来存储复杂的数据结构，例如数组和字符串等等，因为这些数据超过了256比特，即使存储在事件的 `topic` 部分中，也是以哈希的方式存储。另外，`data` 部分的变量在存储上消耗的gas相比于 `topic` 更少。



### 在etherscan上查询事件

> 我们尝试用`_transfer()`函数在`Rinkeby`测试网络上转账100代币，可以在`etherscan`上查询到相应的`tx`：[网址](https://rinkeby.etherscan.io/tx/0x8cf87215b23055896d93004112bbd8ab754f081b4491cb48c37592ca8f8a36c7)。

点击`Logs`按钮，就能看到事件明细：

![gx6_wDMYEl8_Gc_JkTIKn](C:\Users\01\Desktop\智能合约工程师\Smart-contract\img\gx6_wDMYEl8_Gc_JkTIKn.png)

> `Topics`里面有三个元素，`[0]`是这个事件的哈希，`[1]`和`[2]`是我们定义的两个`indexed`变量的信息，即转账的转出地址和接收地址。`Data`里面是剩下的不带`indexed`的变量，也就是转账数量



其他链上分析工具：

- `Nansen`

- ``Dune Analysis``





## 继承



### 规则

- `virtual`: 父合约中的函数，如果希望子合约重写，需要加上`virtual`(拟定)关键字。
- `override`：子合约重写了父合约中的函数，需要加上`override`关键字。





>- 父合约中的函数，如果希望子合约重写，需要加上virtual关键字。
>- 子合约重写了父合约中的函数，需要加上override关键字。
>- 继承时要按辈分最高到最低的顺序排。 
>- 如果某一个函数在多个继承的合约里都存在，在子合约里必须重写，不然会报错。 
>- 重写在多个父合约中都重名的函数时，override关键字后面要加上所有父合约名字。



#### 注意 ：

- 用`override`修饰`public`变量，会重写与变量同名的`getter`函数，

```solidity
mapping(address => uint256) public override balanceOf;
```



### 简单继承



``语法``:

继承语法`contract Baba is Yeye`，非常直观。在`Baba`合约里，我们重写一下`hip()`和`pop()`这两个函数，加上`override`关键字，并将他们的输出改为`”Baba”`；并且加一个新的函数`baba`，输出也是`”Baba”`。

````solidity
contract Yeye {
    event Log(string msg);

    // 定义3个function: hip(), pop(), man()，Log值为Yeye。
    function hip() public virtual{
        emit Log("Yeye");
    }

    function pop() public virtual{
        emit Log("Yeye");
    }

    function yeye() public virtual {
        emit Log("Yeye");
    }
}

contract Baba is Yeye{
    // 继承两个function: hip()和pop()，输出改为Baba。
    function hip() public virtual override{
        emit Log("Baba");
    }

    function pop() public virtual override{
        emit Log("Baba");
    }

    function baba() public virtual{
        emit Log("Baba");
    }
}
````





### 多重继承



`solidity`的合约可以继承多个合约。规则：



>1. 继承时要按辈分最高到最低的顺序排。比如我们写一个`Erzi`合约，继承`Yeye`合约和`Baba`合约，那么就要写成`contract Erzi is Yeye, Baba`，而不能写成`contract Erzi is Baba, Yeye`，不然就会报错。
>2. 如果某一个函数在多个继承的合约里都存在，比如例子中的`hip()`和`pop()`，在子合约里必须重写，不然会报错。
>3. 重写在多个父合约中都重名的函数时，`override`关键字后面要加上所有父合约名字，例如`override(Yeye, Baba)`





```solidity
contract Erzi is Yeye, Baba{
    // 继承两个function: hip()和pop()，输出值为Erzi。
    function hip() public virtual override(Yeye, Baba){
        emit Log("Erzi");
    }

    function pop() public virtual override(Yeye, Baba) {
        emit Log("Erzi");
    }
```



### 修饰器的继承

`Solidity`中的修饰器（`Modifier`）同样可以继承，用法与函数继承类似，在相应的地方加`virtual`和`override`关键字即可。

```solidity
contract Base1 {
    modifier exactDividedBy2And3(uint _a) virtual {
        require(_a % 2 == 0 && _a % 3 == 0);
        _;
    }
}

contract Identifier is Base1 {

    //计算一个数分别被2除和被3除的值，但是传入的参数必须是2和3的倍数
    function getExactDividedBy2And3(uint _dividend) public exactDividedBy2And3(_dividend) pure returns(uint, uint) {
        return getExactDividedBy2And3WithoutModifier(_dividend);
    }

    //计算一个数分别被2除和被3除的值
    function getExactDividedBy2And3WithoutModifier(uint _dividend) public pure returns(uint, uint){
        uint div2 = _dividend / 2;
        uint div3 = _dividend / 3;
        return (div2, div3);
    }
}

//Identifier合约可以直接在代码中使用父合约中的exactDividedBy2And3修饰器，也可以利用override关键字重写修饰器：

    modifier exactDividedBy2And3(uint _a) override {
        _;
        require(_a % 2 == 0 && _a % 3 == 0);
    }
```



版本对比

```solidity
contract Base1 {
    modifier exactDividedBy2And3(uint _a) virtual {
        require(_a % 2 == 0 && _a % 3 == 0);
        _;
    }
}

 modifier exactDividedBy2And3(uint _a) override {
        _;
        require(_a % 2 == 0 && _a % 3 == 0);
    }
```



>前者会在执行修饰器之后再执行函数
>
>后者会先执行函数后执行修饰器检查



### 构造函数的继承

> 子合约有两种方法继承父合约的构造函数。举个简单的例子，父合约`A`里面有一个状态变量`a`，并由构造函数的参数来确定：

```solidity
// 构造函数的继承
abstract contract A {
    uint public a;

    constructor(uint _a) {
        a = _a;
    }
}
```

1. > 在继承时声明父构造函数的参数，例如：`contract B is A(1)`

2. > 在子合约的构造函数中声明构造函数的参数，例如：

```solidity
contract C is A {
    constructor(uint _c) A(_c * _c) {}
}
```





### 调用父合约的函数

> 子合约有两种方式调用父合约的函数，直接调用和利用`super`关键字。

1. 直接调用：子合约可以直接用`父合约名.函数名()`的方式来调用父合约函数，例如`Yeye.pop()`。

```solidity
    function callParent() public{
        Yeye.pop();
    }
```



2. `super`关键字：子合约可以利用`super.函数名()`来调用最近的父合约函数。`solidity`继承关系按声明时从右到左的顺序是：`contract Erzi is Yeye, Baba`，那么`Baba`是最近的父合约，`super.pop()`将调用`Baba.pop()`而不是`Yeye.pop()`：

```solidity
    function callParentSuper() public{
        // 将调用最近的父合约函数，Baba.pop()
        super.pop();
    }
```





### 钻石继承



```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

/* 继承树：
  God
 /  \
Adam Eve
 \  /
people
*/
```

```solidity
contract God {
    event Log(string message);

    function foo() public virtual {
        emit Log("God.foo called");
    }

    function bar() public virtual {
        emit Log("God.bar called");
    }
}

contract Adam is God {
    function foo() public virtual override {
        emit Log("Adam.foo called");
    }

    function bar() public virtual override {
        emit Log("Adam.bar called");
        super.bar();
    }
}

contract Eve is God {
    function foo() public virtual override {
        emit Log("Eve.foo called");
        Eve.foo();
    }

    function bar() public virtual override {
        emit Log("Eve.bar called");
        super.bar();
    }
}

contract people is Adam, Eve {
    function foo() public override(Adam, Eve) {
        super.foo();
    }

    function bar() public override(Adam, Eve) {
        super.bar();
    }
}

```

在这个例子中，调用合约`people`中的`super.bar()`会依次调用`Eve`、`Adam`，最后是`God`合约。

具体细节：https://solidity-cn.readthedocs.io/zh/develop/contracts.html?highlight=%E7%BB%A7%E6%89%BF#index-16





## 抽象合约和接口



### 抽象合约

#### 抽象合约规则

- 必须将该合约标为`abstract`
- 智能合约里至少有一个未实现的函数(函数缺少主体`{}`中的内容)





### 接口

#### 接口规则

> 接口似于抽象合约，但它不实现任何功能。接口的规则：

1. 不能包含状态变量
2. 不能包含构造函数
3. 不能继承除接口外的其他合约
4. 所有函数都必须是external且不能有函数体
5. 继承接口的合约必须实现接口定义的所有功能
6. 

#### 接口的重要性

> 虽然接口不实现任何功能，但它非常重要。接口是智能合约的骨架，定义了合约的功能以及如何触发它们：如果智能合约实现了某种接口（比如`ERC20`或`ERC721`），其他Dapps和智能合约就知道如何与它交互。因为接口提供了两个重要的信息：

1. 合约里每个函数的`bytes4`选择器，以及函数签名`函数名(每个参数类型）`。
2. 接口id（更多信息见[EIP165](https://eips.ethereum.org/EIPS/eip-165)）

> 另外，接口与合约`ABI`（Application Binary Interface）等价，可以相互转换：编译接口可以得到合约的`ABI`，利用[abi-to-sol工具](https://gnidan.github.io/abi-to-sol/)也可以将`ABI json`文件转换为`接口sol`文件。



### ERC721

```solidity
interface IERC721 is IERC165 {

    event Transfer(address indexed from, address indexed to, uint256 indexed tokenId);
    event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);
    event ApprovalForAll(address indexed owner, address indexed operator, bool approved);

    function balanceOf(address owner) external view returns (uint256 balance);

    function ownerOf(uint256 tokenId) external view returns (address owner);

    function safeTransferFrom(address from, address to, uint256 tokenId) external;

    function transferFrom(address from, address to, uint256 tokenId) external;

    function approve(address to, uint256 tokenId) external;

    function getApproved(uint256 tokenId) external view returns (address operator);

    function setApprovalForAll(address operator, bool _approved) external;

    function isApprovedForAll(address owner, address operator) external view returns (bool);

    function safeTransferFrom( address from, address to, uint256 tokenId, bytes calldata data) external
    
}
```


#### IERC721事件

`IERC721`包含3个事件，其中`Transfer`和`Approval`事件在`ERC20`中也有。

- `Transfer`事件：在转账时被释放，记录代币的发出地址`from`，接收地址`to`和`tokenid`。
- `Approval`事件：在授权时释放，记录授权地址`owner`，被授权地址`approved`和`tokenid`。
- `ApprovalForAll`事件：在批量授权时释放，记录批量授权的发出地址`owner`，被授权地址`operator`和授权与否的`approved`。

#### IERC721函数

- `balanceOf`：返回某地址的NFT持有量`balance`。
- `ownerOf`：返回某`tokenId`的主人`owner`。
- `transferFrom`：普通转账，参数为转出地址`from`，接收地址`to`和`tokenId`。
- `safeTransferFrom`：安全转账（如果接收方是合约地址，会要求实现`ERC721Receiver`接口）。参数为转出地址`from`，接收地址`to`和`tokenId`。
- `approve`：授权另一个地址使用你的NFT。参数为被授权地址`approve`和`tokenId`。
- `getApproved`：查询`tokenId`被批准给了哪个地址。
- `setApprovalForAll`：将自己持有的该系列NFT批量授权给某个地址`operator`。
- `isApprovedForAll`：查询某地址的NFT是否批量授权给了另一个`operator`地址。
- `safeTransferFrom`：安全转账的重载函数，参数里面包含了`data`。



### 什么时候使用接口？

如果我们知道一个合约实现了`IERC721`接口，我们不需要知道它具体代码实现，就可以与它交互。

==例子==



## 异常



