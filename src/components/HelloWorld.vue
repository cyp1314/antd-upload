<template>
  <a-card :bordered="false" title="上传">
    <a-row style="margin-bottom: 20px">
      <a-col :span="4">
        <div class="example-side">demo1:上传文件</div>
      </a-col>
      <a-col :span="8">
        <div class="example-side">
          <!-----------------------------------------------------------------demo1---start-->
          <a-upload
            :beforeUpload="beforeFileUpload"
            :defaultFileList="defaultFileList"
            :headers="headers"
            :multiple="true"
            :remove="handleFileRemove"
            @change="handleChange"
            action="http://139.219.1.146:12000/upload"
            style="width: 60%"
          >
            <a-button>
              <a-icon type="upload" />
              上传
            </a-button>
          </a-upload>
          <!-----------------------------------------------------------------demo1---end-->
        </div>
      </a-col>
      <a-col :span="12" class="example-side-row">
        <strong
          >新上传的是不能下载的，defaultFileList记录了当前上传的文件信息</strong
        >
        {{ defaultFileList }}
      </a-col>
    </a-row>

    <a-row style="margin-bottom: 20px">
      <a-col :span="4">
        <div class="example-side">demo2:图片上传</div>
      </a-col>
      <a-col :span="8">
        <div class="example-side">
          <!-----------------------------------------------------------------demo2---start-->
          <a-upload
            :beforeUpload="beforeImageUpload"
            :defaultFileList="defaultImageList"
            :headers="headers"
            :remove="handleImageRemove"
            @change="handleImageChange"
            @preview="handleImagePreview"
            action="http://139.219.1.146:12000/upload"
            listType="picture-card"
            style="width: 60%"
          >
            <div v-if="defaultImageList.length < 10">
              <a-icon :type="imageLoading ? 'loading' : 'plus'" />
              <div class="ant-upload-text">上传</div>
            </div>
          </a-upload>
          <a-modal
            :footer="null"
            :visible="previewVisible"
            @cancel="handleImageCancel"
          >
            <img :src="previewImage" alt="example" style="width: 100%" />
          </a-modal>
          <!-----------------------------------------------------------------demo2---end-->
        </div>
      </a-col>
      <a-col :span="12" class="example-side-row">
        {{ defaultImageList }}
      </a-col>
    </a-row>
  </a-card>
</template>

<script>
export default {
  name: "UploadBox",
  data() {
    return {
      headers: {
        authorization: "",
      },
      // --------- file-upload----start
      defaultFileList: [
        {
          uid: "1",
          name: "测试",
          status: "done",
          url:
            "https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png",
        },
      ],
      // --------- file-upload----end

      // --------- img-upload----start
      defaultImageList: [
        {
          uid: "1",
          name: "Emilia",
          status: "done",
          url:
            "https://zos.alipayobjects.com/rmsportal/jkjgkEfvpUPVyRjUImniVslZfWPnJuuZ.png",
        },
        {
          uid:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFSmAPNfSAAJjsrWmgLs438.jpg",
          name: "111.jpg",
          status: "done",
          url:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFSmAPNfSAAJjsrWmgLs438.jpg",
        },
        {
          uid:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFSyAeVBJAAJjsrWmgLs341.jpg",
          name: "111.jpg",
          status: "done",
          url:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFSyAeVBJAAJjsrWmgLs341.jpg",
        },
        {
          uid:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFTCAF3riAAJjsrWmgLs518.jpg",
          name: "111.jpg",
          status: "done",
          url:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFTCAF3riAAJjsrWmgLs518.jpg",
        },
        {
          uid:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFTSAMqNxAAJjsrWmgLs509.jpg",
          name: "111.jpg",
          status: "done",
          url:
            "http://42.159.8.47/group1/M00/01/0B/CgAAD2CVFTSAMqNxAAJjsrWmgLs509.jpg",
        },
      ],
      imageLoading: false,
      previewVisible: false,
      previewImage: "",
      // --------- img-upload----end
    };
  },
  created() {
    this.headers.authorization = "Bearer " + "AABBCC";
  },
  methods: {
    // ---------------------------------------------file--start--
    handleChange(info) {
      const file = info.file;
      const status = info.file.status;
      if (status === "done") {
        this.$message.success("图片上传成功...");
        const res = file.response.data;
        console.log("res:", res);
        const item = {
          uid: res.newFileName,
          name: res.filename,
          status: "done",
          url: res.url,
        };
        this.defaultFileList.push(item);
      } else if (status === "error") {
        this.$message.error(`${info.file.name} 文件上传失败`);
      }
    },
    handleFileRemove(file) {
      const name = file.name;
      this.defaultFileList = this.defaultFileList.filter((item) => {
        if (name !== item.name) return item;
      });
    },
    beforeFileUpload(file) {
      return new Promise((resolve, reject) => {
        const isJPG = file.type === "image/jpeg";
        if (!isJPG) {
          this.$message.error("您只能上传jpg文件");
          return reject(new Error("您只能上传jpg文件"));
        }
        const isLt2M = file.size / 1024 / 1024 < 2;
        if (!isLt2M) {
          this.$message.error("文件大小不能大于2MB");
          return reject(new Error("文件大小不能大于2MB"));
        }
        this.$message.info("文件正在上传中...");
        return resolve(true);
      });
    },
    // ---------------------------------------------file--end
    // ---------------------------------------------img--start
    handleImageChange(info) {
      console.log(">>>>>:", info);
      const file = info.file;
      const status = info.file.status;
      if (info.file.status === "uploading") {
        this.imageLoading = true;
        return;
      }
      if (status === "done") {
        this.$message.success("图片上传成功...");
        this.imageLoading = false;
        const res = file.response;
        console.log("res:", res);
        const item = {
          uid: res.url,
          name: res.name,
          status: "done",
          url: res.url,
        };
        this.defaultImageList.push(item);
      } else if (status === "error") {
        this.$message.error(`${info.file.name} 图片上传失败`);
      }
    },
    beforeImageUpload(file) {
      return new Promise((resolve, reject) => {
        const isJPG = file.type === "image/jpeg";
        if (!isJPG) {
          this.$message.error("您只能上传jpg文件");
          return reject(new Error("您只能上传jpg文件"));
        }
        const isLt2M = file.size / 1024 / 1024 < 2;
        if (!isLt2M) {
          this.$message.error("文件大小不能大于2MB");
          return reject(new Error("文件大小不能大于2MB"));
        }
        this.$message.info("文件正在上传中...");
        return resolve(true);
      });
    },
    handleImageRemove(file) {
      const name = file.name;
      this.defaultImageList = this.defaultImageList.filter((item) => {
        if (name !== item.name) return item;
      });
    },
    handleImagePreview(file) {
      this.previewImage = file.url || file.thumbUrl;
      this.previewVisible = true;
    },
    handleImageCancel() {
      this.previewVisible = false;
    },
    // ---------------------------------------------img--end
  },
};
</script>

<style scoped>
.example-side {
  display: flex;
  flex-direction: row;
  padding-left: 50px;
}

.example-side-row {
  display: flex;
  flex-direction: column;
  padding-left: 50px;
}

.example-center {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
</style>


