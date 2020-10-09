<template>
    <div id="Content">
        <el-dialog
            id="hello"
            title="图像描述系统使用须知"
            :visible.sync="centerDialogVisible"
            width="35%"
            font="SimSun"
            :before-close="handleClose"
        >
            <el-steps :active="5"  finish-status="process ">
                <el-step title="步骤1" style="width:280px;padding-left: 50px">
                    <template slot="description">
                        <p style="font-size: 16px">上传图像至服务器</p>
                        <br>
                        <br>
                    </template>
                </el-step>
                <el-step title="步骤2" style="width:260px;margin-left:-5px;">
                    <template slot="description">
                        <p>使用训练的模型检测物体区域</p>
                        <p>并生成文字描述</p>
                       <br>
                    </template>
                </el-step>
                <el-step title="步骤3" style="width:260px;margin-left:-5px;">
                    <template slot="description">
                        <div>
                            <p>可视化检测区域</p>
                            <p>和文字描述</p>
                            <br>
                        </div>
                    </template>
                </el-step>
            </el-steps>
            <span slot="footer" class="dialog-footer">
         <el-button type="primary" @click="welcome">开始体验</el-button>
      </span>
        </el-dialog>
        <el-dialog
            title="预测中"
            :visible.sync="dialogTableVisible"
            :show-close="false"
            :close-on-press-escape="false"
            :append-to-body="true"
            :close-on-click-modal="false"
            :center="true"
        >
            <el-progress :percentage="percentage"></el-progress>
            <span slot="footer" class="dialog-footer">非GPU同学服务器性能有限，请耐心等待约一分钟</span>
        </el-dialog>

        <div id="aside">
            <!-- 查看病人信息 -->
            <el-card class="box-card" style="width:250px;height:350px;margin-left:-30px" >
                <div slot="header" class="clearfix">
                    <span>学生信息</span>
                </div>
                <div v-for="(value,name) in patient" :key="name" class="text item">
                    <h3 style="font-weight:normal;">{{name}}:{{value}}</h3>
                </div>
            </el-card>

            <!-- 步骤条：下载 上传 -->
            <el-card
                class="box-card"
                body-style="padding: 15px 5px 15px 10px"
                style="width:250px;height:500px;margin-top:150px;margin-left:-30px"
            >
                <div slot="header" class="clearfix" style="text-align:center;">
                    <span class="steps" style="letter-spacing: 7px;">使用流程</span>
                </div>
                <div style="height: 600px;" class="step_1">
                    <el-steps direction="vertical" :active="active" finish-status="success">
                        <el-step style="height: 120px;" title="步骤 1">
                            <template slot="description" style="font-size: 10px!important;">
                                上传图像至服务器
                                <!-- 下载文件 -->
                   
                            </template>
                        </el-step>
                        <el-step style="height: 150px;" title="步骤 2">
                            <template slot="description">
                                <!-- 上传文件 -->
                                使用训练的模型检测物体区域并生成文字描述
                                <input class="file" name="file" type="file" @change="update">
                            </template>
                        </el-step>

                        <!-- 获得图像 -->
                        <el-step title="可视化检测区域和文字描述" style="height: 200px;">
                            <template slot="description"></template>
                        </el-step>
                    </el-steps>
                </div>
            </el-card>
        </div>
        <!-- 上传返回信息部分：原CT图部分  标出肿瘤的CT图像 图像特征-->
        <div id="CT">
            <!-- CT图像 -->
            <div id="CT_image">
                <!-- 原CT图 -->
                <el-card
                    id="CT_image_1"
                    class="box-card"
                    style="border-radius: 25px;width:1800px;height:700px;margin-left:-450px;"
                >
                    <div class="demo-image__preview1">
                        <div
                            v-loading="loading"
                            element-loading-text="上传图片中"
                            element-loading-spinner="el-icon-loading"
                        >
                            <el-image
                                :src="url_1"
                                class="image_1"
                                :preview-src-list="srcList"
                                style="border-radius: 3px 3px 0 0"
                            >
                                <div slot="error">
                                    <div slot="placeholder" class="error">
                                        <el-button
                                            v-show="showbutton"
                                            type="primary"
                                            icon="el-icon-upload"
                                            class="download_bt"
                                            v-on:click="true_upload"
                                        >
                                            上传图片
                                            <input
                                                ref="upload"
                                                style="display: none"
                                                name="file"
                                                type="file"
                                                @change="update"
                                            >
                                        </el-button>
                                    </div>
                                </div>
                            </el-image>
                        </div>
                        <!-- 原CT图文字 -->
                        <div class="img_info_1" style="border-radius:0 0 5px 5px;">
                            <span style="color:white;letter-spacing:6px;">原始图像</span>
                        </div>
                    </div>
                    <!-- 标出肿瘤的CT图像 -->
                    <div class="demo-image__preview2">
                        <div
                            v-loading="loading"
                            element-loading-text="处理中,请耐心等待"
                            element-loading-spinner="el-icon-loading"
                        >
                            <el-image
                                :src="url_2"
                                class="image_2"
                                :preview-src-list="srcList1"
                                style="border-radius: 3px 3px 0 0;"
                            >
                                <div slot="error">
                                    <div slot="placeholder" class="error">{{wait_return}}</div>
                                </div>
                            </el-image>
                        </div>
                        <!-- 标出肿瘤的CT图像文字 -->
                        <div class="img_info_1" style="border-radius: 0 0 5px 5px;">
                            <span style="color:white;letter-spacing:4px;">标出检测区域的图像</span>
                        </div>
                    </div>
                </el-card>
            </div>


            <!-- 分割线 -->

            <!-- 图像特征部分 -->
            <div id="info_patient">
                <!-- 卡片放置表格 -->
                <el-card style="border-radius: 24px; width:1800px;height:300px;margin-top:-50px; margin-left:-450px">
                    <div slot="header" class="clearfix">
                        <span>文字描述</span>
                        <el-button
                            style="margin-left: 35px"
                            v-show="!showbutton"
                            type="primary"
                            icon="el-icon-upload"
                            class="download_bt"
                            v-on:click="true_upload2"
                        >
                            重新选择图像
                            <input
                                ref="upload2"
                                style="display: none"
                                name="file"
                                type="file"
                                @change="update"
                            >
                        </el-button>
                    </div>

                    <textarea v-model=caption style="width:1700px;height:200px;margin-left:30px;font-size:32px"     v-loading="loading"
                                    element-loading-text="数据正在处理中，请耐心等待"
                                    element-loading-spinner="el-icon-loading"></textarea>
                
                </el-card>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from "axios";

    export default {
        name: "Content",
        data() {
            return {
                // server_url:'http://58.87.66.50:5003',
                server_url:'http://127.0.0.1:5000',
                perimeter_picture_data: 0,
                area_picture_data: 0,
                activeName: "first",
                active: 0,
                centerDialogVisible: true,
                url_1: "",
                url_2: "",
                textarea: "",
                srcList: [],
                srcList1: [],
                feature_list: [],
                feature_list_1: [],
                feat_list: [],
                url: "",
                caption: "",
                visible: false,
                activeName: "second",
                wait_return: "等待上传",
                wait_upload: "等待上传",
                loading: false,
                table: false,
                isNav: false,
                showbutton: true,
                percentage: 0,
                fullscreenLoading: false,
                opacitys: {
                    opacity: 0
                },
                dialogTableVisible: false,
                patient: {
                    姓名: "杨少康",
                    学号: "zy1806712",
                    导师: "牛建伟",
                }
            };
        },
        created: function () {
            document.title = '图像描述演示系统';
        },
        methods: {
            true_upload() {
                this.$refs.upload.click();
            },
            true_upload2() {
                this.$refs.upload2.click();
            },
            handleClose(done) {
                this.$confirm("确认关闭？")
                    .then(_ => {
                        done();
                    })
                    .catch(_ => {
                    });
            },
            next() {
                this.active++;
            },
            // 获得目标文件
            getObjectURL(file) {
                var url = null;
                if (window.createObjcectURL != undefined) {
                    url = window.createOjcectURL(file);
                } else if (window.URL != undefined) {
                    url = window.URL.createObjectURL(file);
                } else if (window.webkitURL != undefined) {
                    url = window.webkitURL.createObjectURL(file);
                }
                return url;
            },   
            // 上传文件
            update(e) {
                this.percentage = 0;
                this.dialogTableVisible = true;
                this.url_1 = "";
                this.url_2 = "";
                this.srcList = [];
                this.srcList1 = [];
                this.wait_return = "";
                this.wait_upload = "";
                this.feature_list = [];
          
                this.feat_list = [];
                this.fullscreenLoading = true;
                this.loading = true;
                this.showbutton = false;
                let file = e.target.files[0];
                this.url_1 = this.$options.methods.getObjectURL(file);
                let param = new FormData(); //创建form对象
                param.append("file", file, file.name); //通过append向form对象添加数据
                // console.log(param.get("file")); //FormData私有类对象，访问不到，可以通过get判断值是否传进去
                //todo aaaa
                var timer = setInterval(() => {
                    this.myFunc();
                }, 30);
                let config = {
                    headers: {"Content-Type": "multipart/form-data"}
                }; //添加请求头
                axios
                    .post(this.server_url+"/upload", param, config)
                    .then(response => {
                        console.log("+++")
                        this.percentage = 100;
                        clearInterval(timer);
                        this.url_1 = response.data.image_url;
                        this.srcList.push(this.url_1);
                        this.url_2 = response.data.draw_url;
                        this.srcList1.push(this.url_2);
                        this.fullscreenLoading = false;
                        this.dialogTableVisible = false;
                        this.feat_list = Object.keys(response.data.image_info);
                        this.caption = response.data.image_info['caption']
                        this.loading = false;
                        console.log(response.data.image_info['caption'])
                        console.log('this.featureslist',this.feat_list)
              
                    });
            },
   
            myFunc() {
                if (this.percentage + 33 < 99) {
                    this.percentage = this.percentage + 33;
                    console.log(this.percentage);
                } else {
                    this.percentage = 99;
                }
            },
     
            welcome() {
                this.centerDialogVisible = false;
                this.next();
             
            },
   
        },

    };
</script>

<style>
    .el-button {
        padding: 12px 20px !important;
    }

    #hello p {
        font-size: 15px !important;
        /*line-height: 25px;*/
    }

    .n1 .el-step__description {
        padding-right: 20%;
        font-size: 14px;
        line-height: 20px;
        /* font-weight: 400; */
    }
</style>

<style scoped>
    * {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }

    .dialog_info {
        margin: 20px auto;
    }

    .text {
        font-size: 14px;
    }

    .item {
        margin-bottom: 18px;
    }

    .clearfix:before,
    .clearfix:after {
        display: table;
        content: "";
    }

    .clearfix:after {
        clear: both;
    }

    .box-card {
        width: 680px;
        height: 200px;
        border-radius: 25px;
        margin-top: -20px;
    }

    .divider {
        width: 50%;
    }

    #CT {
        display: flex;
        height: 100%;
        width: 70%;
        flex-wrap: wrap;
        justify-content: center;
        margin: 0 auto;
        margin-right: 0px;
        max-width: 1200px;
        /* background-color: RGB(239, 249, 251); */
        /* box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04); */
    }

    #CT_image_1 {
        width: 90%;
        height: 40%;
        /* background-color: RGB(239, 249, 251); */
        margin: 0px auto;
        padding: 0px auto;
        /* box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04); */
        margin-right: 180px;
        margin-bottom: 0px;
        border-radius: 25px;
    }

    #CT_image {
        margin-bottom: 60px;
        margin-left: 30px;
        margin-top: 5px;
    }

    .image_1 {
        width: 800px;
        height: 600px;
        background: #ffffff;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    }
    .image_2 {
        width: 800px;
        height: 600px;
        background: #ffffff;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
    }

    .img_info_1 {
        height: 30px;
        width: 800px;
        text-align: center;
        background-color: #21b3b9;
        line-height: 30px;
    }

    .demo-image__preview1 {
        width: 800px;
        height: 290px;
        margin: -5px 20px;
        float: left;
    }

    .demo-image__preview2 {
        width: 800px;
        height: 290px;

        margin: -5px 900px;
        /* background-color: green; */
    }

    .error {
        margin: 200px auto;
        width: 50%;
        padding: 10px;
        text-align: center;
    }

    .block-sidebar {
        position: fixed;
        display: none;
        left: 50%;
        margin-left: 600px;
        top: 350px;
        width: 60px;
        z-index: 99;
    }

    .block-sidebar .block-sidebar-item {
        font-size: 50px;
        color: lightblue;
        text-align: center;
        line-height: 50px;
        margin-bottom: 20px;
        cursor: pointer;
        display: block;
    }

    div {
        display: block;
    }

    .block-sidebar .block-sidebar-item:hover {
        color: #187aab;
    }

    .download_bt {
        padding: 10px 16px !important;
    }

    #upfile {
        width: 104px;
        height: 45px;
        background-color: #187aab;
        color: #fff;
        text-align: center;
        line-height: 45px;
        border-radius: 3px;
        box-shadow: 0 0 2px 0 rgba(0, 0, 0, 0.1), 0 2px 2px 0 rgba(0, 0, 0, 0.2);
        color: #fff;
        font-family: "Source Sans Pro", Verdana, sans-serif;
        font-size: 0.875rem;
    }

    .file {
        width: 200px;
        height: 130px;
        position: absolute;
        left: -20px;
        top: 0;
        z-index: 1;
        -moz-opacity: 0;
        -ms-opacity: 0;
        -webkit-opacity: 0;
        opacity: 0; /*css属性&mdash;&mdash;opcity不透明度，取值0-1*/
        filter: alpha(opacity=0);
        cursor: pointer;
    }

    #upload {
        position: relative;
        margin: 0px 0px;
    }

    #download {
        padding: 0px;
        margin: 0px 0px;
    }

    .patient {
        margin: 50px auto;
        margin-bottom: 100px;
        /* margin-right: 100px; */
        background-color: #187aab;
        border-radius: 5px;
        box-shadow: 0 0 2px 0 rgba(0, 0, 0, 0.1), 0 2px 2px 0 rgba(0, 0, 0, 0.2);
        color: #fff;
        font-family: "Source Sans Pro", Verdana, sans-serif;
        font-size: 0.875rem;
        line-height: 1;
        padding: 0.75rem 1.5rem;
    }

    #Content {
        width: 85%;
        height: 800px;
        background-color: #ffffff;
        margin: 15px auto;
        display: flex;
        min-width: 1200px;
        /* border: 1px solid #e4e7ed; */
        /* box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04); */
    }

    #aside {
        width: 25%;
        background-color: #ffffff;
        padding: 30px;
        margin-right: 80px;
        /* background-color: RGB(239, 249, 251); */
        /* box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04); */
        height: 800px;
    }

    .divider {
        background-color: #eaeaea !important;
        height: 2px !important;
        width: 100%;
        margin-bottom: 50px;
    }

    .divider_1 {
        background-color: #ffffff;
        height: 2px !important;
        width: 100%;
        margin-bottom: 20px;
        margin: 20px auto;
    }

    .steps {
        font-family: "lucida grande", "lucida sans unicode", lucida, helvetica,
        "Hiragino Sans GB", "Microsoft YaHei", "WenQuanYi Micro Hei", sans-serif;
        color: #21b3b9;
        text-align: center;
        margin: 15px auto;
        font-size: 20px;
        font-weight: bold;
        text-align: center;
    }

    .step_1 {
        /*color: #303133 !important;*/
        margin: 20px 26px;
    }

    #info_patient {
        margin-top: 10px;
        margin-right: 160px;
    }
</style>


