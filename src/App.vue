<template>
  <div>
    <cropper ref="cropper" class="upload-example-cropper" :src="image" />
    <img id="image" />
    <div class="button-wrapper">
      <span class="button" @click="$refs.file.click()">
        <input
          type="file"
          ref="file"
          @change="loadImage($event)"
          accept="image/*"
        />
      </span>
    </div>
    <image-compressor
      ref="compressor"
      :preview="true"
      :sizeTo="200"
      :capture="false"
      :debug="1"
      doNotResize="gif"
      :autoRotate="false"
      outputFormat="blob"
      @doneCompression="setImage"
    >
      <label for="fileInput" slot="upload-label">
        <span class="upload-caption">Upload</span>
      </label>
    </image-compressor>
    <button @click="uploadImage">Uploadx</button>
  </div>
</template>

<script>
import { Cropper } from "vue-advanced-cropper";
import ImageCompressor from "./components/ImageCompressor.vue";
import "vue-advanced-cropper/dist/style.css";

export default {
  components: {
    Cropper,
    ImageCompressor,
  },
  data() {
    return {
      image: null,
    };
  },
  methods: {
    reset() {
      this.image = null;
    },
    loadImage(event) {
      // Reference to the DOM input element
      var input = event.target;
      // Ensure that you have a file before attempting to read it
      if (input.files && input.files[0]) {
        // create a new FileReader to read this image and convert to base64 format
        var reader = new FileReader();
        // Define a callback function to run, when FileReader finishes its job
        reader.onload = (e) => {
          // Note: arrow function used here, so that "this.imageData" refers to the imageData of Vue component
          // Read image as base64 and set to imageData
          this.image = e.target.result;
        };
        // Start the reader job - read file as a data url (base64 format)
        reader.readAsDataURL(input.files[0]);
      }
    },
    setImage: function(output) {
      // this.cImage = output;
      console.log(output);
    },
    uploadImage() {
      const { canvas } = this.$refs.cropper.getResult();
      if (canvas) {
        const form = new FormData();
        canvas.toBlob((blob) => {
          form.append("file", blob);
          console.log(blob);
          this.$refs.compressor.handleFile(blob);
          this.$on("onComplete", (data) => {
            console.log(data, "ccc");
          });
          // const reader = new FileReader();
          // reader.readAsDataURL(blob);
          // reader.onloadend = function() {
          //   var base64data = reader.result;
          //   console.log(base64data);
          //   document.querySelector("#image").src = base64data;
          // };
          // You can use axios, superagent and other libraries instead here
          // fetch("http://example.com/upload/", {
          //   method: "POST",
          //   body: form,
          // });
          // Perhaps you should add the setting appropriate file format here
        }, "image/jpeg");
      }
    },
  },
};
</script>
