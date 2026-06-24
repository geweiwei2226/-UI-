# 前端UI规范

> 基于 stock_info/stock、stock_info/watchlist、stock_info/rule 页面总结
> 
> **最后审核**: 2026-03-18 使用 ui-ux-pro-max 技能

---

## 更新记录

| 日期 | 更新内容 |
|------|----------|
| 2026-03-18 | 增加 UI/UX 审核标准、无障碍规范、性能优化 |
| 2026-03-18 | 增加颜色对比度、表格行高、标签样式规范 |

---

## 一、页面结构

所有列表页面统一采用以下结构：

```html
<template>
  <div class="app-container">
    <!-- 1. 搜索表单区域 -->
    <el-form>...</el-form>
    
    <!-- 2. 数据表格区域 -->
    <el-table>...</el-table>
    
    <!-- 3. 分页组件 -->
    <pagination />
    
    <!-- 4. 对话框 -->
    <el-dialog>...</el-dialog>
  </div>
</template>
```

---

## 二、搜索表单规范

### 2.1 表单属性

```html
<el-form 
  :model="queryParams" 
  ref="queryForm" 
  size="small" 
  :inline="true" 
  label-width="68px"
>
```

| 属性 | 值 | 说明 |
|------|-----|------|
| size | small | 统一使用小尺寸 |
| inline | true | 行内表单 |
| label-width | 68px | 标签宽度统一 |

### 2.2 表单项规范

```html
<!-- 输入框 -->
<el-form-item label="字段名" prop="fieldName">
  <el-input 
    v-model="queryParams.fieldName" 
    placeholder="请输入..." 
    clearable 
    style="width: 120px"
    @keyup.enter.native="handleQuery" 
  />
</el-form-item>

<!-- 下拉选择 -->
<el-form-item>
  <el-select 
    v-model="queryParams.type" 
    placeholder="请选择" 
    clearable 
    style="width: 110px"
  >
    <el-option label="选项1" value="1" />
    <el-option label="选项2" value="2" />
  </el-select>
</el-form-item>
```

### 2.3 操作按钮规范

```html
<el-form-item>
  <!-- 必须按钮 -->
  <el-button type="primary" icon="el-icon-search" size="mini" @click="handleQuery">搜索</el-button>
  <el-button icon="el-icon-refresh" size="mini" @click="resetQuery">重置</el-button>
  
  <!-- 功能按钮（按需添加） -->
  <el-button type="primary" plain icon="el-icon-plus" size="mini" @click="handleAdd">新增</el-button>
  <el-button type="danger" plain icon="el-icon-delete" size="mini" :disabled="multiple" @click="handleDelete">删除</el-button>
  <el-button type="success" plain icon="el-icon-download" size="mini" @click="handleExport">导出</el-button>
</el-form-item>
```

**按钮样式对照表：**

| 类型 | type | plain | 场景 |
|------|------|-------|------|
| 搜索 | primary | 否 | 主操作 |
| 重置 | - | 否 | 次操作 |
| 新增 | primary | 是 | 功能操作 |
| 删除 | danger | 是 | 危险操作 |
| 导出 | success | 是 | 功能操作 |
| 导入 | info | 是 | 功能操作 |
| 采集 | primary | 是 | 功能操作 |
| 清空 | danger | 是 | 危险操作 |

---

## 三、表格规范

### 3.1 表格属性

```html
<el-table 
  v-loading="loading" 
  :data="dataList" 
  @selection-change="handleSelectionChange"
  :header-cell-style="{background:'#f5f7fa',color:'#606266',fontWeight:'600',textAlign:'center'}"
  stripe
  border
  class="el-table--compact"
>
```

| 属性 | 值 | 说明 |
|------|-----|------|
| v-loading | loading | 加载状态 |
| stripe | - | 斑马纹 |
| border | - | 边框 |
| header-cell-style | 见上 | 表头样式 |
| class | el-table--compact | 紧凑样式 |

### 3.2 列定义规范

```html
<!-- 选择列 -->
<el-table-column type="selection" width="50" align="center" />

<!-- 普通文本列 -->
<el-table-column label="名称" prop="name" min-width="80" />

<!-- 居中列 -->
<el-table-column label="类型" prop="type" width="80" align="center" />

<!-- 右对齐数字列 -->
<el-table-column label="金额" width="90" align="right">
  <template slot-scope="scope">
    <span class="num-text">{{ formatMoney(scope.row.amount) }}</span>
  </template>
</el-table-column>

<!-- 状态标签列 -->
<el-table-column label="状态" width="90" align="center">
  <template slot-scope="scope">
    <el-tag :type="scope.row.status === '1' ? 'success' : 'danger'" size="mini">
      {{ scope.row.status === '1' ? '启用' : '停用' }}
    </el-tag>
  </template>
</el-table-column>

<!-- 字典标签列 -->
<el-table-column label="类型" width="80" align="center">
  <template slot-scope="scope">
    <dict-tag :options="dict.type.xxx_type" :value="scope.row.type"/>
  </template>
</el-table-column>

<!-- 开关列 -->
<el-table-column label="状态" width="80" align="center">
  <template slot-scope="scope">
    <el-switch 
      v-model="scope.row.status" 
      active-value="1" 
      inactive-value="0" 
      @change="handleStatusChange(scope.row)"
    ></el-switch>
  </template>
</el-table-column>

<!-- 时间列 -->
<el-table-column label="创建时间" prop="createTime" width="155" align="center" />

<!-- 操作列 -->
<el-table-column label="操作" align="center" width="140" fixed="right">
  <template slot-scope="scope">
    <el-button size="mini" type="text" icon="el-icon-edit" @click="handleUpdate(scope.row)">修改</el-button>
    <el-button size="mini" type="text" icon="el-icon-delete" @click="handleDelete(scope.row)">删除</el-button>
  </template>
</el-table-column>
```

### 3.3 列宽参考

| 类型 | 宽度 | 说明 |
|------|------|------|
| 选择列 | 50-55 | 固定 |
| 短编码 | 70-80 | 如：代码、编号 |
| 名称 | min-width 80-120 | 自适应 |
| 状态 | 80-90 | 开关/标签 |
| 数字 | 75-100 | 右对齐 |
| 时间 | 140-160 | YYYY-MM-DD HH:mm |
| 操作 | 按钮数 × 50 | 固定右侧 |

### 3.4 表格行高

统一使用 `el-table--compact` 类，行高 **40px**。

---

## 四、分页规范

```html
<pagination
  v-show="total > 0"
  :total="total"
  :page.sync="queryParams.pageNum"
  :limit.sync="queryParams.pageSize"
  :page-sizes="[10, 15, 20, 30, 50]"
  @pagination="getList"
/>
```

**分页选项：** `[10, 15, 20, 30, 50]`

**默认每页条数：** 15

---

## 五、对话框规范

### 5.1 基础对话框

```html
<el-dialog 
  :title="title" 
  :visible.sync="open" 
  width="600px" 
  append-to-body
>
  <el-form ref="form" :model="form" :rules="rules" label-width="80px">
    <!-- 表单内容 -->
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button type="primary" @click="submitForm">确 定</el-button>
    <el-button @click="cancel">取 消</el-button>
  </div>
</el-dialog>
```

### 5.2 对话框宽度参考

| 场景 | 宽度 |
|------|------|
| 简单表单 | 500px |
| 标准表单 | 600px |
| 复杂表单 | 750px |
| 详情展示 | 650px |
| 进度展示 | 450px |

### 5.3 详情对话框

```html
<el-dialog title="详情" :visible.sync="open" width="650px" append-to-body>
  <el-descriptions :column="2" border size="small">
    <el-descriptions-item label="字段名">{{ form.field }}</el-descriptions-item>
  </el-descriptions>
  <div slot="footer" class="dialog-footer">
    <el-button @click="open = false">关 闭</el-button>
  </div>
</el-dialog>
```

---

## 六、数据结构规范

### 6.1 data 结构

```javascript
data() {
  return {
    // 加载状态
    loading: true,
    // 选中ID数组
    ids: [],
    // 是否单选
    single: true,
    // 是否多选
    multiple: true,
    // 总数
    total: 0,
    // 数据列表
    dataList: [],
    // 弹窗标题
    title: "",
    // 弹窗显示
    open: false,
    // 查询参数
    queryParams: {
      pageNum: 1,
      pageSize: 15,
      // 其他查询字段
    },
    // 表单数据
    form: {},
    // 表单验证
    rules: {}
  };
}
```

### 6.2 方法结构

```javascript
methods: {
  // 获取列表
  getList() {},
  // 查询
  handleQuery() {},
  // 重置
  resetQuery() {},
  // 选择变化
  handleSelectionChange(selection) {},
  // 新增
  handleAdd() {},
  // 修改
  handleUpdate(row) {},
  // 删除
  handleDelete(row) {},
  // 提交表单
  submitForm() {},
  // 取消
  cancel() {},
  // 重置表单
  reset() {}
}
```

---

## 七、全局样式类

### 7.1 表格相关

| 类名 | 用途 |
|------|------|
| `.el-table--compact` | 紧凑表格，行高40px |
| `.num-text` | 数字等宽字体 |
| `.time-text` | 时间灰色字体 |

### 7.2 股票相关

| 类名 | 用途 |
|------|------|
| `.st-stock` | ST股票红色标记 |
| `.price.up` | 上涨价格红色 |
| `.price.down` | 下跌价格绿色 |
| `.change.up-bg` | 上涨背景浅红 |
| `.change.down-bg` | 下跌背景浅绿 |

---

## 八、命名规范

### 8.1 组件命名

- 使用 PascalCase：`StockList`, `StockRule`
- name 属性与组件名一致

### 8.2 方法命名

| 前缀 | 用途 | 示例 |
|------|------|------|
| handle | 事件处理 | handleQuery, handleAdd |
| get | 获取数据 | getList, getDetail |
| format | 格式化 | formatMoney, formatPercent |
| reset | 重置 | resetQuery, resetForm |

### 8.3 变量命名

- 列表数据：`xxxList`
- 弹窗状态：`open`, `xxxOpen`
- 加载状态：`loading`, `xxxLoading`
- 查询参数：`queryParams`

---

## 九、注意事项

1. **表格必须添加 `class="el-table--compact"`**
2. **分页选项统一使用 `[10, 15, 20, 30, 50]`**
3. **默认每页 15 条**
4. **按钮统一使用 `size="mini"`**
5. **表单统一使用 `size="small"`**
6. **操作列固定右侧 `fixed="right"`**
7. **数字列右对齐 `align="right"`**
8. **使用全局样式，避免 scoped 重复定义**

---

## 十、页面模板

```html
<template>
  <div class="app-container">
    <el-form :model="queryParams" ref="queryForm" size="small" :inline="true" label-width="68px">
      <el-form-item label="名称" prop="name">
        <el-input v-model="queryParams.name" placeholder="请输入名称" clearable @keyup.enter.native="handleQuery" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" icon="el-icon-search" size="mini" @click="handleQuery">搜索</el-button>
        <el-button icon="el-icon-refresh" size="mini" @click="resetQuery">重置</el-button>
      </el-form-item>
    </el-form>

    <el-table 
      v-loading="loading" 
      :data="dataList" 
      @selection-change="handleSelectionChange"
      :header-cell-style="{background:'#f5f7fa',color:'#606266',fontWeight:'600',textAlign:'center'}"
      stripe
      border
      class="el-table--compact"
    >
      <el-table-column type="selection" width="50" align="center" />
      <el-table-column label="名称" prop="name" min-width="120" />
      <el-table-column label="状态" width="80" align="center">
        <template slot-scope="scope">
          <dict-tag :options="dict.type.sys_normal_disable" :value="scope.row.status"/>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" width="140" fixed="right">
        <template slot-scope="scope">
          <el-button size="mini" type="text" icon="el-icon-edit" @click="handleUpdate(scope.row)">修改</el-button>
          <el-button size="mini" type="text" icon="el-icon-delete" @click="handleDelete(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="queryParams.pageNum"
      :limit.sync="queryParams.pageSize"
      :page-sizes="[10, 15, 20, 30, 50]"
      @pagination="getList"
    />
  </div>
</template>

<script>
import { listXxx, getXxx, addXxx, updateXxx, delXxx } from "@/api/system/xxx";

export default {
  name: "XxxList",
  dicts: ['sys_normal_disable'],
  data() {
    return {
      loading: true,
      ids: [],
      single: true,
      multiple: true,
      total: 0,
      dataList: [],
      queryParams: {
        pageNum: 1,
        pageSize: 15,
        name: undefined
      }
    };
  },
  created() {
    this.getList();
  },
  methods: {
    getList() {
      this.loading = true;
      listXxx(this.queryParams).then(response => {
        this.dataList = response.rows;
        this.total = response.total;
        this.loading = false;
      });
    },
    handleQuery() {
      this.queryParams.pageNum = 1;
      this.getList();
    },
    resetQuery() {
      this.resetForm("queryForm");
      this.handleQuery();
    },
    handleSelectionChange(selection) {
      this.ids = selection.map(item => item.id);
      this.single = selection.length !== 1;
      this.multiple = !selection.length;
    }
  }
};
</script>
```

---

## 十一、UI/UX 审核标准（2026-03-18 新增）

基于 ui-ux-pro-max 技能的专业指导，所有页面必须通过以下审核：

### 11.1 视觉设计检查清单

| 检查项 | 标准 | 优先级 |
|--------|------|--------|
| 表格行高 | 40px（使用 `el-table--compact`） | P0 |
| 表头对齐 | 统一居中 `textAlign:'center'` | P0 |
| 标签对比度 | 最小 4.5:1（文字与背景） | P0 |
| 按钮间距 | 统一 8px 或 12px | P1 |
| 分页样式 | 与主题色一致，不突兀 | P1 |
| 空状态 | 必须有引导提示 | P2 |
| 加载状态 | `v-loading` 必须显示 spinner | P1 |

### 11.2 无障碍（Accessibility）规范

```html
<!-- 1. 图标按钮必须有 aria-label -->
<el-button aria-label="关闭"><i class="el-icon-close"></i></el-button>

<!-- 2. 表单输入必须有 label -->
<el-form-item label="邮箱" prop="email">
  <el-input v-model="form.email" autocomplete="email" />
</el-form-item>

<!-- 3. 装饰性图标隐藏 -->
<i class="el-icon-info" aria-hidden="true"></i>

<!-- 4. 焦点状态可见 -->
/* 不单独使用 outline-none，必须提供替代 */
:focus-visible {
  outline: 2px solid #409EFF;
}
```

### 11.3 颜色使用规范

**金融类页面推荐配色（Financial Dashboard #7）:**

```css
/* 主色调 */
--primary: #0F172A;           /* 深蓝 - 专业信任 */
--secondary: #1E293B;         /* 次级背景 */
--accent-positive: #22C55E;   /* 上涨绿色 */
--accent-negative: #EF4444;   /* 下跌红色 */
--text-primary: #1E293B;      /* 主文字 */
--text-secondary: #64748B;    /* 次要文字 */
--border: #E2E8F0;            /* 边框 */

/* 标签颜色（Element UI 标准） */
--tag-primary: #409EFF;       /* 蓝色 */
--tag-success: #67C23A;       /* 绿色 */
--tag-warning: #E6A23C;       /* 橙色 */
--tag-danger: #F56C6C;        /* 红色 */
--tag-info: #909399;          /* 灰色 */
```

**对比度要求：**
- 正文文字：最小 4.5:1
- 大文字（18px+）：最小 3:1
- 禁用状态：可接受 3:1

### 11.4 表格最佳实践

```html
<!-- 标准表格结构 -->
<el-table 
  v-loading="loading" 
  :data="dataList"
  :header-cell-style="{background:'#f5f7fa',color:'#606266',fontWeight:'600',textAlign:'center'}"
  stripe
  border
  class="el-table--compact"
>
  <!-- 选择列 -->
  <el-table-column type="selection" width="50" align="center" />
  
  <!-- 普通列 - 居中 -->
  <el-table-column label="状态" width="80" align="center">
    <template slot-scope="scope">
      <!-- 使用 Element UI 标准标签 -->
      <el-tag :type="getTagType(scope.row.status)" size="mini">
        {{ scope.row.status }}
      </el-tag>
    </template>
  </el-table-column>
  
  <!-- 数字列 - 右对齐 -->
  <el-table-column label="金额" width="100" align="right">
    <template slot-scope="scope">
      <span class="num-text">{{ formatMoney(scope.row.amount) }}</span>
    </template>
  </el-table-column>
  
  <!-- 操作列 - 固定右侧 -->
  <el-table-column label="操作" align="center" width="140" fixed="right">
    <template slot-scope="scope">
      <!-- 外链必须带图标 -->
      <el-button size="mini" type="text" @click="handleExternal(scope.row)">
        查看 <i class="el-icon-link"></i>
      </el-button>
    </template>
  </el-table-column>
</el-table>
```

### 11.5 数据格式化规范

```javascript
// 1. 金额格式化
formatMoney(value) {
  if (!value) return '-';
  if (value >= 100000000) return (value / 100000000).toFixed(2) + '亿';
  if (value >= 10000) return (value / 10000).toFixed(2) + '万';
  return value.toFixed(2);
}

// 2. 负数/异常值处理
formatPeRatio(value) {
  if (value === null || value === undefined) return '-';
  if (value < 0) return '亏损';  // 或 '--'
  return value.toFixed(2);
}

// 3. 时间格式化（相对时间）
formatTime(time) {
  const diff = Date.now() - new Date(time).getTime();
  const hours = Math.floor(diff / 3600000);
  if (hours < 1) return '刚刚';
  if (hours < 24) return `${hours}小时前`;
  return parseTime(time, '{y}-{m}-{d} {h}:{i}');
}
```

### 11.6 常见反模式（禁止）

❌ **不要这样做：**

```html
<!-- 1. 不要单独使用 outline-none -->
<button class="outline-none">点击</button>

<!-- 2. 不要禁用缩放 -->
<meta name="viewport" content="maximum-scale=1">

<!-- 3. 不要阻止粘贴 -->
<input @paste="e => e.preventDefault()">

<!-- 4. 不要使用 placeholder 代替 label -->
<input placeholder="请输入邮箱">

<!-- 5. 不要硬编码颜色 -->
<span style="color: #E6A23C">标签</span>
```

✅ **应该这样做：**

```html
<!-- 1. outline-none 必须配合替代方案 -->
<button class="focus:ring-2 focus:ring-blue-500">点击</button>

<!-- 2. 允许用户缩放 -->
<meta name="viewport" content="width=device-width, initial-scale=1">

<!-- 3. 允许粘贴 -->
<input type="password">

<!-- 4. 使用 label -->
<label for="email">邮箱</label>
<input id="email" placeholder="example@mail.com">

<!-- 5. 使用 Element UI 标签组件 -->
<el-tag type="warning">标签</el-tag>
```

### 11.7 性能优化规范

```html
<!-- 1. 图片懒加载 -->
<img loading="lazy" src="..." />

<!-- 2. 大数据表格虚拟滚动 -->
<el-table :data="list" height="400">
  <!-- 超过 50 行考虑虚拟滚动 -->
</el-table>

<!-- 3. 防抖搜索 -->
<el-input 
  v-model="queryParams.keyword"
  @input="debounce(handleQuery, 300)"
/>
```

### 11.8 审核检查表

发布前必须检查：

- [ ] 表格行高是否为 40px
- [ ] 所有表头是否居中对齐
- [ ] 标签颜色对比度是否 ≥4.5:1
- [ ] 外链是否有图标提示
- [ ] 负数/异常值是否格式化显示
- [ ] 空状态是否有引导
- [ ] 加载状态是否显示
- [ ] 按钮间距是否统一
- [ ] 移动端是否可正常操作
- [ ] 键盘导航是否可用

---

*最后更新：2026-03-18*