<script setup>
import { ref, computed, watch } from "vue";
import Logout from "./Logout.vue";
import Deliver from "./Deliver.vue";
import ExportButton from "./ExportButton.vue";
import Select from "./Select.vue";
import Document from "./Document.vue";
import { exportJsonToExcel } from "../utils/excel-export";
import Head from "./Head.vue";
import { useAuthStore } from "../stores/authStore";
import AIOImg from "@/assets/image/AIO.svg";
import AppImg from "@/assets/image/App.svg";
import CameraImg from "@/assets/image/Camera.svg";
import PadImg from "@/assets/image/Pad.svg";
import PhoneImg from "@/assets/image/Phone.svg";
import POSImg from "@/assets/image/POS.svg";
import ProjectorImg from "@/assets/image/Projector.svg";
import RouterImg from "@/assets/image/Router.svg";
import SetTopImg from "@/assets/image/Set-top box.svg";
import SmartHomeImg from "@/assets/image/Smart home.svg";
import TelephoneImg from "@/assets/image/Telephone.svg";
import TreadmillImg from "@/assets/image/Treadmill.svg";
import TVImg from "@/assets/image/TV.svg";
import WatchImg from "@/assets/image/Watch.svg";
import WebsiteImg from "@/assets/image/Website.svg";
import CashRegister from "@/assets/image/CashRegister.svg";
import Earphones from "@/assets/image/Earphones.svg";
import Printer from "@/assets/image/Printer.svg";
import SmartBand from "@/assets/image/SmartBand.svg";
import SmartWhiteboard from "@/assets/image/SmartWhiteboard.svg";
import IntelligentLock from "@/assets/image/IntelligentLock.svg";

const authStore = useAuthStore();

// 密码悬浮窗相关状态
const showPasswordModal = ref(false);
const password = ref("");

// 控制各区块显示/隐藏的状态（默认显示）
const showClause = ref(true);
const showLeadTime = ref(true);
const showTestSample = ref(true);
const showQuotation = ref(false);
const showPaper = ref(false);
const showDeliver = ref(false);

// 常见移动端高亮映射
const osHightMap = {
  Camera: true,
  POS: true,
  "Router/CPE/AP/Gateway": true,
  "Set-top Box": true,
  "Smart Home Devices": true,
  Television: true,
};
const osHight = computed(() => osHightMap[value.value] || false);

// 章节固定全选
const clauseALL = true;
// 章节可选择
const clausechecked = ref([]);

// 下拉选择框
const value = ref(null);
const options = [
  {
    label: "== EAA Product产品 ==",
    options: [
      {
        value: "All in One(AIO)",
        label: "All in One(AIO)丨一体机",
        image: AIOImg,
      },
      { value: "Camera", label: "Camera丨摄像头", image: CameraImg },
      {
        value: "Cash Register",
        label: "Cash Register丨收银机",
        image: CashRegister,
      },
      {
        value: "Earphones",
        label: "Earphones(with App)丨耳机",
        image: Earphones,
      },
      {
        value: "Intelligent Lock",
        label: "Intelligent Lock | 智能门锁",
        image: IntelligentLock,
      },
      { value: "POS/ATM", label: "POS/ATM丨POS机/取款机", image: POSImg },
      { value: "Printer", label: "Printer丨打印机", image: Printer },
      { value: "Projector", label: "Projector丨投影仪", image: ProjectorImg },
      {
        value: "Router/CPE/AP/Gateway",
        label: "Router/CPE/AP/Gateway丨路由器类",
        image: RouterImg,
      },
      { value: "Set-top Box", label: "Set-top Box丨机顶盒", image: SetTopImg },
      { value: "Smart Band", label: "Smart Band丨智能手环", image: SmartBand },
      {
        value: "Smart Home Devices",
        label: "Smart Home Devices丨智能家庭设备",
        image: SmartHomeImg,
      },
      {
        value: "Smart Phone",
        label: "Smart Phone丨智能手机",
        image: PhoneImg,
      },
      {
        value: "Smart Watch",
        label: "Smart Watch丨智能手表",
        image: WatchImg,
      },
      {
        value: "Smart Whiteboard",
        label: "Smart Whiteboard丨智能白板 ",
        image: SmartWhiteboard,
      },
      { value: "Tablet/Pad", label: "Tablet/Pad丨平板电脑", image: PadImg },
      { value: "Telephone", label: "Telephone丨固话机", image: TelephoneImg },
      { value: "Television", label: "Television丨电视", image: TVImg },
      { value: "Treadmill", label: "Treadmill丨跑步机", image: TreadmillImg },
    ],
  },
  {
    label: "== EAA Services服务 ==",
    options: [
      { value: "Website(s)", label: "Website丨网站 ", image: WebsiteImg },
      {
        value: "Mobile App(s)",
        label: "Mobile Application丨应用程序 ",
        image: AppImg,
      },
    ],
  },
];

// 移动端操作系统选择器
const checkedIOS = ref(false);
const checkedAndroid = ref(false);
const checkedOthers = ref(false);
const checkedWindows = ref(false);
const checkedMac = ref(false);
const showOsSelection = ref(false);

const checkedRTT = ref(false);
const checkedHAC = ref(false);

const quantity = computed(() => {
  let quantity = 2;
  if (value.value == "Treadmill") {
    quantity -= 1;
  }
  if (checkedRTT.value) quantity += 3;
  if (checkedHAC.value) quantity += 1;
  return quantity;
});

const inputOS = ref("");
const appNum = ref(null);
const MobileappNum = ref(null);
const linkNum = ref(null);

// 章节映射 - 所有产品默认不包含Clause 11
const clauseMap = {
  "All in One(AIO)": [
    "Clause 5",
    "Clause 7",
    "Clause 8",
    "Clause 10",
    "Clause 12",
  ],
  Camera: ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  "Cash Register": ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  Earphones: ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  "Intelligent Lock": ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  "POS/ATM": ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  Printer: ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  Projector: ["Clause 5", "Clause 7", "Clause 8", "Clause 10", "Clause 12"],
  "Router/CPE/AP/Gateway": [
    "Clause 5",
    "Clause 8",
    "Clause 9",
    "Clause 10",
    "Clause 12",
  ],
  "Set-top Box": ["Clause 5", "Clause 7", "Clause 8", "Clause 10", "Clause 12"],
  "Smart Band": ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  "Smart Home Devices": ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  "Smart Phone": [
    "Clause 5",
    "Clause 6",
    "Clause 7",
    "Clause 8",
    "Clause 10",
    "Clause 12",
  ],
  "Smart Watch": [
    "Clause 5",
    "Clause 6",
    "Clause 7",
    "Clause 8",
    "Clause 10",
    "Clause 12",
  ],
  "Tablet/Pad": ["Clause 5", "Clause 7", "Clause 8", "Clause 10", "Clause 12"],
  Telephone: ["Clause 5", "Clause 6", "Clause 8", "Clause 10", "Clause 12"],
  "Smart Whiteboard": [
    "Clause 5",
    "Clause 7",
    "Clause 8",
    "Clause 10",
    "Clause 12",
  ],
  Treadmill: ["Clause 5", "Clause 8", "Clause 10", "Clause 12"],
  Television: ["Clause 5", "Clause 7", "Clause 8", "Clause 10", "Clause 12"],
  "Website(s)": ["Clause 9"],
  "Mobile App(s)": ["Clause 11"], // 移动应用默认包含Clause 11
};
// 判断是否需要添加Clause 11
const shouldIncludeClause11 = computed(() => {
  return appNum.value !== null || showOsSelection.value;
});

// 统一处理Clause 11的显示逻辑
const Clause = computed(() => {
  // 获取基础条款列表
  const baseClauses = clauseMap[value.value] || [];

  // 特殊情况：移动应用始终包含Clause 11
  if (value.value === "Mobile App(s)") {
    return baseClauses;
  }
  if (shouldIncludeClause11.value) {
    // 复制基础条款数组
    const clausesWith11 = [...baseClauses];
    // 找到Clause 10的位置，在其后插入Clause 11
    const index = clausesWith11.indexOf("Clause 10");
    if (index !== -1) {
      clausesWith11.splice(index + 1, 0, "Clause 11");
    } else {
      // 如果没有Clause 10，添加到末尾
      clausesWith11.push("Clause 11");
    }
    return clausesWith11;
  }

  // 不需要添加Clause 11，直接返回基础条款
  return baseClauses;
});

// 章节标题映射
const titleMap = {
  "Clause 5": "Generic Requirements丨一般要求 丨38 items",
  "Clause 6":
    "ICT supporting continuous bidirectional communication丨支持连续的信息和通信技术功能的信息和技术 丨37 items",
  "Clause 7": "ICT with video capabilities丨具有视频功能的ICT丨8 items",
  "Clause 8": "Hardware丨硬件丨19 items",
  "Clause 9": "Web丨网页 丨57 items",
  "Clause 10": "Non-web documents 丨非网页文档丨57 items",
  "Clause 11": "Software 丨软件 丨69 items",
  "Clause 12":
    "Information about products and services丨有关产品和服务的信息 丨3 items",
};

// 操作系统价格映射
const osPriceMap = { IOS: 10000, Android: 10000, Others: 10000 };

// 条款价格映射
const clausePriceMap = {
  "Clause 5": 15000,
  "Clause 6": 20000,
  "Clause 7": 10000,
  "Clause 8": 5000,
  "Clause 9": 15000,
  "Clause 10": 15000,
  "Clause 11": 25000,
  "Clause 12": 2000,
};

// 价格映射
const priceMap = {
  "All in One(AIO)": 72000,
  Camera: 72000,
  "Cash Register": 62000,
  Earphones: 62000,
  "Intelligent Lock": 62000,
  "POS/ATM": 62000,
  Printer: 72000,
  Projector: 72000,
  "Router/CPE/AP/Gateway": 52000,
  "Set-top Box": 72000,
  "Smart Band": 62000,
  "Smart Home Devices": 72000,
  "Smart Phone": 92000,
  "Smart Watch": 92000,
  "Smart Whiteboard": 92000,
  "Tablet/Pad": 72000,
  Telephone: 82000,
  Television: 72000,
  Treadmill: 72000,
  "Website(s)": 0,
  "Mobile App(s)": 0,
};

// 周期映射
const TimeMap = {
  "All in One(AIO)": [10, 10, 3],
  "Intelligent Lock": [10, 7, 3],
  "POS/ATM": [10, 7, 3],
  Camera: [10, 10, 3],
  Projector: [10, 10, 3],
  "Router/CPE/AP/Gateway": [15, 5, 3],
  "Set-top Box": [15, 5, 3],
  "Smart Band": [15, 5, 3],
  "Smart Home Devices": [10, 10, 3],
  "Smart Phone": [20, 10, 3],
  "Smart Watch": [15, 10, 3],
  "Tablet/Pad": [20, 10, 3],
  Television: [15, 5, 3],
  Telephone: [10, 7, 3],
  Treadmill: [10, 10, 3],
  "Website(s)": [10, 10, 3],
  "Mobile App(s)": [10, 10, 3],
  "Cash Register": [15, 5, 3],
  Printer: [10, 10, 3],
  "Smart Whiteboard": [15, 5, 3],
  Earphones: [10, 7, 3],
};
const Time = computed(() => TimeMap[value.value] || []);

// 计算最终费用
const cost = computed(() => {
  let totalCost = 0;
  if (showOsSelection.value || value.value == "Website(s)") {
    if (checkedWindows.value) totalCost += osPriceMap["IOS"];
    if (checkedMac.value) totalCost += osPriceMap["Android"];
    if (checkedIOS.value) totalCost += osPriceMap["IOS"];
    if (checkedAndroid.value) totalCost += osPriceMap["Android"];
    if (checkedOthers.value) totalCost += osPriceMap["Others"];
  }
  if (authStore.auth == "inner") {
    clausechecked.value.forEach((clause) => {
      totalCost += clausePriceMap[clause] || 0;
    });
  } else {
    if (value.value) totalCost += priceMap[value.value];
    if (!shouldIncludeClause11.value && totalCost !== 0) {
      totalCost -= clausePriceMap["Clause 11"];
    }
  }
  if (linkNum.value) totalCost += linkNum.value * 500;
  if (appNum.value) totalCost += appNum.value * 3000;
  if (MobileappNum.value) totalCost += MobileappNum.value * 3000;
  return totalCost;
});

// 获取选中的操作系统文本
const getSelectedOS = () => {
  if (!showOsSelection.value) return "无";
  const osList = [];
  if (checkedIOS.value) osList.push("IOS");
  if (checkedAndroid.value) osList.push("Android");
  if (checkedOthers.value) osList.push("Others");
  return osList.length ? osList.join(", ") : "无";
};

const handleExport = () => {
  // 导出逻辑（保持不变）
  const titleRow = [{ 项目: "", 信息: "" }];

  const baseInfo = [
    { 项目: "产品/服务类别", 信息: value.value || "未选择" },
    { 项目: "是否支持移动端", 信息: showOsSelection.value ? "是" : "否" },
    { 项目: "支持的操作系统", 信息: getSelectedOS() },
    { 项目: "应用程序数量", 信息: appNum.value || 0 },
    { 项目: "链接数量", 信息: linkNum.value || 0 },
    { 项目: "总费用(CNY)", 信息: `¥${cost.value.toLocaleString()}` },
    { 项目: "", 信息: "" },
  ];

  const testCycle = [
    { 项目: "测试周期详情", 信息: "" },
    {
      项目: "Wave #1: Release Full Test Items",
      信息: Time.value[0] ? `${Time.value[0]} 工作日` : "未定义",
    },
    {
      项目: "Wave #2: Release Full Test Items",
      信息: Time.value[1] ? `${Time.value[1]} 工作日` : "未定义",
    },
    {
      项目: "Wave #3: Release Test Report + VoC",
      信息: Time.value[2] ? `${Time.value[2]} 工作日` : "未定义",
    },
    { 项目: "", 信息: "" },
  ];

  const testClauses = [
    { 项目: "测试条款", 信息: "测试条目数" },
    ...(Clause.value.length
      ? Clause.value.map((clause) => {
          const [, , items] = titleMap[clause]?.split("丨") || ["", "", "未知"];
          return { 项目: clause, 信息: items };
        })
      : [{ 项目: "无", 信息: "未选择产品类别" }]),
    { 项目: "", 信息: "" },
  ];

  const testSamples = [
    { 项目: "测试样品需求", 信息: "数量" },
    { 项目: "正常样机（含出厂配件）", 信息: "2 台" },
    { 项目: "RTT 功能样机", 信息: checkedRTT.value ? "3 台" : "0 台" },
    { 项目: "HAC 功能样机", 信息: checkedHAC.value ? "1 台" : "0 台" },
    { 项目: "总计样品数", 信息: `${quantity.value} 台` },
    { 项目: "", 信息: "" },
  ];

  const requiredDocs = [
    { 项目: "启动测试所需资料", 信息: "备注" },
    {
      项目: "EU 2019/882 测试产品技术资料清单",
      信息: "",
    },
    { 项目: "用户手册（User Manual）", 信息: "需提供完整版本" },
    {
      项目: "型号差异声明（如有多型号）",
      信息: "",
    },
    { 项目: "其他补充资料", 信息: "根据测试类别可能需要额外文件" },
  ];

  const exportData = [
    ...titleRow,
    ...baseInfo,
    ...testCycle,
    ...testClauses,
    ...testSamples,
    ...requiredDocs,
  ];

  const fileName = `EAA_${value.value || "Unselected"}_${
    new Date().toISOString().split("T")[0]
  }`;

  const exportConfig = {
    columnWidths: [{ wch: 60 }, { wch: 60 }],
    cellStyles: [
      {
        condition: (value, row, col) => row === 0,
        style: {
          font: { bold: true, color: { rgb: "FFFFFF" }, sz: 14 },
          fill: {
            patternType: "solid",
            fgColor: { rgb: "064650" },
            bgColor: { indexed: 64 },
          },
          alignment: { horizontal: "center", vertical: "center" },
          border: {
            top: { style: "thin" },
            bottom: { style: "thin" },
            left: { style: "thin" },
            right: { style: "thin" },
          },
        },
      },
      {
        condition: (value, row, col) => row === 1,
        style: {
          font: { bold: true, color: { rgb: "FFFFFF" } },
          fill: {
            patternType: "solid",
            fgColor: { rgb: "7D2222" },
            bgColor: { indexed: 64 },
          },
          alignment: { horizontal: "center" },
        },
      },
      {
        condition: (value, row, col) =>
          [
            2,
            2 + baseInfo.length,
            2 + baseInfo.length + testCycle.length,
            2 + baseInfo.length + testCycle.length + testClauses.length,
          ].includes(row) && col === 0,
        style: {
          font: { bold: true, color: { rgb: "FFFFFF" } },
          fill: {
            patternType: "solid",
            fgColor: { rgb: "7D2222" },
            bgColor: { indexed: 64 },
          },
          alignment: { horizontal: "left" },
        },
      },
      {
        condition: (value, row, col) =>
          row === 2 + baseInfo.length + testCycle.length && col === 1,
        style: {
          font: { bold: true, color: { rgb: "FFFFFF" } },
          fill: {
            patternType: "solid",
            fgColor: { rgb: "7D2222" },
            bgColor: { indexed: 64 },
          },
          alignment: { horizontal: "center" },
        },
      },
      {
        condition: (value, row, col) =>
          typeof value === "string" && value.startsWith("http"),
        style: { font: { underline: true, color: { rgb: "0000FF" } } },
      },
      {
        condition: (value, row, col) =>
          row ===
            2 + baseInfo.length + testCycle.length + testClauses.length + 4 &&
          col === 0,
        style: { font: { bold: true } },
      },
    ],
    merges: [{ s: { r: 0, c: 0 }, e: { r: 0, c: 1 } }],
  };

  exportJsonToExcel(
    exportData,
    fileName,
    { 项目: "SGS EAA 项目报价及认证注意事宜", 信息: "信息" },
    exportConfig
  );
};

// 密码相关方法
const handlePasswordSubmit = () => {
  // 密码提交逻辑
  console.log("输入的密码:", password.value);
  if (password.value === "sgseaa") {
    authStore.changeAuth("inner");
    console.log(authStore.auth);
  }
  // 验证密码后关闭弹窗
  showPasswordModal.value = false;
  password.value = "";
};
const showout = () => {
  authStore.changeAuth(null);
  password.value = "";
};
const closePasswordModal = () => {
  showPasswordModal.value = false;
  password.value = "";
};
</script>
<template>
  <div>
    <Head />
    <div class="product-selection-container">
      <div class="product-card">
        <!-- 产品选择标题 -->
        <div class="card-title-container">
          <h3 class="card-title">Please Select EAA Product/Service</h3>
        </div>
        <!-- 产品下拉选择区 -->
        <div class="form-group">
          <Select
            v-model="value"
            :options="options"
            placeholder="Product Category"
            size="large"
            class="custom-select"
            @change="handleChange"
          />
        </div>

        <div class="link-number">
          <el-input
            v-if="value === 'Website(s)'"
            v-model="linkNum"
            style="width: 300px"
            placeholder="输入Link数量"
          />
        </div>
        <!-- 移动端支持 -->
        <div
          v-if="
            value == 'Smart Phone' ||
            value == 'Smart Watch' ||
            value == 'Tablet/Pad'
          "
          class="apps-input-container"
        >
          <h4 class="section-title">
            Please enter the number of device applications(Apps).
          </h4>
          <div class="apps-input-wrapper">
            <el-input v-model="appNum" placeholder="输入Apps数量" />
          </div>
        </div>
        <div
          v-if="
            value != 'Website(s)' ||
            value != 'Smart Phone' ||
            value != 'Tablet/Pad'
          "
        >
          <!-- 移动端支持复选框 -->
          <div class="mobile-support-option" v-if="value">
            <div v-if="osHight" class="os-hint red-highlight">
              提醒：所选产品除本体支持安装Apps外，还有可能支持移动端Apps控制场景。
            </div>
            <div class="checkbox-text-group">
              <el-checkbox
                v-model="showOsSelection"
                size="large"
                class="os-checkbox"
              />
              <span class="support-text"
                >Does the device support mobile apps? / 是否支持移动端?</span
              >
            </div>
          </div>
          <!-- 移动端操作系统选择区 -->
          <div v-if="showOsSelection" class="os-selection animate-fade-in">
            <h6 class="section-title" style="font-size: 12px;">
              <span v-if="value != 'Mobile App(s)'">如支持移动端Apps，</span
              >请勾选Apps安装在移动端的操作系统：
            </h6 >
            <div class="checkbox-group"style="display: flex; gap: 0px; align-items: center;">
              <el-checkbox
                v-model="checkedIOS"
                label="iOS"
                size="small"
                class="custom-checkbox"
              />
              <el-checkbox
                v-model="checkedAndroid"
                label="Android"
                size="small"
                class="custom-checkbox"
              />
              <el-checkbox
                v-model="checkedOthers"
                label="Others"
                size="small"
                class="custom-checkbox"
              />
              <el-input
                v-if="checkedOthers"
                v-model="inputOS"
                style="width: 120px"
                placeholder="输入系统名称"
              />
            </div>
            <el-input
              v-if="showOsSelection"
              v-model="MobileappNum"
              style="width: 150px"
              placeholder="输入Apps数量"
            />
          </div>
        </div>
        <div v-else>
          <div class="mobile-support-option" v-if="value">
            <h4 class="section-title">请勾选Website(s)适配的操作系统：</h4>
            <div class="checkbox-group">
              <el-checkbox
                v-model="checkedWindows"
                label="Windows"
                size="large"
                class="custom-checkbox"
              />
              <el-checkbox
                v-model="checkedMac"
                label="Mac OS"
                size="large"
                class="custom-checkbox"
              />
              <el-checkbox
                v-model="checkedIOS"
                label="IOS"
                size="large"
                class="custom-checkbox"
              />
              <el-checkbox
                v-model="checkedAndroid"
                label="Android"
                size="large"
                class="custom-checkbox"
              />
              <el-checkbox
                v-model="checkedOthers"
                label="Others"
                size="large"
                class="custom-checkbox"
              />
              <el-input
                v-if="checkedOthers"
                v-model="inputOS"
                style="width: 120px"
                placeholder="输入系统名称"
              />
            </div>
          </div>
        </div>
        <!-- 适用条款区 -->
        <div v-if="Clause.length" class="clause-section-wrapper">
          <div class="section-header">
            <h4 class="section-title">1.Test Clause</h4>
            <button class="toggle-btn" @click="showClause = !showClause">
              {{ showClause ? "隐藏" : "显示" }}
            </button>
          </div>
          <div v-if="showClause" class="clause-section animate-fade-in">
            <div class="red-alert-wrapper">
              <div class="red-alert-text">
                EAA Lab only evaluates the requirements in Table ZB.1 of the
                EN 301 549.
              </div>
              <div
                class="red-alert-text"
                v-if="
                  value == 'Smart Phone' ||
                  value == 'Smart Watch' ||
                  value == 'Tablet/Pad'
                "
              >
                This quotation does not include the HAC tests.
              </div>
            </div>
            <el-checkbox-group
              class="checkbox-group clause-checkboxes"
              v-model="clausechecked"
              v-if="authStore.auth == 'inner'"
            >
              <el-checkbox
                v-for="(item, index) in Clause"
                :key="index"
                :label="item"
                class="custom-checkbox clause-checkbox-item"
                :title="titleMap[item]"
              >
                <div class="clause-item">
                  <span class="clause-label">{{ item }}</span>
                </div>
              </el-checkbox>
            </el-checkbox-group>
            <el-checkbox-group
              class="checkbox-group clause-checkboxes"
              v-model="clauseALL"
              v-else
            >
              <el-checkbox
                v-for="(item, index) in Clause"
                :key="index"
                :label="item"
                class="custom-checkbox clause-checkbox-item"
                :title="titleMap[item]"
                checked="true"
              >
                <div class="clause-item">
                  <span class="clause-label">{{ item }}</span>
                </div>
              </el-checkbox>
            </el-checkbox-group>
          </div>
        </div>

        <!-- 周期区域 -->
        <div v-if="value" class="cycle-section-wrapper">
          <div class="section-header">
            <h4 class="section-title">2.Lead Time(Working Days)</h4>
            <div v-if="value == 'Website(s)'" class="cycle-note">
              Every hundred web pages
            </div>
            <div v-if="value == 'Mobile App(s)'" class="cycle-note">
              Every ten App(s)
            </div>
            <button class="toggle-btn" @click="showLeadTime = !showLeadTime">
              {{ showLeadTime ? "隐藏" : "显示" }}
            </button>
          </div>
          <div v-if="showLeadTime" class="cycle-section animate-fade-in">
            <div class="cycle-content">
              <div class="cycle-item">
                <span class="cycle-label"
                  >Wave #1: Release High Risk Test Items</span
                >
                <span class="cycle-value">{{ Time[0] }} </span>
                <span class="cycle-label">WD</span>
              </div>
              <div class="cycle-item">
                <span class="cycle-label"
                  >Wave #2: Release Full Test Items</span
                >
                <span class="cycle-value">{{ Time[1] }} </span>
                <span class="cycle-label">WD</span>
              </div>
              <div class="cycle-item">
                <span class="cycle-label"
                  >Wave #3: Release Test Report + VoC</span
                >
                <span class="cycle-value">{{ Time[2] }} </span>
                <span class="cycle-label">WD</span>
              </div>
            </div>
            <div class="cycle-remark">
              Remark: The period is based on the sample entering the normal test
              / 周期基于样品进入正常测试
            </div>
          </div>
        </div>

        <!-- 测试样本区域 -->
        <div v-if="value" class="test-samples-section-wrapper">
          <div class="section-header">
            <h4 class="section-title">3.Test Sample(Unit)</h4>
            <button
              class="toggle-btn"
              @click="showTestSample = !showTestSample"
            >
              {{ showTestSample ? "隐藏" : "显示" }}
            </button>
          </div>
          <div v-if="showTestSample" class="test-samples-section">
            <div class="test-samples-group">
              <div class="sample-quantity">
                Sample Quantity
                <span class="quantity-value">{{ quantity }}</span>
              </div>
              <div v-if="value == 'Smart Phone' || value == 'Smart Watch'">
                <el-checkbox
                  v-model="checkedRTT"
                  label="是否支持RTT"
                  size="large"
                  class="sample-checkbox"
                />
              </div>
              <div v-if="value == 'Smart Phone' || value == 'Telephone'">
                <el-checkbox
                  v-model="checkedHAC"
                  label="是否支持HAC"
                  size="large"
                  class="sample-checkbox"
                />
              </div>
            </div>
          </div>
        </div>
        <!-- 费用展示区 -->
        <div class="cost-section-wrapper">
          <div class="section-header">
            <h4 class="section-title">4.Quotation(CNY, Not includingVAT)</h4>
            <button class="toggle-btn" @click="showQuotation = !showQuotation">
              {{ showQuotation ? "隐藏" : "显示" }}
            </button>
          </div>
          <div v-if="showQuotation" class="cost-section animate-fade-in">
            <div class="cost-display">¥{{ cost.toLocaleString() }} (CNY)</div>
          </div>
        </div>
        <!-- 文件清单区 -->
        <div class="documents-section-wrapper">
          <div class="section-header">
            <h4 class="section-title">5.Documents Needed for EAA Test</h4>
            <button class="toggle-btn" @click="showPaper = !showPaper">
              {{ showPaper ? "隐藏" : "显示" }}
            </button>
          </div>
          <div v-if="showPaper" class="documents-section animate-fade-in">
            <Document />
          </div>
        </div>

        <div class="deliver-section-wrapper">
          <div class="section-header">
            <h4 class="section-title">6.Delivery</h4>
            <button class="toggle-btn" @click="showDeliver = !showDeliver">
              {{ showDeliver ? "隐藏" : "显示" }}
            </button>
          </div>
          <div v-if="showDeliver">
            <Deliver />
          </div>
        </div>
      </div>
      <div class="export-section">
        <ExportButton :isDisabled="!value" @export="handleExport" />
        <!-- 新增：密码输入按钮 -->
        <el-button
          type="primary"
          style="margin-left: 10px"
          @click="showPasswordModal = true"
          v-if="authStore.auth !== 'inner'"
        >
          编辑
        </el-button>
        <el-button
          type="primary"
          style="margin-left: 10px"
          @click="showout"
          v-if="authStore.auth == 'inner'"
        >
          退出
        </el-button>
      </div>
    </div>
    <logout />
    <!-- 新增：密码悬浮窗 -->
    <div v-if="showPasswordModal" class="password-modal-overlay">
      <div class="password-modal animate-fade-in">
        <div class="modal-header">
          <h3>请输入密码</h3>
          <button class="close-btn" @click="closePasswordModal">&times;</button>
        </div>
        <div class="modal-body">
          <el-input
            v-model="password"
            type="password"
            placeholder="请输入密码"
            class="password-input"
          />
        </div>
        <div class="modal-footer">
          <el-button @click="closePasswordModal">取消</el-button>
          <el-button type="primary" @click="handlePasswordSubmit"
            >确认</el-button
          >
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
/* 基础样式重置与核心变量 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* 容器布局 */
.product-selection-container {
  max-width: 700px;
  margin: 0 auto;
  padding: 0 15px;
  width: 100%;
}

.product-card {
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  padding: 20px;
  width: 100%;
}

/* 标题样式 */
.card-title {
  font-size: clamp(14px, 3vw, 18px);
  font-weight: 600;
  color: white;
  padding-bottom: 12px;
  margin-bottom: 20px;
  border-bottom: 2px solid rgba(255, 255, 255, 0.2);
  text-align: center;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 100%;
}

/* 表单区域 */
.form-group {
  margin-bottom: 20px;
}

.custom-select {
  width: 100%;
}

/* 链接数量输入区 */
.link-number {
  margin-bottom: 20px;
}

/* 移动端支持选项区 */
.mobile-support-option {
  padding: 0 15px;
  margin: 0;
  border-radius: 8px;
  background: #fff;
  border: 1px solid rgba(237, 109, 45, 0.2);
}

.os-hint.red-highlight {
  color: #ed6d2d;
  font-size: 13px;
  line-height: 1.5;
  margin: 0 0 10px 0;
  padding: 8px 12px;
  border-left: 3px solid #ed6d2d;
  background: rgba(237, 109, 45, 0.05);
  border-radius: 0 4px 4px 0;
}

/* 操作系统选择区 */
.os-selection {
  padding: 5px 15px;
  margin: 10px 0;
  border-radius: 8px;
  background: #fff;
  border: 1px solid rgba(237, 109, 45, 0.1);
}

/* 复选框组样式 - 核心优化：强制靠左对齐 */
.checkbox-group {
  display: flex;
  flex-wrap: wrap;
  padding: 5px 0;
  align-items: center;
  gap: 8px 10px;
  justify-content: flex-start; /* 强制子项靠左对齐 */
  align-content: flex-start; /* 多行时依然靠左 */
}

/* 章节复选框组特殊优化 */
.clause-checkboxes {
  gap: 8px 12px;
  justify-content: flex-start; /* 明确靠左对齐 */
}

/* 自定义复选框样式 - 确保左对齐 */
.custom-checkbox {
  flex: 0 0 auto; /* 不拉伸，保持自身宽度 */
  min-width: calc(20% - 10px); /* 假设每行5个，根据实际情况调整 */
  padding: 0;
  display: flex;
  align-items: center;
  cursor: pointer;
  margin: 0;
  justify-content: flex-start;
}

/* 章节复选框项特殊样式 */
.clause-checkbox-item {
  flex: 1 0 min-content;
  margin: 0;
  padding: 2px 0; /* 移除右侧内边距避免对齐偏移 */
  justify-content: flex-start;
}

/* 章节项内容样式 - 确保左对齐 */
.clause-item {
  background: rgba(237, 109, 45, 0.05);
  padding: 6px 10px;
  border-radius: 6px;
  font-size: 13px;
  color: #333;
  transition: all 0.2s ease;
  width: auto;
  min-width: 80px;
  text-align: left; /* 文本靠左 */
  margin: 0;
}

.clause-item:hover {
  background: rgba(237, 109, 45, 0.1);
}

/* 区块标题样式 */
.section-title {
  font-size: 15px;
  font-weight: 600;
  color: white;
  margin: 0 0 10px 0; /* 增加底部间距，与下方选项区拉开距离 */
  padding: 2px 12px;
  border-radius: 4px;
  background: #ed6d2d;
  display: inline-block;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* 区块头部 */
.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 15px;
  background: rgba(237, 109, 45, 0.05);
  border-bottom: 1px solid rgba(237, 109, 45, 0.1);
  flex-wrap: nowrap;
  gap: 10px;
}

/* 切换按钮样式 */
.toggle-btn {
  background: rgba(237, 109, 45, 0.1);
  border: 1px solid #ed6d2d;
  border-radius: 4px;
  padding: 4px 10px;
  font-size: 12px;
  cursor: pointer;
  color: #ed6d2d;
  transition: all 0.2s ease;
  white-space: nowrap;
  flex-shrink: 0;
  min-width: 60px;
}

.toggle-btn:hover {
  background: #ed6d2d;
  color: #fff;
}

/* 条款区域容器 */
.clause-section-wrapper,
.cycle-section-wrapper,
.test-samples-section-wrapper,
.cost-section-wrapper {
  margin: 5px 0;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.03);
  border: 1px solid rgba(237, 109, 45, 0.2);
}

.clause-section,
.cycle-section,
.test-samples-section,
.cost-section {
  padding: 8px 15px;
  background: #fff;
}

/* 红色提示文本 */
.red-alert-wrapper {
  margin-bottom: 15px;
}

.red-alert-text {
  color: #ed6d2d;
  font-size: 12px;
  line-height: 1.5;
  margin: 0 0 8px 0;
  padding-left: 6px;
  font-weight: 500;
  border-left: 2px solid rgba(237, 109, 45, 0.2);
}

/* 周期项样式 */
.cycle-content {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.cycle-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 12px;
  border-radius: 6px;
  background: rgba(237, 109, 45, 0.05);
  flex-wrap: nowrap;
  gap: 8px;
}

.cycle-label:first-child {
  flex: 1;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  min-width: 0;
}

.cycle-value,
.cycle-label:last-child {
  flex-shrink: 0;
  white-space: nowrap;
}

.cycle-value {
  font-size: 14px;
  color: #ed6d2d;
  font-weight: 600;
  padding: 0 4px;
  margin-right: 4px;
}

.cycle-label {
  font-size: 13px;
  color: #333;
}

/* 测试样本区域 */
.test-samples-group {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 15px;
  padding: 5px 0;
  justify-content: flex-start; /* 同样靠左对齐 */
}

.sample-quantity {
  display: flex;
  align-items: center;
  font-size: 13px;
  color: #333;
  padding-left: 12px;
  border-left: 1px solid rgba(237, 109, 45, 0.2);
  flex: 1;
  min-width: 200px;
}

.quantity-value {
  color: #ed6d2d;
  font-weight: 600;
  margin-left: 8px;
  font-size: 14px;
}

/* 费用显示区 */
.cost-display {
  font-size: 24px;
  font-weight: 600;
  color: #ed6d2d;
  padding: 12px;
  background: rgba(237, 109, 45, 0.05);
  border-radius: 8px;
  text-align: center;
  border: 1px solid rgba(237, 109, 45, 0.1);
}

/* 导出按钮区域 */
.export-section {
  margin: 20px 0;
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 响应式优化 - 保持左对齐 */
@media (max-width: 768px) {
  .product-selection-container {
    padding: 8px;
  }

  .product-card {
    padding: 15px;
  }

  .card-title {
    font-size: clamp(15px, 3vw, 17px);
    padding-bottom: 10px;
    margin-bottom: 15px;
  }

  .section-header {
    padding: 8px 10px;
    gap: 8px;
  }

  .section-title {
    font-size: 14px;
    padding: 4px 8px;
    max-width: calc(100% - 70px);
  }

  .toggle-btn {
    padding: 3px 8px;
    font-size: 12px;
    min-width: 55px;
  }

  .checkbox-group {
    gap: 6px 8px;
  }

  .cycle-item {
    padding: 8px 10px;
    gap: 6px;
  }

  .cycle-label {
    font-size: 12px;
  }

  .cycle-value {
    font-size: 13px;
    padding: 0 2px;
  }

  .test-samples-group {
    flex-direction: column;
    align-items: flex-start; /* 垂直排列时靠左 */
    gap: 10px;
  }

  .sample-quantity {
    border-left: none;
    padding-left: 0;
    margin-bottom: 5px;
    min-width: 100%;
  }

  .el-input {
    width: 100% !important;
  }

  .cost-display {
    font-size: 20px;
    padding: 10px;
  }
}

@media (max-width: 480px) {
  .card-title {
    font-size: clamp(14px, 4vw, 16px);
    padding: 0 5px 10px;
  }

  .custom-checkbox {
    flex: 1 0 45%;
  }

  .clause-item {
    font-size: 12px;
    padding: 5px 10px;
  }

  .section-title {
    font-size: 13px;
    padding: 3px 6px;
    max-width: calc(100% - 65px);
  }

  .toggle-btn {
    font-size: 11px;
    padding: 2px 6px;
    min-width: 50px;
  }

  .cycle-item {
    padding: 6px 8px;
    gap: 4px;
  }

  .cycle-label {
    font-size: 12px;
  }

  .cycle-value {
    font-size: 13px;
  }
}

/* 超小屏幕优化 */
@media (max-width: 360px) {
  .clause-checkbox-item {
    flex: 1 0 100%;
  }

  .checkbox-group {
    gap: 5px;
  }
}

/* 动画效果 */
.animate-fade-in {
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(5px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
/* Apps数量输入区容器样式 */
.apps-input-container {
  margin: 10px 0;
  padding: 8px 15px;
  border-radius: 8px;
  border: 1px solid rgba(237, 109, 45, 0.2);
  background: #fff;
}

/* 确保标题与其他区块标题样式统一并添加底部间距 */
.apps-input-container .section-title {
  margin-bottom: 12px;
  display: block;
  padding: 4px 12px;
}

/* 优化输入框样式，移除固定宽度，使用响应式设计 */
.apps-input-container .el-input {
  width: 100% !important;
  max-width: 400px; /* 保留最大宽度限制 */
  margin: 0;
  padding: 0;
}

/* 输入框容器样式，确保与标题左对齐 */
.apps-input-wrapper {
  padding-left: 12px; /* 与标题内边距匹配，确保左对齐 */
}

/* 响应式调整 */
@media (max-width: 768px) {
  .apps-input-container {
    padding: 8px 10px;
  }

  .apps-input-container .section-title {
    padding: 4px 8px;
    margin-bottom: 10px;
  }

  .apps-input-wrapper {
    padding-left: 8px;
  }
}

@media (max-width: 480px) {
  .apps-input-container {
    margin: 8px 0;
    padding: 6px 8px;
  }

  .apps-input-container .section-title {
    font-size: 13px;
    padding: 3px 6px;
    margin-bottom: 8px;
  }

  .apps-input-wrapper {
    padding-left: 6px;
  }
}
/* 周期说明文本样式 */
.cycle-note {
  color: #ed6d2d; /* 与标题同色系保持协调 */
  font-size: 12px;
  font-weight: 500;
  white-space: nowrap; /* 防止文本换行 */
  flex-shrink: 0; /* 避免在flex布局中被压缩 */
  margin: 0 5px; /* 与左右元素保持间距 */
  padding: 2px 0; /* 垂直方向居中对齐 */
}

/* 响应式调整 */
@media (max-width: 768px) {
  .cycle-note {
    font-size: 11px; /* 小屏幕缩小字体 */
    margin: 0 3px; /* 减小间距 */
  }
}

@media (max-width: 480px) {
  .cycle-note {
    font-size: 10px; /* 超小屏幕进一步缩小 */
    margin: 0 2px;
  }
}
/* 文件清单区域样式 - 与其他区块保持视觉一致性 */
.documents-section-wrapper {
  margin: 5px 0;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.03);
  border: 1px solid rgba(237, 109, 45, 0.2);
}

/* 资料内容区域样式 */
.documents-section {
  padding: 12px 15px;
  background: #fff;
}

/* 单个资料项样式 */
.document-item {
  margin-bottom: 12px;
  padding: 8px 0;
  border-bottom: 1px dashed rgba(237, 109, 45, 0.1);
}

.document-item:last-child {
  margin-bottom: 0;
  border-bottom: none;
}

/* 资料标题文本样式 */
.document-item > div:first-child {
  font-size: 13px;
  color: #333;
  margin-bottom: 6px;
  font-weight: 500;
  padding-left: 4px;
}

/* 链接样式优化 - 增强可点击性 */
.document-item a {
  display: inline-block;
  color: #0066cc; /* 标准链接蓝色 */
  font-size: 12px;
  text-decoration: underline;
  line-height: 1.5;
  max-width: 100%;
  word-break: break-all; /* 长链接自动换行 */
  padding-left: 4px;
  transition: color 0.2s ease;
}

.document-item a:hover {
  color: #ed6d2d; /*  hover时与主题色呼应 */
  text-decoration: none;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .documents-section {
    padding: 10px 12px;
  }

  .document-item {
    margin-bottom: 10px;
    padding: 6px 0;
  }

  .document-item > div:first-child {
    font-size: 12px;
    margin-bottom: 4px;
  }

  .document-item a {
    font-size: 11px;
    line-height: 1.6;
  }
}

@media (max-width: 480px) {
  .documents-section {
    padding: 8px 10px;
  }

  .document-item {
    margin-bottom: 8px;
  }
}
/* 周期备注文本样式 */
.cycle-remark {
  color: #666; /* 深灰色，既清晰又不喧宾夺主 */
  font-size: 12px; /* 比正文稍小，体现次要信息属性 */
  line-height: 1.5; /* 优化行高，提升可读性 */
  margin: 8px 15px 5px; /* 上下间距分离内容，左对齐周期区域 */
  padding-left: 6px; /* 左侧缩进，与周期项内容对齐 */
  border-left: 2px solid rgba(237, 109, 45, 0.1); /* 浅色左侧边框，强化"补充说明"视觉属性 */
}

/* 响应式调整 */
@media (max-width: 768px) {
  .cycle-remark {
    font-size: 11px; /* 小屏幕缩小字体 */
    margin: 6px 12px 3px; /* 减小间距 */
    padding-left: 4px;
  }
}

@media (max-width: 480px) {
  .cycle-remark {
    font-size: 10px; /* 超小屏幕进一步优化 */
    margin: 5px 10px 2px;
  }
}
/* 标题容器样式 - 背景色设置 */
.card-title-container {
  background-color: #ed6d2d; /* 橙色背景 */
  padding: 10px 15px; /* 内边距确保文字不贴边 */
  border-radius: 8px 8px 8px 8px; /* 顶部圆角与卡片呼应 */
  margin: 0 -20px 20px; /* 负外边距抵消父容器内边距，使背景全屏宽 */
}

/* 标题文字样式 - 白色字体 */
.card-title {
  color: white !important; /* 强制白色字体 */
  border-bottom: 2px solid rgba(255, 255, 255, 0.2); /* 白色半透明边框，适配橙色背景 */
  margin-bottom: 0; /* 移除底部外边距，避免与容器内边距冲突 */
  padding: 5px 0; /* 调整内边距 */
  text-align: center; /* 文字居中 */
}

/* 响应式适配 */
@media (max-width: 768px) {
  .card-title-container {
    margin: 0 -15px 15px; /* 适配小屏幕父容器内边距 */
    padding: 8px 10px;
  }
}

@media (max-width: 480px) {
  .card-title-container {
    margin: 0 -8px 12px;
    padding: 6px 8px;
  }
}

/* 标题文字样式 - 白色字体 */
.card-title {
  color: white !important; /* 强制白色字体 */
  border-bottom: none; /* 移除下划白线 */
  margin-bottom: 0; /* 移除底部外边距，避免与容器内边距冲突 */
  padding: 5px 0; /* 调整内边距 */
  text-align: center; /* 文字居中 */
}

/* 新增：密码悬浮窗样式 */
.password-modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.password-modal {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  width: 90%;
  max-width: 400px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 1px solid #eee;
}

.modal-header h3 {
  margin: 0;
  color: #333;
  font-size: 16px;
}

.close-btn {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: #999;
  padding: 0 5px;
}

.close-btn:hover {
  color: #ed6d2d;
}

.modal-body {
  margin-bottom: 20px;
}

.password-input {
  width: 100% !important;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 10px;
}
/* 解决复选框组第二行均匀分布问题 - 核心修复 */
.clause-checkboxes {
  /* 确保父容器没有默认的justify-content覆盖 */
  justify-content: flex-start !important;
  align-content: flex-start !important;
  /* 移除可能的默认内边距影响 */
  padding: 0 !important;
}

/* 强制每个复选框项固定宽度且不拉伸 */
.clause-checkbox-item {
  /* 关键：禁用拉伸，固定基础宽度（5个元素一行） */
  flex: 0 0 calc(20% - 10px) !important; /* 20%宽度 - 间距补偿 */
  min-width: calc(20% - 10px) !important; /* 确保最小宽度一致 */
  max-width: calc(20% - 10px) !important; /* 限制最大宽度 */
  margin: 0 !important; /* 彻底移除默认margin */
  padding: 4px 0 !important; /* 仅保留垂直内边距，避免水平偏移 */
  box-sizing: border-box !important; /* 确保宽度计算包含内边距 */
}

/* 修复Element UI复选框内部样式干扰 */
.el-checkbox {
  margin: 0 !important; /* 清除组件默认margin */
}

.el-checkbox__label {
  display: inline-block !important; /* 避免标签换行导致宽度异常 */
  width: 100% !important; /* 标签占满复选框项宽度 */
}

/* 响应式适配（确保小屏幕依然靠左） */
@media (max-width: 768px) {
  .clause-checkbox-item {
    flex: 0 0 calc(33.33% - 10px) !important; /* 3个元素一行 */
    min-width: calc(33.33% - 10px) !important;
  }
}

@media (max-width: 480px) {
  .clause-checkbox-item {
    flex: 0 0 calc(50% - 10px) !important; /* 2个元素一行 */
    min-width: calc(50% - 10px) !important;
  }
}

/* 移动端支持选项文本样式优化 */
.mobile-support-option {
  display: flex;
  align-items: center;
  gap: 8px; /* 调整复选框与文本的间距 */
  padding: 10px 15px; /* 增加内边距，让背景更完整 */
  border-radius: 8px;
  margin-bottom: 15px; /* 与下方内容拉开距离 */
}

.mobile-support-option span {
  background-color: #ed6d2d !important;
  color: white !important;
  padding: 6px 12px; /* 适当内边距，让背景完全包裹文字 */
  border-radius: 4px; /* 轻微圆角，提升视觉效果 */
  font-size: 13px;
  line-height: 1.4; /* 优化行高，确保文字垂直居中 */
  flex: 1; /* 让文本区域自适应宽度 */
}

/* 调整复选框位置，确保与带背景的文本垂直居中对齐 */
.mobile-support-option .os-checkbox {
  margin-top: 0 !important;
  align-self: center;
}
.section-title {
  font-size: 15px;
  font-weight: 600;
  color: white;
  margin: 0 0 10px 0;
  padding: 2px 12px;
  border-radius: 4px;
  background: #ed6d2d;
  display: inline-block;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  /* 新增：固定宽度，确保所有标题背景色块长度一致 */
  width: 320px; /* 可根据实际需求调整此值 */
  box-sizing: border-box; /* 确保宽度计算包含内边距 */
}

/* 响应式调整 - 确保在不同屏幕尺寸下保持一致比例 */
@media (max-width: 768px) {
  .section-title {
    width: 260px; /* 平板设备上的宽度 */
    font-size: 14px;
    padding: 4px 8px;
  }
}

@media (max-width: 480px) {
  .section-title {
    width: calc(100% - 70px); /* 手机设备上自适应宽度 */
    font-size: 13px;
    padding: 3px 6px;
  }
}
/* 修改section-header样式，确保更稳定的对齐 */
.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px 15px;
  background: rgba(237, 109, 45, 0.05);
  border-bottom: 1px solid rgba(237, 109, 45, 0.1);
  flex-wrap: nowrap;
  gap: 10px;
  box-sizing: border-box;
}

/* 修改标题样式，移除固定宽度，使用flex布局 */
.section-title {
  font-size: 15px;
  font-weight: 600;
  color: white;
  margin: 0;
  padding: 2px 12px;
  border-radius: 4px;
  background: #ed6d2d;
  display: inline-flex; /* 使用flex确保内容居中 */
  align-items: center;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  /* 关键：让标题占据剩余空间但不挤压按钮 */
  flex: 1;
  max-width: calc(100% - 80px); /* 为按钮预留空间 */
  min-width: 0; /* 允许内容收缩 */
}

/* 确保按钮尺寸固定，不被挤压 */
.toggle-btn {
  background: rgba(237, 109, 45, 0.1);
  border: 1px solid #ed6d2d;
  border-radius: 4px;
  padding: 4px 10px;
  font-size: 12px;
  cursor: pointer;
  color: #ed6d2d;
  transition: all 0.2s ease;
  white-space: nowrap;
  flex-shrink: 0; /* 关键：按钮不收缩 */
  width: 60px; /* 固定宽度 */
  text-align: center; /* 文字居中 */
}

/* 响应式调整 */
@media (max-width: 768px) {
  .section-title {
    font-size: 14px;
    padding: 4px 8px;
    max-width: calc(100% - 70px);
  }

  .toggle-btn {
    width: 55px;
    padding: 3px 8px;
  }
}

@media (max-width: 480px) {
  .section-title {
    font-size: 13px;
    padding: 3px 6px;
    max-width: calc(100% - 65px);
  }

  .toggle-btn {
    width: 50px;
    font-size: 11px;
    padding: 2px 6px;
  }
}
.section-title {
  font-size: 15px;
  font-weight: 600;
  color: white;
  margin: 0 0 10px 0;
  padding: 2px 12px;
  border-radius: 4px;
  background: #ed6d2d;
  display: inline-flex;
  align-items: center;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  flex: 1;
  /* 调整为更合适的最大宽度，避免过长 */
  max-width: 340px;
  min-width: 0;
  box-sizing: border-box;
}

/* 响应式调整 - 适配不同屏幕 */
@media (max-width: 768px) {
  .section-title {
    max-width: 220px; /* 平板设备进一步缩短 */
    font-size: 14px;
    padding: 4px 8px;
  }
}

@media (max-width: 480px) {
  .section-title {
    max-width: calc(100% - 70px); /* 手机设备自适应，不超过屏幕可用宽度 */
    font-size: 13px;
    padding: 3px 6px;
  }
}
/* 移动端支持选项容器 */
.mobile-support-option {
  padding: 12px 15px;
  border-radius: 8px;
  background: #fff;
  border: 1px solid rgba(237, 109, 45, 0.2);
  margin-bottom: 15px;
}

/* 复选框与文本组合容器 */
.checkbox-text-group {
  display: flex;
  align-items: center; /* 垂直居中对齐 */
  gap: 10px; /* 复选框与文本间距 */
  width: 100%;
  padding: 5px 0;
}

/* 优化复选框样式 */
.os-checkbox {
  flex-shrink: 0; /* 防止复选框被压缩 */
  margin: 0 !important; /* 清除默认margin */
  padding: 0 !important;
}

/* 优化span文本样式 */
.support-text {
  font-size: 14px;
  color: #333;
  line-height: 1.5; /* 优化行高 */
  flex-grow: 1; /* 文本占据剩余空间 */
  padding: 2px 0; /* 垂直居中微调 */
  word-wrap: break-word; /* 长文本自动换行 */
  white-space: normal; /* 允许换行 */
}

/* 响应式调整 */
@media (max-width: 768px) {
  .mobile-support-option {
    padding: 10px 12px;
  }

  .support-text {
    font-size: 13px;
  }

  .checkbox-text-group {
    gap: 8px;
  }
}

@media (max-width: 480px) {
  .support-text {
    font-size: 12px;
    line-height: 1.4;
  }

  .checkbox-text-group {
    gap: 6px;
  }
}
/* 父容器改为垂直布局 */
.mobile-support-option {
  padding: 12px 15px;
  border-radius: 8px;
  background: #fff;
  border: 1px solid rgba(237, 109, 45, 0.2);
  margin-bottom: 15px;
  /* 关键：设置为垂直flex布局 */
  display: flex;
  flex-direction: column; /* 子元素上下排列 */
  gap: 10px; /* 两个子元素之间的间距 */
}

/* 提示信息样式优化 */
.os-hint.red-highlight {
  margin: 0; /* 清除原有margin，使用父容器gap控制间距 */
  padding: 8px 12px;
  border-left: 3px solid #ed6d2d;
  background: rgba(237, 109, 45, 0.05);
  border-radius: 0 4px 4px 0;
  font-size: 13px;
  line-height: 1.5;
}

/* 复选框组保持水平对齐 */
.checkbox-text-group {
  display: flex;
  align-items: center; /* 复选框与文本垂直居中 */
  gap: 10px; /* 复选框与文本间距 */
  padding: 2px 0; /* 轻微内边距，避免内容贴边 */
}

/* 其他样式保持不变 */
.os-checkbox {
  flex-shrink: 0;
  margin: 0 !important;
}

.support-text {
  font-size: 14px;
  color: #333;
  line-height: 1.5;
  flex-grow: 1;
  word-wrap: break-word;
  white-space: normal;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .mobile-support-option {
    padding: 10px 12px;
    gap: 8px;
  }
  
  .os-hint.red-highlight {
    font-size: 12px;
    padding: 6px 10px;
  }
  
  .support-text {
    font-size: 13px;
  }
}

@media (max-width: 480px) {
  .os-hint.red-highlight {
    font-size: 11px;
  }
  
  .support-text {
    font-size: 12px;
  }
  
  .checkbox-text-group {
    gap: 6px;
  }
}
/* 父容器设置左对齐 */
.mobile-support-option {
  padding: 12px 15px;
  border-radius: 8px;
  background: #fff;
  border: 1px solid rgba(237, 109, 45, 0.2);
  margin-bottom: 15px;
  display: flex;
  flex-direction: column; /* 垂直布局 */
  gap: 10px; /* 上下间距 */
  align-items: flex-start; /* 关键：子元素左对齐 */
}

/* 提示信息确保左对齐 */
.os-hint.red-highlight {
  margin: 0;
  padding: 8px 12px;
  border-left: 3px solid #ed6d2d;
  background: rgba(237, 109, 45, 0.05);
  border-radius: 0 4px 4px 0;
  font-size: 13px;
  line-height: 1.5;
  width: 100%; /* 宽度占满父容器，确保左边缘对齐 */
  box-sizing: border-box; /* 包含padding在宽度内 */
}

/* 复选框组确保左对齐 */
.checkbox-text-group {
  display: flex;
  align-items: center; /* 复选框与文本垂直居中 */
  gap: 10px; /* 复选框与文本间距 */
  padding: 2px 0;
  width: 100%; /* 宽度占满父容器 */
}

/* 其他样式保持不变 */
.os-checkbox {
  flex-shrink: 0;
  margin: 0 !important;
}

.support-text {
  font-size: 14px;
  color: #333;
  line-height: 1.5;
  flex-grow: 1;
  word-wrap: break-word;
  white-space: normal;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .mobile-support-option {
    padding: 10px 12px;
    gap: 8px;
  }
}
/* 针对操作系统选择区标题的特殊优化 */
.os-selection .section-title {
  padding: 6px 14px; /* 增大上下内边距，横向也略增，让色块更舒展 */
  font-size: 15px; /* 微调字体大小，与色块匹配 */
  line-height: 1.6; /* 优化行高，避免文本拥挤 */
  white-space: normal; /* 允许文本自然换行，确保长文本也被背景完全包裹 */
  max-width: 100%; /* 取消最大宽度限制，让色块根据文本长度自适应 */
  margin-bottom: 12px; /* 增加与下方内容的间距，提升层次感 */
}

/* 响应式适配调整 */
@media (max-width: 768px) {
  .os-selection .section-title {
    padding: 5px 12px; /* 平板设备上适度缩小内边距 */
    font-size: 14px;
  }
}

@media (max-width: 480px) {
  .os-selection .section-title {
    padding: 4px 10px; /* 手机设备上保持合适比例 */
    font-size: 13px;
    line-height: 1.5;
  }
}
/* 操作系统选择区标题 - 不换行且增大背景色块 */
.os-selection .section-title {
  padding: 6px 16px; /* 增大上下内边距，横向留足空间，确保色块足够大 */
  font-size: 15px;
  white-space: nowrap; /* 强制文字不换行 */
  overflow: visible; /* 允许内容超出父容器（避免截断） */
  min-width: max-content; /* 色块宽度至少与文字内容等宽 */
  max-width: 100%; /* 最大不超过父容器宽度，避免溢出页面 */
  margin-bottom: 12px;
  line-height: 1.5; /* 确保文字垂直居中 */
}

/* 响应式适配（小屏幕下保持不换行，允许横向滚动） */
@media (max-width: 768px) {
  .os-selection .section-title {
    padding: 5px 14px;
    font-size: 14px;
    min-width: auto; /* 平板设备适当放宽最小宽度限制 */
  }
}

@media (max-width: 480px) {
  .os-selection .section-title {
    padding: 4px 12px;
    font-size: 13px;
    /* 手机端若文字过长，通过父容器横向滚动容纳 */
    parent {
      overflow-x: auto;
      padding-bottom: 5px; /* 预留滚动条空间 */
    }
  }
}
</style>