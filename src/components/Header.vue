<template>
    <div id="Header">
        <!-- 免费咨询 -->
        <div class="top-left-edition">
   
        </div>
        <!-- CT图像处理字（可删除放图片） -->
        <div id="word">

            <h1>{{msg}}</h1>
        </div>

        <!-- 导航栏 -->
<!--        <el-menu-->
<!--            :default-active="activeIndex"-->
<!--            class="el-menu-demo"-->
<!--            id="menu"-->
<!--            mode="horizontal"-->
<!--            @select="handleSelect"-->
<!--        >-->
<!--            <el-menu-item index="1">处理中心</el-menu-item>-->
<!--            <el-submenu index="2">-->
<!--                <template slot="title">我的工作台</template>-->
<!--                <el-menu-item index="2-1">选项1</el-menu-item>-->
<!--                <el-menu-item index="2-2">选项2</el-menu-item>-->
<!--                <el-menu-item index="2-3">选项3</el-menu-item>-->
<!--                <el-submenu index="2-4">-->
<!--                    <template slot="title">选项4</template>-->
<!--                    <el-menu-item index="2-4-1">选项1</el-menu-item>-->
<!--                    <el-menu-item index="2-4-2">选项2</el-menu-item>-->
<!--                    <el-menu-item index="2-4-3">选项3</el-menu-item>-->
<!--                </el-submenu>-->
<!--            </el-submenu>-->
<!--            <el-menu-item index="0">处理中心</el-menu-item>-->
<!--            <el-menu-item index="1">处理中心</el-menu-item>-->
<!--        </el-menu>-->
    </div>
</template>
<script>
    export default {
        name: "Header",
        data() {
            return {
                msg: "图像描述系统",
                activeIndex: "1"
            };
        },
        methods: {
            handleSelect(key, keyPath) {
                console.log(key, keyPath);
            }
        }
    };
</script>
<style scoped>
    #Header {
        padding: 30px 110px 0 150px;
        width: 90%;
        margin: 10px auto;
    }

    #word {
        margin-left: 40%;
        margin-top: -35px;
        margin-bottom: 37px;
        height: 60px;
        /* box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12), 0 0 6px rgba(0, 0, 0, 0.04); */
        line-height: 3.2em;
    }

    h1 {
        /*text-align: center;*/
        color: #21b3b9;
        letter-spacing: 30px;
        font-size: 3.3em;
    }

    .el-menu-demo {
        width: 80%;
        margin: 0px auto;
        padding: 0px auto;
    }

    .top-left-edition span i {
        float: left;
        margin-right: 10px;
    }

    i,
    input,
    label {
        vertical-align: middle;
    }

    i {
        border: 0;
        display: block;
        cursor: pointer;
    }

    .top-left-edition span {
        float: left;
        font-size: 16px;
        color: #999999;
        line-height: 24px;
        margin-right: 40px;
    }
</style>


