<script setup lang="ts">
import { ref, reactive, onMounted } from "vue";
import { ElMessageBox } from "element-plus";

type cityType = {
  city: string,
  code: string,
  value?: string
}

const accuracy = reactive({
  count: 0,
  percentage: '',
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
  { city: "兴义", code: "ACX" },
  { city: "贵阳", code: "KWE" },
  { city: "思茅", code: "SYM" },
  { city: "安顺", code: "AVA" },
  { city: "昆明", code: "KMG" },
  { city: "济南", code: "TNA" },
  { city: "毕节", code: "BFJ" },
  { city: "桂林", code: "KWL" },
  { city: "太原", code: "TYN" },
  { city: "包头", code: "BAV" },
  { city: "博鳌", code: "BAR" },
  { city: "赣州", code: "KOW" },
  { city: "唐山", code: "TVS" },
  { city: "北海", code: "BHY" },
  { city: "凯里", code: "KJH" },
  { city: "青岛", code: "TAO" },
  { city: "成都", code: "CTU" },
  { city: "兰州", code: "LHW" },
  { city: "铜仁", code: "TEN" },
  { city: "重庆", code: "CKG" },
  { city: "丽江", code: "LJG" },
  { city: "乌鲁木齐", code: "URC" },
  { city: "常州", code: "CZX" },
  { city: "泸州", code: "LZO" },
  { city: "榆林", code: "UYN" },
  { city: "长沙", code: "CSX" },
  { city: "六盘水", code: "LPF" },
  { city: "无锡", code: "WUX" },
  { city: "郑州", code: "CGO" },
  { city: "荔波", code: "LLB" },
  { city: "万州", code: "WXN" },
  { city: "长春", code: "CGQ" },
  { city: "拉萨", code: "LXA" },
  { city: "武汉", code: "WUH" },
  { city: "广州", code: "CAN" },
  { city: "柳州", code: "LZH" },
  { city: "梧州", code: "WUZ" },
  { city: "鄂尔多斯", code: "DSN" },
  { city: "洛阳", code: "LYA" },
  { city: "温州", code: "WNZ" },
  { city: "大连", code: "DLC" },
  { city: "绵阳", code: "MIG" },
  { city: "武夷山", code: "WUS" },
  { city: "襄阳", code: "XFN" },
  { city: "厦门", code: "XMN" },
  { city: "杭州", code: "HGH" },
  { city: "南充", code: "NAO" },
  { city: "徐州", code: "XUZ" },
  { city: "西安", code: "XIY" },
  { city: "宜昌", code: "YIH" },
  { city: "盐城", code: "YNZ" },
  { city: "扬州", code: "YTY" },
  { city: "运城", code: "YCU" },
  { city: "台北", code: "TPE" },
  { city: "清州", code: "CJJ" },
  { city: "芽庄", code: "CXR" },
  { city: "曼谷", code: "BKK" },
  { city: "香港", code: "HKG" },
  { city: "甲米", code: "KBV" },
  { city: "澳门", code: "MFM" },
  { city: "河内", code: "HAN" },
  { city: "暹粒", code: "REP" },
  { city: "首尔", code: "SEL" },
  { city: "普吉", code: "HKT" },
  { city: "吉隆坡", code: "KUL" },
  { city: "米兰", code: "MIL" },
  { city: "釜山", code: "PUS" },
  { city: "仁川", code: "ICN" },
  { city: "新加坡", code: "SIN" },
  { city: "名古屋", code: "NGO" },
]);

const nextSubject = () => {

  if(!formValue.value) {
    return false
  }
  
  activeIndex.value = activeIndex.value + 1;
  
  if ((activeIndex.value + 1) >  cityCode.length) {
    console.log('balance')
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

  accuracy.percentage = (((accuracy.count / cityCode.length) * 100).toFixed(2)).toString();
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
  <div class="container p-6 flex justify-center items-center h-lvh">
    <div
      class="shadow-2xl w-full p-6 rounded-md"
      v-if="!(activeIndex + 1 > cityCode.length)"
    >
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        VV 刷题记！！
      </p>

      <div class="subject mb-4">
        <p>
          {{ activeIndex + 1 }}.
          {{ cityCode[activeIndex].city }}的城市代码是下列中的那一个?
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
      class="balance shadow-2xl w-full p-6 rounded-md"
      v-if="activeIndex + 1 > cityCode.length"
    >
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        结算！！
      </p>

      <div class="balance-grid grid">
        <div
          :class="{ success: v.code == v.value }"
          v-for="v in cityCode"
          :key="v.code"
        ></div>
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
      <el-button type="danger" @click="clearCache">清除缓存</el-button>
    </div>
  </div>
  <!-- <p class="text-blue-600 text-2xl text-center">
    The quick brown fox...
  </p> -->
</template>

<style scoped>
::v-deep .el-radio-group {
  flex-direction: column;
  align-items: flex-start;
}

::v-deep .el-radio-group .el-radio.el-radio--large {
  height: 30px;
}

.balance-grid {
  grid-template-columns: repeat(auto-fill, 18px);
  gap: 5px;
}

.balance-grid div {
  height: 18px;
  width: 18px;
  background: red;
}

.balance-grid div.success {
  background: green;
}
</style>
