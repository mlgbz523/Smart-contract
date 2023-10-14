## 数值类型

### 布尔型 bool

### 整形

```solidity
int 
unint //正整数
unint256 //256
```

### 地址类型

```solidity
地址类型(address)存储一个 20 字节的值（以太坊地址的大小）
有普通的地址和可以转账ETH的地址（payable)
// 地址
    address public _address = 0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;
    address payable public _address1 = payable(_address); // payable address，可以转账、查余额
    // 地址类型的成员
    uint256 public balance = _address1.balance; // balance of addres
```



### 定长字节数组

```solidity
字节数组bytes分两种，一种定长（byte, bytes8, bytes32）
    // 固定长度的字节数组
    bytes32 public _byte32 = "MiniSolidity"; 
    bytes1 public _byte = _byte32[0]; 
    
    
    在Solidity中，bytesN类型是一个N字节长的字节序列。因此，bytes4是一个4字节长的字节序列。由于每个字节由两个16进制位表示，所以bytes4类型总共有8个16进制位。

```

### 枚举enum

​	``

```solidity
    // 用enum将uint 0， 1， 2表示为Buy, Hold, Sell
    enum ActionSet { Buy, Hold, Sell }
    // 创建enum变量 action
    ActionSet action = ActionSet.Buy;
```



## Solidity中的函数

```solidity
  function <function name>(<parameter types>) {internal|external|public|private} [pure|view|payable] [returns (<return types>)]
```

==方括号中的是可写可不写的关键字==



1. `function`：声明函数时的固定用法，想写函数，就要以function关键字开头。

2. `<function name>`：函数名。

3. `(<parameter types>)`：圆括号里写函数的参数，也就是要输入到函数的变量类型和名字。

4. `{internal|external|public|private}`：函数可见性说明符，一共4种。没标明函数类型的，默认`public`。合约之外的函数，即"自由函数"，始终具有隐含`internal`可见性。

	- `public`: 内部外部均可见。
	- `private`: 只能从本合约内部访问，继承的合约也不能用。
	- `external`: 只能从合约外部访问（但是可以用`this.f()`来调用，`f`是函数名）。
	- `internal`: 只能从合约内部访问，继承的合约可以用。

	**Note 1**: 没有标明可见性类型的函数，默认为`public`。

	**Note 2**: `public|private|internal` 也可用于修饰状态变量。 `public`变量会自动生成同名的`getter`函数，用于查询数值。

	**Note 3**: 没有标明可见性类型的状态变量，默认为`internal`。

5. `[pure|view|payable]`：决定函数权限/功能的关键字。`payable`（可支付的）很好理解，带着它的函数，运行的时候可以给合约转入`ETH`。`pure`和`view`的介绍见下一节。

6. `[returns ()]`：函数返回的变量类型和名称。



```solidity
    // pure: 纯纯牛马
    function addPure(uint256 _number) external pure returns(uint256 new_number){
        new_number = _number + 1;
    }
    // view: 看客
    function addView() external view returns(uint256 new_number) {
        new_number = number + 1;
    }


    // internal: 内部
    function minus() internal {
        number = number - 1;
    }

    // 合约内的函数可以调用内部函数
    function minusCall() external {
        minus();
    }
    
        // payable: 递钱，能给合约支付eth的函数
    function minusPayable() external payable returns(uint256 balance) {
        minus();    
        balance = address(this).balance;
    }
```



>
>
>在以太坊中，以下语句被视为修改链上状态：
>
>1. 写入状态变量。
>2. 释放事件。
>3. 创建其他合约。
>4. 使用`selfdestruct`.
>5. 通过调用发送以太币。
>6. 调用任何未标记`view`或`pure`的函数。
>7. 使用低级调用（low-level calls）。
>8. 使用包含某些操作码的内联汇编。

## 返回值 return和returns

- `returns`加在函数名后面，用于声明返回的变量类型及变量名；
- `return`用于函数主体中，返回指定的变量。

>
>
>    // 返回多个变量
>    function returnMultiple() public pure returns(uint256, bool, uint256[3] memory){
>            return(1, true, [uint256(1),2,5]);
>        }

我们可以在`returns`中标明返回变量的名称，这样`solidity`会自动给这些变量初始化，并且自动返回这些函数的值，不需要加`return`。

>
>
>    // 命名式返回
>    function returnNamed() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
>        _number = 2;
>        _bool = false; 
>        _array = [uint256(3),2,1];
>    }

当然，你也可以在命名式返回中用`return`来返回变量：

```solidity
    // 命名式返回，依然支持return
    function returnNamed2() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
        return(1, true, [uint256(1),2,5]);
    }
```



## 解构式赋值

`solidity`使用解构式赋值的规则，支持读取函数的全部或部分返回值。

- 读取所有返回值：声明变量，并且将要赋值的变量用`,`隔开，按顺序排列。
- 读取部分返回值：声明要读取的返回值对应的变量，不读取的留空。下面这段代码中，我们只读取`_bool`，而不读取返回的`_number`和`_array`：

```

        uint256 _number;
        bool _bool;
        uint256[3] memory _array;
        (_number, _bool, _array) = returnNamed();
        
        
        
                (, _bool2, ) = returnNamed();

```

## Solidity中的引用类型

**引用类型(Reference Type)**：包括数组（`array`），结构体（`struct`）和映射（`mapping`）

赋值时候直接传递地址（类似指针）



## 数据位置

solidity数据存储位置有三类：`storage`，`memory`和`calldata`。不同存储位置的`gas`成本不同。

`storage`类型的数据存在链上，类似计算机的硬盘，消耗`gas`多；`memory`和`calldata`类型的临时存在内存里，消耗`gas`少

1. `storage`：合约里的状态变量默认都是`storage`，存储在链上。
2. `memory`：函数里的参数和临时变量一般用`memory`，存储在内存中，不上链。
3. `calldata`：和`memory`类似，存储在内存中，不上链。与`memory`的不同点在于`calldata`变量不能修改（`immutable`），一般用于函数的参数。例子：

```solidity
function fCalldata(uint[] calldata _x) public pure returns(uint[] calldata){
    //参数为calldata数组，不能被修改
    // _x[0] = 0 //这样修改会报错
    return(_x);
}
```



## 变量的作用域

`Solidity`中变量按作用域划分有三种，分别是

状态变量（state variable）

局部变量（local variable）

全局变量(global variable)





| 存储位置 | 数据存储方式       | 存储范围                               | gas 成本 |
| :------- | :----------------- | :------------------------------------- | :------- |
| storage  | 永久存储在区块链上 | 合约部署后一直存在                     | 高       |
| memory   | 临时存储在内存中   | 函数调用期间存在                       | 低       |
| calldata | 临时存储在内存中   | 函数调用时从调用方传递给被调用方的数据 | 低       |



以下是 memory 和计算机内存的具体区别：

| 特性     | memory       | 计算机内存   |
| :------- | :----------- | :----------- |
| 存储位置 | 内存         | 内存         |
| 存储范围 | 函数调用期间 | 程序运行期间 |
| gas 成本 | 低           | 高           |
| 访问速度 | 快           | 快           |
