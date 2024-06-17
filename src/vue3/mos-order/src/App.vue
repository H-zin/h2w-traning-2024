<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import router from './router';
import { useCartStore } from './stores/Cart';



/**
 * Function that handles the click event for the sign-in button.
 * It logs a message to the console and displays an alert.
 */
function clickSignIn() {
  console.log('clickSignIn')
  alert('clickSignIn')
}

/**
 * Function that handles the click event for the login button.
 * It logs a message to the console and displays an alert.
 */
function clickLogin() {
  console.log('clickLogin')
  alert('clickLogin')
}

function clickCart() {
  router.push('/cart')
}

// カートに商品情報を取得
const cartStore = useCartStore();
const { items } = cartStore;

// カートボタンの上にカートの商品数を表示
function countCart() {
  return items.length;
}





</script>

<template>
  <header>
    <div>
      <h1>ハンバーガーのネット注文</h1>
      <button class="btn btn-primary" @click="clickCart">カート</button>
      <button class="btn btn-primary" @click="clickSignIn">会員登録</button>
      <button class="btn btn-primary" @click="clickLogin">ログイン</button>
    </div>
    <div class="wrapper">
      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
        <RouterLink to="/top">TOP</RouterLink>
        <RouterLink to="/category">カテゴリー</RouterLink>
        <RouterLink to="/product">商品一覧</RouterLink>
        <RouterLink to="/picks-list">お勧め商品一覧</RouterLink>
        <RouterLink to="/cart">カート</RouterLink>
        <RouterLink to="/order-list">注文画面</RouterLink>
      </nav>
    </div>
    <button v-if="items.length > 0" class="onCart" @click="clickCart">カート：{{ countCart() }}</button>
    
  </header>
  


  <RouterView />
  <hr />
  <footer>
    <div>copylight by h2works.co.jp</div>
  </footer> 
</template>

<style scoped>
/*画面の右下にカートボタンを設置*/
.onCart {
  position: fixed;
  bottom: 10%;
  right: 10%;
  background-color: #f00;
  color: #fff;
  border: none;
  padding: 40px 20px;
  border-radius: 50px;
  cursor: pointer;
}



header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
