<template>
    <div class="header-wrapper">
        <div class="header">
            <div class="left">
                <nuxt-link to="/">
                    <img class="logo" src="https://o4j806krb.qnssl.com/public/images/cnodejs_light.svg" alt="logo">
                </nuxt-link>
                <form class="search" @submit.prevent="">
                    <input
                        class="search-input"
                        :class="{ focus: isFocus || mouseover }"
                        v-model="searchValue"
                        type="text"
                        autocomplete="off"
                        @focus="toggleFocus"
                        @blur="toggleFocus"
                        @mouseover="toggleOver"
                        @mouseout="toggleOver"
                    >
                </form>
            </div>
            <div class="right">
                <span v-for="(menu, index) in menus"
                    class="menu"
                    @click="switchMenu(index)"
                    :key="menu.text"
                    v-if="menu.login === undefined ||  menu.login === isLogin"
                >
                    {{ menu.text }}
                </span>
            </div>
        </div>
    </div>
</template>

<script>
    import { mapGetters, mapActions } from 'vuex'

    let Cookie

    export default {
        name: 'commonHeader',
        data() {
            return {
                isFocus: false,
                mouseover: false,
                searchValue: '',
                menus: [
                    { text: '首页', path: '/' },
                    { text: '未读消息', path: '/user/messages', login: true },
                    { text: '新手入门', path: '' },
                    { text: 'API', path: '' },
                    { text: '关于', path: '' },
                    { text: '注册', path: '', login: false },
                    { text: '登录', path: '/login', login: false },
                    { text: '设置', path: '', login: true },
                    { text: '退出', handler: 'loginOut', login: true }
                ]
            }
        },
        mounted() {
            Cookie = require('js-cookie')
        },
        methods: {
            ...mapActions([
                'setUserInfo'
            ]),
            toggleFocus(e) {
                this.isFocus = e.type === 'focus'
            },
            toggleOver(e) {
                this.mouseover = e.type === 'mouseover'
            },
            switchMenu(index) {
                let { path, handler } = this.menus[index]
                if (path) {
                    this.$router.push(path)
                    return
                }
                if (handler) {
                    this[handler]()
                }
            },
            loginOut() {
                Cookie.remove('access_token')
                this.setUserInfo({
                    loginState: false,
                    user: {},
                    accessToken: ''
                })
                this.$router.push('/')
            }
        },
        computed: {
            ...mapGetters([
                'isLogin'
            ])
        }
    }
</script>

<style lang="stylus">
    .header-wrapper
        background-color #444
        .header
            margin 0 auto
            max-width 1400px
            display flex
            flex-wrap wrap
            justify-content space-between
            .left
                display flex
                align-items center
                min-height 50px
                max-width 373px
                justify-content center
                flex-wrap wrap
                .logo
                    width 120px
                    height 28px
                    padding 7px 0
                .search
                    width 233px
                    height 26px
                    margin-left 20px
                    position relative
                    font-size 13px
                    .search-input
                        border none
                        outline none
                        width 100%
                        height 100%
                        box-sizing border-box
                        padding 3px 5px 3px 22px
                        background url(https://o4j806krb.qnssl.com/public/images/search.e53b380a.hashed.png) no-repeat 4px 4px #888
                        transition all 0.5s
                        border-radius 15px
                        color #666
                        &.focus
                            background-color #ffffff
            .right
                display flex
                align-items center
                min-height 50px
                flex-wrap wrap
                .menu
                    font-size 13px
                    color #cccccc
                    padding 10px 15px
                    cursor pointer

    @media screen and (max-width: 980px)
        .header-wrapper
            margin 0 5px
</style>
