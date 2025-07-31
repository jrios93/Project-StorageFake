<script  setup>
import CartIcon from './icons/CartIcon.vue'
import LogoIcon from './icons/LogoIcon.vue'
import {onMounted, ref,onUnmounted,computed} from 'vue'

const isCartModalVisible =ref(false)
const scrolled =ref(false)
// Funcioanes para manejar el modal
const toggleCartModal = ()=>{isCartModalVisible.value = !isCartModalVisible.value}
const showCartModal = ()=>{isCartModalVisible.value=true}
const hideCartModal = ()=>{isCartModalVisible.value=false}

const handleScroll =()=>{
    scrolled.value =window.scrollY>50
}

const props = defineProps({
    cart:{
        type:Array,
        required:true
    }
})

onMounted(()=> window.addEventListener('scroll',handleScroll))
onUnmounted(()=> window.removeEventListener('scroll',handleScroll))

const emit = defineEmits(['increment-quantity','decrement-quantity','remove-product','empty-cart'])

const totalPaymend = computed(()=>{
    const total = props.cart.reduce((total,product)=>{ return total + (product.quantity * product.price)},0)
    return Math.round(total*100)/100
})

</script>

<template>
    <header class=" w-full  flex items-center justify-center m-auto">
        <nav :class="['w-full p-6 fixed top-0 z-50 transition-all duration-300',scrolled?' bg-gray-50 shadow-lg':'bg-transparent']">
            <div class="flex items-center justify-between">
                <a class="flex gap-2 items-center"><LogoIcon/>FakeStore</a>
                <div class="flex gap-2 items-center relative">
                    <!-- Botón del carrito con eventos de hover y click -->
                     <div class="flex items-center gap-2 cursor-pointer p-2 rounded-md hover:bg-gray-100 transition-colors"
                     
                     @click="toggleCartModal"
                     >

                     <CartIcon/> Cart {{cart.length}}
                    </div>
                    <!-- Modal del carrito -->
                    <div v-show="isCartModalVisible" class="absolute top-full right-0 mt-2 bg-white border-gray-200 rounded-lg shadow-lg
                     p-4 w-96 z-50"
                     @mouseenter="showCartModal"
                     @mouseleave="hideCartModal"
                     >
                     <!-- Contenido cuando el carrito está vacio -->
                    <div v-if="cart && cart.length===0" >
                        <p class="text-center m-0">El carrito esta vacio</p>
                    </div>
                    <!-- Contenido cundo hay productos en el carrito -->
                    <div v-else>
                        <table class="w-full mb-4">
                            <thead >
                                <tr class="border-b">
                                    <th class="text-left py-2">Imagen</th>
                                    <th class="text-left py-2 ">Nombre</th>
                                    <th class="text-left py-2 ">Precio</th>
                                    <th class="text-left py-2 ">Cantidad</th>
                                    <th class="text-left py-2"></th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="products in cart" class="border-b">
                                    <td class="py-2">
                                        <img class="w-12 h-12 object-cover rounded" :src="products.image" alt="imagen de "/>
                                    </td>
                                    <td class="py-2">{{products.title}}</td>
                                    <td class="font-bold py-2 ">{{products.price}}</td>
                                    <td class="py-2">
                                        <div class="flex items-center gap-2">
                                            <button class=" rounded-md bg-black w-8 h-8 text-white p-2 text-sm font-semibold hover:bg-gray-800 cursor-pointer" @click="$emit('decrement-quantity',products.id)">-</button>
                                            <span class="text-sm min-w-[20px] text-center ">{{products.quantity}}</span>
                                            <button class=" rounded-md bg-black w-8 h-8 text-white p-2 text-sm font-semibold hover:bg-gray-800 cursor-pointer" @click="$emit('increment-quantity',products.id)">+</button>
                                        </div>
                                    </td>
                                    <td class="py-2">
                                        <button class="bg-red-600 px-2 py-1 rounded-md text-white text-xs font-semibold hover:bg-red-700 cursor-pointer" type="button" @click="$emit('remove-product',products.id)">x</button>
                                    </td>
                                </tr>
                            </tbody>

                        </table>
                        <div class="border-t pt-3">

                            <p class=" text-right mb-3">Total a pagar: <span class=" font-bold">${{totalPaymend}}</span></p>
                            <button class=" bg-black w-full text-white rounded-md p-2 mt-3 hover:bg-gray-800 cursor-pointer" @click="$emit('empty-cart')">Vaciar carrito</button>
                        </div>
                    </div>
                </div></div>
            </div>
        </nav>

    </header>
</template>

<style scoped>
.cart-modal-enter-active,.cart-modal-leave-active{
    transition: opacity 0.2s ease, transform 0.2s ease;
}
.cart-modal-enter-from,.cart-modal-leave-to{
    opacity: 0;
    transform: translateY(-10px);
}


</style>