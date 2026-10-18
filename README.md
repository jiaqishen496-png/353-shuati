# 353 刷题室

一个纯静态的 353 卫生综合刷题网站，参考 [公卫研习室](https://github.com/xinl-c137/public-health-notes) 的技术路线与视觉语言：无需构建、无需后端，直接部署到 GitHub Pages 即可使用。

在线地址：<https://jiaqishen496-png.github.io/353-shuati/>

内置 **7 个科目、345 道题**，覆盖流行病学、卫生统计学、社会医学、环境卫生学、职业卫生与职业医学、营养与食品卫生学，以及可选的毒理学模块。

## 功能

- 按科目、章节练习；支持顺序练习与随机抽题
- 单选题、多选题、判断题，作答后即时显示解析与考点
- 自动错题本：答错进入，连续答对 2 次自动移出
- 收藏夹：随时标记反复记不住的题
- 模拟考试：按科目比例抽题、倒计时、答题卡、标记、统一阅卷、分科目成绩单
- 学习统计：累计题次、正确率、掌握数、连续天数、近 12 周热力图、薄弱章节
- 学习进度保存在浏览器本地；支持导出 / 导入 JSON，换设备可迁移
- 自定义题库：导入 JSON 后作为“我的题库”科目出现
- 深色 / 浅色 / 跟随系统主题；移动端抽屉目录与响应式布局
- 键盘快捷键：`A–D` 选择、`Enter` 下一题、`F` 收藏、`M` 标记

## 本地运行

浏览器会阻止 `file://` 页面读取题库文件，需要通过本地服务器打开。

方式一（零依赖，需要 Node.js 18+）：

```powershell
node scripts/serve.mjs
```

方式二（需要 Python）：

```powershell
python -m http.server 8000
```

然后访问 <http://localhost:8000>。

## 手机使用

### 同一 Wi-Fi 下访问电脑

1. 电脑运行 `node scripts/serve.mjs`；
2. 记下终端打印的“手机（同一 Wi-Fi）”地址，例如 `http://192.168.3.5:8000`；
3. 手机连同一个 Wi-Fi，用浏览器打开该地址；
4. 如果 Windows 弹出防火墙提示，请允许 Node.js 访问“专用网络”。

这种方式适合临时刷题。局域网 HTTP 不是安全上下文，手机浏览器可能不允许安装 PWA、也不启用离线缓存；需要完整手机应用体验时，请部署到 GitHub Pages。

### 添加到手机桌面

- iPhone：Safari 打开网站 → 分享 → **添加到主屏幕**
- Android：Chrome 打开网站 → 右上角菜单 → **安装应用 / 添加到主屏幕**

通过 GitHub Pages（HTTPS）访问时，首次联网加载后会缓存应用外壳和题库，之后可以离线刷题；答题进度始终保存在本机浏览器。

## 修改题库

题目按科目存放在 `data/questions/*.json`，每个文件是一个数组。新增或修改题目后，运行：

```powershell
node scripts/build-index.mjs
```

脚本会校验题目格式（ID 唯一、答案字母合法、单选/多选答案数量等），并重新生成 `data/manifest.json`。校验失败时不会写入索引，终端会指出具体文件和题号。

### 题目格式

```json
{
  "id": "epi-001",
  "subject": "epidemiology",
  "chapter": "绪论与疾病分布",
  "type": "single",
  "stem": "流行病学的研究对象是",
  "options": ["个体患者", "人群", "病原体", "医院住院病例"],
  "answer": ["B"],
  "tag": "流行病学定义",
  "explain": "流行病学以人群为研究对象……",
  "source": "可选：题目来源，如“北大 2026 模拟卷 A · A1”"
}
```

- `type`：`single`（单选）、`multiple`（多选）、`judge`（判断）
- `answer`：答案字母数组。多选题写多个字母，如 `["A","C"]`
- 判断题的 `options` 固定为 `["正确","错误"]`，答案用 `["A"]` 或 `["B"]`
- `id` 格式为 `科目缩写-三位数字`，且全库唯一
- `chapter` 决定章节分组；同一科目内章节顺序按首次出现顺序
- `source` 可选，会显示在解析右下角

新增科目时，需要同时：

1. 在 `scripts/build-index.mjs` 的 `SUBJECTS` 中加入科目元信息（id、名称、简称、颜色、描述）；
2. 在 `data/questions/` 新建对应 `<id>.json`；
3. 运行 `node scripts/build-index.mjs`。

### 自定义题库

网站内“设置与题库 → 导入题库”支持直接导入 JSON，格式同上（`subject`/`id` 可省略，导入时自动生成）。可以参考 [samples/custom-questions.example.json](samples/custom-questions.example.json)。

导入的自定义题库只保存在当前浏览器，不会写入仓库文件；导入会替换上一份自定义题库。

## 部署到 GitHub Pages

整个目录就是发布产物，不需要构建步骤：

1. 新建仓库，把本目录全部文件提交到 `main` 分支；
2. 仓库 **Settings → Pages**，Source 选择 **Deploy from a branch**；
3. 分支选 `main`，目录选 `/ (root)`，保存；
4. 等待 Pages 部署完成，访问 `https://<用户名>.github.io/<仓库名>/`。

如果部署在子路径下，当前代码使用相对路径（`assets/...`、`data/...`），无需修改。

也可以部署到 Cloudflare Pages、Netlify、Vercel 等任意静态托管平台。

## 目录结构

```text
353-shuati/
├─ index.html                 页面骨架
├─ assets/
│  ├─ styles.css              视觉系统与响应式布局
│  ├─ store.js                本地进度、错题、收藏、设置
│  ├─ data.js                 题库加载、索引、统计
│  ├─ ui.js                   通用 UI 工具
│  ├─ practice.js             练习引擎
│  ├─ exam.js                 模拟考试引擎
│  └─ app.js                  路由、页面视图、事件
├─ data/
│  ├─ manifest.json           自动生成的科目索引
│  └─ questions/              分科目题库
├─ samples/                   自定义题库示例
└─ scripts/
   ├─ build-index.mjs         校验并生成索引
   └─ serve.mjs               零依赖本地服务器
```

## 内容说明

- 题目为按通用教材知识点原创整理，**不是官方真题**，也不保证覆盖任何一所院校的全部考点。
- 部分 A1 选择题改编自公开的北大 2026 模拟卷，已在题目 `source` 字段中标注。
- 各校 353 的科目组合、题型、分值和参考教材不同，请以目标院校当年考试大纲为准。
- 发现题目错误时，请以教材原文复核后修改 JSON；运行校验脚本可避免格式问题。
- 社会医学部分参考了北大版社会医学笔记的章节结构；流行病学、卫生统计学等以通用教材概念为准。

## 数据与隐私

网站没有后端，不上传任何答题数据。所有进度、错题、收藏、考试记录都在浏览器 `localStorage` 中，清除浏览器数据会一并删除，建议定期在“设置”中导出备份。

## 参考

- [xinl-c137/public-health-notes](https://github.com/xinl-c137/public-health-notes)：静态笔记站的技术路线与视觉参考
- 北京大学公共卫生考研资料（用户提供的本地压缩包）：社会医学章节结构与部分模拟题来源
