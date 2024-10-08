
最近在搬迁文档，因为之前加了文档分类预览，所有每次都要配配置 `sidebars.ts` 文件，有点费劲。
## 自动脚本实现

### 前景

**痛点：** 手动添加的效果,相当于我们每次新增一个文档类就要手动添加一条数据。有时候忘记添加了，新增的文档是没渲染出来的。

```typescript
export default {
  docs: [
    "intro",
    {
      type: 'category',
      label: 'Guides',
      link: {type: 'doc', id: 'Guides/index'},
      items: [],
    },
    {
      type: 'category1',
      label: 'Guides',
      link: {type: 'doc', id: 'Guides/index'},
      items: [],
    },
    {
      type: 'category2',
      label: 'Guides',
      link: {type: 'doc', id: 'Guides/index'},
      items: [],
    },
    ...
  ],
};

```

### 脚本实现

在和 Docs 文件同目录下创建 `sidebars.js` 文件

#### 生成 TS 版

```typescript
const fs = require('fs');
const path = require('path');

// 自动读取 docs 目录下的子目录
const docsDir = path.resolve(__dirname, 'docs');
const directories = fs.readdirSync(docsDir).filter((dir) => {
    const dirPath = path.join(docsDir, dir);
    return fs.statSync(dirPath).isDirectory();
});
// 自动生成侧边栏配置
const sidebars = {
  docs: [
    "intro",
    ...directories.map((dir) => ({
      label: dir,
      type: "category",
      link: { type: "generated-index" },
      items: [{ type: "autogenerated", dirName: dir }],
    })),
  ],
};
// 将生成的配置写入 sidebars.js 文件
const outputPath = path.resolve(__dirname, 'sidebars.js');
const content = `import type { SidebarsConfig } from "@docusaurus/plugin-content-docs";

const sidebars: SidebarsConfig = ${JSON.stringify(sidebars, null, 2)}; // 第二个参数是替换 key 的值，默认为空即可，第三个参数是缩进

export default sidebars;

`;

fs.writeFileSync(outputPath, content);

console.log('sidebars.js has been generated!');
```

#### 生成 JS 版

```javascript
const fs = require('fs');
const path = require('path');


const docsDir = path.resolve(__dirname, 'docs');
const directories = fs.readdirSync(docsDir).filter((dir) => {
    const dirPath = path.join(docsDir, dir);
    return fs.statSync(dirPath).isDirectory();
});


const sidebars = {
  docs: [
    "intro",
    ...directories.map((dir) => ({
      label: dir,
      type: "category",
      link: { type: "generated-index" },
      items: [{ type: "autogenerated", dirName: dir }],
    })),
  ],
};


const outputPath = path.resolve(__dirname, 'sidebars.js');
const content = `import type { SidebarsConfig } from "@docusaurus/plugin-content-docs";

const sidebars: SidebarsConfig = ${JSON.stringify(sidebars, null, 2)};

export default sidebars;

`;

fs.writeFileSync(outputPath, content);

console.log('sidebars.js has been generated!');
```

#### 可选目录版本

```javascript
const fs = require('fs');
const path = require('path');

const directories = ['Egg', 'KCP', 'Pomelo', 'Sequelize', 'TypeScript', 'UDP'];
const sidebars = {
  docs: [
    "intro",
    ...directories.map((dir) => ({
      label: dir,
      type: "category",
      link: { type: "generated-index" },
      items: [{ type: "autogenerated", dirName: dir }],
    })),
  ],
};

const outputPath = path.resolve(__dirname, 'sidebars.js');
const content = `import type { SidebarsConfig } from "@docusaurus/plugin-content-docs";

const sidebars: SidebarsConfig = ${JSON.stringify(sidebars, null, 2)};

export default sidebars;

`;

fs.writeFileSync(outputPath, content);

console.log('sidebars.js has been generated!');
```

#### 运行

```bash
node generate-sidebars.js
```
