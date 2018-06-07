<template>
  <div class="page">
    <header class="page-header">
      <!-- <h1>請上傳食物圖片</h1> -->
      <input name="uploadFile" class="page-header__input" @change="onChange" type="file">
      <label class="page-header__tips" for="uploadFile"></label>
      <div class="page-header__photo">
        <img :src="userPhoto">
      </div>
    </header>
    <section class="page__body">
    </section>
    <!-- <footer class="page__footer">Copyright © 慧如許願池</footer> -->
  </div>
</template>

<script>
export default {
  name: 'Index',
  data() {
    return {
      userPhoto: '',
    };
  },
  methods: {
    onChange(e) {
      if (e.target.files && e.target.files[0]) {
        const reader = new FileReader();
        reader.readAsDataURL(e.target.files[0]);
        reader.onload = (data) => {
          this.userPhoto = `data:image/png;base64,${data.target.result.replace(/.*,/, '')}`;
        };
      }
    },
  },
  watch: {
  },
};
</script>

<style>
.page {
  width: 90%;
  margin: 50px 5%;
  text-align: center;
}

.page-header__input {
  display: none;
}

.page-header__tips {
  width: 20vw;
  height: 20vw;
  z-index: 1;
  cursor: pointer;
  border: 1px solid #e8e8e8;
  display: inline-block;
}

.photo{
  width: 20vw;
  height: 20vw;
  border: 1px solid #e8e8e8;
  margin: auto;
}

</style>
