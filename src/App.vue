<script setup lang="ts">
import { ref, reactive, onMounted } from "vue";
import { ElMessageBox } from "element-plus";

type cityType = {
  city: string;
  code: string;
  value?: string;
  type?: number;
};

const cityType: any = {
  1: "城市代码",
  2: "航空公司"
}

const accuracy = reactive({
  count: 0,
  percentage: "",
});

let alertOptions = reactive({
  type: "success",
  text: "题目",
  show: false,
});

const activeIndex = ref<number>(0);

let activeAnswer = reactive<string[]>([]);

let disabled = ref(false);

const formValue = ref<string>("");

let cityCode = reactive<cityType[]>([
  {
    city: "万州",
    code: "WXN",
    type: 1,
  },
  {
    city: "三亚",
    code: "SYX",
    type: 1,
  },
  {
    city: "上海",
    code: "SHA",
    type: 1,
  },
  {
    city: "丽江",
    code: "LJG",
    type: 1,
  },
  {
    city: "义乌",
    code: "YIW",
    type: 1,
  },
  {
    city: "乌鲁木齐",
    code: "URC",
    type: 1,
  },
  {
    city: "九寨沟",
    code: "JZH",
    type: 1,
  },
  {
    city: "仁川",
    code: "ICN",
    type: 1,
  },
  {
    city: "仁怀(茅台）",
    code: "REH（WMT)",
    type: 1,
  },
  {
    city: "六盘水",
    code: "LPF",
    type: 1,
  },
  {
    city: "兰州",
    code: "LHW",
    type: 1,
  },
  {
    city: "兴义",
    code: "ACX",
    type: 1,
  },
  {
    city: "凯里",
    code: "KJH",
    type: 1,
  },
  {
    city: "包头",
    code: "BAV",
    type: 1,
  },
  {
    city: "北京",
    code: "PEK",
    type: 1,
  },
  {
    city: "北京大兴",
    code: "PKX",
    type: 1,
  },
  {
    city: "北海",
    code: "BHY",
    type: 1,
  },
  {
    city: "南京",
    code: "NKG",
    type: 1,
  },
  {
    city: "南充",
    code: "NAO",
    type: 1,
  },
  {
    city: "南宁",
    code: "NNG",
    type: 1,
  },
  {
    city: "南昌",
    code: "KHN",
    type: 1,
  },
  {
    city: "南苑",
    code: "NAY",
    type: 1,
  },
  {
    city: "南通",
    code: "NTG",
    type: 1,
  },
  {
    city: "博鳌",
    code: "BAR",
    type: 1,
  },
  {
    city: "厦门",
    code: "XMN",
    type: 1,
  },
  {
    city: "台北",
    code: "TPE",
    type: 1,
  },
  {
    city: "台州",
    code: "HYN",
    type: 1,
  },
  {
    city: "合肥",
    code: "HFE",
    type: 1,
  },
  {
    city: "吉隆坡",
    code: "KUL",
    type: 1,
  },
  {
    city: "名古屋",
    code: "NGO",
    type: 1,
  },
  {
    city: "吕梁",
    code: "LLV",
    type: 1,
  },
  {
    city: "呼和",
    code: "HET",
    type: 1,
  },
  {
    city: "哈尔滨",
    code: "HRB",
    type: 1,
  },
  {
    city: "唐山",
    code: "TVS",
    type: 1,
  },
  {
    city: "大连",
    code: "DLC",
    type: 1,
  },
  {
    city: "天府",
    code: "TFU",
    type: 1,
  },
  {
    city: "天津",
    code: "TSN",
    type: 1,
  },
  {
    city: "太原",
    code: "TYN",
    type: 1,
  },
  {
    city: "宁波",
    code: "NGB",
    type: 1,
  },
  {
    city: "安庆",
    code: "AOG",
    type: 1,
  },
  {
    city: "安顺",
    code: "AVA",
    type: 1,
  },
  {
    city: "宜宾",
    code: "YBP",
    type: 1,
  },
  {
    city: "宜昌",
    code: "YIH",
    type: 1,
  },
  {
    city: "宜春",
    code: "YIC",
    type: 1,
  },
  {
    city: "常州",
    code: "CZX",
    type: 1,
  },
  {
    city: "广州",
    code: "CAN",
    type: 1,
  },
  {
    city: "库尔勒",
    code: "KRL",
    type: 1,
  },
  {
    city: "延安",
    code: "ENY",
    type: 1,
  },
  {
    city: "徐州",
    code: "XUZ",
    type: 1,
  },
  {
    city: "思茅",
    code: "SYM",
    type: 1,
  },
  {
    city: "惠州",
    code: "HUZ",
    type: 1,
  },
  {
    city: "成都",
    code: "CTU",
    type: 1,
  },
  {
    city: "扬州",
    code: "YTY",
    type: 1,
  },
  {
    city: "拉萨",
    code: "LXA",
    type: 1,
  },
  {
    city: "新加坡",
    code: "SIN",
    type: 1,
  },
  {
    city: "无锡",
    code: "WUX",
    type: 1,
  },
  {
    city: "昆明",
    code: "KMG",
    type: 1,
  },
  {
    city: "昭通",
    code: "ZAT",
    type: 1,
  },
  {
    city: "晋江",
    code: "JJN",
    type: 1,
  },
  {
    city: "普吉",
    code: "HKT",
    type: 1,
  },
  {
    city: "暹粒",
    code: "REP",
    type: 1,
  },
  {
    city: "曼谷",
    code: "BKK",
    type: 1,
  },
  {
    city: "杭州",
    code: "HGH",
    type: 1,
  },
  {
    city: "柳州",
    code: "LZH",
    type: 1,
  },
  {
    city: "桂林",
    code: "KWL",
    type: 1,
  },
  {
    city: "梧州",
    code: "WUZ",
    type: 1,
  },
  {
    city: "榆林",
    code: "UYN",
    type: 1,
  },
  {
    city: "武夷山",
    code: "WUS",
    type: 1,
  },
  {
    city: "武汉",
    code: "WUH",
    type: 1,
  },
  {
    city: "毕节",
    code: "BFJ",
    type: 1,
  },
  {
    city: "汕头",
    code: "SWA",
    type: 1,
  },
  {
    city: "沈阳",
    code: "SHE",
    type: 1,
  },
  {
    city: "河内",
    code: "HAN",
    type: 1,
  },
  {
    city: "泸州",
    code: "LZO",
    type: 1,
  },
  {
    city: "洛阳",
    code: "LYA",
    type: 1,
  },
  {
    city: "济南",
    code: "TNA",
    type: 1,
  },
  {
    city: "浦东",
    code: "PVG",
    type: 1,
  },
  {
    city: "海口",
    code: "HAK",
    type: 1,
  },
  {
    city: "淮安",
    code: "HIA",
    type: 1,
  },
  {
    city: "深圳",
    code: "SZX",
    type: 1,
  },
  {
    city: "清州",
    code: "CJJ",
    type: 1,
  },
  {
    city: "温州",
    code: "WNZ",
    type: 1,
  },
  {
    city: "湛江",
    code: "ZHA",
    type: 1,
  },
  {
    city: "澳门",
    code: "MFM",
    type: 1,
  },
  {
    city: "珠海",
    code: "ZUH",
    type: 1,
  },
  {
    city: "甲米",
    code: "KBV",
    type: 1,
  },
  {
    city: "百色",
    code: "AEB",
    type: 1,
  },
  {
    city: "盐城",
    code: "YNZ",
    type: 1,
  },
  {
    city: "石家庄",
    code: "SJW",
    type: 1,
  },
  {
    city: "福州",
    code: "FOC",
    type: 1,
  },
  {
    city: "米兰",
    code: "MIL",
    type: 1,
  },
  {
    city: "绵阳",
    code: "MIG",
    type: 1,
  },
  {
    city: "芜湖",
    code: "WHA",
    type: 1,
  },
  {
    city: "芽庄",
    code: "CXR",
    type: 1,
  },
  {
    city: "荔波",
    code: "LLB",
    type: 1,
  },
  {
    city: "衡阳",
    code: "HNY",
    type: 1,
  },
  {
    city: "襄阳",
    code: "XFN",
    type: 1,
  },
  {
    city: "西安",
    code: "XIY",
    type: 1,
  },
  {
    city: "贵阳",
    code: "KWE",
    type: 1,
  },
  {
    city: "赣州",
    code: "KOW",
    type: 1,
  },
  {
    city: "运城",
    code: "YCU",
    type: 1,
  },
  {
    city: "遵义",
    code: "ZYI",
    type: 1,
  },
  {
    city: "郑州",
    code: "CGO",
    type: 1,
  },
  {
    city: "鄂尔多斯",
    code: "DSN",
    type: 1,
  },
  {
    city: "重庆",
    code: "CKG",
    type: 1,
  },
  {
    city: "釜山",
    code: "PUS",
    type: 1,
  },
  {
    city: "铜仁",
    code: "TEN",
    type: 1,
  },
  {
    city: "银川",
    code: "INC",
    type: 1,
  },
  {
    city: "长春",
    code: "CGQ",
    type: 1,
  },
  {
    city: "长沙",
    code: "CSX",
    type: 1,
  },
  {
    city: "青岛",
    code: "TAO",
    type: 1,
  },
  {
    city: "首尔",
    code: "SEL",
    type: 1,
  },
  {
    city: "香港",
    code: "HKG",
    type: 1,
  },
  {
    city: "黎平",
    code: "HZH",
    type: 1,
  },
  { city: "九元", code: "AQ", type: 2 },
  { city: "捷星太平洋", code: "BL", type: 2 },
  { city: "奥凯", code: "BK", type: 2 },
  { city: "国航", code: "CA", type: 2 },
  { city: "南航", code: "CZ", type: 2 },
  { city: "东海", code: "DZ", type: 2 },
  { city: "瑞丽", code: "DR", type: 2 },
  { city: "成都", code: "EU", type: 2 },
  { city: "泰新时代", code: "E3", type: 2 },
  { city: "上海", code: "FM", type: 2 },
  { city: "福州", code: "FU", type: 2 },
  { city: "天津", code: "GS", type: 2 },
  { city: "华夏", code: "G5", type: 2 },
  { city: "多彩", code: "GY", type: 2 },
  { city: "北部湾", code: "GX", type: 2 },
  { city: "桂林", code: "GT", type: 2 },
  { city: "长龙", code: "GJ", type: 2 },
  { city: "海航", code: "HU", type: 2 },
  { city: "吉祥", code: "HO", type: 2 },
  { city: "香港", code: "HX", type: 2 },
  { city: "首都", code: "JD", type: 2 },
  { city: "昆明", code: "KY", type: 2 },
  { city: "联合", code: "KN", type: 2 },
  { city: "东航", code: "MU", type: 2 },
  { city: "厦航", code: "MF", type: 2 },
  { city: "河北", code: "NS", type: 2 },
  { city: "澳门", code: "NX", type: 2 },
  { city: "顺丰", code: "O3", type: 2 },
  { city: "马印航空", code: "OD", type: 2 },
  { city: "泰东方", code: "OX", type: 2 },
  { city: "西部", code: "PN", type: 2 },
  { city: "城市快线", code: "QG", type: 2 },
  { city: "青岛", code: "QW", type: 2 },
  { city: "江西", code: "RY", type: 2 },
  { city: "俄罗斯", code: "RL", type: 2 },
  { city: "山东", code: "SC", type: 2 },
  { city: "西藏", code: "TV", type: 2 },
  { city: "越南航空", code: "VN", type: 2 },
  { city: "越捷", code: "VJ", type: 2 },
  { city: "扬子江", code: "Y8", type: 2 },
  { city: "深航", code: "ZH", type: 2 },
  { city: "天翼亚洲", code: "ZA", type: 2 },
  { city: "川航", code: "3U", type: 2 },
  { city: "捷星", code: "3K", type: 2 },
  { city: "祥鹏", code: "8L", type: 2 },
  { city: "春秋", code: "9C", type: 2 },
  { city: "长安", code: "9H", type: 2 },
]);

const nextSubject = () => {
  if (!formValue.value) {
    return false;
  }

  activeIndex.value = activeIndex.value + 1;

  if (activeIndex.value + 1 > cityCode.length) {
    console.log("balance");
    balance();
    return false;
  }

  disabled.value = false;
  alertOptions.show = false;
  formValue.value = "";

  randAnswer();
};

const randAnswer = () => {
  const nums = randNumber();
  const result = reactive<string[]>([]);

  console.log(nums);

  nums.forEach((element) => {
    result.push(cityCode[element].code);
  });

  activeAnswer = result;
};

const randNumber = (): Array<number> => {
  const numbs = [activeIndex.value];

  while (numbs.length < 4) {
    let rand = Math.floor(Math.random() * cityCode.length);
    if (!numbs.includes(rand)) {
      // 确保随机数不重复
      numbs.push(rand);
    }
  }

  return numbs.sort(function () {
    return 0.5 - Math.random();
  });
};

const changeAnswer = () => {
  const code = cityCode[activeIndex.value].code;

  window.localStorage.setItem("cityCode", JSON.stringify(cityCode));
  window.localStorage.setItem("activeIndex", JSON.stringify(activeIndex.value));

  cityCode[activeIndex.value].value = formValue.value;
  disabled.value = true;

  if (code == formValue.value) {
    alertOptions.show = true;
    alertOptions.text = "答案正确";
    alertOptions.type = "success";
  } else {
    alertOptions.show = true;
    alertOptions.text = `答案错误，正确答案为${code}`;
    alertOptions.type = "error";
  }
};

const clearCache = () => {
  ElMessageBox.alert("确认清除缓存吗", "清除缓存", {
    // if you want to disable its autofocus
    // autofocus: false,
    confirmButtonText: "OK",
    callback: () => {
      window.localStorage.removeItem("cityCode");
      window.localStorage.removeItem("activeIndex");

      window.location.reload();
    },
  });
};

const balance = () => {
  // 结算
  cityCode.forEach((e) => {
    if (e.code == e.value) {
      accuracy.count++;
    }
  });

  accuracy.percentage = ((accuracy.count / cityCode.length) * 100)
    .toFixed(2)
    .toString();
};

onMounted(() => {
  const city = window.localStorage.getItem("cityCode");
  const index = window.localStorage.getItem("activeIndex");

  if (city && index) {
    cityCode = reactive(JSON.parse(city));
    activeIndex.value = parseInt(JSON.parse(index));
  }

  randAnswer();
});
</script>

<template>
  <div class="container p-6 pt-32 h-lvh">
    <div
      class="shadow-2xl w-full p-6 rounded-md bg-white"
      v-if="!(activeIndex + 1 > cityCode.length)"
    >
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        VV 刷题记！！
      </p>

      <div class="subject mb-4">
        <p>
          {{ activeIndex + 1 }}.
          {{ cityCode[activeIndex].city }}的{{cityType[cityCode[activeIndex].type]}}是下列中的那一个?
        </p>

        <el-radio-group
          class="ml-2 mt-2"
          :validate-event="false"
          v-model="formValue"
          @change="changeAnswer"
          :disabled="disabled"
        >
          <el-radio
            v-for="v in activeAnswer"
            :value="v"
            size="large"
            :key="v"
            >{{ v }}</el-radio
          >
        </el-radio-group>
      </div>

      <el-alert
        v-if="alertOptions.show"
        :title="alertOptions.text"
        :type="alertOptions.type"
        effect="dark"
      />

      <div class="options mt-4">
        <el-button type="primary" @click="nextSubject">下一题</el-button>
        <el-button type="danger" @click="clearCache">清除缓存</el-button>
      </div>
    </div>

    <div
      class="balance shadow-2xl w-full p-6 rounded-md bg-white"
      v-if="activeIndex + 1 > cityCode.length"
    >
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        结算！！
      </p>

      <div class="balance-grid grid">
        <div
          :class="{ success: v.code == v.value }"
          v-for="(v, i) in cityCode"
          :key="v.code"
          class="text-white"
        >
          <el-tooltip
            class="box-item"
            effect="dark"
            :content="`题目:${v.city}, 答案: ${v.code}, 类型: ${cityType[v.type]}`"
            placement="top-start"
          >
            {{ i }}
          </el-tooltip>
        </div>
      </div>

      <div class="flex mt-4">
        <div class="text-xl">
          {{ accuracy.count + "/" + cityCode.length }}
          <span class="text-sm">正确数量</span>
        </div>
        <div class="text-xl ml-6">
          {{ accuracy.percentage }} %<span class="text-sm">正确率</span>
        </div>
      </div>
      <el-button class="mt-2" type="danger" @click="clearCache"
        >清除缓存(重置)</el-button
      >
    </div>
  </div>
  <!-- <p class="text-blue-600 text-2xl text-center">
    The quick brown fox...
  </p> -->
</template>

<style scoped>

/* .container {
  background: url("@/assets/images/background.jpg") no-repeat center/cover;
} */

::v-deep .el-radio-group {
  flex-direction: column;
  align-items: flex-start;
}

::v-deep .el-radio-group .el-radio.el-radio--large {
  height: 30px;
}

.balance-grid {
  grid-template-columns: repeat(auto-fill, 22px);
  gap: 5px;
}

.balance-grid div {
  height: 22px;
  width: 22px;
  background: red;
  font-size: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.balance-grid div.success {
  background: green;
}
</style>
