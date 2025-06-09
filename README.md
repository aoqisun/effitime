# 高效时光 EffiTime

<div align="center">
  <img src="docs/images/logo.png" alt="EffiTime Logo" width="120" height="120">
  
  <h3>基于《高效能人士的七个习惯》理念的智能时间管理应用</h3>
  
  [![Flutter](https://img.shields.io/badge/Flutter-3.16.0-blue.svg)](https://flutter.dev/)
  [![Node.js](https://img.shields.io/badge/Node.js-18.0+-green.svg)](https://nodejs.org/)
  [![MySQL](https://img.shields.io/badge/MySQL-8.0-orange.svg)](https://www.mysql.com/)
  [![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
</div>

## 🎯 项目简介

高效时光(EffiTime)不是简单的待办清单应用，而是一款基于史蒂芬·柯维《高效能人士的七个习惯》科学理念打造的时间管理助手。我们将"以终为始"和"要事第一"原则与现代番茄工作法完美结合，帮助用户构建从人生愿景到日常行动的完整管理体系。

### ✨ 核心理念

- **以终为始** - 从人生愿景开始，逐层分解至日常任务
- **要事第一** - 基于四象限法则的智能任务优先级管理
- **深度专注** - 科学的番茄工作法提升专注力和执行效率
- **持续改进** - 通过数据分析获得个性化的效率洞察

## 🚀 功能特性

### 🎯 目标管理 - 以终为始
- 愿景驱动的目标层级体系（愿景→年度→季度→月度→周→日）
- 目标关联和进度可视化追踪
- 智能目标分解和里程碑设置

### 📋 任务管理 - 要事第一
- 基于重要性和紧急性的四象限智能分类
- 智能任务排序和优先级推荐
- 任务与目标的动态关联

### 🍅 番茄工作法 - 深度专注
- 25分钟工作+5分钟休息的科学节奏
- 专注度评分和中断处理
- 个性化番茄时长设置

### 📊 数据分析 - 持续改进
- 全面的效率报告和趋势分析
- 智能洞察和个性化改进建议
- 多维度数据可视化

### 🔄 多端同步 - 随时随地
- 离线优先的数据同步机制
- 端到端加密保护隐私
- 冲突解决和版本控制

## 🏗️ 技术架构

### 前端技术栈
- **Flutter** - 跨平台移动应用开发
- **Riverpod** - 状态管理和依赖注入
- **Go Router** - 声明式路由管理
- **Hive** - 本地数据存储
- **FL Chart** - 数据可视化图表

### 后端技术栈
- **Node.js** + **Express** - RESTful API服务
- **TypeScript** - 类型安全的开发体验
- **MySQL** - 关系型数据库
- **Redis** - 缓存和会话存储
- **JWT** - 用户认证和授权

### 基础设施
- **Docker** - 容器化部署
- **Nginx** - 反向代理和负载均衡
- **GitHub Actions** - CI/CD自动化
- **Let's Encrypt** - SSL/TLS证书

## 📱 项目结构

```
effitime/
├── mobile/                 # Flutter移动应用
│   ├── lib/
│   │   ├── features/       # 功能模块
│   │   ├── core/          # 核心配置
│   │   └── shared/        # 共享组件
│   ├── android/           # Android配置
│   └── ios/              # iOS配置
├── backend/               # Node.js后端API
│   ├── src/
│   │   ├── controllers/   # 控制器
│   │   ├── models/       # 数据模型
│   │   ├── routes/       # 路由配置
│   │   └── services/     # 业务逻辑
│   └── docs/             # API文档
├── database/             # 数据库相关
│   ├── migrations/       # 数据迁移
│   └── seeds/           # 初始数据
├── deploy/              # 部署配置
│   ├── docker-compose.yml
│   ├── nginx/
│   └── ssl/
├── docs/                # 项目文档
│   ├── api/             # API文档
│   ├── design/          # 设计文档
│   └── deployment/      # 部署文档
└── .github/             # GitHub Actions
    └── workflows/
```

## 🚀 快速开始

### 环境要求
- **Flutter** 3.16.0+
- **Node.js** 18.0+
- **MySQL** 8.0+
- **Redis** 7.0+
- **Docker** 20.10+ (生产环境)

### 本地开发

#### 1. 克隆项目
```bash
git clone https://github.com/aoqisun/effitime.git
cd effitime
```

#### 2. 后端设置
```bash
cd backend
npm install
cp .env.example .env
# 编辑 .env 文件配置数据库连接
npm run dev
```

#### 3. 移动端设置
```bash
cd mobile
flutter pub get
flutter run
```

#### 4. 数据库初始化
```bash
# 导入数据库结构
mysql -u root -p < database/schema.sql
```

### 生产部署

#### 使用Docker Compose
```bash
cd deploy
cp env.production.example .env
# 编辑环境变量
docker-compose up -d
```

详细部署指南请参考：[部署文档](docs/deployment-guide.md)

## 📖 文档

- [产品需求文档](产品需求调研报告.md)
- [技术架构文档](技术架构设计与选型.md)
- [数据库设计文档](数据库设计文档.md)
- [API接口文档](backend/docs/api.md)
- [部署指南](docs/deployment-guide.md)
- [隐私政策](docs/app-store/privacy-policy.md)

## 🤝 贡献指南

我们欢迎所有形式的贡献！请阅读 [贡献指南](CONTRIBUTING.md) 了解如何参与项目开发。

### 开发流程
1. Fork 项目
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 创建 Pull Request

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🙏 致谢

- 史蒂芬·柯维的《高效能人士的七个习惯》为产品理念提供了理论基础
- Flutter团队提供了优秀的跨平台开发框架
- 所有开源项目贡献者的无私奉献

## 📞 联系我们

- **邮箱**: contact@effitime.com
- **官网**: https://www.effitime.com
- **问题反馈**: [GitHub Issues](https://github.com/aoqisun/effitime/issues)

---

<div align="center">
  <p>让科学的时间管理改变您的生活！</p>
  <p>⭐ 如果这个项目对您有帮助，请给我们一个星标支持！</p>
</div>