<template>
  <div class="edit">
    <div class="photo-container">
      <ImgDisplay :url="'http://35.185.111.183'+photo.file_location.url"/>
    </div>
    <div class="main-container">
      <PhotoForm
        :title="photo.title"
        :description="photo.description"
        v-if="photo.title"
        @photo-form-submit="patchUpdate"
      />
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
  data: function() {
    return {
      photo: {}
    };
  },
  created() {
    var _this = this;
    var id = this.$route.params.id;
    var url = "http://35.185.111.183/api/v1/photos/" + id;
    console.log(url);
    if (localStorage.getItem("photo-album-user")) {
      var token = JSON.parse(localStorage.getItem("photo-album-user"))
        .authToken;
      var params = { auth_token: token };
    }
    // get data from api
    axios
      .get(url, { params })
      .then(function(res) {
        _this.photo = res.data;
      })
      .catch(function(err) {
        console.error(err.response.data);
      });
  },
  methods: {
    patchUpdate: function(payload) {
      var that = this;
      // generate request url
      var id = this.$route.params.id;
      var updateUrl = "http://35.185.111.183/api/v1/photos/" + id;
      var token = JSON.parse(localStorage.getItem("photo-album-user"))
        .authToken;
      // pack params using FormData
      var params = new FormData();
      params.append("auth_token", token);
      params.append("title", payload.title);
      params.append("date", payload.date);
      params.append("description", payload.description);
      params.append("file_location", payload.file_location);
      console.log(params);
      // get data from api
      axios
        .patch(updateUrl, params, {
          headers: {
            "Content-Type": "multipart/form-data"
          }
        })
        .then(function(res) {
          that.$router.push("/photos/" + res.data.result.id);
        })
        .catch(function(err) {
          console.error(err.response.data);
        });
    }
  }
};
</script>

 <style scoped>
.edit {
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