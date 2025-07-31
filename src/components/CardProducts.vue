<script setup>
const props = defineProps({
    product:{
        type:Object,
        required:true
    }
})

const emit = defineEmits(['add-to-cart'])


</script>

<template>
    <div class=" flex flex-col justify-between gap-2 ">
        <div class="w-full h-48 flex items-center justify-center bg-gray-100 shadow-md">
            <img class="max-w-full max-h-full object-contain p-2" :src="product.image"/>
        </div>
        <p class="text-xl font-bold text-slate-900">{{product.title}}</p>
        <p class="text-md font-light truncate ">{{product.description}}</p>
        <p class="font-bold">${{product.price}}</p>
        <button class="bg-black text-white py-3 rounded-md cursor-pointer" @click="$emit('add-to-cart',product)">Agregar al carrito</button>

    </div>
</template>

<style scoped>

</style>