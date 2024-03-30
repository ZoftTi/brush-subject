<script setup lang="ts">
import { ref, reactive, onMounted } from "vue";
import { ElMessageBox } from "element-plus";
import { subject } from 'src/assets/data/subject_data'

type cityType = {
  city: string;
  code: string;
  value?: string;
  type?: number;
  answer?: string[];
};

const cityType: any = ["城市代码","航空公司","测试题目"];

const accuracy = reactive({
  count: 0,
  percentage: "",
});

let alertOptions = reactive({
  type: "success",
  text: "题目",
  show: false,
});

// 题目的index
const activeIndex = ref<number>(0);

// 活跃题目的答案
let activeAnswer = ref<string[]>([]);

// 禁止输入
let disabled = ref(false);

const formValue = ref<string>("");

const answerType = ref<number>(0);

let subjectCode = ref<cityType[]>(subject[0]);

const changeSubject = () => {
  console.log(answerType.value, subjectCode[answerType.value])
  ElMessageBox.alert("确认切换吗", "确认", {
    // if you want to disable its autofocus
    // autofocus: false,
    confirmButtonText: "OK",
    callback: () => {
      // subjectCode.value.splice(0, subjectCode.value.length);
      // subjectCode.value.length = 0
      subjectCode.value = []
      subjectCode.value.push(...subject[answerType.value]);

      subjectCode.value.sort(() => Math.random() - 0.5);
      disabled.value = false;
      alertOptions.show = false;
      formValue.value = "";
      randAnswer()

      console.log(subjectCode.value, answerType.value);

      window.localStorage.removeItem("subjectCode");
      window.localStorage.removeItem("activeIndex");
      window.localStorage.setItem(
        "answerType",
        JSON.stringify(answerType.value)
      );
    },
  });
};

const nextSubject = () => {
  if (!formValue.value) {
    return false;
  }

  activeIndex.value = activeIndex.value + 1;

  if (activeIndex.value + 1 > subjectCode.value.length) {
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

  const subject_next = subjectCode.value[activeIndex.value]

  console.log(subject_next)

  if(subject_next?.answer) {
    activeAnswer.value = [subject_next.code, ...subject_next.answer.sort(() => Math.random() - 0.5)]
    return
  }
  
  const nums = randNumber();
  const result:string[] = [];

  console.log(nums);

  nums.forEach((element) => {
    result.push(subjectCode.value[element].code);
  });

  activeAnswer.value = result;
};

const randNumber = (): Array<number> => {
  const numbs = [activeIndex.value];

  while (numbs.length < 4) {
    let rand = Math.floor(Math.random() * subjectCode.value.length);
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
  const code = subjectCode.value[activeIndex.value].code;

  window.localStorage.setItem("subjectCode", JSON.stringify(subjectCode.value));
  window.localStorage.setItem("activeIndex", JSON.stringify(activeIndex.value));
  window.localStorage.setItem("answerType", JSON.stringify(answerType.value));

  subjectCode.value[activeIndex.value].value = formValue.value;
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
      window.localStorage.removeItem("subjectCode");
      window.localStorage.removeItem("activeIndex");
      window.localStorage.removeItem("answerType");

      window.location.reload();
    },
  });
};

const balance = () => {
  // 结算
  subjectCode.value.forEach((e) => {
    if (e.code == e.value) {
      accuracy.count++;
    }
  });

  accuracy.percentage = ((accuracy.count / subjectCode.value.length) * 100)
    .toFixed(2)
    .toString();
};

onMounted(() => {
  const city = window.localStorage.getItem("subjectCode");
  const index = window.localStorage.getItem("activeIndex");
  const type = window.localStorage.getItem("answerType");

  if (city && index) {
    subjectCode.value = reactive(JSON.parse(city));
    activeIndex.value = parseInt(JSON.parse(index));
    answerType.value = parseInt(JSON.parse(type));
  }

  randAnswer();
});
</script>

<template>
  <div class="container p-6 pt-32 h-lvh">
    <div class="p-2 bg-white rounded-md mb-2">
      <el-radio-group v-model="answerType" @change="changeSubject">
        <el-radio-button :label="type" :value="index" v-for="(type, index) in cityType" :key="type" />
      </el-radio-group>
    </div>

    <div
      class="shadow-2xl w-full p-6 rounded-md bg-white"
      v-if="!(activeIndex + 1 > subjectCode.length)"
    >
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        VV 刷题记！！
      </p>

      <div class="subject mb-4">
        <p>
          {{ activeIndex + 1 }}. {{ subjectCode[activeIndex].city }}的{{
            cityType[subjectCode[activeIndex].type]
          }}是下列中的那一个?
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
      v-if="activeIndex + 1 > subjectCode.length"
    >
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        结算！！
      </p>

      <div class="balance-grid grid">
        <div
          :class="{ success: v.code == v.value }"
          v-for="(v, i) in subjectCode"
          :key="v.code"
          class="text-white"
        >
          <el-tooltip
            class="box-item"
            effect="dark"
            :content="`题目:${v.city}, 答案: ${v.code}, 类型: ${
              cityType[v.type]
            }`"
            placement="top-start"
          >
            {{ i }}
          </el-tooltip>
        </div>
      </div>

      <div class="flex mt-4">
        <div class="text-xl">
          {{ accuracy.count + "/" + subjectCode.length }}
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
.container {
  /* background: url("@/assets/images/background.jpg") no-repeat center/cover; */
  background: #f4f3f2;
}

.subject ::v-deep .el-radio-group {
  flex-direction: column;
  align-items: flex-start;
}

.subject ::v-deep .el-radio-group .el-radio.el-radio--large {
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
