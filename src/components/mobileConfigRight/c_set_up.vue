<template>
    <div class="setUp">
        <template>
            <el-tabs v-model="configData.tabVal">
                <el-tab-pane label="内容设置"/>
                <el-tab-pane label="样式设置"/>
            </el-tabs>
        </template>
    </div>
</template>

<script>
    export default {
        name: 'c_set_up',
        props: {
            configObj: {
                type: Object
            },
            configNme: {
                type: String
            }
        },
        data () {
            return {
                defaults: {},
                configData: {}
            }
        },
        watch: {
            configObj: {
                handler (nVal, oVal) {
                    this.defaults = nVal
                    this.configData = nVal[this.configNme]
                },
                deep: true
            }
        },
        mounted () {
            this.$nextTick(() => {
                this.defaults = this.configObj
                this.configData = this.configObj[this.configNme]
            })
        },
        methods: {
            onClickTab (e) {
                // this.$emit('getConfig', e);
            }
        }
    }
</script>

<style scoped lang="scss">
    .setUp /deep/.ivu-tabs-nav-scroll{
        padding: 0 30px;
    }
    .setUp /deep/.ivu-tabs-nav .ivu-tabs-tab{
        padding: 8px 45px;
    }
</style>
