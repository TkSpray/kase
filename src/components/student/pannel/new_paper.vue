<template>
    <div class="modal-body">
        <form class="needs-validation" novalidate>
            <div class="input-group mb-3">
                <div class="input-group-prepend">
                    <label class="input-group-text">选择项目</label>
                </div>
                <select class="custom-select" v-model="pid">
                    <option v-for="item in prj_list_show" :value="item.pid" selected>
                        {{item.name}}
                    </option>
                </select>
                <div class="input-group-append">
                    <label class="input-group-text">
                        <i class="fas fa-search"></i>
                    </label>
                </div>
                <input type="text" class="form-control" v-model="key_word" @keyup="search">
            </div>
            <div class="input-group mb-3">
                <div class="input-group-prepend">
                    <span class="input-group-text">课题名称</span>
                </div>
                <input type="text" class="form-control" v-model="name" required>
            </div>
            <div class="input-group mb-3">
                <div class="input-group-prepend">
                    <span class="input-group-text">申请人</span>
                </div>
                <input type="text" class="form-control" v-model="applicant" required>
            </div>
            <button class="btn btn-outline-primary" @click="apply">提交</button>
        </form>
    </div>
</template>

<script>
    // 新建课题表单，需要课题名称，项目编号和申请人名称
    import api from '@/service/api'
    import store from '@/store'

    export default {
        name: "new_paper",
        data: function () {
            return {
                name: '',
                pid: '',
                applicant: '',
                key_word: '',
                prj_list: [],
                prj_list_show: []
            }
        },
        async created() {
            this.prj_list = (await api.data.app.list_prj()).data
            this.search()
        },
        methods: {
            search: function () {
                console.debug('[Search]', this.key_word)
                this.prj_list_show = this.prj_list.filter((v) =>
                    v.name.search(this.key_word) !== -1
                )
            },
            async apply() {
                await api.data.app.new_app({
                    name: this.name,
                    pid: this.pid,
                    applicant: this.applicant
                }).catch(e => {
                    console.debug(e)
                })
                //TODO: 提示成功
                this.$emit('finished')
                this.$router.push('/')
            }
        },
        computed: {}
    }
</script>

<style scoped>

</style>
