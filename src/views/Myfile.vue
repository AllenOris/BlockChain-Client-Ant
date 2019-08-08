<template>
    <div class="fillcontain">
        <el-button @click="resetDateFilter">清除日期过滤器</el-button>
        <el-button @click="clearFilter">清除所有过滤器</el-button>
        <el-button @click="refresh">刷新</el-button>
        <el-table
                ref="filterTable"
                :data="tableData"
                stripe
                style="width: 100%">
            <el-table-column
                    prop="uploadTime"
                    label="上传日期"
                    sortable
                    column-key="uploadTime"
                    :filters="[{text: '2016-05-01', value: '2016-05-01'}, {text: '2016-05-02', value: '2016-05-02'}, {text: '2016-05-03', value: '2016-05-03'}, {text: '2016-05-04', value: '2016-05-04'}]"
                    :filter-method="filterHandler"
            >
            </el-table-column>
            <el-table-column
                    prop="fileID"
                    label="文件ID"
                    sortable>
            </el-table-column>
            <el-table-column
                    prop="name"
                    label="文件名"
                    sortable>
            </el-table-column>
            <el-table-column
                    prop="size"
                    label="文件大小(KB)"
                    sortable
            >
            </el-table-column>
            <el-table-column
                    prop="count"
                    label="我的下载次数"
                    sortable
                    column-key="count"
            >
            </el-table-column>
            <el-table-column
                    prop="valid"
                    label="是否通过审核"
                    sortable
            ></el-table-column>
            <el-table-column
                    prop="type"
                    label="文件类型"
                    :filters="tags"
                    :filter-method="filterTag"
                    filter-placement="bottom-end">
                <template slot-scope="scope">
                    <el-tag
                            :type="scope.row.tag === 'zip' ||scope.row.tag === 'rar'||scope.row.tag === '7z'? 'primary' :scope.row.tag === 'txt'? 'success':scope.row.tag === 'doc'||scope.row.tag === 'docx'?'warning':scope.row.tag === 'mp3'||scope.row.tag === 'wav'?'danger':scope.row.tag === 'ppt'||scope.row.tag === 'pptx'?'info':'text'"
                            disable-transitions>{{scope.row.tag}}
                    </el-tag>
                </template>
            </el-table-column>
            <el-table-column
                    fixed="right"
                    label="操作">
                <template slot-scope="scope">
                    <el-button @click="handleDownload(scope.row)" type="text" size="small">下载</el-button>
                    <el-button @click="handleDelete(scope.row)" type="text" size="small">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
    </div>
</template>

<script>
    export default {
        name: "myfile",
        data() {
            return {
                tableData: [],
                tags: [],
                isRouterAlive: true
            }
        },
        provide() { //提供
            return {
                reload: this.reload
            }
        },
        created() {
            this.getProfile();
        },
        methods: {
            handle(row) {
                this.download(row);
            },
            handleDownload(row) {
                this.download(row);
            },
            handleDelete(row) {
                let fd = new FormData();
                // fd.append('name', this.$name);
                //fd.append('password', this.$password);
                fd.append('fileID', row.fileID);
                this.$axios.post("/api/user/delete", fd).then(async res => {
                    if (res.data.code == 0) {
                        this.$message({message: "删除成功", type: "success"});
                        this.getProfile();
                    } else {
                        this.$message.error(res.data.message);
                    }
                });
            },
            refresh() {
                this.getProfile();
            },
            getProfile() {

            },
            resetDateFilter() {
                this.$refs.filterTable.clearFilter('date');
            }
            ,
            clearFilter() {
                this.$refs.filterTable.clearFilter();
            }
            ,
            formatter(row, column) {
                return row.address;
            }
            ,
            filterTag(value, row) {
                return row.tag === value;
            }
            ,
            filterHandler(value, row, column) {
                const property = column['property'];
                return row[property] === value;
            }
        }
    }
</script>

<style scoped>
    .fillcontain {
        width: 100%;
        height: 100%;
        padding: 16px;
        box-sizing: border-box;
    }
</style>
