<template>
    <div class="Cart-wrap">
        <h2>カート内の商品</h2>
        <p v-show="!items.length"><i>カートに商品は入っていません。</i></p>
    
        <ul class="Cart-list">
            <li v-for="product in items" :key="product.id" class="Cart-item">
                <div class="Cart-inner">
                    <div class="Cart-img">
                        <!--商品画像-->
                        <img width="150" height="150" :src="`http://localhost:8000/storage/images/${product.image}.jpeg`" alt="product.name">
                    </div>
                    <div class="Cart-box">
                        <!--商品名-->
                        <p class="Cart-name">{{ product.name }}</p>
                        <p class="Cart-price">¥{{ product.price }}</p>
                    </div>
                    <hr>
                    <div class="Cart-subtotal">
                        <!--小計-->
                        ¥{{ (product.price * product.quantity).toLocaleString() }}
                    </div>
                </div>
            </li>
        </ul>
    </div>
    <hr>
    <p class="Cart-total">合計: ¥{{ total.toLocaleString() }}</p>
    <hr>
    <!--購入画面へ-->
    <button @click="clickOrder" class="btn-order">注文確定</button>

</template>


<script setup lang="ts">
import { useCartStore } from '../stores/Cart';
import { storeToRefs } from 'pinia';

const { items, total } = storeToRefs(useCartStore());

// 注文確定
function clickOrder() {
    alert('注文確定しました。');
}

</script>

<style scoped>

.Cart-wrap {
    background-color: beige;
    padding: 30px;
}

.Cart-item {
    list-style: none;
    margin-left: auto;
    margin-right: auto;
    
}

.Cart-inner {
    background-color: white;
    padding: 20px;
    margin: 10px;
    overflow: auto;
}

.Cart-img {
    float: left;
    margin-right: 20px;
    width: 150px;
    height: 150px;
    background-color: bisque;
}

.Cart-box {
    display: flex;
}

.Cart-name {
    font-size: 20px;
    width: 80%;
    min-width: 100px;
}

.Cart-price {
    font-size: 20px;
    opacity: 0.7;
    width: 10%;
    min-width: 70px;
}

.Cart-subtotal {
    font-size: 30px;
    text-align: right;
}


.Cart-total {
    font-size: 40px;
    text-align: right;
    margin-right: 50px;
}

.btn-order {
    display: flex;
    margin: 0 auto;
    padding: 10px 20px;
}

</style>