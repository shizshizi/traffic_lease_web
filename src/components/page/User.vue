<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item>
                    <i class="el-icon-lx-cascades"></i> 用户信息
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">

                <el-input placeholder="用户名" class="handle-input mr10" v-model="username" @input="getUserData"></el-input>
                <el-button type="primary" icon="el-icon-search" @click="getUserData">搜索</el-button>
            </div>


            
            <el-table :data="users" border class="table" ref="multipleTable" header-cell-class-name="table-header">

                <el-table-column prop="username" label="用户名" align="center">

                </el-table-column>
                <el-table-column prop="phoneNumber" label="电话号码" align="center">

                </el-table-column>
                <el-table-column prop="createTimeStr" label="注册时间" width="180px" align="center"></el-table-column>

                <el-table-column label="状态" align="center">
                    <template slot-scope="scope">
                        <span v-if="scope.row.state==0">
                            正常
                        </span>
                        <span v-if="scope.row.state==-1">
                            已停用
                        </span>

                    </template>
                </el-table-column>

                <el-table-column label="操作" align="center">
                    <template slot-scope="scope">
                        <span v-if="scope.row.state==0">
                            <el-button type="danger" @click="changeUserState(scope.row)">注销</el-button>
                        </span>
                        <span v-if="scope.row.state==-1">
                            <el-button type="success" @click="changeUserState(scope.row)"> 启用</el-button>
                        </span>

                    </template>
                </el-table-column>


            </el-table>

        </div>

        共<span v-text="total"></span>条数据,<span v-text="num"></span>/<span v-text="maxNum"></span>页
        <div style="text-align: right">
            <template>
                <el-input-number v-model="num" :min="1" :max="maxNum" @change="getUserData" label="分页">
                </el-input-number>
            </template>
        </div>

    </div>
</template>

<script>
    export default {

        data() {
            return {
                users: [],
                num: 1,
                maxNum: 1,
                total: 1,
                username: ''

            };
        },
        created() {
            this.getUserData()
        },
      
        methods: {
            getUserData() {
                this.getRequest('/user?pageNum=' + this.num + '&username=' + this.username).then(resp => {
                    this.users = resp.data.list
                    this.num = resp.data.pageNum
                    this.maxNum = resp.data.pages
                    this.total = resp.data.totalm
                })
            },
            changeUserState(user) {
                let state = -1
                if (user.state == -1) {
                    state = 0
                }
                this.postRequest('/user/changeState', {
                    'state': state,
                    "userId": user.id
                }).then(resp => {
                    this.getUserData()
                })
            },
           


        }
    };
</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }

    .table {
        width: 100%;
        font-size: 14px;
    }

    .red {
        color: #ff0000;
    }

    .mr10 {
        margin-right: 10px;
    }

    .table-td-thumb {
        display: block;
        margin: auto;
        width: 40px;
        height: 40px;
    }
</style>