<template>
  <el-row>
    <el-col :md="12">
      <draggable
          :list="questionList"
          class="list-group"
          ghost-class="ghost"
          :move="checkMove"
          @start="dragging = true"
          @end="dragging = false"
      >
        <div v-for="(item, index) in questionList" :key="index" class="question-wrp" @click="readyEdit(index)">
          <div>
            <div v-if="isEditingIndex === index">
              <div> 题目 <el-input v-model="inputData.question" size="medium" ></el-input></div>
              <div>
                类型：
                <el-select value="单选题" size="mini">
                  <el-option value="radio">单选题</el-option>
                </el-select>
              </div>
              <div>
                选项：
                <div class="option-wrp">
                  <draggable
                      :list="inputOptions"
                      class="list-group"
                      ghost-class="ghost"
                      :move="checkMove"
                      @start="dragging = true"
                      @end="dragging = false"
                      handle=".handle"
                  >
                    <div
                        class="list-group-item"
                        v-for="(element, idx) in inputOptions"
                        :key="idx"
                    >
                      <el-input size="small" v-model="element.text">
                        <i slot="prefix" class="el-input__icon el-icon-rank handle" />
                        <i slot="suffix" class="el-input__icon el-icon-close" @click="item.options.splice(idx, 1)" />
                      </el-input>
                    </div>
                  </draggable>
                  <div @click="appendOptionToInputData">新增选项</div>
                </div>

              </div>

            </div>
            <div v-else>
              <span>{{index + 1}}. {{item.question}}</span>

              <div>
                <div v-for="(option, idx) in item.options" :key="idx">
                  <div>{{option.text}}</div>
                </div>
              </div>
            </div>

            <div class="user-ctrl" v-if="isEditingIndex === index">
              <el-link @click.stop="cancelEdit(index)">取消</el-link>
              <el-button @click.stop="confirmEdit(index)" type="primary">确定</el-button>
            </div>
          </div>
        </div>

      </draggable>
      <el-button @click="addQuestion">新增问题</el-button>
    </el-col>
    <el-col :md="12">
      <div>预览</div>
      <div>
        <div v-for="(item, index) in questionList" :key="index">
          <div class="question-item">
            <b>{{ index + 1 }}. </b>
            <span>{{item.question}}</span> <span v-if="item.required" style="color: red">*</span>

            <div class="options-wrp">
              <div v-for="(option, idx) in item.options" :key="idx">
                <el-radio-group>
                  <el-radio :label="idx">{{option.text }}</el-radio>
                </el-radio-group>
              </div>
            </div>
          </div>
        </div>
      </div>
    </el-col>
  </el-row>
</template>

<script>
import draggable from 'vuedraggable';
export default {
  name: "simple",
  display: "Simple",
  order: 0,
  components: {
    draggable
  },
  data() {
    return {
      enabled: true,
      isEditingIndex: -1, // 是否正在编辑，正在编辑无法进行其他操作
      questionList: [
        {
          question: '男孩对男子，正如女孩对',
          remark: '',
          type: 'radio',
          required: true,
          options: []
        }
      ],
      list: [
        { name: "John", id: 0 },
        { name: "Joao", id: 1 },
        { name: "Jean", id: 2 }
      ],
      inputData: {
        question: '',
      },
      inputOptions: [],
      dragging: false
    };
  },
  computed: {
    canSave() {
      return this.inputData.question && this.inputOptions.length
    }
  },
  methods: {
    appendOptionToInputData() {
      this.inputOptions.push({text: ''})
    },
    addQuestion() {
      this.questionList.push({
        question: '', options: [],
        __new: true
      })
      this.isEditingIndex = this.questionList.length - 1;
    },
    readyEdit(index) {
      if (this.isEditingIndex !== -1) {
        return;
      }
      this.isEditingIndex = index;
      this.inputData.question = this.questionList[index].question;
      this.inputOptions = this.questionList[index].options;
    },
    cancelEdit(index) {
      if (this.questionList[index].__new) {
        this.questionList.splice(index, 1);
      }
      console.log(index);
      this.isEditingIndex = -1;
      this.inputData.question = '';
      this.inputOptions = [];
    },
    confirmEdit(index) {
      if (this.questionList[index].__new) {
        if (this.canSave) {
          // 保存题目，保存选项
          delete this.questionList[index].__new;
          this.questionList[index].question = this.inputData.question;
          this.questionList[index].options = [...this.inputOptions];
          this.isEditingIndex = -1
        }
      }
    },
    checkMove: function(e) {
      window.console.log("Future index: " + e.draggedContext.futureIndex);
    }
  }
};
</script>
<style scoped>

.question-wrp {
  padding: 10px;
}

.question-wrp:hover {
  background-color: #eee;
  cursor: move;
}
.ghost {
  opacity: 0.5;
  background-color: #eee;
}
.handle {
  cursor: move;
}
</style>