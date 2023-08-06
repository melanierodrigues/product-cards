<template>
    <!-- *********************************************
    *                     Filters                    *
    ********************************************** -->
    <div class="checkbox-container">
        <div style="max-width: calc(100% - 14px);" @click="[isOpen = !isOpen, isVisible = false]">Filters</div>
        <transition
        name="show-details"
        @after-enter="isVisible = true">
            <div v-if="isOpen" class="checkbox-group">
                <!-- *********************************************
                *                  Price Filter                  *
                ********************************************** -->
                <div class="price-container">
                    <input id="minValue" name="minValue" type="number" min="0" v-model="minValue" @blur="validationInputs()"/>
                    <span> - </span>
                    <input id="maxValue" name="maxValue" type="number" min="0" v-model="maxValue" @blur="validationInputs()"/>
                    <span> €</span>
                    <div style="font-size: 14px; margin-top: 8px;" @click="[minValue = null, maxValue = null, validationInputs()]">Clear price range</div>
                </div>
                <!-- *********************************************
                *                  Brands Filter                 *
                ********************************************** -->
                <div>
                    <checkbox
                    v-for="checkbox in checkboxGroupParams"
                    :id="checkbox.id"
                    :text="checkbox.text"
                    :selected="checkbox.selected"
                    @clicked="[checkboxClicked($event), checkbox.selected = !checkbox.selected]"/>
                </div>
            </div>
        </transition>
    </div>

    <!-- *********************************************
    *                  Product List                  *
    ********************************************** -->
    <div v-if="!!productsList" class="product-list-container">
        <product-card v-for="product in filteredProductsList ? filteredProductsList : productsList" :product="product"/>
    </div>
</template>

<script setup>
import Checkbox from '../../../components/checkbox/Base.vue'
import ProductCard from '../../../components/cards/Product.vue'

/* Vue */
import { ref, watch } from 'vue'

const productsList = ref()
const filteredProductsList = ref()

/*************************************************
*              Brand Filter Logic                *
*************************************************/
const brandList = ref()

const isOpen = ref(false)
const isVisible = ref(false)

const checkboxSelected = ref([])
const checkboxGroupParams = ref([])

const checkboxClicked = event => {
    filteredProductsList.value = productsList.value

    /* Remove */
    if (checkboxSelected.value.find(el => el === event) && !productsList.value.filter(el => el.brand === event).selected) {
        const index = checkboxSelected.value.indexOf(event)
        checkboxSelected.value.splice(index, 1)

        let container = []
        for (var item in checkboxSelected.value) {
            container.push(productsList.value.filter(el => el.brand === checkboxSelected.value[item]))
            container = container.reduce((list, sub) => list.concat(sub), [])
        }

        filteredProductsList.value = container
        validationInputs()
        return
    }
    
    /* Add */
    let container = []
    checkboxSelected.value.push(event)

    for (var item in checkboxSelected.value) {
		container.push(productsList.value.filter(el => el.brand === checkboxSelected.value[item]))
	}

    container = container.reduce((list, sub) => list.concat(sub), [])
    filteredProductsList.value = container
    validationInputs()
}

watch(() => checkboxSelected.value.length === 0, (newVal, oldVal) => {
    if (filteredProductsList.value.length === 0) filteredProductsList.value = productsList.value
})

/*************************************************
*              Price Filter Logic                *
*************************************************/
const minValue = ref()
const maxValue = ref()

const validationInputs = () => {
    let container = []
    for (var item in checkboxSelected.value) {
        container.push(productsList.value.filter(el => el.brand === checkboxSelected.value[item]))
        container = container.reduce((list, sub) => list.concat(sub), [])
    }

    filteredProductsList.value = container

    filteredProductsList.value = checkboxSelected.value.length > 0 ? filteredProductsList.value : productsList.value
    if (maxValue.value && minValue.value) return filteredProductsList.value = filteredProductsList.value.filter(el => el.price >= minValue.value && el.price <= maxValue.value)
    if (minValue.value && !maxValue.value) filteredProductsList.value = filteredProductsList.value.filter(el => el.price > minValue.value)
    if (maxValue.value && !minValue.value) filteredProductsList.value = filteredProductsList.value.filter(el => el.price < maxValue.value)
}

/*************************************************
*                Initail Requests                *
*************************************************/
const initialRequests = async () => {
    let response = await fetch('https://dummyjson.com/products')
    .then(res => res.json())

    productsList.value = response.products
    brandList.value = response.products.forEach(item => {
        if (checkboxGroupParams.value.find(el => el.text === item.brand)) return
        
        checkboxGroupParams.value.push({
            id: item.brand,
            text: item.brand,
            selected: false
       })
    })
}

initialRequests()
</script>

<style lang="scss" scoped>
.checkbox-container {
    width: 250px;
    max-height: 300px;
    padding: 14px 0 14px 14px;
    border-radius: 16px;
    border: 1px solid rgb(141, 141, 238);
    display: flex;
    flex-direction: column;
    background-color: rgb(242, 233, 250);
    overflow: auto;
    color: blueviolet;
    font-weight: 700;

    transition: all 800ms ease-in;
    position: fixed;
    top: 18px;
    left: 18px;

    cursor: pointer;

    .checkbox-group {
        overflow-y: scroll;
        text-align: start;

        .price-container {
            width: fit-content;
            margin: 16px 0;

            input {
                width: 63px;
                background-color: transparent;
                border: 1px solid rgb(141, 141, 238);
                border-radius: 16px;
                color: blueviolet;
                font-weight: 700;
                line-height: 3.38;
                padding-inline: 1rem;
            }
        }

        &.show-details-enter-active,
        &.show-details-leave-active {
            max-height: 1000px;
            opacity: 1;
            transition: all 800ms ease-in;
        }

        &.show-details-enter-from,
        &.show-details-leave-to {
            max-height: 0;
            opacity: 0.2;
            transition: all 500ms ease-out;
        }
    }

    ::-webkit-scrollbar {
        width: 24px;
    }

    ::-webkit-scrollbar-track {
        box-shadow: inset 0 0 20px 20px transparent;
        border: solid 20px transparent;
    }

    ::-webkit-scrollbar-thumb {
        box-shadow: inset 0 0 20px 20px blueviolet;
        border: solid 9px transparent;
        border-radius: 14px;
    }

    ::-webkit-scrollbar-button {
        display: none;
    }
}
.product-list-container {
    min-width: fit-content;
    // height: fit-content;
    display: flex;
    flex-direction: row;
    justify-content: center;
    flex-wrap: wrap;
    gap: 16px;
    margin-top: 80px;
}

@media only screen and (max-width: 600px) {
    .checkbox-container {
        width: calc(100% - 64px);
    }
}
</style>