<template>
    <div class="card-wrapper" :key="product.id">
        <!-- *********************************************
        *                    Top Info                    *
        ********************************************** -->
        <div class="top-info" :style="{'backgroundImage': `url(${product.images[counter]})`}">
            <div class="info">
                <!-- Rating -->
                <div class="rating">
                    <icon-star />
                    <div>{{ product.rating.toFixed(1) }}</div>
                </div>
                <!-- Discount -->
                <div class="discount">Save {{ Math.round(product.discountPercentage) }}%</div>
            </div>
        </div>
        <!-- *********************************************
        *                   Bottom Info                  *
        ********************************************** -->
        <div class="bottom-info">
            <!-- Category -->
            <div class="category">{{ product.category }}</div>
            <div class="product">
                <!-- Title -->
                <div class="title">{{ product.title }}</div>
                <!-- Brand -->
                <div class="brand">| {{ product.brand }}</div>
            </div>
            <!-- Description -->
            <div class="description">{{ product.description }}</div>
            <div class="price-wrapper">
                <!-- Price -->
                <div class="price">{{ product.price }}€</div>
                <!-- Info when stock is less than 40 units -->
                <div v-if="product.stock < 40" class="units-left">Only a few units left</div>
            </div>
        </div>
    </div>
</template>
  
<script setup>
import iconStar from '../../assets/icons/star.svg'

import { ref, watch } from 'vue';

/* props received
@product { Object }
*/
const props = defineProps({
    product: Object
})

const counter = ref()
const slideImage = () => {
    if (props.product.images.length === 1) return counter.value = 0
    if (counter.value < (props.product.images.length - 1)) {
        counter.value++
    } else {
        counter.value = 0
    }
}

watch(() => counter.value, () => {
    setTimeout(() => { slideImage() }, 5000)
})
slideImage()
</script>
  
<style scoped>
.card-wrapper {
    width: 230px;
    height: fit-content;

    border: 1px #DEDEDE solid;
    border-radius: 16px;

    .top-info {
        max-width: 230px;
        height: 120px;
        flex-shrink: 0;

        transition: all 0.8s cubic-bezier(0.390, 0.575, 0.565, 1.000);
        background-size: cover;
        border-radius: 16px 16px 0px 0px;

        .info {
            width: 100%;
            box-sizing: border-box;
            padding: 8px 10px;
            display: flex;
            flex-direction: row;
            justify-content: space-between;

            .rating {
                display: flex;
                flex-direction: row;
                padding: 4px 8px;
                align-items: center;
                gap: 4px;
                border-radius: 16px;
                background: #FFF;
            }

            .discount {
                display: flex;
                flex-direction: row;
                padding: 4px 8px;
                align-items: center;
                gap: 4px;

                border-radius: 16px;
                background: #77AA43;
                color: #FFF;
                /* font-family: Inter; */
                font-size: 12px;
                font-weight: 700;
            }
        }
    }

    .bottom-info {
        max-width: 230px;
        height: 230px; /* 302px ? */
        flex-shrink: 0;

        background-color: white;
        border-radius: 0px 0px 16px 16px;
        padding: 16px;
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        gap: 8px;


        .category {
            color: #312F2E;
            /* font-family: Proxima Nova; */
            font-size: 12px;
            font-weight: 400;
            line-height: 130%;
        }

        .product {
            display: flex;
            flex-direction: row;
            flex-wrap: wrap;
            gap: 8px;
            row-gap: 0px;

            .title {
                color: #312F2E;
                /* font-family: Proxima Nova; */
                font-size: 18px;
                font-weight: 700;
                line-height: 130%;
                text-align: start;
            }
        }

        .description {
            color: #312F2E;
            /* font-family: Inter; */
            font-size: 12px;
            font-weight: 400;
            line-height: 150%;
            text-align: start;
        }

        .price-wrapper {
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            .price {
                color: #312F2E;
                text-align: center;
                /* font-family: Inter; */
                font-size: 48px;
                font-weight: 700;
            }

            .units-left {
                color: #9FABBC;
                /* font-family: Proxima Nova; */
                font-size: 12px;
                font-weight: 400;
                line-height: 130%;
            }
        }
    }
}
</style>