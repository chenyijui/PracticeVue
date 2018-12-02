<template>
  <div class="create">
    <div class="photo-container">
      <ImgDisplay/>
    </div>
    <div class="main-container">
      <PhotoForm @photo-form-submit="postCreate"/>
    </div>
  </div>
</template>

 <script>
import ImgDisplay from "@/components/ImgDisplay";
import PhotoForm from "@/components/PhotoForm";
import axios from "axios";

export default {
  components: {
    ImgDisplay: ImgDisplay,
    PhotoForm: PhotoForm
  },
  methods: {
    postCreate: function(payload) {
      var _this = this;
      var createPhotoUrl = "http://35.185.111.183/api/v1/photos";
      var token = JSON.parse(localStorage.getItem("photo-album-user"))
        .authToken;
      var formData = new FormData();
      var headers = { "Content-Type": "multipart/form-data" };
      formData.append("auth_token", token);
      formData.append("title", payload.title);
      formData.append("description", payload.description);
      formData.append("date", payload.date);
      formData.append("file_location", payload.file_location);

      axios
        .post(createPhotoUrl, formData, { headers })
        .then(function(res) {
          console.log(res.data);
          _this.$router.push("/photos/" + res.data.result.id);
        })
        .catch(function(err) {
          console.log(err.response.data);
        });
    }
  }
};
</script>

 <style scoped>
.create {
  display: flex;
  justify-content: center;
}
.photo-container {
  padding-top: 100px;
  margin-right: 50px;
}
.main-container {
  padding-top: 100px;
}
</style>