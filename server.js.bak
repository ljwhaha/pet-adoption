const express = require('express');
const cors = require('cors');
const Database = require('better-sqlite3');
const path = require('path');

const app = express();
const PORT = 3000;

// 中间件
app.use(cors());
app.use(express.json());
app.use(express.static('public'));

// 数据库初始化
const db = new Database('pet_adoption.db');

// 创建表
db.exec(`
  CREATE TABLE IF NOT EXISTS pets (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    breed TEXT,
    age INTEGER,
    gender TEXT,
    description TEXT,
    image TEXT,
    status TEXT DEFAULT 'available',
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
  );

  CREATE TABLE IF NOT EXISTS adoptions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    pet_id INTEGER,
    applicant_name TEXT NOT NULL,
    phone TEXT NOT NULL,
    email TEXT,
    reason TEXT,
    status TEXT DEFAULT 'pending',
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (pet_id) REFERENCES pets(id)
  );
`);

// 初始化宠物数据
const petCount = db.prepare('SELECT COUNT(*) as count FROM pets').get();
if (petCount.count === 0) {
  const insertPet = db.prepare(`
    INSERT INTO pets (name, type, breed, age, gender, description, image, status)
    VALUES (?, ?, ?, ?, ?, ?, ?, ?)
  `);

  const pets = [
    ['小白', 'dog', '中华田园犬', 2, '公', '活泼可爱，已绝育，适合有孩子的家庭', 'https://images.unsplash.com/photo-1587300003388-59208cc962cb?w=400', 'available'],
    ['黑豆', 'dog', '拉布拉多', 1, '母', '聪明伶俐，喜欢玩球，已打疫苗', 'https://images.unsplash.com/photo-1552053831-71594a27632d?w=400', 'available'],
    ['咪咪', 'cat', '英国短毛猫', 3, '母', '温顺粘人，喜欢撒娇', 'https://images.unsplash.com/photo-1514888286974-6c03e2ca1dba?w=400', 'available'],
    ['奥利奥', 'cat', '美国短毛猫', 2, '公', '调皮捣蛋，适应能力强', 'https://images.unsplash.com/photo-1573865526739-10659fec78a5?w=400', 'available'],
    ['毛球', 'rabbit', '垂耳兔', 1, '母', '超级可爱，吃货一枚', 'https://images.unsplash.com/photo-1585110396000-c9ffd4e4b308?w=400', 'available'],
    ['旺财', 'dog', '柯基', 2, '公', '小短腿，大屁股，笑起来超甜', 'https://images.unsplash.com/photo-1612536051302-5a5d39fb429c?w=400', 'available'],
    ['小橘', 'cat', '中华田园猫', 1, '母', '胖橘潜力股，能吃能睡', 'https://images.unsplash.com/photo-1574158622682-e40e69881006?w=400', 'adopted'],
    ['花花', 'dog', '边牧', 3, '母', '智商第一，学的会各种技能', 'https://images.unsplash.com/photo-1503256207526-0d5d80fa2f47?w=400', 'available'],
  ];

  pets.forEach(pet => insertPet.run(...pet));
}

// API 路由

// 获取所有宠物
app.get('/api/pets', (req, res) => {
  const { type, status, search } = req.query;
  let sql = 'SELECT * FROM pets WHERE 1=1';
  const params = [];

  if (type) {
    sql += ' AND type = ?';
    params.push(type);
  }
  if (status) {
    sql += ' AND status = ?';
    params.push(status);
  }
  if (search) {
    sql += ' AND (name LIKE ? OR breed LIKE ? OR description LIKE ?)';
    const searchTerm = `%${search}%`;
    params.push(searchTerm, searchTerm, searchTerm);
  }

  sql += ' ORDER BY created_at DESC';
  const pets = db.prepare(sql).all(...params);
  res.json(pets);
});

// 获取单个宠物
app.get('/api/pets/:id', (req, res) => {
  const pet = db.prepare('SELECT * FROM pets WHERE id = ?').get(req.params.id);
  if (!pet) {
    return res.status(404).json({ error: '宠物不存在' });
  }
  res.json(pet);
});

// 提交领养申请
app.post('/api/adoptions', (req, res) => {
  const { pet_id, applicant_name, phone, email, reason } = req.body;

  if (!pet_id || !applicant_name || !phone || !reason) {
    return res.status(400).json({ error: '请填写完整信息' });
  }

  // 检查宠物是否可领养
  const pet = db.prepare('SELECT * FROM pets WHERE id = ?').get(pet_id);
  if (!pet) {
    return res.status(404).json({ error: '宠物不存在' });
  }
  if (pet.status !== 'available') {
    return res.status(400).json({ error: '该宠物已被领养' });
  }

  // 插入申请
  const result = db.prepare(`
    INSERT INTO adoptions (pet_id, applicant_name, phone, email, reason)
    VALUES (?, ?, ?, ?, ?)
  `).run(pet_id, applicant_name, phone, email, reason);

  res.json({ 
    success: true, 
    message: '申请提交成功！我们会尽快联系您',
    id: result.lastInsertRowid 
  });
});

// 获取申请列表
app.get('/api/adoptions', (req, res) => {
  const adoptions = db.prepare(`
    SELECT a.*, p.name as pet_name, p.type as pet_type
    FROM adoptions a
    LEFT JOIN pets p ON a.pet_id = p.id
    ORDER BY a.created_at DESC
  `).all();
  res.json(adoptions);
});

// 静态页面
app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});

app.listen(PORT, () => {
  console.log(`🐾 宠物领养系统运行在 http://localhost:${PORT}`);
});
