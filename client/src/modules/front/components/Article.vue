<template>
    <div class="article">
        <div class="article__loading" v-if="isLoading">
            <Loading :loadingMsg='loadingMsg'></Loading>
        </div>
        <Side :isInList='false' :category='category'></Side>
        <div class="article__main" v-if="!isLoading">
            <h1 class="article__title">{{currentPost.title}}</h1>
            <p class="article__time">{{currentPost.createTime}}</p>
            <div class="article__line"></div>
            <div class="article__content markdown-body" v-html="currentPostCompile" ref="post">
            </div>
        </div>
    </div>
</template>

<script>
    import Loading from 'publicComponents/Loading.vue'
    import articleApi from 'api/article.js'
    import marked from 'lib/marked.js'
    import Side from './common/Side.vue'

    import {
        mapGetters,
        mapActions
    } from 'vuex'

    export default {
        name: 'article',
        data() {
            return {
                category: [],
                isLoading: false,
                loadingMsg: '加载中...'
            }
        },
        computed: {
            ...mapGetters([
                'currentPost',
                'currentPostCompile'
            ]),
            compiledPost() {
                return this.compiledMarkdown(this.$store.state.currentPost.content)
            }
        },
        components: {
            Side,
            Loading
        },
        beforeMount() {
            // 如果想等说明数据已经拿到，就没必要进行再去取数据了
            if (this.currentPost.id == this.$route.params.id) {
                this.$nextTick(() => {
                    // 提取文章标签，生成目录
                    Array.from(this.$refs.post.querySelectorAll("h1,h2,h3,h4,h5,h6")).forEach((item, index) => {
                        item.id = item.localName + '-' + index;
                        this.category.push({
                            tagName: item.localName,
                            text: item.innerText,
                            href: '#' + item.localName + '-' + index
                        })
                    })
                })
                return;
            }
            this.isLoading = true;
            this.getPost(this.$route.params.id).then(() => {
                this.isLoading = false;
                this.$nextTick(() => {
                    // 提取文章标签，生成目录
                    Array.from(this.$refs.post.querySelectorAll("h1,h2,h3,h4,h5,h6")).forEach((item, index) => {
                        item.id = item.localName + '-' + index;
                        this.category.push({
                            tagName: item.localName,
                            text: item.innerText,
                            href: '#' + item.localName + '-' + index
                        })
                    })
                })
            })
        },
        preFetch(store) {
            return store.dispatch('getPost', store.state.route.params.id)
        },
        mounted() {
            //this.compiledPost = this.compiledMarkdown(this.currentPost.content)
            //this.isLoading = false
        },
        methods: {
            ...mapActions([
                'getPost'
            ]),
            compiledMarkdown(value) {
                return marked(value)
            }
        }
    }
</script>

<style lang="stylus">
    @import '../assets/stylus/markdown.styl'
</style>
<style lang="stylus" scoped>
    @import '../assets/stylus/_settings.styl'
    .article
        max-width 1000px
        margin 85px auto 0 auto
        padding 10px 20px 0px 20px
        &__main
            min-height 100%
            background-color: #ffffff
            margin-left: 150px
            padding: 25px
            box-shadow: 1px 1px 1px hsl(0, 0%, 80%)
        &__title
            font-size 24px
            word-break break-all
            color: #005cc5
        &__time
            color #7f8c8d
            font-weight 400
            margin-bottom 10px
        &__column
            color #7f8c8d
        &__line
            border-top: 1px solid rgba(0, 0, 0, 0.1);
            border-bottom: 1px solid rgba(255, 255, 255, 0.3);
            margin-bottom: 10px

        &__loading
            position fixed
            top 50%
            left 50%
            width 300px
            height 200px
            margin-left -(@width/ 2) + 125
            margin-top -(@height/ 2) + 60

    @media screen and (max-width: 850px)
        .article
            position relative
            margin-top 80px
            &__main
                margin-left 0
            &__loading
                position absolute
                top 200px
                left 50%
                width 300px
                margin-left -(@width/ 2)
</style>
