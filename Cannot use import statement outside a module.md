# 问题：Uncaught SyntaxError: Cannot use import statement outside a module (at toolbar.js:2:1)

# 解决方案
来自DeepSeek R1

---

这里有一个 **5分钟极简解决方案**，无需构建工具，保持原有代码结构：

---

### **步骤一：修改文件结构**
```diff
extension/
└── content/
    ├── toolbar/
    │   ├── toolbar.js  # 保持普通JS文件
    └── content.js      # 主入口文件
```

---

### **步骤二：改造 `toolbar.js`**
移除所有 `import/export` 改为全局变量：
```javascript
// 顶部移除所有 import 语句

// 改为全局类声明
class SelectionToolbar {
  // 保持原有代码不变...
}

// 直接挂载到 window
window.SelectionToolbar = SelectionToolbar;

// 自动初始化
document.addEventListener('DOMContentLoaded', () => {
  window.selectionToolbar = new SelectionToolbar();
});
```

---

### **步骤三：简化 `manifest.json`**
```diff
{
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": [
        "lib/kuromoji.js",
        "content/tokenizer.js", 
        "content/rubyText.js",
+       "content/toolbar/toolbar.js",  # 直接加载
        "content/content.js"
      ],
-     "mjs": ["content/toolbar/toolbar.mjs"],
      "css": ["content/toolbar/toolbar.css"]
    }
  ]
}
```

---

### **步骤四：处理依赖（如有）**
如果 `toolbar.js` 需要其他模块：
```javascript
// 在 toolbar.js 顶部添加
const translatorService = {
  translate: (text) => {
    // 直接实现简单翻译逻辑
    return fetch(`https://api.example.com/translate?text=${text}`);
  }
};
```

---

### **步骤五：测试加载顺序**
在 `content.js` 中添加：
```javascript
console.log('【1】content.js 已加载');
setTimeout(() => {
  console.log('【3】5秒后工具栏状态:', window.selectionToolbar);
}, 5000);
```

在 `toolbar.js` 中添加：
```javascript
console.log('【2】toolbar.js 已加载');
```

---

### **最终效果**
- ✅ 完全移除模块语法
- ✅ 保持原有功能
- ✅ 无需构建步骤
- ✅ 5分钟快速修复

---

**注意事项**：
1. 如果使用第三方库，直接通过 `<script>` 标签加载：
```html
<!-- 在 popup.html 中 -->
<script src="lib/vue.global.js"></script> 
```
2. 全局变量命名加前缀防止冲突：
```javascript
window.__JP_TOOLBAR__ = new SelectionToolbar();
```
