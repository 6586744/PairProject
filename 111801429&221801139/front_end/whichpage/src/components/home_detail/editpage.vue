<template>
    <div id="edit_page">
        <div id="edit_part">
            <el-form>
                <el-form-item label="论文题目: ">
                    <el-input class="edit_input" v-model="page_name"></el-input>
                </el-form-item>
                <el-form-item label="论文年份: ">
                    <el-input
                        class="edit_input"
                        v-model="page_time"
                        :disabled="true"
                    ></el-input>
                </el-form-item>
                <el-form-item label="论文链接: ">
                    <el-input class="edit_input" v-model="page_link"></el-input>
                </el-form-item>
                <el-form-item label="关键词组: ">
                    <el-input
                        class="edit_input"
                        v-model="page_tag"
                        :disabled="true"
                    ></el-input>
                </el-form-item>
                <el-form-item label="论文摘要: ">
                    <el-input
                        class="edit_abstract"
                        type="textarea"
                        :rows="6"
                        v-model="page_abstract"
                        :disabled="true"
                    >
                    </el-input>
                </el-form-item>
            </el-form>
        </div>

        <div id="edit_btn">
            <el-button plain @click="save_page">保存修改</el-button>
        </div>
    </div>
</template>

<script>
export default {
    name: "Editpage",

    data() {
        return {
            page_isbn: this.$route.params.isbn,
            page_name: "",
            page_time: "",
            page_link: "",
            page_tag: "",
            page_abstract: "",
        };
    },

    created() {
        this.$axios({
            method: "GET",
            url: `/page/detail/${this.page_isbn}`,
        }).then((re) => {
            console.log(re);
            if (re.data.errno == 0) {
                let { data } = re.data;
                this.page_abstract = data.abstract;
                this.page_tag = data.tag;
                this.page_link = data.link;
                this.page_name = data.title;
                this.page_time = data.year;
            }
        });
    },

    methods: {
        save_page(e) {
            let target = e.target;
            if (target.nodeName == "SPAN") {
                target = target.parentNode;
            }
            target.blur();

            if (this.page_name == "" || this.page_link == "") {
                this.$notify({
                    title: "警告",
                    message: "论文题目/论文链接不能为空！",
                    type: "warning",
                    duration: 2000,
                    showClose: false,
                });

                return;
            }

            let data = {
                title: this.page_name,
                link: this.page_link,
            };

            this.$axios({
                method: "PUT",
                url: `/page/detail/${this.page_isbn}`,
                data: data,
            }).then((re) => {
                console.log(re);
                if (re.data.errno == 0) {
                    this.$message({
                        message: "修改成功！",
                        type: "success",
                    });

                    this.$router.push({
                        name: "detailpage",
                        params: { isbn: this.page_isbn },
                    });
                }
            });
        },
    },
};
</script>

<style scoped>
#edit_page {
    /* border: 1px red solid;
    box-sizing: border-box; */
    height: 100%;
    width: 77%;
}

#edit_part {
    /* border: 1px red solid;
    box-sizing: border-box; */
    width: 67%;
    height: 70%;
    margin: 0 auto;
    margin-top: 5%;
    background-color: #ecf5de;
    border-top-right-radius: 90px;
    box-shadow: 5px 5px 5px #add4cf;
    overflow: hidden;
}

.el-form {
    /* border: 1px red solid;
    box-sizing: border-box; */
    width: 88%;
    margin: 0 auto;
    margin-top: 4%;
}

.edit_input,
.el-textarea {
    width: 88%;
    font-size: 16px;
}

.edit_input >>> .el-input__inner {
    border: 1px #ecf5de solid;
    border-bottom: 1px #033 solid;
    border-radius: 0;
    background-color: #ecf5de;
}

.edit_abstract >>> .el-textarea__inner {
    resize: none;
}

#edit_btn {
    /* border: 1px red solid;
    box-sizing: border-box; */
    display: flex;
    justify-content: flex-end;
    width: 67%;
    margin: 0 auto;
    margin-top: 2%;
}

.el-button {
    border-color: #033;
    color: #033;
    font-weight: bold;
    border-radius: 10px;
}

.el-button:hover {
    color: #fcfdf5;
    background-color: #033;
}
</style>

<style>
.el-form .el-form-item__label {
    color: #033;
    font-size: 16px;
    font-weight: bold;
}

.el-textarea.is-disabled .el-textarea__inner {
    cursor: default;
    background-color: #fcfdf5;
    color: #033;
}

.el-input.is-disabled .el-input__inner {
    cursor: default;
    background-color: #ecf5de;
    color: #033;
    border: 1px #ecf5de solid;
    overflow: auto;
}
</style>