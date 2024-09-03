<script setup lang="ts">
import { ref, reactive, onMounted } from "vue";
import { ElMessage, ElMessageBox } from "element-plus";
import { subject } from 'src/assets/data/subject_data'

// 结算数据
const accuracy = reactive({
  count: 0,
  percentage: "",
});

let alertOptions = reactive({
  type: "success",
  text: "题目",
  show: false,
});

// 取出题库并随机题库
const subjectGroup = subject.map(item => {
  return {
    ...item,
    subject: item.subject.sort(() => Math.random() - 0.5)
  }
})

// 题库的index
const activeSubjectGroupIndex = ref<number>(0);

// 题目的index
const activeSubjectIndex = ref<number>(0);

// 禁止输入
let disabled = ref(false);

const formValue = ref<string>("");

const handleChangeSubject = () => {
  disabled.value = false;
  alertOptions.show = false;
  formValue.value = "";

  // 重置题目索引
  activeSubjectIndex.value = 0

  if (subjectGroup[activeSubjectGroupIndex.value].type === 'choice') {
    handleRandAnswer()
  }
};

const handleNextSubject = () => {
  if (!formValue.value) {
    ElMessage('请输入答案')
    return false;
  }

  activeSubjectIndex.value++

  if (activeSubjectIndex.value + 1 > subjectGroup[activeSubjectGroupIndex.value].subject.length) {
    ElMessage('已经是最后一题拉')
    // 调用结算方法
    handleBalance()
    return false;
  }

  disabled.value = false;
  alertOptions.show = false;
  formValue.value = "";

  if (subjectGroup[activeSubjectGroupIndex.value].type === 'choice') {
    handleRandAnswer()
  }
};

const choiceAnswerArray = ref<string[]>([])

const handleRandAnswer = () => {

  const randNumber = (): Array<number> => {
    const numbs = [activeSubjectIndex.value];

    while (numbs.length < 4) {
      let rand = Math.floor(Math.random() * subjectGroup[activeSubjectGroupIndex.value].subject.length);
      if (!numbs.includes(rand)) {
        // 确保随机数不重复
        numbs.push(rand);
      }
    }

    return numbs.sort(function () {
      return 0.5 - Math.random();
    });
  };

  const nums = randNumber();
  const result: string[] = [];

  nums.forEach((element) => {
    result.push(subjectGroup[activeSubjectGroupIndex.value].subject[element].code);
  });

  console.log(result)

  choiceAnswerArray.value = result;
};

const handleAnswer = () => {
  const activeSubject = subjectGroup[activeSubjectGroupIndex.value].subject[activeSubjectIndex.value]
  const activeSubjectGroup = subjectGroup[activeSubjectGroupIndex.value]

  if (activeSubjectGroup.type === 'choice') {
    const code = activeSubject.code
    if (formValue.value) activeSubject.value = formValue.value
    disabled.value = true;
    if (code == formValue.value) {
      alertOptions.show = true;
      alertOptions.text = "答案正确";
      alertOptions.type = "success";
      handleNextSubject()
    } else {
      alertOptions.show = true;
      alertOptions.text = `答案错误，正确答案为${code}`;
      alertOptions.type = "error";
    }
  }

  if (activeSubjectGroup.type === 'fill') {
    if (formValue.value) activeSubject.value = formValue.value

    ElMessageBox.confirm(`你的答案: ${formValue.value} <br/> 标准答案: ${activeSubject.answer}`, '对比答案', {
      dangerouslyUseHTMLString: true,
      confirmButtonText: '对',
      cancelButtonText: '错',
    }).then(() => {
      // 将批改结果设置为对
      activeSubject.correct = true
      handleNextSubject()
    }).catch(() => {
      // 将批改结果设置为错
      activeSubject.correct = false
      handleNextSubject()
    })

  }

};

// 结算功能
const handleBalance = () => {
  console.log(subjectGroup[activeSubjectGroupIndex.value])

  const activeSubjectGroup = subjectGroup[activeSubjectGroupIndex.value]

  activeSubjectGroup.subject.forEach(e => {
    if (activeSubjectGroup.type === 'choice') {
      if (e.code === e.value) accuracy.count++
    }

    if (activeSubjectGroup.type === 'fill') {
      if (e?.correct) accuracy.count++
    }
  })

  accuracy.percentage = ((accuracy.count / activeSubjectGroup.subject.length) * 100).toFixed(2).toString();
};

onMounted(() => {
  if (subjectGroup[activeSubjectGroupIndex.value].type === 'choice') {
    handleRandAnswer()
  }
})

</script>

<template>
  <div class="container p-6 pt-32 h-lvh">
    <div class="p-2 bg-white rounded-md mb-2">
      <el-radio-group v-model="activeSubjectGroupIndex" @change="handleChangeSubject">
        <el-radio-button :label="item.title" :value="index" v-for="(item, index) in subjectGroup" :key="item.title" />
      </el-radio-group>
    </div>

    <div class="shadow-2xl w-full p-6 rounded-md bg-white" v-if="activeSubjectIndex !== subjectGroup[activeSubjectGroupIndex].subject.length">
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        VV 刷题记！！
      </p>

      <div class="subject" v-if="subjectGroup[activeSubjectGroupIndex].type !== 'choice'">
        <p>
          <el-tag size="small" :type="['primary', 'warning', 'danger'][subjectGroup[activeSubjectGroupIndex].subject[activeSubjectIndex].difficulty]">{{ ['易', '中', '难'][subjectGroup[activeSubjectGroupIndex].subject[activeSubjectIndex].difficulty] }}</el-tag>
          {{ activeSubjectIndex + 1 }}.{{ subjectGroup[activeSubjectGroupIndex].subject[activeSubjectIndex].label }}
        </p>

        <div class="fill-answer">
          <span>填写答案</span>
          <el-input type="textarea" v-model="formValue" :rows="3" placeholder="请输入答案"></el-input>
        </div>

        <div class="options mt-4">
          <el-button type="primary" @click="handleAnswer">提交并查看答案</el-button>
        </div>
      </div>

      <div class="subject" v-else>
        <p>
          {{ activeSubjectIndex + 1 }}. {{ subjectGroup[activeSubjectGroupIndex].subject[activeSubjectIndex]?.city }}的{{
            subjectGroup[activeSubjectGroupIndex].title
          }}是下列中的那一个?
        </p>

        <el-radio-group class="ml-2 mt-2 mb-4" :validate-event="false" v-model="formValue" @change="handleAnswer"
          :disabled="disabled">
          <el-radio v-for="v in choiceAnswerArray" :value="v" size="large" :key="v">{{ v }}</el-radio>
        </el-radio-group>

        <el-alert v-if="alertOptions.show" :title="alertOptions.text" :type="alertOptions.type" effect="dark" />

        <div class="options mt-4">
          <el-button type="primary" @click="handleNextSubject">下一题</el-button>
        </div>
      </div>
    </div>

    <!-- 结算功能 -->
    <div class="balance shadow-2xl w-full p-6 rounded-md bg-white" v-else>
      <p class="title line-clamp-1 mb-2 block text-lg font-semibold">
        结算！！
      </p>

      <div class="balance-grid grid">
        <div :class="{ success: (v?.code == v?.value || v?.correct) }" v-for="(v, i) in subjectGroup[activeSubjectGroupIndex].subject"
          :key="v.code" class="text-white">
          <el-tooltip v-if="subjectGroup[activeSubjectGroupIndex].type === 'choice'" class="box-item" effect="dark"
            :content="`题目:${v.city}, 答案: ${v.code}, 类型: ${subjectGroup[activeSubjectGroupIndex].title}`"
            placement="top-start">
            {{ i + 1 }}
          </el-tooltip>

          <div class="box-item" v-else-if="subjectGroup[activeSubjectGroupIndex].type === 'fill'" @click="() => {
            ElMessageBox.confirm(`你的答案: ${v.value} <br/> 标准答案: ${v.answer}`, '对比答案', {
              dangerouslyUseHTMLString: true,
              showCancelButton: false
            })
          }">
            {{ i + 1 }}
          </div>
        </div>
      </div>

      <div class="flex mt-4">
        <div class="text-xl">
          {{ accuracy.count + "/" + subjectGroup[activeSubjectGroupIndex].subject.length }}
          <span class="text-sm">正确数量</span>
        </div>
        <div class="text-xl ml-6">
          {{ accuracy.percentage }} %<span class="text-sm">正确率</span>
        </div>
      </div>
    </div>
  </div>
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

.balance-grid > div {
  height: 22px;
  width: 22px;
  background: red;
  font-size: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.balance-grid div.success {
  background: green !important;
}

.fill-answer {
  margin-top: 10px;
}

.fill-answer>span {
  font-size: 14px;
  display: inline-block;
  margin-bottom: 5px;
}
</style>
