<script setup>
import { useAuthStore } from "../stores/authStore";
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";

const authStore = useAuthStore();
const router = useRouter();
const clientShow = ref(false);
const innerShow = ref(false);
const username = ref("");
const password = ref("");
const errorMessage = ref("");
const loading = ref(false);
const loginCard = ref(null);

function validateString(str) {
  const regex = /^1\d{10}$/;
  return regex.test(str);
}

const authData = [
  { username: "123", password: "123" },
  { username: "Test", password: "Test" },
  { username: "ryan.yang", password: "sgs0755" },
  { username: "SGSEAA", password: "sgseaa" },
  { username: "cynthia.kong", password: "18243399875" },
  { username: "tom.huang", password: "15013567729" },
  { username: "apple-ww.wang", password: "15921745934" },
  { username: "maggie-mt.guo", password: "13045680426" },
  { username: "antti.zhu", password: "13914986263" },
  { username: "jeff-jf.zhang", password: "13772189423" },
  { username: "tina.Yuan", password: "13913917146" },
  { username: "ivan.cui", password: "13584802881" },
  { username: "Bella-jj.Zhang", password: "13405189037" },
  { username: "mike.gong", password: "18620373810" },
  { username: "gavin-h.chen", password: "13823653476" },
  { username: "Winnie-JH.Wen", password: "18814118621" },
  { username: "Thomas.Tao", password: "13083201002" },
  { username: "Vicki.Chen", password: "18800208560" },
  { username: "fiona.zheng", password: "18017840748" },
  { username: "Vivily.song", password: "15528819725" },
  { username: "leon.lau", password: "13560714488" },
  { username: "jason-gm.tang", password: "13509643013" },
  { username: "Sophie.tang", password: "18823716013" },
  { username: "bella-f.liu", password: "13530687745" },
  { username: "banghai.chen", password: "16625149089" },
  { username: "kenneth.yang", password: "18687381201" },
  { username: "sandy-ss.liang", password: "13420912650" },
  { username: "richter.quan", password: "18627346920" },
  { username: "mandy.wu", password: "13715250527" },
  { username: "kellen.liu", password: "13713968242" },
  { username: "rainie.deng", password: "18033317112" },
  { username: "sasa.shao", password: "15236821055" },
  { username: "michael-yj.xie", password: "13312997991" },
  { username: "abby.jiang", password: "19854794656" }
];

const judgeLogin = (name, word) => {
  for (let i = 0; i < authData.length; i++) {
    if (authData[i].username == name && authData[i].password == word) {
      return true;
    }
  }
  return false;
};

const handleLogin = async () => {
  errorMessage.value = "";

  if (!username.value) {
    errorMessage.value = "请输入账号";
    return;
  }

  if (!password.value) {
    errorMessage.value = "请输入密码";
    return;
  }

  loading.value = true;

  try {
    await new Promise((resolve) => setTimeout(resolve, 800));
    if (judgeLogin(username.value, password.value)) {
      localStorage.setItem("token", "true");
      
      loginCard.value.classList.add("success-animation");
      setTimeout(() => router.push("/Main"), 500);
    } else {
      errorMessage.value = "账号或密码错误";
      loginCard.value.classList.add("shake-animation");
      setTimeout(
        () => loginCard.value.classList.remove("shake-animation"),
        500
      );
    }
  } catch (error) {
    console.log(error);
    errorMessage.value = "登录过程中发生错误，请重试";
  } finally {
    loading.value = false;
  }
};

const changeClient = () => {
  clientShow.value = true;
  authStore.changeAuth("client");
  localStorage.setItem("token", "visit");
  loginCard.value.classList.add("success-animation");
  setTimeout(() => router.push("/Main"), 500);
};

const changeInner = () => {
  innerShow.value = true;
};

onMounted(() => {
  loginCard.value = document.querySelector(".login-card");
});
</script>

<template>
  <div class="login-container">
    <!-- 背景图片 -->
    <div class="bg-image"></div>

    <!-- 半透明遮罩 -->
    <div class="bg-overlay"></div>

    <!-- 新增：Accessibility Team卡片 -->
    <div class="team-card">
      <div class="team-card-wrapper">
        <div class="team-info">
          <div class="text-group">
            <div class="main-title">Accessibility Team</div>
            <div class="standard-code">DIRECTIVE (EU) 2019/882</div>
            <div class="version-details">
              <span class="standard-version">EN 301 549 </span>
              <span class="standard-version">V4.1.1c(2025-05)</span>
              <span class="app-version">- v.0.0.15</span>
            </div>
          </div>
          <img src="../assets/logo.png" class="team-logo" alt="实验室Logo" />
        </div>
      </div>
    </div>

    <!-- 登录卡片 -->
    <div class="login-card animate-fade-in" ref="loginCard">
      <!-- 错误提示 -->
      <div v-if="errorMessage" class="error-notification">
        <i class="fa fa-exclamation-circle"></i>
        <span>{{ errorMessage }}</span>
      </div>

      <!-- 登录表单 -->
      <form @submit.prevent="handleLogin" class="login-form">
        <!-- 账号输入 -->
        <div class="form-group">
          <div class="input-group" :class="{ 'is-error': errorMessage }">
            <span class="input-icon">
              <i class="fa fa-user"></i>
            </span>
            <el-input
              v-model="username"
              type="text"
              placeholder="请输入英文名"
              class="form-input"
              autocomplete="off"
              required
            >

            </el-input>
          </div>
        </div>

        <!-- 密码输入 -->
        <div class="form-group">
          <div class="input-group" :class="{ 'is-error': errorMessage }">
            <span class="input-icon">
              <i class="fa fa-lock"></i>
            </span>
            <input
              v-model="password"
              type="password"
              placeholder="请输入手机号"
              class="form-input"
              required
            />
          </div>
        </div>

        <!-- 登录按钮 -->
        <button
          type="submit"
          class="login-btn"
          :disabled="loading"
          :class="{ 'btn-loading': loading }"
        >
          <span v-if="!loading">登录系统</span>
          <span v-else>
            <i class="fa fa-spinner fa-spin"></i> 登录中...
          </span>
        </button>
      </form>
    </div>
  </div>
</template>

<style scoped>
/* 核心：禁止全局滚动 */
html,
body {
  overflow: hidden;
  height: 100%;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Inter", system-ui, -apple-system, sans-serif;
}

/* 登录容器：确保占满视口且无溢出 */
.login-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  position: relative;
  overflow: hidden;
  padding: 16px;
}

/* 背景图片：确保不超出容器 */
.bg-image {
  position: absolute;
  inset: 0;
  background-repeat: no-repeat;
  background-position: center;
  background-size: contain;
  opacity: 0.3;
  z-index: 0;
  transform: scale(1.1);
}

/* 遮罩层 */
.bg-overlay {
  position: absolute;
  inset: 0;
  background: linear-gradient(
    135deg,
    rgba(255, 255, 255, 0.7) 0%,
    rgba(245, 247, 250, 0.95) 100%
  );
  z-index: 1;
}

/* 新增：团队卡片样式 */
.team-card {
  position: relative;
  z-index: 2;
  width: 100%;
  max-width: 500px;
  margin-bottom: 25px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  border-radius: 16px;
  overflow: hidden;
  transform: translateY(-10px);
  animation: slideInDown 0.5s ease-out forwards;
}

.team-card-wrapper {
  padding: 20px;
  background: linear-gradient(135deg, #ffffff 0%, #f1f0f0 100%);
  border-radius: 16px;
}

.team-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}

.text-group {
  flex: 1;
  color: white;
  min-width: 0;
}

.main-title {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 8px;
  letter-spacing: 0.5px;
  text-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
}

.standard-code {
  font-size: 16px;
  font-weight: 500;
  margin-bottom: 6px;
  opacity: 0.9;
}

.version-details {
  font-size: 14px;
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  opacity: 0.85;
}

.standard-version {
  font-weight: 500;
}

.team-logo {
  width: 80px;
  height: 80px;
  object-fit: contain;
  border-radius: 10px;
  padding: 8px;
  background-color: rgba(255, 255, 255, 0.15);
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
}

/* 登录卡片：限制最大高度，确保不超出视口 */
.login-card {
  width: 100%;
  max-width: 400px;
  min-width: 280px;
  background: white;
  border-radius: 20px;
  padding: clamp(24px, 8vw, 36px) clamp(20px, 5vw, 30px);
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.08);
  position: relative;
  z-index: 2;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  max-height: 85vh;
  overflow-y: auto;
  scrollbar-width: none;
}

/* 隐藏内部滚动条（webkit浏览器） */
.login-card::-webkit-scrollbar {
  display: none;
}

/* 标题 */
.login-title {
  font-size: clamp(18px, 5vw, 22px);
  font-weight: 500;
  color: #303133;
  text-align: center;
  margin-bottom: clamp(18px, 5vw, 24px);
}

/* 错误提示 */
.error-notification {
  display: flex;
  align-items: center;
  padding: 10px;
  background-color: #fef0f0;
  color: #f56c6c;
  border-radius: 8px;
  margin-bottom: 18px;
  font-size: 13px;
  opacity: 0;
  transform: translateY(-10px);
  animation: fadeInUp 0.3s ease forwards;
}

.error-notification .fa-exclamation-circle {
  margin-right: 8px;
}

/* 表单组 */
.form-group {
  margin-bottom: clamp(16px, 4vw, 20px);
}

/* 输入框容器 */
.input-group {
  display: flex;
  align-items: center;
  border: 1px solid #dcdfe6;
  border-radius: 10px;
  padding: 0 14px;
  height: clamp(42px, 10vw, 48px);
  transition: all 0.3s ease;
}

.input-group:hover {
  border-color: #c0c4cc;
}

.input-group.is-error {
  border-color: #f56c6c;
}

/* 输入框图标 */
.input-icon {
  width: 22px;
  color: #505052;
  margin-right: 10px;
  flex-shrink: 0;
}

/* 输入框样式 */
.form-input {
  flex: 1;
  height: 100%;
  border: none;
  outline: none;
  font-size: clamp(14px, 3vw, 15px);
  color: #0a0a0a;
  background: transparent;
}

.form-input::placeholder {
  color: #c0c4cc;
}

/* 登录按钮 */
.login-btn {
  width: 100%;
  height: clamp(44px, 10vw, 48px);
  background: linear-gradient(135deg, #d45a1a, #d45a1a);
  border: none;
  border-radius: 10px;
  color: white;
  font-size: clamp(14px, 3vw, 15px);
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 5px 15px rgba(64, 158, 255, 0.2);
  margin-top: 8px;
}

.login-btn:hover:not(.btn-loading) {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(64, 158, 255, 0.3);
}

.login-btn:active:not(.btn-loading) {
  transform: translateY(0);
  box-shadow: 0 3px 10px rgba(64, 158, 255, 0.2);
}

.login-btn.btn-loading {
  background: linear-gradient(135deg, #d45a1a, #d45a1a);
  cursor: not-allowed;
}

/* 动画效果 */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes success-pulse {
  0% {
    transform: scale(1);
    box-shadow: 0 0 0 rgba(66, 184, 131, 0);
  }
  50% {
    transform: scale(1.03);
    box-shadow: 0 0 20px rgba(66, 184, 131, 0.6);
  }
  100% {
    transform: scale(1);
    box-shadow: 0 0 0 rgba(66, 184, 131, 0);
  }
}

@keyframes shake-animation {
  0%,
  100% {
    transform: translateX(0);
  }
  10%,
  30%,
  50%,
  70%,
  90% {
    transform: translateX(-4px);
  }
  20%,
  40%,
  60%,
  80% {
    transform: translateX(4px);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes slideInDown {
  from {
    opacity: 0;
    transform: translateY(-30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fadeIn 0.5s ease forwards;
}

.success-animation {
  animation: success-pulse 1s ease-in-out;
}

.shake-animation {
  animation: shake-animation 0.5s ease-in-out;
}

/* 多断点优化 */
@media (max-width: 768px) {
  .login-card {
    box-shadow: 0 8px 25px rgba(0, 0, 0, 0.06);
    max-height: 90vh;
  }
  
  .team-card {
    max-width: 400px;
    margin-bottom: 20px;
  }
  
  .main-title {
    font-size: 20px;
  }
  
  .standard-code {
    font-size: 15px;
  }
  
  .version-details {
    font-size: 13px;
  }
  
  .team-logo {
    width: 70px;
    height: 70px;
  }
}

@media (max-width: 576px) {
  .login-container {
    padding: 10px;
  }

  .login-card {
    border-radius: 16px;
    max-height: 92vh;
  }
  
  .team-card {
    max-width: 350px;
    margin-bottom: 15px;
  }
  
  .main-title {
    font-size: 18px;
  }
  
  .standard-code {
    font-size: 14px;
  }
  
  .version-details {
    font-size: 12px;
    gap: 6px;
  }
  
  .team-logo {
    width: 60px;
    height: 60px;
  }
}

@media (max-width: 360px) {
  .input-group {
    padding: 0 10px;
  }
  
  .team-card {
    max-width: 320px;
  }
}

/* 登录表单容器 */
.login-form {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: clamp(16px, 4vw, 20px);
  margin-top: 8px;
}

/* 表单组容器 - 增强间距控制 */
.form-group {
  width: 100%;
  position: relative;
}

/* 输入框容器 - 优化布局与状态反馈 */
.input-group {
  display: flex;
  align-items: center;
  border: 1px solid #dcdfe6;
  border-radius: 10px;
  padding: 0 14px;
  height: clamp(42px, 10vw, 48px);
  transition: all 0.3s ease;
  background-color: #fff;
}

/* 输入框容器交互状态 */
.input-group:hover {
  border-color: #b3d8ff;
  box-shadow: 0 0 0 2px rgba(64, 160, 255, 0);
}

.input-group:focus-within {
  border-color: #409eff;
  box-shadow: 0 0 0 2px rgba(64, 160, 255, 0);
  outline: none;
}

/* 错误状态强化 */
.input-group.is-error {
  border-color: #f56c6c;
  box-shadow: 0 0 0 2px rgba(245, 108, 108, 0.15);
}

.input-group.is-error:hover {
  border-color: #f78989;
}

/* 输入框图标优化 */
.input-icon {
  width: 22px;
  color: #909399;
  margin-right: 10px;
  flex-shrink: 0;
  transition: color 0.3s ease;
}

.input-group:focus-within .input-icon {
  color: #409eff;
}

.input-group.is-error .input-icon {
  color: #f56c6c;
}

/* 统一输入框样式（兼容el-input和原生input） */
.form-input,
.el-input {
  flex: 1;
  height: 100%;
  border: none;
  outline: none;
  font-size: clamp(14px, 3vw, 15px);
  color: #303133;
  background: transparent;
  padding: 0;
}

/* 适配el-input内部输入框 */
.el-input__inner {
  height: 100% !important;
  padding: 0 !important;
  border: none !important;
  border-radius: 0 !important;
  box-shadow: none !important;
  font-size: clamp(14px, 3vw, 15px) !important;
}

/* el-input后缀文本样式 */
.el-input__append {
  background: transparent !important;
  border-left: none !important;
  padding: 0 0 0 8px !important;
  color: #909399 !important;
  font-size: clamp(14px, 3vw, 15px) !important;
}

/* 输入框占位符样式统一 */
.form-input::placeholder,
.el-input__inner::placeholder {
  color: #c0c4cc;
  transition: color 0.3s ease;
}

.input-group:focus-within .form-input::placeholder,
.input-group:focus-within .el-input__inner::placeholder {
  color: #dcdfe6;
}

/* 登录按钮优化 */
.login-btn {
  width: 100%;
  height: clamp(44px, 10vw, 48px);
  background: linear-gradient(135deg, #409eff, #66b1ff);
  border: none;
  border-radius: 10px;
  color: white;
  font-size: clamp(14px, 3vw, 15px);
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 5px 15px rgba(64, 158, 255, 0.2);
  margin-top: 8px;
  position: relative;
  overflow: hidden;
}

/* 按钮悬停效果增强 */
.login-btn:hover:not(.btn-loading) {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(64, 158, 255, 0.3);
  background: linear-gradient(135deg, #3689e6, #5ca8f8);
}

/* 按钮点击效果 */
.login-btn:active:not(.btn-loading) {
  transform: translateY(0);
  box-shadow: 0 3px 10px rgba(64, 158, 255, 0.2);
  background: linear-gradient(135deg, #3079d1, #549be6);
}

/* 禁用状态优化 */
.login-btn:disabled {
  opacity: 0.8;
  cursor: not-allowed;
  transform: none;
  box-shadow: 0 5px 15px rgba(64, 158, 255, 0.15);
  background: linear-gradient(135deg, #a0cfff, #c6e2ff);
}

/* 加载状态图标优化 */
.login-btn .fa-spinner {
  margin-right: 6px;
  font-size: 16px;
  animation: spin 1s linear infinite;
}

/* 加载动画优化 */
@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

/* 小屏幕适配调整 */
@media (max-width: 360px) {
  .input-group {
    padding: 0 10px;
  }

  .input-icon {
    margin-right: 8px;
    width: 20px;
  }

  .login-btn {
    height: 44px;
    font-size: 14px;
  }
}
</style>