<template>
    <div class="about">
        <Side :isInList='false' :category='category'></Side>
        <div class="content">
            <p>一名小游戏前端，从事游戏6年多，有开发出过亿的游戏，也有一塌糊涂的产品，混迹于页游、手游、H5游戏间，目前从事H5游戏开发。</p>
            <p>博客采用vue <a href="https://github.com/BUPT-HJM/vue-blog" target="_blank">BUPT-HJM</a>
                进行修改，其实主要是学习vue的过程。网站并没有有用的内容。有正在学习、摸索中的道友可加微信 cooluid 聊聊人生，互相探讨交流</p>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
    import Side from './common/Side.vue'
    export default{
        data() {
            return {
                category: []
            }
        },
        components: {
            Side
        }
    }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
    .about
        padding 10px
        max-width 1000px
        margin 0 auto
        padding-top 85px
        .content
            width: 90%
            margin: 50px 0 50px 140px
            padding 25px 10px 10px 25px
            background-color: #ffffff
            border-radius: 1px
            box-shadow: 1px 1px 1px hsl(0, 0%, 80%)
            line-height: 50px
            font-family Monaco

</style>