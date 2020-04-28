<template>
  <div class="tinymce-box">
    <editor
      v-model="myValue"
      :init="init"
      :disabled="disabled"
      @onClick="onClick"
    >
    </editor>
  </div>
</template>

<script>
import tinymce from "tinymce/tinymce"; //tinymce默认hidden，不引入不显示
import Editor from "@tinymce/tinymce-vue";
import "tinymce/themes/silver";
// 编辑器插件plugins
// 更多插件参考：https://www.tiny.cloud/docs/plugins/
import "tinymce/plugins/image"; // 插入上传图片插件
// import "tinymce/plugins/media"; // 插入视频插件
import "tinymce/plugins/table"; // 插入表格插件
import "tinymce/plugins/lists"; // 列表插件
import "tinymce/plugins/wordcount"; // 字数统计插件
import "tinymce/plugins/fullscreen"; // 全屏插件
import "tinymce/plugins/preview"; // 预览
import "tinymce/plugins/visualblocks"; // 区域方块
import "tinymce/plugins/print"; // 打印
import "tinymce/plugins/link"; // 链接
// import "tinymce/plugins/paste"; // 粘贴
import "tinymce/plugins/hr"; // 换行符
// import "tinymce/plugins/imagetools"; //
import "tinymce/plugins/pagebreak"; // 分页符
import "tinymce/plugins/autolink"; // 链接
//测试
import "tinymce/plugins/advlist"; // 分页符
import "tinymce/plugins/anchor"; // 链接import
import "tinymce/plugins/save"; // 链接
export default {
  components: {
    Editor
  },
  name: "tinymce",
  props: {
    value: {
      type: String,
      default: ""
    },
    disabled: {
      type: Boolean,
      default: false
    },
    plugins: {
      type: [String, Array],
      default:
        "lists image table wordcount fullscreen preview hr visualblocks print link autolink pagebreak"
    },
    toolbar: {
      type: [String, Array],
      default:
        "undo redo | styleselect formatselect fontsizeselect fontselect | formatting alignment forecolor backcolor hr pagebreak | bullist numlist outdent indent | lists imagetools image  link autolink table | removeformat | fullscreen preview visualblocks print | anchor"
    }
  },
  data() {
    return {
      myValue: this.value,
      init: {
        // inline: true,
        //超出工具按钮换行显示
        // toolbar_mode: "sliding",
        language_url: "tinymce/langs/zh_CN.js",
        language: "zh_CN",
        // skin_url: "/tinymce/skins/ui/oxide",
        skin_url: "tinymce/skins/ui/oxide-dark", //暗色系
        height: 500,
        plugins: this.plugins,
        toolbar: this.toolbar,
        toolbar_groups: {
          formatting: {
            text: "文字格式",
            tooltip: "Formatting",
            items: "bold italic underline strikethrough | superscript subscript"
          },
          alignment: {
            icon: "align-left",
            tooltip: "alignment",
            items: "alignleft aligncenter alignright alignjustify"
          }
        },
        // toolbar: [
        //   {
        //     name: "history",
        //     items: ["undo", "redo"]
        //   },
        //   {
        //     name: "styles",
        //     items: ["formatselect", "fontsizeselect", "fontselect"]
        //   },
        //   {
        //     name: "formatting",
        //     items: [
        //       "bold",
        //       "italic",
        //       "underline",
        //       "strikethrough",
        //       "forecolor",
        //       "backcolor",
        //       "superscript",
        //       "subscript",
        //       "removeformat"
        //     ]
        //   },
        //   {
        //     name: "alignment",
        //     items: ["alignleft", "aligncenter", "alignright", "alignjustify"]
        //   },
        //   {
        //     name: "indentation",
        //     items: [
        //       "outdent",
        //       "indent",
        //       "bullist",
        //       "numlist",
        //       "Blockquote",
        //       "visualblocks"
        //     ]
        //   },
        //   {
        //     name: "link",
        //     items: ["lists", "imagetools", "image", "link", "autolink", "table"]
        //   },
        //   {
        //     name: "link2",
        //     items: ["fullscreen", "preview", "print", "anchor"]
        //   }
        // ],
        // style_formats: [],
        branding: false,
        //顶部工具栏 false 隐藏
        menubar: "file edit insert view format table tools help",
        // menubar: false,
        // 可隐藏底栏的元素路径
        elementpath: false,
        // 隐藏编辑器底部的状态栏
        statusbar: false,
        // 允许粘贴图像
        paste_data_images: true,
        // 此处为图片上传处理函数，这个直接用了base64的图片形式上传图片，
        // 如需ajax上传可参考https://www.tiny.cloud/docs/configure/file-image-upload/#images_upload_handler
        images_upload_handler: (blobInfo, success, failure) => {
          const img = "data:image/jpeg;base64," + blobInfo.base64();
          success(img);
          //   console.log(blobInfo, 555);
        },
        //文件类型限制
        file_picker_types: "file  media",
        file_picker_callback: function(
          callback,
          value,
          meta,
          success,
          failure
        ) {
          //文件分类
          let filetype =
            ".pdf, .txt, .zip, .rar, .7z, .doc, .docx, .xls, .xlsx, .ppt, .pptx, .mp3, .mp4";
          //后端接收上传文件的地址
          let upurl = "/demo/upfile.php";
          //为不同插件指定文件类型及后端地址
          switch (meta.filetype) {
            case "image":
              filetype = ".jpg, .jpeg, .png, .gif";
              upurl = "upimg.php";
              break;
            case "media":
              filetype = ".mp3, .mp4";
              upurl = "upfile.php";
              break;
            case "file":
            default:
          }
          //模拟出一个input用于添加本地文件
          let input = document.createElement("input");
          input.setAttribute("type", "file");
          input.setAttribute("accept", filetype);
          input.click();
          input.onchange = function() {
            let file = this.files[0];
            let xhr, formData;
            console.log(file.name);
            // xhr = new XMLHttpRequest();
            // xhr.withCredentials = false;
            // xhr.open("POST", upurl);
            // xhr.onload = function() {
            //   let json;
            //   if (xhr.status != 200) {
            //     failure("HTTP Error: " + xhr.status);
            //     return;
            //   }
            //   json = JSON.parse(xhr.responseText);
            //   if (!json || typeof json.location != "string") {
            //     failure("Invalid JSON: " + xhr.responseText);
            //     return;
            //   }
            //   callback(json.location);
            // };
            console.log("地址", file, file.name);

            formData = new FormData();
            formData.append("file", file, file.name);
            alert("发送文件：" + file.name);
            //TODO:发送文件获取 返回地址
            //   xhr.send(formData);
          };
        },
        //head设置
        // block_formats: "Paragraph=p; Header 1=h1; Header 2=h2; Header 3=h3",
        fontsize_formats:
          "10px 12px 14px 16px 20px 24px 30px 32px 35px 40px 50px",
        //允许撤销级别 10
        custom_undo_redo_levels: 10
      }
    };
  },
  mounted() {
    tinymce.init({});
  },
  methods: {
    // 添加相关的事件，可用的事件参照文档=> https://github.com/tinymce/tinymce-vue => All available events
    // 需要什么事件可以自己增加
    onClick(e) {
      this.$emit("onClick", e, tinymce);
    },
    // 可以添加一些自己的自定义事件，如清空内容
    clear() {
      //清除内容
      //   this.myValue = "";
      console.log("测试输出内容", this.myValue);
    }
  },
  watch: {
    value(newValue) {
      this.myValue = newValue;
    },
    myValue(newValue) {
      this.$emit("input", newValue);
    }
  }
};
</script>
<style acoped>
.tox .tox-tbtn--bespoke .tox-tbtn__select-label {
  width: 4em !important;
}
.tox .tox-dialog__body-content iframe {
  background: #fff;
}
</style>
