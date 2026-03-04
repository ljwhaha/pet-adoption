# 🐾 宠物领养系统

一个简单可爱的宠物领养平台，让流浪动物找到温暖的家。

## 功能特性

- 🐕 浏览待领养宠物
- 🔍 按类型、年龄筛选
- 📝 申请领养
- ❤️ 收藏喜欢的宠物
- 📋 管理领养申请

## 技术栈

- **前端**: HTML + Vue.js 3 + TailwindCSS
- **后端**: Node.js + Express
- **数据库**: SQLite (轻量级)

## 快速开始

### 安装依赖

```bash
npm install
```

### 启动后端

```bash
node server.js
```

后端服务运行在 http://localhost:3000

### 打开前端

直接在浏览器打开 `index.html`

或者使用静态服务器：

```bash
npx serve .
```

## API 接口

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /api/pets | 获取所有宠物 |
| GET | /api/pets/:id | 获取宠物详情 |
| POST | /api/adoptions | 提交领养申请 |
| GET | /api/adoptions | 获取申请列表 |

## 项目结构

```
pet-adoption/
├── public/
│   ├── index.html      # 前端页面
│   ├── css/
│   │   └── style.css   # 样式
│   └── js/
│       └── app.js      # 前端逻辑
├── server.js           # 后端服务
├── database.js         # 数据库
├── package.json
└── README.md
```

## 宠物数据结构

```json
{
  "id": 1,
  "name": "小白",
  "type": "dog",
  "breed": "中华田园犬",
  "age": 2,
  "gender": "公",
  "description": "活泼可爱，已绝育",
  "image": "/images/dog1.jpg",
  "status": "available"
}
```

## 领养申请

```json
{
  "petId": 1,
  "name": "张三",
  "phone": "13800138000",
  "reason": "非常喜欢小动物..."
}
```

---

Made with ❤️ for furry friends 🐱🐶
