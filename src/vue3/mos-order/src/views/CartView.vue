<template>
    <div class="Cart-wrap">
        <h2>カート内の商品</h2>
        <p v-show="!items.length"><i>カートに商品は入っていません。</i></p>
    
        <ul class="Cart-list">
            <li v-for="product in items" :key="product.id" class="Cart-item">
                <div class="Cart-inner">
                    <div class="Cart-img">
                        <!--商品画像-->
                        <img :src="product.image" alt="product.name">
                    </div>
                    <div class="Cart-box">
                        <!--商品名-->
                        <p class="Cart-name">{{ product.name }}</p>
                        <p class="Cart-price">¥{{ product.price }}</p>
                        <div class="Cart-quantity">
                            <!--増加-->
                            <button @click="incrementQuantity(product); updateQuantity(items)" class="btn-increment">+</button>
                            <!--数量-->
                            <span class="Cart-quantify-number">{{ product.quantity }}</span> 
                            <!--減少-->
                            <button @click="decrementQuantity(product); updateQuantity(items)" class="btn-increment">-</button>
                            個
                        </div>
                    </div>
                    <hr>
                    <div class="Cart-subtotal">
                        <!--小計-->
                        ¥{{ (product.price * product.quantity).toLocaleString() }}
                    </div>
                    <!--削除ボタン-->
                    <button @click="removeCart(product.id)" class="Cart-delete">削除</button>
                </div>
            </li>
        </ul>
    </div>
    <hr>
    <p class="Cart-total">合計: ¥{{ total.toLocaleString() }}</p>
    <hr>
    <!--購入画面へ-->
    <button @click="clickOrder" class="btn-order">購入手続きへ</button>

</template>

<script setup lang="ts">
import { useCartStore } from '../stores/Cart';
import { storeToRefs } from 'pinia';
import { useRouter } from 'vue-router';

const router = useRouter();
const cartStore = useCartStore();

const { items, total } = storeToRefs(useCartStore());

function removeCart(id) {
    if (confirm('削除しますか？')) {
        cartStore.removeCart(id);
    }
}

function updateQuantity(items) {
    console.log(items);
    cartStore.updateQuantity(items);
}

function incrementQuantity(items) {
    console.log('increment ' + items.name);
    cartStore.incrementQuantity(items);
}

function decrementQuantity(items) {
    console.log('decrement ' + items.name);
    cartStore.decrementQuantity(items);
}

// 購入画面へ もしカートに商品がない場合は無効化
const clickOrder = () => {
    if (items.value.length === 0) {
        console.log('カートに商品がありません');
        alert('カートに商品がありません');
    } else {
        console.log('購入手続きへ');
        router.push('/order-list');
    }
};




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

.Cart-quantity {
    font-size: 20px;
    width: 15%;
    min-width: 100px;
    text-align: right;
}

.Cart-quantify-number {
    font-size: 20px;
    margin: 0 5px;
}

.Cart-subtotal {
    font-size: 30px;
    text-align: right;
}

.Cart-delete {
    float: right;
    margin-top: 10px;
}

.btn-increment {
    border-radius: 10%;
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
