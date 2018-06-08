<template>
  <el-container class="page">
    <h1>UPLOAD YOUR PHOTOS</h1>
    <header class="page__header" v-loading="isFetchItem">
      <input id="uploadFile" class="page-header__input" @change="onChange" type="file">
      <label :class="['page-header__tips', hasUpload ? 'upload' : '']" for="uploadFile">
        <el-button class="page-header__tipsbtn" v-if="!hasUpload" type="primary">
          upload
          <i class="el-icon-upload el-icon--right"></i>
        </el-button>
        <div class="page-header__photo" v-if="hasUpload">
          <img :src="userPhotoBase64">
        </div>
      </label>
    </header>
    <el-main v-if="hasUpload">
      <el-button type="success" @click="onSubmit">
        START
        <i class="el-icon-refresh el-icon--right"></i>
      </el-button>
      <span class="item-name" v-if="hasVision">NAME: {{ item.name }}</span>
    </el-main>
    <el-footer class="footer">Copyright © 慧如許願池</el-footer>
  </el-container>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Index',
  data() {
    return {
      apiKey: 'AIzaSyCJmd5VpiUmlg8o-ROm-4rW6DLYKJa5oVA',
      userPhotoBase64: '',
      isFetchItem: false,
      hasUpload: false,
      hasVision: false,
      item: {
        name: '',
      },
    };
  },
  computed: {
    userPhoto() {
      return this.userPhotoBase64.split(',')[1];
    },
  },
  methods: {
    onChange(e) {
      if (e.target.files && e.target.files[0]) {
        const reader = new FileReader();
        reader.readAsDataURL(e.target.files[0]);
        reader.onload = (data) => {
          this.hasUpload = true;
          this.userPhotoBase64 = data.target.result;
        };
      }
    },
    onSubmit() {
      if (this.isFetchItem) return;

      this.isFetchItem = true;
      this.hasVision = false;
      const data = {
        requests: [
          {
            image: {
              content: this.userPhoto,
            },
            features: [
              {
                type: 'WEB_DETECTION',
              },
            ],
          },
        ],
      };
      axios({
        method: 'post',
        url: `https://vision.googleapis.com/v1/images:annotate?key=${this.apiKey}`,
        data,
      })
        .then((res) => {
          console.log(res.data);
          this.hasVision = true;
          this.item.name = res.data.responses[0].webDetection.bestGuessLabels[0].label;
          console.log(this.item.name);
        })
        .catch((error) => {
          console.log(error);
        });
      this.isFetchItem = false;
    },
  },
  watch: {
  },
};
</script>

<style>
*{
  box-sizing: border-box;
}

body{
  background-color: #409EFF;
}
.page {
  width: 90%;
  margin: 0 5%;
  text-align: center;
}

h1{
  color: #fff;
}

.page-header__input {
  display: none;
}

.page-header__tips {
  position: relative;
  width: auto;
  min-height: auto;
  z-index: 1;
  cursor: pointer;
  border: 1px solid #F2F6FC;
  display: inline-block;
  vertical-align: top;
  font-size: 0;
  text-align: center;
}

.page-header__tipsbtn {
  pointer-events: none;
}

.page-header__tips.upload{
  position: relative;
  width: 20vw;
  min-height: 10vw;
}

.page-header__photo{
  width: 100%;
  height: 100%;
  border: 1px solid #F2F6FC;
  margin: auto;
  overflow: hidden;
}

.page-header__photo img{
  width: 100%;
  height: auto;
}

.item-name{
  width: 100%;
  margin: 2vw 0;
  display: block;
  color: #fff;
}

.footer{
  font-size: 0.5vw;
  text-align: center;
  margin-top: 2vw;
  color: #606266;
}

@media screen and (max-width: 980px) {
  .page {
    width: 90%;
    margin: 0 5%;
    text-align: center;
  }

  .page-header__tips.upload{
    position: relative;
    width: 80vw;
    min-height: 10vw;
  }

  .item-name{
    width: 100%;
    margin: 10vw 0;
    display: block;
    color: #fff;
  }
}

</style>
