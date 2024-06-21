<template>
    <div>
        <h1>ハンバーガー注文 商品一覧</h1>
        <!--選択された店舗名を表示---->
        <h2>{{ store.name }}</h2>

        <select v-model="store.id" @change="onStoreChange">
            <option v-for="store in stores" :key="store.id" :value="store.id">
                {{ store.name }}                
            </option>
        </select>

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
                <tr v-for="productstore in productstores" :key="productstore.id">
                    <td>{{ productstore.id }}</td>
                    <td>{{ productstore.slug }}</td>
                    <td>{{ productstore.name }}</td>
                    <td>{{ productstore.description }}</td>
                    <td><img width="60" height="60" :src="`http://localhost:8000/storage/images/${productstore.image}.jpeg`" alt="product.name"></td>
                    <td>{{ productstore.price + '円' }}</td>
                    <td class="checkbox"><input type="checkbox"></td>
                    <td class="checkbox"><input type="checkbox"></td>
                    <td>
                        <button class="btn btn-primary" @click="onedit(productstore)">編集</button>        
                        <button class="btn btn-danger" @click="ondelete(productstore)">削除</button>        
                        <button class="btn btn-secondary" @click="onUp(productstore)">上へ</button>        
                        <button class="btn btn-secondary" @click="onDown(productstore)">下へ</button>        
                    </td>
                </tr>
            </tbody>
        </table>

        <button class="btn btn-primary" @click="onload">一覧を取得</button>   
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';
import { log } from 'console';

const router = useRouter();

interface ProductStore {
    id: number;
    slug: string;
    name: string;
    description: string;
    image: string;
    price: string;
    sortid: number;
    display: number;
    created_at: string;
    updated_at: string;
    delete_at: string;
}

interface Store {
    id: number;
    name: string;
    address: string;
}


const productstores = ref([
    { id: 1, slug: 'slug 1', name: 'Category 1' , description: 'Description 1', image: 'Image 1',  price: 'Price 1', sortid: 1, display: 1 ,created_at: 'created_at 1', updated_at: 'updated_at 1',delete_at: null},
]);

const stores = ref<Store[]>([
    { id: 1, name: 'Store 1', address: '' },
]);

const store = ref<Store>({
    id: 0,
    name: '',
    address: '',
});

async function onload() {
    const id = ref(router.currentRoute.value.params.id);
    const url = `http://localhost:8000/api/stores/${id.value}/products`;
    const response = await axios.get(url);
    store.value = response.data.data;

    const items = response.data.data.products.sort((a, b) => a.sortid - b.sortid);
    console.log(items);
    
    productstores.value = [];
    for (const item of items) {
        //console.log(item.product);
        productstores.value.push(item.product);
    }

}
onMounted(onload);

async function onload2() {
    const url = `http://localhost:8000/api/stores`;
    const response = await axios.get(url);
    stores.value = response.data.data;
}
onMounted(onload2);


async function onStoreChange() {
    console.log(`${store.value.name}から${stores.value.find(s => s.id === store.value.id)?.name}へ変更しました`);
    await router.push({ name: 'product-store-list', params: { id: store.value.id } });
    onload();
}





// 新規作成ボタン
function onadd() {
    console.log('onadd');
    router.push({ name: 'product-store-list-item', params: { id: 0 } });
}

// 編集ボタン
function onedit(item) {
    console.log('onedit ' + item.name);
    // ルーティング
    router.push({ name: 'product-store-list-item', params: { id: item.store_id } });
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
        const url = `http://localhost:8000/api/stores/${item.id}/products/${item.id}`;
        await axios.delete(url);
        productstores.value = productstores.value.filter(productstore => productstore.id !== item.id);
    } catch (error) {
        console.error(error);
    }
};

// 上へボタン 上の行と選択した行のsortidを交換する
// （上のsortidを選択した行にいれる処理＋選択した行のsortidを上の行の要素に入れる処理）アップデート二回
async function onUp(item) {
    console.log('onUp' + item.name);
    const currentIndex = productstores.value.findIndex(productstore => productstore.id === item.id);
    if (currentIndex > 0) {
        const tempSortid = productstores.value[currentIndex - 1].sortid;
        productstores.value[currentIndex - 1].sortid = item.sortid;
        item.sortid = tempSortid;
        onupdate(item);
        await onupdate(productstores.value[currentIndex - 1]);
        console.log(item.sortid);
        await onload();
    }
};
// 下へボタン
async function onDown(item) {
    console.log('onDown' + item.name);
    const currentIndex = productstores.value.findIndex(productstore => productstore.id === item.id);
    if (currentIndex < productstores.value.length - 1) {
        const tempSortid = productstores.value[currentIndex + 1].sortid;
        productstores.value[currentIndex + 1].sortid = item.sortid;
        item.sortid = tempSortid;
        onupdate(item);
        await onupdate(productstores.value[currentIndex + 1]);
        console.log(item.sortid);
        await onload();
    }
};

// 更新処理
async function onupdate(item) {
    console.log('onupdate');
    const url = `http://localhost:8000/api/stores/${item.id}/product/${item.id}`;
    try {
        await axios.put(url, item);
        router.push({ name: 'product-store-list-item' });
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