# GoAdmin系统架构与运行流程图

## 1. 系统整体架构图

```mermaid
graph TB
    subgraph "Web框架层"
        Gin[Gin]
        Echo[Echo]
        Beego[Beego]
        Iris[Iris]
        Fiber[Fiber]
        Chi[Chi]
    end
    
    subgraph "适配器层"
        GinAdapter[Gin适配器<br/>adapter/gin/gin.go]
        EchoAdapter[Echo适配器<br/>adapter/echo/echo.go]
        BeegoAdapter[Beego适配器<br/>adapter/beego/beego.go]
        IrisAdapter[Iris适配器<br/>adapter/iris/iris.go]
        OtherAdapters[其他适配器...]
    end
    
    subgraph "核心引擎"
        Engine[Engine核心引擎<br/>engine/engine.go]
        Context[上下文处理<br/>context/context.go]
        Router[路由系统<br/>context/trie.go]
    end
    
    subgraph "插件系统"
        PluginManager[插件管理器<br/>plugins/plugins.go]
        AdminPlugin[Admin插件<br/>plugins/admin/admin.go]
        CustomPlugins[自定义插件]
    end
    
    subgraph "核心模块"
        Auth[认证模块<br/>modules/auth/auth.go]
        DB[数据库模块<br/>modules/db/connection.go]
        Config[配置模块<br/>modules/config/config.go]
        Menu[菜单模块<br/>modules/menu/menu.go]
        Logger[日志模块<br/>modules/logger/logger.go]
        UI[UI模块<br/>modules/ui/ui.go]
        Language[语言模块<br/>modules/language/language.go]
        File[文件模块<br/>modules/file/file.go]
    end
    
    subgraph "模板系统"
        Template[模板引擎<br/>template/template.go]
        Components[UI组件<br/>template/components/]
        Themes[主题系统]
        ChartJS[图表支持<br/>template/chartjs/]
    end
    
    subgraph "数据存储"
        MySQL[(MySQL)]
        PostgreSQL[(PostgreSQL)]
        SQLite[(SQLite)]
        MSSQL[(MSSQL)]
    end
    
    %% 连接关系
    Gin --> GinAdapter
    Echo --> EchoAdapter
    Beego --> BeegoAdapter
    Iris --> IrisAdapter
    
    GinAdapter --> Engine
    EchoAdapter --> Engine
    BeegoAdapter --> Engine
    IrisAdapter --> Engine
    OtherAdapters --> Engine
    
    Engine --> Context
    Engine --> Router
    Engine --> PluginManager
    
    PluginManager --> AdminPlugin
    PluginManager --> CustomPlugins
    
    Engine --> Auth
    Engine --> DB
    Engine --> Config
    
    AdminPlugin --> Menu
    AdminPlugin --> UI
    AdminPlugin --> Template
    
    Template --> Components
    Template --> Themes
    Template --> ChartJS
    
    DB --> MySQL
    DB --> PostgreSQL
    DB --> SQLite
    DB --> MSSQL
    
    Auth --> Logger
    Config --> Language
    UI --> File
```

## 2. 用户请求处理流程图

```mermaid
sequenceDiagram
    participant User as 用户浏览器
    participant WebFW as Web框架<br/>(Gin/Echo等)
    participant Adapter as 适配器<br/>adapter/[framework]/
    participant Engine as 核心引擎<br/>engine/engine.go
    participant Context as 上下文<br/>context/context.go
    participant Auth as 认证模块<br/>modules/auth/
    participant Plugin as Admin插件<br/>plugins/admin/
    participant Controller as 控制器<br/>plugins/admin/controller/
    participant DB as 数据库<br/>modules/db/
    participant Template as 模板系统<br/>template/template.go
    participant Response as 响应

    User->>WebFW: 1. HTTP请求
    WebFW->>Adapter: 2. 框架路由转发
    Adapter->>Engine: 3. 调用引擎处理
    Engine->>Context: 4. 创建请求上下文
    
    Context->>Auth: 5. 检查Cookie/Session
    Auth->>Auth: 6. 验证用户身份
    
    alt 用户未认证
        Auth-->>User: 重定向到登录页
    else 用户已认证
        Auth->>Auth: 7. 检查权限
        
        alt 权限不足
            Auth->>Template: 8a. 渲染403错误页
            Template-->>User: 返回403页面
        else 权限通过
            Auth->>Plugin: 8b. 转发到插件处理
            Plugin->>Controller: 9. 调用对应控制器
            Controller->>DB: 10. 数据库操作
            DB-->>Controller: 11. 返回数据
            Controller->>Template: 12. 准备模板数据
            Template->>Template: 13. 渲染HTML模板
            Template-->>Response: 14. 生成响应内容
            Response-->>Adapter: 15. 返回处理结果
            Adapter-->>WebFW: 16. 适配器响应
            WebFW-->>User: 17. HTTP响应
        end
    end
```

## 3. 系统初始化流程图

```mermaid
flowchart TD
    Start([系统启动]) --> LoadConfig[加载配置<br/>modules/config/config.go]
    LoadConfig --> InitDB[初始化数据库连接<br/>modules/db/connection.go]
    InitDB --> SetAdapter[设置Web框架适配器<br/>adapter/adapter.go]
    SetAdapter --> InitServices[初始化服务列表<br/>modules/service/service.go]
    InitServices --> LoadPlugins[加载插件<br/>plugins/plugins.go]
    LoadPlugins --> InitAuth[初始化认证服务<br/>modules/auth/auth.go]
    InitAuth --> SetupRoutes[注册路由<br/>plugins/admin/router.go]
    SetupRoutes --> InitTemplate[初始化模板系统<br/>template/template.go]
    InitTemplate --> StartServer[启动Web服务器]
    StartServer --> Ready([系统就绪])

    %% 详细步骤
    LoadConfig --> ConfigJSON{配置文件类型}
    ConfigJSON -->|JSON| ReadJSON[读取JSON配置]
    ConfigJSON -->|YAML| ReadYAML[读取YAML配置]
    ConfigJSON -->|INI| ReadINI[读取INI配置]
    
    ReadJSON --> ValidateConfig[验证配置]
    ReadYAML --> ValidateConfig
    ReadINI --> ValidateConfig
    ValidateConfig --> InitDB
    
    InitDB --> ConnMySQL[MySQL连接]
    InitDB --> ConnPostgreSQL[PostgreSQL连接]
    InitDB --> ConnSQLite[SQLite连接]
    InitDB --> ConnMSSQL[MSSQL连接]
    
    LoadPlugins --> AdminPlugin[Admin插件初始化]
    LoadPlugins --> CustomPlugin[自定义插件初始化]
    AdminPlugin --> RegisterControllers[注册控制器]
    CustomPlugin --> RegisterControllers
```

## 4. 请求到响应的详细数据流

```mermaid
graph LR
    subgraph "请求入口"
        HTTPReq[HTTP请求<br/>GET /admin/info/user]
    end
    
    subgraph "适配器处理"
        A1[路由匹配<br/>adapter/gin/gin.go]
        A2[上下文转换<br/>gin.Context → context.Context]
        A3[中间件执行<br/>认证、CORS等]
    end
    
    subgraph "认证流程"
        Auth1[获取Cookie<br/>modules/auth/session.go]
        Auth2[验证Session<br/>modules/auth/auth.go]
        Auth3[权限检查<br/>RBAC验证]
        Auth4[用户信息<br/>plugins/admin/models/]
    end
    
    subgraph "业务处理"
        B1[路由分发<br/>plugins/admin/router.go]
        B2[控制器执行<br/>plugins/admin/controller/]
        B3[数据库查询<br/>modules/db/statement.go]
        B4[数据处理<br/>业务逻辑]
    end
    
    subgraph "模板渲染"
        T1[模板选择<br/>template/template.go]
        T2[数据绑定<br/>Panel数据结构]
        T3[组件渲染<br/>template/components/]
        T4[主题应用<br/>AdminLTE等]
        T5[HTML生成<br/>最终页面]
    end
    
    subgraph "响应输出"
        R1[设置响应头<br/>Content-Type等]
        R2[写入响应体<br/>HTML内容]
        R3[发送给浏览器]
    end
    
    HTTPReq --> A1
    A1 --> A2
    A2 --> A3
    A3 --> Auth1
    Auth1 --> Auth2
    Auth2 --> Auth3
    Auth3 --> Auth4
    Auth4 --> B1
    B1 --> B2
    B2 --> B3
    B3 --> B4
    B4 --> T1
    T1 --> T2
    T2 --> T3
    T3 --> T4
    T4 --> T5
    T5 --> R1
    R1 --> R2
    R2 --> R3
```

## 5. 插件系统架构图

```mermaid
classDiagram
    class Plugin {
        <<interface>>
        +GetHandler() HandlerMap
        +InitPlugin(services)
        +GetGenerators() GeneratorList
        +Name() string
        +Prefix() string
        +GetInfo() Info
    }
    
    class BasePlugin {
        +App *context.App
        +Services service.List
        +Conn db.Connection
        +UI *ui.Service
        +PlugName string
        +URLPrefix string
        +Info Info
    }
    
    class AdminPlugin {
        +controllers map[string]Controller
        +routers map[string]Handler
        +generators map[string]Generator
        +InitPlugin()
        +RegisterRoutes()
    }
    
    class CustomPlugin {
        +customLogic()
        +customRoutes()
    }
    
    class PluginManager {
        +plugins []Plugin
        +Add(Plugin)
        +Remove(Plugin)
        +Init()
        +RegisterAll()
    }
    
    Plugin <|-- BasePlugin
    BasePlugin <|-- AdminPlugin
    BasePlugin <|-- CustomPlugin
    PluginManager --> Plugin : manages
    
    AdminPlugin --> Controller : contains
    AdminPlugin --> Router : contains
    AdminPlugin --> Generator : contains
```

## 6. 模板渲染流程图

```mermaid
flowchart TD
    Start([开始渲染]) --> GetTemplate[获取模板<br/>template/template.go]
    GetTemplate --> CheckPjax{是否Pjax请求?}
    
    CheckPjax -->|是| PjaxTemplate[使用Pjax模板<br/>只渲染内容部分]
    CheckPjax -->|否| FullTemplate[使用完整模板<br/>包含头部脚部]
    
    PjaxTemplate --> PrepareData[准备模板数据]
    FullTemplate --> PrepareData
    
    PrepareData --> CreatePage[创建Page对象<br/>types.NewPage]
    CreatePage --> SetUser[设置用户信息<br/>models.UserModel]
    SetUser --> SetMenu[设置菜单<br/>menu.GetGlobalMenu]
    SetMenu --> SetPanel[设置面板内容<br/>Panel.GetContent]
    SetPanel --> SetAssets[设置静态资源<br/>CSS/JS]
    SetAssets --> SetButtons[设置导航按钮<br/>权限检查]
    
    SetButtons --> ExecuteTemplate[执行模板渲染<br/>tmpl.ExecuteTemplate]
    ExecuteTemplate --> RenderComponents[渲染UI组件<br/>template/components/]
    
    RenderComponents --> Table[表格组件]
    RenderComponents --> Form[表单组件]
    RenderComponents --> Chart[图表组件]
    RenderComponents --> Button[按钮组件]
    
    Table --> ApplyTheme[应用主题样式<br/>AdminLTE/其他主题]
    Form --> ApplyTheme
    Chart --> ApplyTheme
    Button --> ApplyTheme
    
    ApplyTheme --> AddAssets[添加静态资源<br/>template.GetComponentAssetImportHTML]
    AddAssets --> AddHeadHTML[添加头部HTML<br/>template.GetHeadHTML]
    AddHeadHTML --> AddFootJS[添加底部JS<br/>template.GetFootJS]
    
    AddFootJS --> GenerateHTML[生成最终HTML]
    GenerateHTML --> WriteBuffer[写入Buffer]
    WriteBuffer --> SetContentType[设置Content-Type<br/>text/html; charset=utf-8]
    SetContentType --> SendResponse[发送响应]
    SendResponse --> End([渲染完成])
    
    %% 错误处理
    ExecuteTemplate -->|出错| ErrorPanel[错误面板<br/>template.WarningPanel]
    ErrorPanel --> LogError[记录错误日志<br/>modules/logger/]
    LogError --> SendResponse
```

## 核心文件对应关系

| 流程步骤 | 核心文件 | 作用 |
|---------|---------|------|
| 系统启动 | `engine/engine.go` | 引擎初始化和配置加载 |
| 框架适配 | `adapter/[framework]/[framework].go` | Web框架接入 |
| 请求路由 | `context/context.go`, `context/trie.go` | 上下文和路由处理 |
| 身份认证 | `modules/auth/auth.go`, `modules/auth/middleware.go` | 用户认证和权限检查 |
| 插件处理 | `plugins/admin/admin.go`, `plugins/admin/router.go` | 业务逻辑处理 |
| 数据操作 | `modules/db/connection.go`, `modules/db/statement.go` | 数据库交互 |
| 模板渲染 | `template/template.go`, `template/components/` | UI渲染和主题应用 |
| 响应输出 | `adapter/adapter.go` | 响应格式化和输出 | 
