---
name: fix-admin-nav-hide
overview: 从 index.html 单文件版中移除"管理"导航入口，保持与 Header.tsx 一致
todos:
  - id: remove-admin-nav
    content: 从 index.html 导航栏移除"管理"入口
    status: completed
  - id: add-password-validation
    content: 为 index.html 密码修改添加当前密码验证
    status: completed
---

## 用户需求

1. 隐藏导航页"管理"入口
2. 管理员后台登录界面的修改密码功能需要设置权限验证

## 现状确认

项目有两个版本：

- `src/components/Header.tsx` - React版（管理已隐藏 ✅）
- `index.html` - 单文件版（管理未隐藏，密码修改无验证 ❌）

用户访问的是 `index.html` 单文件版，第811行还保留着管理入口。

## 核心功能

1. 移除导航栏"管理"按钮
2. 密码修改增加三重验证：当前密码 + 新密码至少6位 + 确认密码一致性

## 技术方案

修改 `index.html` 单文件版（用户实际访问的版本）

### 修改1：隐藏管理导航

删除 `navItems` 数组中的 `{ id: 'admin', label: '管理', icon: 'settings' }` 条目

### 修改2：密码修改权限验证

参考 React 版的实现，为单文件版添加：

- 新增状态：`currentPwd`（当前密码）、`confirmPwd`（确认密码）、`pwdError`（错误提示）
- 更新 `handleSetPassword` 验证逻辑：
- 验证当前密码是否正确
- 新密码至少6位
- 确认密码与新密码一致
- 更新弹窗UI：添加当前密码输入框、确认密码输入框、错误提示显示