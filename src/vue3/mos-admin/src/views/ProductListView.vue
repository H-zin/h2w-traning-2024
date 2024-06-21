<template>
    <div>
        <h1>ハンバーガー注文 商品一覧</h1>

        <select v-model="stores">
            <option v-for="store in stores" :key="store.id" :value="store.id">
                <a v-bind:href="`/product-store-list/${store.id}`">{{ store.name }}</a>
            </option>
        </select>


        <div>
        <ul>
            <li v-for="store in stores" :key="store.id">
                {{ store.name }} {{ store.id }}
                <RouterLink :to="{ name: 'product-store-list', params: { id: store.id  } }">{{ store.name }}</RouterLink>                
            </li>
        </ul>
        </div>


        <button class="btn btn-success" @click="onadd">新規作成</button>

        <table class="table">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>カテゴリ名</th>
                    <th>商品名</th>
                    <th>商品説明</th>
                    <th>写真</th>
                    <th>価格</th>
                    <th>表示</th>
                    <th>有効</th>
                    <th>操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="product in products" :key="product.id">
                    <td>{{ product.id }}</td>
                    <td>{{ product.slug }}</td>
                    <td>{{ product.name }}</td>
                    <td>{{ product.description }}</td>
                    <td><img width="60" height="60" :src="`http://localhost:8000/storage/images/${product.image}.jpeg`" alt="product.name"></td>
                    <td>{{ product.price + '円' }}</td>
                    <td class="checkbox"><input type="checkbox"></td>
                    <td class="checkbox"><input type="checkbox"></td>
                    <td>
                        <button class="btn btn-primary" @click="onedit(product)">編集</button>        
                        <button class="btn btn-danger" @click="ondelete(product)">削除</button>        
                        <button class="btn btn-secondary" @click="onUp(product)">上へ</button>        
                        <button class="btn btn-secondary" @click="onDown(product)">下へ</button>        
                    </td>
                </tr>
            </tbody>
        </table>

        <button class="btn btn-primary" @click="onload">一覧を取得</button>   
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { RouterLink, useRouter } from 'vue-router';
import axios from 'axios';

const router = useRouter();


const products = ref([
    { id: 1, slug: 'slug 1', name: 'Category 1' , description: 'Description 1', image: 'Image 1', sortid: 1, price: 'Price 1', display: false ,created_at: 'created_at 1', updated_at: 'updated_at 1',is_delete: false},
]);

const stores = ref([
    { id: 1, name: 'Store 1' , address: '' ,created_at: 'created_at 1', updated_at: 'updated_at 1',delete_at: null, products: [
        { id: 1, store_id: 1, product_id: 1, stock: 1,created_at: 'created_at 1', updated_at: 'updated_at 1',delete_at: null, product: {
             id: 1, slug: 'slug 1', name: 'Category 1' , description: 'Description 1', image: 'Image 1', price: 'Price 1', sortid: 1, display: false ,created_at: 'created_at 1', updated_at: 'updated_at 1',is_delete: false},
    }]},
]);

async function onload() {
    const url = 'http://localhost:8000/api/products';
    const response = await axios.get(url);
    products.value = response.data.data.sort((a, b) => a.sortid - b.sortid);
}
onMounted(onload);


async function onload2() {
    const url = `http://localhost:8000/api/stores`;
    console.log( url )
    const response = await axios.get(url);
    stores.value = response.data.data; 
}
onMounted(onload2) ;


// 新規作成ボタン
function onadd() {
    console.log('onadd');
    router.push({ name: 'product-item', params: { id: 0 } });
}

// 編集ボタン
function onedit(item) {
    console.log('onedit ' + item.name);
    // ルーティング
    router.push({ name: 'product-item', params: { id: item.id } });
}

// 削除ボタン
function ondelete(item) {
    console.log('ondelete' + item.name);
    // 削除確認
    if (confirm('削除しますか？')) {
        // 削除処理
        deleteProduct(item);
    }
};

// 削除処理 DBから削除
async function deleteProduct(item) {
    try {
        const url = `http://localhost:8000/api/products/${item.id}`;
        await axios.delete(url);
        products.value = products.value.filter(product => product.id !== item.id);
    } catch (error) {
        console.error(error);
    }
};

// 上へボタン 上の行と選択した行のsortidを交換する
// （上のsortidを選択した行にいれる処理＋選択した行のsortidを上の行の要素に入れる処理）アップデート二回
async function onUp(item) {
    console.log('onUp' + item.name);
    const currentIndex = products.value.findIndex(product => product.id === item.id);
    if (currentIndex > 0) {
        const tempSortid = products.value[currentIndex - 1].sortid;
        products.value[currentIndex - 1].sortid = item.sortid;
        item.sortid = tempSortid;
        onupdate(item);
        await onupdate(products.value[currentIndex - 1]);
        console.log(item.sortid);
        await onload();
    }
};
// 下へボタン
async function onDown(item) {
    console.log('onDown' + item.name);
    const currentIndex = products.value.findIndex(product => product.id === item.id);
    if (currentIndex < products.value.length - 1) {
        const tempSortid = products.value[currentIndex + 1].sortid;
        products.value[currentIndex + 1].sortid = item.sortid;
        item.sortid = tempSortid;
        onupdate(item);
        await onupdate(products.value[currentIndex + 1]);
        console.log(item.sortid);
        await onload();
    }
};

// 更新処理
async function onupdate(item) {
    console.log('onupdate');
    const url = `http://localhost:8000/api/products/${item.id}`;
    try {
        await axios.put(url, item);
        router.push({ name: 'product-list' });
    } catch (error) {
        console.error(error);
    }
}


// checkbox


</script>

<style>
/* Add your custom styles here */

h1 {
    color: white;
    background-color: green;
    padding: 20px;
    font-size: 1.5em;
}

th {
    text-align: left;
}

.btn {
    margin-right: 5px;
    margin-left: 5px;
}
.btn-danger {
    margin-right: 15px;
}
.btn-success {
    margin-right: 10px;
    position: relative;
    left: 57%;
}

select {
    width: 150px;
    height: 30px;
    margin: 15px;
    text-align: center;
    position: relative;
    left: 8%;
}

.checkbox {
    text-align: center;
}

</style>