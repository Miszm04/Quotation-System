<template>
  <div class="custom-select" :class="sizeClass">
    <!-- 选择器触发区域 -->
    <div
      class="select-trigger"
      :class="{
        'is-active': isOpen,
        'is-focused': hasFocus,
        'is-disabled': disabled,
      }"
      @click="toggleDropdown"
      @focus="hasFocus = true"
      @blur="hasFocus = false"
      :tabindex="disabled ? -1 : 0"
    >
      <span class="selected-value" :class="{ 'is-selected': selectedLabel }">
        <!-- 选中项显示图片（如果有） -->
        <img
          v-if="selectedImage"
          :src="selectedImage"
          alt="Selected item image"
          class="selected-item-image"
          @error="handleImageError('selected')"
        />
        {{ selectedLabel || placeholder }}
      </span>
      <i class="arrow-icon" :class="{ rotate: isOpen }"></i>
    </div>

    <!-- 下拉菜单 -->
    <div
      class="select-dropdown"
      :class="{ 'is-visible': isOpen }"
      v-if="isOpen"
    >
      <div class="options-container">
        <!-- 分组选项 -->
        <div class="option-group" v-for="group in options" :key="group.label">
          <div class="group-label">{{ group.label }}</div>
          <div
            class="option-item"
            v-for="item in group.options"
            :key="item.value"
            @click="selectOption(item)"
            :class="{
              'is-selected': selectedValue === item.value,
              'is-hover': hoveredValue === item.value,
              'is-highlighted': item.highlight,
            }"
            @mouseenter="hoveredValue = item.value"
            @mouseleave="hoveredValue = null"
          >
            <!-- 选项图片（如果有） -->
            <img
              v-if="item.image"
              :src="item.image"
              :alt="item.label"
              class="option-image"
              @error="handleImageError(item.value)"
            />
            {{ item.label }}
          </div>
        </div>
      </div>

      <!-- 底部提示 -->
      <div class="select-footer">如找不到对应类别，请联系Ryan Yang报价</div>
    </div>

    <!-- 用于表单提交的隐藏输入 -->
    <input type="hidden" :name="name" :value="selectedValue" />
  </div>
</template>

<script setup>
import { ref, watch, computed, onBeforeUnmount, getCurrentInstance } from "vue";

// 先定义getLabelByValue函数，确保在使用前已声明
const getLabelByValue = (value, options) => {
  if (value === null || value === undefined) return "";

  for (const group of options) {
    const found = group.options.find((option) => option.value === value);
    if (found) {
      return found.label;
    }
  }
  return "";
};

// 获取选项图片
const getImageByValue = (value, options) => {
  if (value === null || value === undefined) return "";

  for (const group of options) {
    const found = group.options.find((option) => option.value === value);
    if (found && found.image) {
      return found.image;
    }
  }
  return "";
};

// 定义组件属性
const props = defineProps({
  // 双向绑定的值
  modelValue: {
    type: [String, Number, null],
    default: null,
  },
  // 选项数据
  options: {
    type: Array,
    required: true,
    validator: (value) => {
      return value.every((group) => {
        return (
          group.label &&
          Array.isArray(group.options) &&
          group.options.every(
            (option) => option.label !== undefined && option.value !== undefined
          )
        );
      });
    },
  },
  // 占位符文本
  placeholder: {
    type: String,
    default: "请选择",
  },
  // 尺寸
  size: {
    type: String,
    default: "large",
    validator: (value) => ["large", "medium", "small"].includes(value),
  },
  // 表单名称
  name: {
    type: String,
    default: "",
  },
  // 是否禁用
  disabled: {
    type: Boolean,
    default: false,
  },
  // 图片加载失败时显示的默认图片
  defaultImage: {
    type: [String, Object],
    default: "",
  },
});

// 定义事件
const emit = defineEmits(["update:modelValue", "change", "image-error"]);

// 组件状态
const isOpen = ref(false);
const hasFocus = ref(false);
const hoveredValue = ref(null);
const selectedValue = ref(props.modelValue);
const selectedLabel = ref(getLabelByValue(props.modelValue, props.options));
const selectedImage = ref(getImageByValue(props.modelValue, props.options));

// 计算属性
const sizeClass = computed(() => `size-${props.size}`);

// 监听modelValue变化
watch(
  () => props.modelValue,
  (newVal) => {
    selectedValue.value = newVal;
    selectedLabel.value = getLabelByValue(newVal, props.options);
    selectedImage.value = getImageByValue(newVal, props.options);
  }
);

// 监听内部选中值变化，同步到外部
watch(selectedValue, (newVal) => {
  emit("update:modelValue", newVal);
  emit("change", newVal);
});

// 方法
const toggleDropdown = () => {
  if (props.disabled) return;
  isOpen.value = !isOpen.value;

  // 如果打开下拉菜单，添加点击外部关闭的事件监听
  if (isOpen.value) {
    document.addEventListener("click", handleClickOutside);
  } else {
    document.removeEventListener("click", handleClickOutside);
  }
};

// 点击外部关闭下拉菜单
const handleClickOutside = (e) => {
  const instance = getCurrentInstance();
  if (instance && !instance.vnode.el.contains(e.target)) {
    isOpen.value = false;
    document.removeEventListener("click", handleClickOutside);
  }
};

// 选择选项
const selectOption = (item) => {
  selectedValue.value = item.value;
  selectedLabel.value = item.label;
  selectedImage.value = item.image || "";
  isOpen.value = false;
  document.removeEventListener("click", handleClickOutside);
};

// 处理图片加载错误
const handleImageError = (itemId) => {
  emit("image-error", itemId);
  // 如果提供了默认图片，使用默认图片
  if (props.defaultImage) {
    if (itemId === "selected") {
      selectedImage.value = props.defaultImage;
    }
  }
};

// 组件销毁时移除事件监听
onBeforeUnmount(() => {
  document.removeEventListener("click", handleClickOutside);
});
</script>

<style scoped>
/* 基础样式设置 */
.custom-select {
  position: relative;
  width: 100%;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica,
    Arial, sans-serif;
}

/* 选择器触发区域（重点放大高度） */
.select-trigger {
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
  border: 1px solid #dcdfe6;
  border-radius: 6px;
  background-color: #fff;
  cursor: pointer;
  box-sizing: border-box;
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
  will-change: border-color, box-shadow;
}

/* 尺寸类 - 重点放大触发区域高度 */
.select-trigger.size-large {
  height: 64px;
  font-size: 18px;
}

.select-trigger.size-medium {
  height: 56px;
  font-size: 16px;
}

.select-trigger.size-small {
  height: 48px;
  font-size: 14px;
}

/* 交互状态样式（全部改为红色系） */
.select-trigger:not(.is-disabled):hover {
  border-color: #f59f00; /* 红色系 hover 边框 */
}

.select-trigger.is-active {
  border-color: #ff4d4f; /* 主红色边框 */
  box-shadow: 0 0 0 2px rgba(255, 77, 79, 0.2); /* 红色阴影 */
}

.select-trigger.is-focused {
  outline: none;
}

.select-trigger.is-disabled {
  background-color: #f5f7fa;
  cursor: not-allowed;
  color: #c0c4cc;
  border-color: #e4e7ed;
}

/* 选中值显示区域 */
.selected-value {
  flex: 1;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  text-align: left;
  color: #303133;
  display: flex;
  align-items: center;
  gap: 12px;
}

/* 选中项在触发器中的高亮样式（红色） */
.selected-value.is-selected {
  color: #ff4d4f; /* 主红色文字 */
  font-weight: 500;
}

/* 图片样式 - 放大尺寸 */
.option-image,
.selected-item-image {
  width: 26px;
  height: 26px;
  object-fit: contain;
  flex-shrink: 0;
  border-radius: 4px;
  transition: opacity 0.2s ease;
}

.option-image[src=""] {
  opacity: 0;
}

/* 箭头图标 - 红色系 */
.arrow-icon {
  display: inline-block;
  width: 0;
  height: 0;
  margin-left: 10px;
  border-style: solid;
  border-width: 7px 7px 0;
  border-color: #d93025 transparent transparent; /* 深红色箭头 */
  transition: transform 0.25s cubic-bezier(0.4, 0, 0.2, 1);
  will-change: transform;
}

.arrow-icon.rotate {
  transform: rotate(180deg);
}

/* 下拉菜单 - 协调红色系 */
.select-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  margin-top: 6px;
  padding: 8px 0;
  border: 1px solid #fce8e6; /* 浅红边框 */
  border-radius: 6px;
  background-color: #fff;
  box-shadow: 0 6px 18px rgba(255, 77, 79, 0.12); /* 红色阴影 */
  box-sizing: border-box;
  z-index: 10;
  max-height: 360px;
  overflow-y: auto;
  opacity: 0;
  visibility: hidden;
  transform: translateY(5px);
  transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
  will-change: opacity, visibility, transform;
}

.select-dropdown.is-visible {
  opacity: 1;
  visibility: visible;
  transform: translateY(0);
}

/* 自定义滚动条 - 红色系 */
.select-dropdown::-webkit-scrollbar {
  width: 8px;
}

.select-dropdown::-webkit-scrollbar-track {
  background: #fff2f0; /* 浅红轨道 */
  border-radius: 4px;
}

.select-dropdown::-webkit-scrollbar-thumb {
  background: #ffb4a9; /* 红色滚动条 */
  border-radius: 4px;
}

.select-dropdown::-webkit-scrollbar-thumb:hover {
  background: #ff8a80; /* 深一点的红色 */
}

/* 选项分组 */
.option-group {
  margin-bottom: 12px;
}

.option-group:last-child {
  margin-bottom: 0;
}

.group-label {
  padding: 8px 20px;
  color: #d93025; /* 红色分组标题 */
  font-size: 14px;
  font-weight: 500;
  text-align: center;
  background-color: #fff2f0; /* 浅红背景 */
  margin: 0 8px;
  border-radius: 4px;
  position: relative;
  overflow: hidden;
}

.group-label::after,
.group-label::before {
  content: "";
  position: absolute;
  top: 50%;
  width: 30px;
  height: 1px;
  background-color: #fce8e6; /* 浅红分隔线 */
}

.group-label::before {
  left: 20px;
}

.group-label::after {
  right: 20px;
}

/* 选项项 - 红色系交互 */
.option-item {
  padding: 14px 20px;
  color: #303133;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.2s ease;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: flex;
  align-items: center;
  gap: 12px;
  position: relative;
}

.option-item:hover,
.option-item.is-hover {
  background-color: #fff2f0; /* 浅红背景 */
  color: #ff4d4f; /* 红色文字 */
}

.option-item.is-selected {
  background-color: #fff2f0; /* 浅红背景 */
  color: #ff4d4f; /* 红色文字 */
  font-weight: 500;
}

/* 选中状态指示（红色） */
.option-item.is-selected::after {
  content: "✓";
  position: absolute;
  right: 20px;
  font-size: 16px;
  color: #ff4d4f; /* 红色对勾 */
}

/* 高亮选项 - 红色强化 */
.option-item.is-highlighted {
  color: #d93025; /* 深红文字 */
  font-weight: bold;
}

.option-item.is-highlighted.is-selected {
  background-color: #feebe9; /* 更深的浅红背景 */
  color: #d93025;
}

.option-item.is-highlighted:hover {
  background-color: #feebe9;
  color: #d93025;
}

/* 底部提示 - 红色系文字 */
.select-footer {
  padding: 12px 20px;
  margin-top: 8px;
  border-top: 1px solid #fce8e6; /* 浅红边框 */
  color: #d93025; /* 红色提示文字 */
  font-size: 14px;
  text-align: center;
  background-color: #fff2f0; /* 浅红背景 */
}

/* 图片错误状态（红色系） */
.option-image.error,
.selected-item-image.error {
  background-color: #fff2f0; /* 浅红背景 */
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ff8a80; /* 红色提示 */
}
</style>