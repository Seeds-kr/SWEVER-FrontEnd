<template>
    <div class="no-data" v-if="isLoading">
        <div class="lds-ring">
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
        </div>
    </div>

    <div class="box" v-else>
        <div class="writeBtn">
            <h2>Review</h2>
                <button v-if="this.$store.state.isLoggedIn" @click="goToReviewPostPage">
                    <i class="bi bi-pen"></i> Post Review
                </button>
        </div>
        <div class="search-list-wrapper">
            <ul class="search-list">
                <li v-for="(item, index) in reviews" :key="index">
                    <base-card>
                        <div class="Search-section1">
                            <div class="info">
                                <!-- item.type이 'crawling'일 경우 link로 이동 -->
                                <a v-if="item.type === 'crawling' && item.link" :href="item.link" target="_blank">
                                    <h3>{{ item.title }}</h3>
                                </a>
                                <!-- 'crawling'이 아닐 경우, ReviewDetail.vue로 이동 -->
                                <!-- <router-link v-else :to="'/review/detail/' + item.id"> -->
                                <h3 v-else @click="goToReviewDetail(item.id)">{{ item.title }}</h3>
                                <!-- </router-link> -->
                                <ul v-if="item.type !== 'crawling'">
                                    <li v-html="item.content"></li>
                                </ul>
                            </div>
                            <div class="imgbox">
                                <img v-if="!item.thumbnail || item.thumbnail === 'undefined'"
                                    :src="require('@/components/img/image.png')" alt="company picture" class="default" />
                                <img v-else :src="item.thumbnail" alt="company picture" class="info-img" />
                            </div>
                        </div>
                    </base-card>
                </li>
            </ul>
        </div>
        <div class="right">
            <ReviewPagingView></ReviewPagingView>
        </div>
    </div>
</template>
<script>
import ReviewPagingView from "@/components/UI/ReviewPagingView.vue"
export default {
    components: {
        ReviewPagingView,
    },
    watch: {
        '$route.params.page': function (newPage) {
            this.$store.dispatch('fetchReviews', newPage);
        }
    },
    computed: {
        isLoading() {
            return this.$store.state.isLoading;
        },
        reviews() {
            return this.$store.state.reviews;
        },
    },
    mounted() {
        const page = this.$route.params.page;
        this.$store.dispatch('fetchReviews', page);
    },
    methods: {
        goToReviewPostPage() {
            this.$router.push('/review-post');
        },
        goToReviewDetail(postId) {
            this.$router.push({ name: 'ReviewDetail', params: { id: postId } });
        },
    },
}
</script>

<style scoped>
* {
    font-size: 16px;
}

.box {
    display: flex;
    width: 70rem;
    padding-top: 5rem;
    max-width: 70rem;
    margin: 0 auto;
    justify-content: center;
    flex-direction: column;

}

.right {
    display: flex;
    width: 100%;
    justify-content: flex-end;
    margin-bottom: 5rem;

}

h2 {
    font-size: 2rem;
    font-weight: 700;
}

.writeBtn {
    display: flex;
    justify-content: space-between;
    margin-bottom: 3rem;
    width: 100%;
}

.writeBtn>button {
    border-radius: 15px;
    background-color: #F73859;
    border: none;
    color: #fff;
    padding: 15px;
}

.bi-pen::before {
    content: "\f4c8";
    color: #fff;
}

.search-list-wrapper {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
}

.search-list {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    grid-column-gap: 20px;
    /* 아이템들 사이의 간격 */
    list-style-type: none;
    padding: 0;
    /* 기본 ul 패딩 제거 */
}

.search-list>li {
    flex: 0 0 calc(50% - 10px);
    /* 2열 레이아웃 설정, 10px은 gap의 반 값으로 gap을 고려한 너비 설정 */
}



.Search-section1 {
    display: flex;
    justify-content: space-between;
    align-items: center;
    /* margin-bottom: 20px; */
}

.imgbox {
    width: 80px;
    height: 80px;
}

.imgbox>img.default {
    width: 80px;
    height: 80px;
    filter: invert(44%) sepia(61%) saturate(7445%) hue-rotate(333deg) brightness(120%) contrast(71%);
}

.info-img {
    width: 80px;
    height: 80px;
    border-radius: 20px;
    display: block;
    background-color: #f73859;
}

.info {
    display: flex;
    max-width: 36rem;
    flex-direction: column;
    align-items: flex-start;
    margin-right: 30px;
}

.info>ul {
    list-style-type: none;
    padding: 0;
    margin-top: 10px;
}

.info>ul>li {
    float: left;
    margin-left: 10px;
}

.info>ul>li:first-child {
    margin-left: 0px;
}

.info>a {
    text-decoration: none;
    color: black;
}

.info h3 {
    font-size: 20px;
    font-weight: 700;
    margin: 0;
}

.bi {
    color: #f73859;
}

.description {
    padding: 20px;
    width: auto;
    height: 3rem;
    border-radius: 10px;
    background: #e3e3e3;
    display: inline-block;
}

.no-data {
    margin: 0 auto;
    padding: 10rem;
    margin-top: 5rem;
    text-align: center;
    font-size: 1.2rem;
    color: #ADADAD;
    display: flex;
    justify-content: center;
    align-items: center;
}

/* loading css */
.lds-ring {
    display: inline-block;
    position: relative;
    width: 80px;
    height: 80px;
}

.lds-ring div {
    box-sizing: border-box;
    display: block;
    position: absolute;
    width: 64px;
    height: 64px;
    margin: 8px;
    border: 8px solid #F73859;
    border-radius: 50%;
    animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;
    border-color: #F73859 transparent transparent transparent;
}

.lds-ring div:nth-child(1) {
    animation-delay: -0.45s;
}

.lds-ring div:nth-child(2) {
    animation-delay: -0.3s;
}

.lds-ring div:nth-child(3) {
    animation-delay: -0.15s;
}

@keyframes lds-ring {
    0% {
        transform: rotate(0deg);
    }

    100% {
        transform: rotate(360deg);
    }
}
</style>
