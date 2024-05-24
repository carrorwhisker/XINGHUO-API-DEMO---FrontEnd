<template>
  <div class="image-preview-container">
    <!-- 图片预览 -->
    <div class="preview-area">
      <img :src="'data:image/png;base64,' + previewImage" alt="预览图片" v-if="previewImage" class="preview-image"/>
      <div v-else class="preview-placeholder">请输入描述并点击发送按钮生成图片</div>
    </div>

    <!-- 输入框和发送按钮 -->
    <div class="input-button-group">
      <input type="text" v-model="content" placeholder="请输入描述" class="input-field"/>
      <button :disabled="buttonStatus" @click="getImage" class="send-button">{{ buttonText }}</button>
    </div>
  </div>
</template>
<script setup>
import {ref} from 'vue'

let previewImage = ref(null);
let content = ref(null);
let buttonText = ref('发送');
let buttonStatus = ref(false);

async function postData(url = '', data = {}) {
  // 默认选项都在这里
  const response = await fetch(url, {
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    headers: {
      'Content-Type': 'application/json'
      // 如果API需要授权，可以添加Authorization头部
      // 'Authorization': 'Bearer YOUR_TOKEN'
    },
    body: JSON.stringify(data) // body 数据类型必须与指定的“Content-Type”一致
  });
  return response.json(); // 解析 JSON
}

function getImage() {
  if (!content.value) {
    alert('请输入内容再发送')
    return
  }
  buttonText.value = '图片生成中...';
  buttonStatus.value = true;
  postData('http://localhost:8080/getImage', content.value)
      .then(data => {
        previewImage.value = data.payload.choices.text[0].content; // 请求返回的数据
        buttonStatus.value = false;
        buttonText.value = '发送';
      })
      .catch(error => {
        console.error('Error:', error);
      });
}
</script>
<style scoped>
.image-preview-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%; /* 可以根据需要调整 */
  padding: 20px;
  box-sizing: border-box;
}

.preview-area {
  margin-bottom: 20px;
  text-align: center;
}

.preview-image {
  width: 200px;
  height: 200px;
  height: auto; /* 图片高度自动以保持比例 */
  display: block;
  margin: 0 auto;
}

.preview-placeholder {
  color: #888; /* 占位符文本颜色 */
  font-size: 16px; /* 占位符文本大小 */
}

.input-button-group {
  display: flex;
  align-items: center;
  justify-content: center;
}

.input-field {
  margin-right: 10px; /* 输入框和按钮之间的间距 */
  padding: 5px 10px; /* 输入框内边距 */
  border: 1px solid #ccc; /* 输入框边框 */
  border-radius: 4px; /* 输入框边框圆角 */
  width: 200px; /* 输入框宽度 */
}

.send-button {
  padding: 5px 10px; /* 按钮内边距 */
  background-color: #4CAF50; /* 按钮背景色 */
  color: white; /* 按钮文本颜色 */
  border: none; /* 按钮无边框 */
  border-radius: 4px; /* 按钮边框圆角 */
  cursor: pointer; /* 鼠标悬停时变为小手图标 */
}
</style>
