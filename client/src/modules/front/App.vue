<template>
    <div id="app">
        <div class="wrap">
            <Top></Top>
            <transition name="fade" mode="out-in">
                <!-- keep-alive排除article -->
                <keep-alive exclude="article">
                    <router-view>
                    </router-view>
                </keep-alive>
            </transition>
        </div>
        <footer class="footer">
            <p>Copyright © <a href='http://xxxx.cool'>夕阳醉了</a>&nbsp;&nbsp;2018 by cool-blog</p>
            <p>粤ICP备16046513号</p>
        </footer>
    </div>
</template>

<script>
    import Top from './components/common/Top.vue'

    export default {
        name: 'app',
        components: {
            Top
        }
    }
</script>

<style lang="stylus">
    @import 'assets/stylus/main.styl'
    @import 'assets/stylus/markdown.styl'
    $footer-height = 60px
    #app
        width 100%
        height 100%

    .fade-enter-active, .fade-leave-active
        transition all .2s ease

    .fade-enter, .fade-leave-active
        opacity 0

    .wrap
        min-height 100%
        margin-bottom -($footer-height)
        background-color: #f6f6f6
        margin-top: -25px

    .wrap:after
        content ""
        display block
        height $footer-height + 50px

    .footer
        height $footer-height
        text-align center
        color $grey
        text-align center
        line-height 20px
        font-size: 12px
        a
            text-decoration none
            color $blue-link
            &:hover
                text-decoration underline

    @media screen and (max-width: 850px)
        .fade-enter
            transform translate(30px, 0px)

        .fade-leave-active
            transform translate(-30px, 0px)
</style>
