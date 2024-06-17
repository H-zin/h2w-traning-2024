<template>
    <div>
        <h1>注文方法を選択</h1>
    </div>
    <div class="order-button">
        <button class="btn btn-primary" @click="onOrder">お持ち帰り</button>
        <button class="btn btn-primary" @click="onOrder">お届け</button>
        <!--
        <router-link class="btn btn-primary" to="/category" >お持ち帰り</router-link>
        <router-link class="btn btn-primary" to="/category">お届け</router-link>
        -->
    </div>
    <br />

    <Carousel :itemsToShow="carouselItemsToShow" :wrapAround="true" :autoplay="5000" :transition="1000" :pauseAutoplayOnHover="true">
        <slide v-for="image in images" :key="image.id" class="slide-list">
            <a href="/product-list/special">
                <img width="500" height="300" src="http://localhost:8000/storage/images/m001.jpeg"  alt="ハンバーガー"/>
            </a>
        </slide>
        <template #addons>
            <Pagination />
            <Navigation />
        </template>
    </Carousel>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount} from 'vue';
import { defineComponent } from 'vue'
import { Carousel, Slide, Navigation, Pagination } from 'vue3-carousel';
import 'vue3-carousel/dist/carousel.css';
import { useRouter } from 'vue-router';

const router = useRouter();


const images = ref([
    { id: 1, src: '../assets/top-hamburger.jpg', alt: 'ハンバーガー' },
    { id: 2, src: '../assets/top-hamburger.jpg', alt: 'ハンバーガー' },
    { id: 3, src: '../assets/top-hamburger.jpg', alt: 'ハンバーガー' },
    { id: 4, src: '../assets/top-hamburger.jpg', alt: 'ハンバーガー' },
    { id: 5, src: '../assets/top-hamburger.jpg', alt: 'ハンバーガー' },
]);

function onOrder() {
    console.log('onOrder');
    router.push('/category');
}


const carouselItemsToShow = ref(1.8); // カルーセルの初期表示数

const adjustCarouselSize = () => {
    // ウィンドウの幅を取得して、適切なカルーセルの表示数を計算する
    const windowWidth = window.innerWidth;
    if (windowWidth < 768) {
        carouselItemsToShow.value = 1; // スマートフォン用の表示数
    } else if (windowWidth < 1024) {
        carouselItemsToShow.value = 1.5; // タブレット用の表示数
    } else {
        carouselItemsToShow.value = 1.8; // デスクトップ用の表示数
    }
};

onMounted(() => {
    // ウィンドウのリサイズイベントを監視して、カルーセルのサイズを調整する
    window.addEventListener("resize", adjustCarouselSize);
    // 初期化時にカルーセルのサイズを調整する
    adjustCarouselSize();
});

onBeforeUnmount(() => {
    // コンポーネントが破棄される前にイベントリスナーを削除する
    window.removeEventListener("resize", adjustCarouselSize);
});



</script>

<style scoped>
h1 {
    color: blue;
    text-align: center;
}

.order-button {
    display: flex;
    justify-content: center;
    gap: 20px;
    margin-top: 20px;
    
    /* ボタンのサイズを変更 */
    .btn {
        width: 250px;
        height: 100px;
        font-size: 1.6em;
    }
}

.slide-list {
}

.carousel__slide {
    background-color: red;

    &:nth-child(2n) {
    background-color: green;
  }
}
</style>


