<template>
    <div class="list">
        <Side :isInList='true'></Side>
        <div class="list__loading" v-if="isLoading">
            <Loading :loadingMsg='loadingMsg'></Loading>
        </div>
        <ul class="list__article">
            <li class="list__article__filterMsg" v-if="(selectTags.length !== 0)">
                筛选
                <span>{{filterMsg}}</span>
                分类
            </li>
            <template v-if="posts.length!==0 && isLoading == false">
                <li v-for="(article, index) in posts" class="list__article__item">
                    <h1 class="list__article__item__title">
                        <router-link :to="'article/'+article.id">{{ article.title }}</router-link>
                    </h1>
                    <div class="list__article__item__info">
                        <p class="list__article__item__time">{{article.createTime}}</p>
                        <div class="list__article__item__abstract markdown-body"
                             v-html="compiledMarkdown(article.abstract)"></div>
                        <span class="list__article__tag" v-for="tag in article.tags"> {{tag.name}}</span>
                        <div class="continue-reading">
                            <router-link :to="'/article/'+article.id" class="continue-reading">阅读全文 »</router-link>
                        </div>
                    </div>
                </li>
                <Pagination :curPage='curPage' :allPage='allPage' @changePage='changePage'></Pagination>
            </template>
            <div v-if="posts.length==0 && isLoading==false" class="msg-box">
                <p>暂时没有相关文章</p>
            </div>
        </ul>
    </div>
</template>

<script>
    import Pagination from 'publicComponents/Pagination.vue'
    import Loading from 'publicComponents/Loading.vue'
    import Side from './common/Side.vue'
    import articleApi from 'api/article.js'
    import marked from 'lib/marked.js'

    import {
        mapGetters,
        mapActions
    } from 'vuex'

    export default {
        name: 'list',
        computed: {
            ...mapGetters([
                'posts',
                'tags',
                'curPage',
                'allPage',
                'selectTags',
                'searchTags',
                'currentPost'
            ]),
            filterMsg() {
                let msg = ''
                this.selectTags.forEach((item) => {
                    msg += item.name + '、'
                })
                return msg.replace(/、$/, '')
            }
        },
        components: {
            Pagination,
            Side,
            Loading
        },
        data() {
            return {
                isLoading: false,
                loadingMsg: '加载中...'
            }
        },
        created() {
        },
        beforeMount() {
            // 用来判断是否有数据，有数据就不再请求了
            if (this.currentPost.id == '') {
                // 这句话说明不是从文章详细页过来的
                return;
            }
            this.isLoading = true;
            this.getAllPosts({page: this.$store.state.route.params.page}).then(() => {
                this.isLoading = false;
            })
        },
        preFetch(store) {
            store.dispatch('getAllTags')
            return store.dispatch('getAllPosts', {page: store.state.route.params.page}).then(() => {
            })
        },
        methods: {
            ...mapActions([
                'getAllPosts',
                'getAllTags'
            ]),
            compiledMarkdown(value) {
                return marked(value)
            },
            changePage(cur) {
                this.isLoading = true;
                this.$router.push('/page/' + cur)
                this.getAllPosts({tag: this.searchTags, page: cur}).then(() => {
                    this.isLoading = false;
                })
            }
        },
        watch: {
            selectTags() {
                this.isLoading = true;
                this.getAllPosts({
                    tag: this.searchTags
                }).then(() => {
                    this.isLoading = false;
                })
            }
        }
    }
</script>

<style lang="stylus" scoped>
    @import '../assets/stylus/_settings.styl'
    .list
        padding 10px
        max-width 1000px
        margin 0 auto
        padding-top 85px
        &__article
            list-style none
            margin-left 150px
        &__article__item
            margin 0 auto
            padding 15px 10px 10px 15px
            margin-bottom 15px
            background-color: #ffffff
            border-radius: 1px
            box-shadow: 1px 1px 1px hsl(0, 0%, 80%)

        &__article__item__title
            font-size 20px
            word-break break-all
            a
                text-decoration none
                color black
        &__article__item__time
            color #7f8c8d
            font-weight 400
            margin-bottom 10px
            margin-top 2px
        &__article__item__abstract
            margin-bottom 5px
        .continue-reading
            color: #175199
            text-align: right
            text-decoration: none
        &__article__tag
            color: #ffffff
            margin: 5px
            background-color: #0366d6
            font-size: 12px
            border-radius: 2px
            padding: 3px 3px 3px 3px
            box-shadow: 2px 2px 2px #ccc
        &__article__filterMsg
            font-size 20px
            text-align center
            span
                color $blue
        &__loading
            position fixed
            top 50%
            left 50%
            width 300px
            height 200px
            margin-left -(@width/ 2) + 125
            margin-top -(@height/ 2) + 60
        .msg-box
            position fixed
            top 50%
            left 50%
            width 200px
            height 200px
            margin-left -(@width/ 2) + 125
            margin-top -(@height/ 2) + 60
            text-align center
            color #bfbfbf

    @media screen and (max-width: 850px)
        .list
            position relative
            padding-top 80px
            &__article__item
                margin-bottom 10px
            &__article
                margin-left 0
            &__article__filterMsg
                font-size 18px
            .msg-box
                position absolute
                top 250px
                left 50%
                width 300px
                margin-left -(@width/ 2)
            &__loading
                position absolute
                top 250px
                left 50%
                width 300px
                margin-left -(@width/ 2)

</style>
