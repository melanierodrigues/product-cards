<template>
    <div class="product-list-container">
        <product-card v-for="product in productsList" :product="product"/>
    </div>
</template>

<script setup>
import ProductCard from '../../../components/cards/Product.vue'

/* Vue */
import { ref } from 'vue'

const productsList = ref()

const initialRequests = async () => {
    let response = await fetch('https://dummyjson.com/products')
    .then(res => res.json())

    productsList.value = response.products
}

initialRequests()
</script>

<style scoped>
.product-list-container {
    min-width: fit-content;
    height: fit-content;
    display: flex;
    flex-direction: row;
    justify-content: center;
    flex-wrap: wrap;
    gap: 16px;
}
</style>