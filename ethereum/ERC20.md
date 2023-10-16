## ERC 20



```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// ERC-20 标准接口
interface IERC20 {
    // 获取总供应量
    function totalSupply() external view returns (uint256);
    // 获取指定地址的代币余额
    function balanceOf(address account) external view returns (uint256);

    // 获取 owner 地址授权给 spender 地址的代币数量
    function allowance(address owner, address spender) external view returns (uint256);

    // 转账代币到指定地址
    function transfer(address to, uint256 value) external returns (bool);

    // 授权 spender 地址能够从调用者账户转移指定数量的代币
    function approve(address spender, uint256 value) external returns (bool);

    // 从 from 地址转移代币到 to 地址
    function transferFrom(address from, address to, uint256 value) external returns (bool);

    // 代币转移事件
    event Transfer(address indexed from, address indexed to, uint256 value);

    // 授权事件
    event Approval(address indexed owner, address indexed spender, uint256 value);
    
}
```