<template>
    <table class="table table-hover table-sm mb-0">
        <thead>
        <tr>
            <th scope="col" v-for="item in formHeaders">
                {{item}}
            </th>
        </tr>
        <tr>
            <th scope="col" v-for="(item,idx) in formHeaders">
                <input class="form-control" @keyup="filter" :aria-label="item"
                       v-model="filterKey[idx]" :placeholder="placeholders[idx]">
            </th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="item in listShow" @click="jump(item.cid)">
            <td>{{item.cid}}</td>
            <td>{{item.name}}</td>
            <td>{{prj_list[item.pid]}}</td>
            <td>{{item.applicant}}</td>
            <td>
                <span class="badge badge-pill"
                      :class="[item.status===0?'badge-primary':item.status===1?'badge-success':'badge-danger']">
                    {{stat_text[item.status]}}
                </span>
            </td>
            <td>{{item.time}}</td>
            <td>
                <i class="fas" :class="[item.pdf?'fa-check text-success':'fa-minus text-danger']"></i>
            </td>
            <td>
                <i class="fas" :class="[item.zip?'fa-check text-success':'fa-minus text-danger']"></i>
            </td>
        </tr>
        </tbody>
    </table>
</template>

<script>
    // FIXME: 使用props分发
    import api from '@/service/api'

    export default {
        name: "list",
        data: function () {
            return {
                /**
                 * 课题列表
                 */
                list: [],
                /**
                 * 过滤后展示的课题列表
                 */
                listShow: [],
                /**
                 * 状态名称
                 */
                stat_text: ['Pending', 'Accepted', 'Rejected'],
                /**
                 * 项目列表
                 */
                prj_list: [],
                /**
                 * 标题列表
                 */
                formHeaders: ['ID', '课题名称', '项目名称', '申请人', '当前状态', '申请日期', '文档', '附件'],
                /**
                 * 过滤关键词，来自input
                 */
                filterKey: [],
                /**
                 * input的placeholder
                 */
                placeholders: ['', '', '', '', 'Accepter/Rejected/Pending', '', 'y/n', 'y/n']
            }
        },
        props: {
            projectList: Array
        },
        async created() {
            //FIXME:仅限App
            // 拉取课题列表和项目列表
            this.list = (await api.data.app.list()).data
            this.listShow = this.list
            const projectList = (await api.data.app.list_prj()).data
            // 存储原始数据
            this.$store.commit('change_state', {projectList})
            // 方便索引
            this.prj_list = projectList.reduce((pre, cur) => {
                pre[cur.pid] = cur.name
                return pre
            }, [])
        },
        methods: {
            // 跳转到课题
            async jump(cid) {
                console.debug("real???//")
                const {pdf, zip, title} = this.list.reduce((pre, cur) => {
                    return cur.cid === cid ? {pdf: cur.pdf, zip: cur.zip, title: cur.name} : pre
                }, {pdf: false, zip: false, title: ''})
                this.$emit('list-click', {cid, pdf, zip, title})
            },
            // 课题过滤
            filter() {
                let pids = []
                if (this.filterKey[2]) {
                    // 查找项目名称，pid填充
                    pids = this.$store.state.projectList.reduce((pre, cur) => {
                        if (cur.name.indexOf(this.filterKey[2]) !== -1)
                            pre.push(cur.pid)
                        return pre
                    }, [])
                }
                this.listShow = this.list.filter((item) => {
                    if (this.filterKey[0]) {
                        // 过滤ID
                        if (item.cid.toString().indexOf(this.filterKey[0]) === -1)
                            return false
                    }
                    if (this.filterKey[1]) {
                        // 过滤课题名称
                        if (item.name.toString().indexOf(this.filterKey[1]) === -1)
                            return false
                    }
                    if (this.filterKey[2]) {
                        // 过滤项目名称
                        if (pids.indexOf(item.pid) === -1)
                            return false
                    }
                    if (this.filterKey[3]) {
                        // 过滤申请人
                        if (item.applicant.indexOf(this.filterKey[3]) === -1)
                            return false
                    }
                    if (this.filterKey[4]) {
                        // 过滤状态
                        if (this.filterKey[4] === 'Rejected' && item.status !== -1) {
                            return false
                        } else if (this.filterKey[4] === 'Pending' && item.status !== 0) {
                            return false
                        } else if (this.filterKey[4] === 'Accepted' && item.status !== 1) {
                            return false
                        }
                    }
                    if (this.filterKey[5]) {
                        // 过滤申请日期
                        if (item.time.indexOf(this.filterKey[5]) === -1)
                            return false
                    }
                    if (this.filterKey[6]) {
                        // 过滤文档
                        if (this.filterKey[6] !== 'y' && this.filterKey[6] !== 'n')
                            return false
                        if (this.filterKey[6] === 'y' ^ item.pdf)
                            return false
                    }
                    if (this.filterKey[7]) {
                        // 过滤文档
                        if (this.filterKey[7] !== 'y' && this.filterKey[7] !== 'n')
                            return false
                        if (this.filterKey[7] === 'y' ^ item.zip)
                            return false
                    }
                    return true
                })
            }
        }
    }
</script>

<style scoped>

</style>
