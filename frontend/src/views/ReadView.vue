<template>
  <div class="common-layout">
    <el-container>
      <el-header style="height: auto;">
        <el-button @click="backToIndex">回到主页</el-button>
        这是头部
      </el-header>
      <el-main>
        <PostHead :postHead="postHead"></PostHead>
        <el-table class="reply-table" :data="replyList" border style="width: 100%;">
          <el-table-column>
            <template #default="list">
              <Reply :reply="list.row"></Reply>
            </template>
          </el-table-column>
          <template #empty>
            <div style="text-align: center; padding: 20px;">
              <p style=" font-size: 24px;color: black">暂无回复</p>
            </div>
          </template>
        </el-table>
      </el-main>
      <el-footer class="footer">
        <div style="margin-bottom: 20px;">
          <el-input type="textarea" v-model="form.text" placeholder="发表你的回复" :rows="5" resize="none"
                    style="margin-top: 15px"/>
          <el-button @click="putReply(form)">提交</el-button>
          <InsertNote :text="form.text" @update:text="updateText"></InsertNote>
        </div>
      </el-footer>
    </el-container>
  </div>
</template>

<script setup>
import {getPostAndReply} from "@/net/read/getPostAndReply.js";
import PostHead from "@/components/PostHead.vue";
import Reply from "@/components/Reply.vue";
import {tableRowClick} from "@/js/tableRowClick.js";
import {putReply} from "@/net/post/putReply.js";
import {backToIndex} from "@/js/backToIndex.js";
import InsertNote from "@/components/InsertNote.vue";
import {checkMe} from "@/net/auth/checkMe.js";
import {ElMessage} from "element-plus";

let logged = ref(false);
const postHead = ref()
const replyList = ref([])
onBeforeMount(() => {
  let query = new URLSearchParams(window.location.search)
  let pid = query.get('pid');
  if (pid) {
    form.pid = pid
    getPostAndReply(pid, postHead, replyList)
  }
  checkMe(false,
      () => logged.value = true,
      (msg) => {
        if (msg !== null) ElMessage.warning(msg)
        localStorage.setItem('userToken', '')
      }
  )
})

const form = reactive({
  pid: '',
  text: ''
})

const updateText = (newText) => {
  form.text = newText;
};
</script>

<style>

.el-table__header-wrapper {
  display: none;
}

.reply-table .el-table__body-wrapper table tr:hover > td {
  background-color: inherit !important; /* 取消背景色变化 */
  cursor: default !important; /* 禁用指针变化 */
}


.footer {
  position: relative;
  bottom: 0;
  width: 100%;
  text-align: center;
}

</style>