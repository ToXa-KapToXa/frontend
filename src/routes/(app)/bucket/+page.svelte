<script>
    import { onMount } from "svelte";
    import {browser} from '$app/environment';
    export let products = [];
    export let sum = 0;
    let result;
    export let user_session;
    if (browser){
        console.log(localStorage.getItem('token'));
        user_session = localStorage.getItem('token');
    }
    let user_id;
    // import user_id from "../+layout.svelte";
    onMount(async function () {
        const isAuth = await fetch("https://gg1-back.onrender.com/api/check/" + user_session);
        const dataAuth = await isAuth.json();
        console.log(dataAuth);
        user_id = dataAuth["data"];
        
        const response = await fetch("https://gg1-back.onrender.com/api/get_bucket/" + user_id);
        const data = await response.json();
        console.log(data);
        products = data["data"]["bucket"];
        console.log(products);

        for (var key in products) {
            sum = sum + parseInt(products[key]["price"]);
        }
    });

    async function delete_bucket (product_id) {
        console.log(product_id);
        const res = await fetch('https://gg1-back.onrender.com/api/delete_bucket/' + user_id + '/' + product_id, {
            method: 'DELETE',
            headers: {
                'Content-Type': 'application/json'
            }
        })

        const json = await res.json()
        result = json['response_code']
        if (result == 200) {
            window.location.href = '/bucket';
        }
        }
</script>

<main>
<!-- СЕКЦИЯ -->
<div class="section">
    <!-- БЛОК -->
    <div class="container">
        <!-- РЯД -->
        <div class="row">
            <h2>Ваша корзина</h2>
            {#each products as product}
            <div>
                <div class="product-widget">
                    <div class="product-img">
                        <img src={product.namefile} alt="">
                    </div>
                    <div class="product-body">
                        <h3 class="product-name"><a href="/">{product.name}</a></h3>
                        <h4 class="product-price">{product.price}₽</h4>
                    </div>
                    <div>
                        <button class="add-to-cart-btn plus" on:click={delete_bucket(product.product_id)}>Удалить</button>
                    </div>
                </div>
            </div>
            {/each}

            <h3 id="itogoo">Итого: {sum}₽</h3>

        </div>
        <!-- РЯД -->
    </div>
    <!-- БЛОК -->
</div>
<!-- СЕКЦИЯ -->
</main>

<style>

</style>