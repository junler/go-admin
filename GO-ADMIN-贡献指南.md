# Go-Admin 贡献指南

## 📖 项目简介

Go-Admin 是一个用Golang编写的数据可视化与管理平台构建框架，灵感来源于laravel-admin。它可以帮助你的golang应用快速实现数据可视化，搭建一个数据管理平台。

**项目特色：**
- 🚀 **高生产效率**: 10分钟内做一个好看的管理后台
- 🎨 **主题系统**: 默认为adminlte，支持多种主题
- 🔢 **插件化架构**: 真正的插件化设计
- ✅ **RBAC认证**: 开箱即用的权限控制系统
- ⚙️ **多框架支持**: 支持Gin、Echo、Beego、Iris等主流框架

## 🎯 贡献方式

我们欢迎各种形式的贡献：

- **Bug修复** - 发现并修复项目中的错误
- **功能开发** - 添加新功能或改进现有功能
- **文档完善** - 改进项目文档和示例
- **主题开发** - 开发新的UI主题
- **插件开发** - 创建实用的插件
- **翻译工作** - 帮助项目国际化
- **性能优化** - 提升项目性能

## 🚀 快速开始

### 环境要求

- **Go版本**: 1.22.10+
- **Git**: 用于代码版本控制
- **GitHub账号**: 用于提交代码
- **IDE**: 推荐VSCode、GoLand等

### 第一步：Fork 项目

1. 访问 [Go-Admin GitHub页面](https://github.com/GoAdminGroup/go-admin)
2. 点击右上角的 "Fork" 按钮
3. 选择你的GitHub账号，完成Fork

### 第二步：克隆项目到本地

```bash
# 克隆你fork的仓库
git clone git@github.com:YOUR_USERNAME/go-admin.git

# 进入项目目录
cd go-admin

# 添加上游仓库
git remote add upstream https://github.com/GoAdminGroup/go-admin.git

# 验证远程仓库配置
git remote -v
```

### 第三步：同步最新代码

```bash
# 获取上游最新代码
git fetch upstream

# 切换到主分支
git checkout main

# 合并上游代码
git merge upstream/main

# 推送到你的fork
git push origin main
```

## 💻 开发流程

### 1. 创建功能分支

```bash
# 基于最新的main分支创建新分支
git checkout main
git pull upstream main
git checkout -b feature/your-feature-name

# 或者修复bug分支
git checkout -b fix/your-bug-description
```

**分支命名规范：**
- `feature/功能描述` - 新功能开发
- `fix/bug描述` - Bug修复
- `docs/文档描述` - 文档更新
- `refactor/重构描述` - 代码重构
- `perf/性能优化描述` - 性能优化

### 2. 进行开发

```bash
# 安装依赖
go mod tidy

# 进行代码开发...
# 修改、添加、删除文件

# 查看修改状态
git status
git diff
```

### 3. 提交代码

```bash
# 添加修改的文件
git add .

# 提交代码（遵循提交规范）
git commit -m "feat: 添加用户管理功能

- 实现用户CRUD操作
- 添加用户权限控制
- 完善用户界面交互

Closes #123" -s

# 推送到你的fork
git push origin feature/your-feature-name
```

### 4. 提交 Pull Request

1. 访问你的GitHub仓库页面
2. 点击 "Compare & pull request" 按钮
3. 填写PR标题和详细描述
4. 等待代码审查

## 📝 代码规范

### Go代码规范

```go
// 包注释
// Package admin 提供管理后台核心功能
package admin

// 函数注释要清晰说明功能
// CreateUser 创建新用户
// 参数:
//   - name: 用户名
//   - email: 邮箱地址
// 返回:
//   - *User: 创建的用户对象
//   - error: 错误信息
func CreateUser(name, email string) (*User, error) {
    // 实现代码...
}
```

### 目录结构规范

```
go-admin/
├── engine/          # 核心引擎
├── modules/         # 功能模块
│   ├── auth/       # 认证模块
│   ├── db/         # 数据库模块
│   └── ui/         # UI模块
├── plugins/         # 插件系统
├── adapter/         # 框架适配器
├── template/        # 模板文件
└── examples/        # 示例代码
```

### 提交信息规范

```
<类型>(<范围>): <描述>

[正文]

[页脚]
```

**类型说明：**
- `feat`: 新功能
- `fix`: Bug修复
- `docs`: 文档更新
- `style`: 代码格式修改
- `refactor`: 代码重构
- `perf`: 性能优化
- `test`: 测试相关
- `chore`: 构建工具或辅助工具的变动

**示例：**
```
feat(auth): 添加用户登录功能

- 实现用户名密码登录
- 添加记住登录状态功能
- 完善登录错误提示

Closes #456
```

## 🧪 测试要求

### 运行现有测试

```bash
# 运行全部测试
make test

# 运行局部单元测试
make unit-test

# 运行单元测试（不需要数据库）
go test ./modules/...
go test ./plugins/...

# 运行特定模块测试
go test ./modules/auth/...
```

### 添加新测试

为新功能编写测试用例：

```go
func TestCreateUser(t *testing.T) {
    // 测试用例实现
    user, err := CreateUser("test", "test@example.com")
    assert.NoError(t, err)
    assert.Equal(t, "test", user.Name)
}
```

## 🏗️ 项目架构理解

### 核心组件

1. **引擎层** (`engine/`) - 系统核心，负责初始化和配置
2. **适配器层** (`adapter/`) - Web框架适配，支持多种框架
3. **模块层** (`modules/`) - 功能模块，包含各种业务逻辑
4. **插件层** (`plugins/`) - 插件系统，支持功能扩展

### 数据流

```
用户请求 → Web框架适配器 → 路由处理 → 权限验证 → 业务逻辑 → 数据访问 → 响应返回
```

## ❓ 常见问题

### Q: 如何运行示例项目？
A: 进入 `examples/gin/` 目录，配置数据库后运行 `go run main.go`

### Q: 测试时数据库连接失败怎么办？
A: 如果只是修复小bug，可以跳过需要数据库的集成测试，专注于单元测试

### Q: 如何添加新的Web框架支持？
A: 在 `adapter/` 目录下参考现有适配器实现新的适配器

### Q: 如何开发新插件？
A: 参考 `plugins/example/` 示例，实现插件接口

### Q: 代码审查被拒绝怎么办？
A: 根据审查意见修改代码，在同一分支上提交新的commit即可

## 🔄 代码审查失败后的处理

如果你的PR在代码审查中被要求修改：

```bash
# 1. 在原分支上继续修改
git checkout your-feature-branch

# 2. 进行代码修改...

# 3. 提交修改（建议使用 fixup commit）
git add .
git commit --fixup HEAD  # 或者正常commit

# 4. 推送更新（PR会自动更新）
git push origin your-feature-branch
```

## 🌍 社区交流

- **官方文档**: [http://doc.go-admin.cn/zh](http://doc.go-admin.cn/zh)
- **在线演示**: [https://demo.go-admin.cn](https://demo.go-admin.cn)
- **问题反馈**: [GitHub Issues](https://github.com/GoAdminGroup/go-admin/issues)
- **讨论论坛**: [http://discuss.go-admin.com](http://discuss.go-admin.com)
- **QQ群**: 694446792（请备注加群来意）
- **Telegram**: [加入群组](https://t.me/joinchat/NlyH6Bch2QARZkArithKvg)

## 📋 检查清单

提交PR前请确认：

- [ ] 代码符合Go语言规范
- [ ] 已添加必要的注释和文档
- [ ] 已编写或更新相关测试
- [ ] 提交信息格式正确
- [ ] 已同步最新的上游代码
- [ ] 本地测试通过
- [ ] PR描述清晰详细

## 🔧 实际Bug修复示例

以下是一个真实的bug修复案例，展示了完整的贡献流程：

### Bug描述
在 template/types/info.go 的 GetFilterFormFields 方法中，有这样一段代码：
```go
if filter.Operator.AddOrNot() {
    ff := headField + parameter.FilterParamOperatorSuffix + keySuffix
    filterForm = append(filterForm, FormField{
        Field:      ff,
        FieldClass: ff,
        Head:       f.Head,
        TypeName:   f.TypeName,
        Value:      template.HTML(filter.Operator.Value()),
        FormType:   filter.Type,
        Hide:       true,
    })
}
```

当使用 `FieldFilterable(types.FilterType{Operator: types.FilterOperatorLike})` 时，页面会错误地显示一个 "name like" 的form-group查询表单，但这个表单不应该出现。

### 问题分析
通过代码分析发现，在 `template/types/operators.go` 中：

1. **UI显示问题**: `Label()` 方法对 `FilterOperatorLike` 返回空字符串，但 `AddOrNot()` 返回 `true`
2. **功能性问题**: 系统仍然创建了隐藏的操作符字段，但UI渲染时可能显示异常
3. **SQL查询依赖**: 后端 SQL 查询生成依赖于这个隐藏的操作符字段来确定使用哪个操作符

### 修复方案
我们的最终修复策略采用了一个全新的系统架构方法：

**问题根源**：Like操作符需要在两个层面正常工作：
- **UI层面**：不应该显示额外的操作符选择字段
- **后端层面**：必须能够生成正确的SQL查询（`LIKE '%value%'`）

**修复策略**：
1. **修改 `template/types/operators.go`** - 让Like操作符不生成UI字段：
   ```go
   func (o FilterOperator) AddOrNot() bool {
       return string(o) != "" && o != FilterOperatorFree && o != FilterOperatorLike
   }
   ```

2. **在 `plugins/admin/modules/parameter/parameter.go`** 中创建Like操作符字段的全局注册表：
   ```go
   // Global registry for Like operator fields
   var likeOperatorFields = make(map[string]bool)

   // RegisterLikeOperatorField registers a field as using the Like operator
   func RegisterLikeOperatorField(fieldName string) {
       likeOperatorFields[fieldName] = true
   }

   // IsLikeOperatorField checks if a field is registered as using the Like operator
   func IsLikeOperatorField(fieldName string) bool {
       return likeOperatorFields[fieldName]
   }
   ```

3. **修改SQL查询逻辑** - 在`Statement`方法中检查Like操作符字段注册表：
   ```go
   } else if !strings.Contains(key, FilterParamOperatorSuffix) {
       // Check if this field is registered as a Like operator field
       if IsLikeOperatorField(key) {
           op = "like"
       } else {
           op = operators[param.GetFieldOperator(key, keyIndexSuffix)]
       }
   }
   ```

4. **在 `template/types/info.go`** 中自动注册Like操作符字段：
   ```go
   // Register Like operator fields for global tracking
   if filter.Operator == FilterOperatorLike {
       parameter.RegisterLikeOperatorField(headField)
   }
   ```

### 技术创新
这个修复方案的精妙之处在于：
- **分离关注点**：UI显示逻辑和SQL查询逻辑完全分离
- **自动化注册**：开发者无需手动注册Like字段，系统自动识别并注册
- **向后兼容**：不影响其他操作符的现有功能
- **优雅设计**：通过全局注册表机制，避免了隐藏字段的UI副作用

### 验证结果
- ✅ 所有测试通过
- ✅ Like操作符不显示UI操作符字段
- ✅ Like操作符能正确生成SQL查询（自动添加通配符`%value%`）
- ✅ 其他操作符功能不受影响

### 提交流程
```bash
# 1. 创建修复分支
git checkout -b fix/filter-operator-like-ui-display

# 2. 修改代码并添加测试
# 3. 提交修改
git add .
git commit -m "fix: 修复FilterOperatorLike的UI显示问题同时保持功能正确生成Like的sql语句

- 保持AddOrNot()返回true以确保后端SQL查询正常工作
- Label()返回空字符串避免UI显示多余的操作符标签
- UI层面：Like操作符不再显示额外的操作符选择字段
- 后端层面：Like操作符能正确生成SQL查询（LIKE '%value%'）

Fixes #XXX" -s

# 4. 推送并创建PR
git push origin fix/filter-operator-like-ui-display
```

这个示例展示了：
- 如何深入理解问题的根本原因
- 为什么第一次修复可能不完整
- 如何平衡UI显示和功能性需求
- 如何编写全面的测试验证修复
- 如何规范地提交代码

### 关键经验
1. **全面分析**: 不仅要解决表面问题，还要理解整个功能链路
2. **影响评估**: 修复时要考虑对其他功能的潜在影响
3. **测试验证**: 既要测试修复目标，也要测试相关功能是否受影响
4. **文档更新**: 复杂修复需要详细说明技术原理和决策过程

## 🙏 致谢

感谢每一位贡献者的努力，正是有了大家的参与，Go-Admin才能不断发展壮大！

---

**注意**: 如果你在贡献过程中遇到任何问题，欢迎在社区群组中提问，我们会及时帮助解决。 
