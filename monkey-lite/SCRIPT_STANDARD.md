# Monkey Lite 脚本开发规范

本文档制定了本项目中 Userscript（油猴脚本）的开发标准，旨在统一代码风格、UI 交互及架构模式，确保所有脚本具有一致的高质量体验。

## 1. 元数据规范 (Metadata)

所有脚本必须包含完整的元数据块，并遵循以下标准：

- **@updateURL / @downloadURL**: 必须指向 GitHub Pages 的 raw 链接，用于自动更新。
- **@license**: 统一使用 MIT。
- **@author**: 统一为 `CCTTYYLAB`。
- **@icon**: 使用 Google Favicons 服务：`https://www.google.com/s2/favicons?sz=64&domain=目标域名`。

**模板：**

```javascript
// ==UserScript==
// @name         脚本名称 (中文优先)
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  简明扼要的功能描述。
// @author       CCTTYYLAB
// @match        *://目标域名/*
// @icon         https://www.google.com/s2/favicons?sz=64&domain=目标域名
// @grant        GM_getValue
// @grant        GM_setValue
// @grant        GM_registerMenuCommand
// @license      MIT
// @updateURL    https://raw.githubusercontent.com/ccttyylab/ccttyylab.github.io/refs/heads/main/monkey-lite/YOUR_FILENAME.user.js
// @downloadURL  https://raw.githubusercontent.com/ccttyylab/ccttyylab.github.io/refs/heads/main/monkey-lite/YOUR_FILENAME.user.js
// ==/UserScript==
```

## 2. 代码架构 (Architecture)

脚本逻辑必须采用 **面向对象 (OOP)** 的方式组织，并包裹在立即执行函数 (IIFE) 中。

**标准结构：**

```javascript
(function () {
  'use strict';

  // 1. 常量定义
  const CONSTANTS = {
    DEFAULT_CONFIG: { ... },
    SELECTORS: { ... }
  };

  // 2. 核心类
  class ScriptName {
    constructor() {
      this.config = this.loadConfig();
    }

    // 初始化入口
    init() {
      console.log('[Script Name] Initialized');
      this.registerMenu();
      this.startMainLogic();
    }

    // 加载配置 (使用 GM_getValue)
    loadConfig() {
      return GM_getValue('config', CONSTANTS.DEFAULT_CONFIG);
    }

    // 注册菜单，通用都加一个设置菜单，就叫设置2个字
    registerMenu() {
      GM_registerMenuCommand('设置', () => this.showSettingsUI());
    }

    // 核心逻辑
    startMainLogic() {
      // ...
    }
    
    // UI 渲染 (见 UI 规范)
    showSettingsUI() {
        // ...
    }
  }

  // 3. 启动
  new ScriptName().init();

})();
```

## 3. UI 交互规范 (UI Standards)

为了保证体验一致性，所有脚本的设置界面或弹窗必须遵循以下 UI 规范：

### 3.1 基础原则
- **Shadow DOM**: 必须使用 Shadow DOM 隔离样​​式，避免污染原网页。
- **Host 容器**: 宿主元素（Shadow Root 的挂载点）必须添加类名 `ccttyy-script-host`，以便统一管理（如反向缩放）。
- **字体标准**: 默认字体大小统一为 **12px**，标题可适当增大 (14px)。
- **响应式布局**: 采用 **Mobile-First** 策略，PC 与移动端共用一套紧凑布局。

### 3.2 弹窗样式模板
弹窗应居中显示，具有圆角和阴影。

```css
/* 容器 */
.overlay {
    position: fixed;
    top: 0; left: 0;
    width: 100vw; height: 100vh;
    height: 100dvh;
    background: rgba(0, 0, 0, 0.5);
    z-index: 10000;
    display: flex;
    justify-content: center;
    align-items: center;
}

/* 弹窗主体 - 采用 Flex 布局 */
.modal {
    background: white;
    padding: 16px;
    border-radius: 12px;
    width: 85%;           /* 移动端宽度 */
    max-width: 500px;     /* 最大宽度 500px */
    height: 80vh;         /* 固定高度，确保切换 Tab 时高度不跳动 */
    height: 80dvh;
    max-height: 90vh;     /* 防止溢出屏幕 */
    max-height: 90dvh;
    font-size: 12px;
    display: flex;
    flex-direction: column;
    box-shadow: 0 4px 24px rgba(0,0,0,0.2);
    overflow: hidden;     /* 必须隐藏溢出，由内容区控制滚动 */
}

/* 1. 顶部区域 (Header) - 固定高度 */
.header {
    height: 40px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid #eee;
    flex-shrink: 0;       /* 禁止压缩 */
}

/* 2. 标签切换 (Tabs) - 固定高度 */
.tabs {
    display: flex;
    border-bottom: 1px solid #eee;
    margin-top: 10px;
    flex-shrink: 0;       /* 禁止压缩 */
}

/* 3. 内容区域 (Content) - 自动填充并内部滚动 */
.tab-content {
    flex: 1;              /* 占据所有剩余空间 */
    overflow-y: auto;     /* 启用内部滚动 */
    padding: 10px 0;
}

/* 4. 底部区域 (Footer) - 固定高度 */
.footer {
    height: 50px;
    display: flex;
    gap: 10px;
    align-items: center;  /* 垂直居中 */
    border-top: 1px solid #eee;
    flex-shrink: 0;       /* 禁止压缩 */
    margin-top: 10px;
}

/* 控件详情样式见下文... */

```

### 3.3 控件样式
- **按钮**:
  - **Primary (主要操作)**: 蓝色背景 (`#007aff`)，白色文字。
  - **Secondary (次要操作)**: 白色背景，灰色边框。
  - **Danger (危险操作)**: 红色文字 (`#ff3b30`)。
  - **布局**: 底部操作栏推荐使用 `grid` 布局 (3列)，按钮文字 `white-space: nowrap`。
- **输入框**:
  - `flex: 1` 自适应宽度。
  - `padding: 8px 12px`。

### 3.5 Tab Interface Standard (多标签页规范)

为方便数据查看与调试，设置面板应包含两个标签页：**“设置”** (Settings) 和 **“数据”** (Data)。

**CSS 样式参考：**
```css
.tabs {
    display: flex;
    border-bottom: 1px solid #eee;
    margin-bottom: 16px;
}
.tab-item {
    padding: 8px 16px;
    cursor: pointer;
    color: #666;
    border-bottom: 2px solid transparent;
    transition: all 0.2s;
}
.tab-item.active {
    color: #007aff;
    border-bottom-color: #007aff;
    font-weight: 500;
}
/* 内容区域需支持滚动 */
.tab-content {
    flex: 1;
    overflow-y: auto;
    min-height: 200px;
    display: flex;
    flex-direction: column;
}
.tab-pane {
    display: none;
    flex: 1;
    flex-direction: column;
}
.tab-pane.active {
    display: flex;
}
/* 数据展示区域样式 */
pre {
    background: #f5f5f7;
    padding: 12px;
    border-radius: 8px;
    overflow-x: auto;
    font-family: monospace;
    white-space: pre-wrap;    /* 保留换行和空格 */
    word-wrap: break-word;    /* 长文本自动换行 */
    margin: 0;
    flex: 1;
}
```

**HTML 结构参考：**
```html
<div class="modal">
    <!-- 1. Header -->
    <div class="header">
        <div class="title">设置中心</div>
        <div class="close-btn">×</div>
    </div>
    
    <!-- 2. Tabs -->
    <div class="tabs">
        <div class="tab-item active" data-tab="settings">设置</div>
        <div class="tab-item" data-tab="data">数据</div>
    </div>
    
    <!-- 3. Content -->
    <div class="tab-content">
        <div class="tab-pane active" id="pane-settings">
            <!-- 设置控件 -->
        </div>
        <div class="tab-pane" id="pane-data">
            <pre><!-- 数据展示 --></pre>
        </div>
    </div>
    
    <!-- 4. Footer -->
    <div class="footer">
        <button class="btn btn-secondary">取消</button>
        <button class="btn btn-primary">保存</button>
    </div>
</div>
```

**数据展示规范 (Data Tab Standards)：**
1.  **数据源标注**：必须清晰标注数据来源，例如 `【 Tampermonkey Storage 】` 或 `【 LocalStorage 】`。
2.  **按需展示**：仅展示本脚本实际使用或产生的核心数据，**严禁**列出无关的全局数据（如无关的 LocalStorage 或 Cookies）。
3.  **格式化要求**：
    *   **对象 (Object)**：使用标准 JSON 格式化 (`null, 2`)，并注意内层缩进对齐 (可使用 `.replace(/\n/g, '\n  ')`)。
    *   **数组 (Array)**：为了节省空间，建议使用紧凑格式 (`["a", "b", ...]`)，利用 CSS 自动换行。
    *   **空状态**：如果无数据，应显示 `(Empty)`。

**代码参考 (数据格式化 helper)：**
```javascript
const formatKeyValue = (key, value) => {
    let valStr;
    if (typeof value === 'string') {
        try {
            const parsed = JSON.parse(value);
            // 对象多行显示，修正缩进以对齐 Key
            valStr = JSON.stringify(parsed, null, 2).replace(/\n/g, '\n  ');
        } catch {
            valStr = JSON.stringify(value); // 普通字符串 (带引号)
        }
    } else if (Array.isArray(value)) {
        // 数组紧凑显示，逗号分隔
        valStr = '[' + value.map(k => JSON.stringify(k)).join(', ') + ']';
    } else {
        // 其他类型格式化并缩进
        valStr = JSON.stringify(value, null, 2).replace(/\n/g, '\n  ');
    }
    return `  "${key}": ${valStr}`;
};
```

## 4. 最佳实践 (Best Practices)

- **MutationObserver**: 处理单页应用 (SPA) 或动态加载内容时，必须使用 Observer 监听 DOM 变化。
- **错误处理**: 对关键逻辑添加 try-catch，避免脚本报错阻断页面正常功能。
- **日志输出**: 使用带前缀的控制台日志，如 `console.log('[Script Name] ...')`，方便调试。
