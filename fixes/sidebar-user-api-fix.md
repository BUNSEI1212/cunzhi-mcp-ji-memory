# Sidebar用户信息API串通修复

## 问题描述
Sidebar组件左下角的用户信息完全是硬编码的虚拟数据，未与后端API串通，导致显示不一致。

### 硬编码的虚拟数据
- 用户名：`"创作者用户"`
- 套餐：`"高级创作套餐"`  
- 配额：`"2.3K / 10K"`
- 角色标识：固定显示 `"PRO"`

## 修复方案

### 1. 导入用户API Hook
```typescript
import { useCurrentUser } from '@/hooks/useNewAPI'
```

### 2. 获取真实用户数据
```typescript
const { data: currentUser, isLoading: userLoading, error: userError } = useCurrentUser()
```

### 3. 替换硬编码数据

#### 用户名显示
```typescript
{currentUser.displayName || currentUser.username}
```

#### 角色标识
```typescript
{currentUser.role === 100 ? 'SUPER' : currentUser.role === 10 ? 'ADMIN' : 'USER'}
```

#### 配额显示
```typescript
{currentUser.usedQuota ? `${(currentUser.usedQuota / 1000000).toFixed(2)}M` : '0M'} / 
{currentUser.quota ? `${(currentUser.quota / 1000000).toFixed(2)}M` : '无限制'}
```

#### 配额进度条
```typescript
width: currentUser.quota 
  ? `${Math.min((currentUser.usedQuota / currentUser.quota) * 100, 100)}%` 
  : '0%'
```

### 4. 用户头像
```typescript
{(currentUser.displayName || currentUser.username)?.charAt(0)?.toUpperCase() || 'U'}
```

### 5. 加载状态处理
```typescript
{userLoading ? (
  <div className="flex items-center space-x-2">
    <ClientIcon icon={Loader2} className="w-4 h-4 text-orange-600 animate-spin" />
    <p className="text-sm text-orange-700">加载中...</p>
  </div>
) : currentUser ? (
  // 显示真实用户数据
) : (
  <div className="text-xs text-orange-700">
    <p>未登录用户</p>
  </div>
)}
```

## 修复效果
- ✅ 左下角用户信息与右上角一致显示真实数据
- ✅ 实时显示用户配额使用情况
- ✅ 正确显示用户角色和权限等级
- ✅ 支持加载状态和错误处理
- ✅ 头像显示用户名首字母

## 后续改进
- 可考虑添加用户头像图片上传功能
- 可添加用户状态（在线/离线）检测
- 可优化配额显示格式（如单位自动选择）