<template>
    <div class="container">
        <h1 class="text-center">Categories Item View: {{ id }}</h1>
        <div class="row">
            <div class="col-md-6">
                <div class="mb-3">
                    <label for="id" class="form-label">ID:</label>
                    {{ item.id }}
                </div>
                <div class="mb-3">
                    <label for="slug" class="form-label">Slug:</label>
                    <input type="text" id="slug" v-model="item.slug" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="title" class="form-label">Title:</label>
                    <input type="text" id="title" v-model="item.title" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="description" class="form-label">Description:</label>
                    <textarea id="description" v-model="item.description" class="form-control"></textarea>
                </div>
                <div class="mb-3">
                    <label for="image" class="form-label">Image:</label>
                    <input type="text" id="image" v-model="item.image" class="form-control">
                </div>
            </div>
            <div class="col-md-6">
                <div class="mb-3">
                    <label for="sortid" class="form-label">Sort ID:</label>
                    <input type="number" id="sortid" v-model="item.sortid" class="form-control">
                </div>
                <div class="mb-3">
                    <label for="display" class="form-label">Display:</label>
                    <input type="checkbox" id="display" v-model="item.display" class="form-check-input">
                </div>
                <div class="mb-3" v-if="item.id !=0">
                    <label for="created_at" class="form-label">Created At:</label>
                    <div>{{ formatDateTime(item.created_at.toString()) }}</div>
                </div>
                <div class="mb-3" v-if="item.id !=0">
                    <label for="updated_at" class="form-label">Updated At:</label>
                    <div>{{ formatDateTime(item.updated_at.toString()) }}</div>
                </div>
            </div>
        </div>
        <div class="text-center">
            <button @click="onupdate" class="btn btn-primary">更新</button>
            <button @click="oncancel" class="btn btn-secondary">キャンセル</button>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';
import { useCategoryStore } from '@/stores/category';

interface Category {
    id: number;
    slug: string;
    title: string;
    description: string;
    image: string;
    sortid: number;
    price: number;
    display: boolean;
    created_at: Date;
    updated_at: Date;
    deleted_at: Date | null;
}

const categoryStore = useCategoryStore();

const router = useRouter();
const id = ref(router.currentRoute.value.params.id);

// sortidの現在の最大値を取得
async function getMaxSortid() {
    const maxSortid = categoryStore.categories.values.reduce((max, category) => {
        return category.sortid > max ? category.sortid : max;
    }, 0);
    return maxSortid;
}

// 初期値を設定 sortidは現在の最大値+1
const item = ref<Category>({ 
    id: 0, 
    slug: '', 
    title: '', 
    description: '', 
    image: '', 
    sortid: 0, 
    price: 0,
    display: false, 
    created_at: new Date(), 
    updated_at: new Date(), 
    deleted_at: null,
});
getMaxSortid().then(maxSortid => {
    item.value.sortid = maxSortid + 1;
});

async function onload() {
    console.log('onload');
    var url = "http://localhost:8000/api/categories/" + id.value;
    const response = await axios.get(url);
    item.value = response.data.data;
    // 日時をフォーマット変換しておく
    // item.value.created_at = formatDateTime(item.value.created_at.toString());
    // item.value.updated_at = formatDateTime(item.value.updated_at.toString();
}

onMounted(onload);

// バリデーションチェック
async function validate() {
    // slugが空の場合はエラー
    if (item.value.slug === '') {
        alert('Slugを入力してください');
        return false;
    }
    // slugが重複している場合はエラー
    const found = categoryStore.categories.values.find((category) => category.slug === item.value.slug);
    if (found && found.id !== item.value.id) {
        alert('Slugが重複しています');
        return false;
    }
    // タイトルが空の場合はエラー
    if (item.value.title === '') {
        alert('タイトルを入力してください');
        return false;
    }
    // sortidが0以下の場合はエラー
    if (item.value.sortid <= 0) {
        alert('Sort IDは1以上を入力してください');
        return false;
    }
    return true;
}


/**
 *Updates the category item.
 * @async
 * @function onupdate
 * @returns {Promise<void>}
 */

async function onupdate() {
    console.log('onupdate');
    // バリデーションチェック
    if (!await validate()) {
        return;
    }
    var url = 'http://localhost:8000/api/categories/' + id.value;
    const response = await axios.put(url, item.value)
    router.push({ name: 'categories-list' });
}

/**
 * Handles the cancel action.
 * Navigates back to the 'category' route.
 */
function oncancel() {
    console.log('oncancel');
    router.push({ name: 'categories-list' });
}



/*
// idが0の場合は、初期値を設定 sortidは現在の最大値+1
if (id.value == 0) {
    item.value = { 
        id: 0, 
        slug: '', 
        title: '', 
        description: 'aaa', 
        image: '', 
        sortid: 0, 
        price: 0,
        display: false, 
        created_at: new Date(),
        updated_at: new Date(),
        deleted_at: null,
    };
    getMaxSortid().then(maxSortid => {
        item.value.sortid = maxSortid + 1;
    });
}
*/






/**
 * Formats a given datetime string into the desired format.
 * @param {string} datetimeStr - The datetime string to be formatted.
 * @returns {string} The formatted datetime string in the format "YYYY-MM-DDTHH:MM".
 */
function formatDateTime(datetimeStr: string) {
    // Dateオブジェクトを作成
    const date = new Date(datetimeStr);
    
    // 年、月、日、時、分を取得
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 目的の形式にフォーマット
    return `${year}年${month}月${day}日${hours}時${minutes}分`;
}

/**
 * Converts a datetime string to ISO 8601 format.
 * @param {string} datetimeStr - The datetime string to convert.
 * @returns {string} The datetime string in ISO 8601 format.
 */
function convertToISO(datetimeStr: string) {
    // 入力形式を分割して、各部分を取得
    const [datePart, timePart] = datetimeStr.split('T');
    const [year, month, day] = datePart.split('-');
    const [hours, minutes] = timePart.split(':');
    
    // 新しい Date オブジェクトを作成
    const date = new Date(Date.UTC(year, month - 1, day, hours, minutes));
    
    // ISO 8601 形式の文字列に変換
    const isoString = date.toISOString();
    
    // 必要な部分を抽出して返す
    return isoString.replace('.000Z', '.000000Z');
}





</script>

<style scoped>
/* Your styles here 
*/
</style>