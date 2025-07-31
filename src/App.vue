<script setup>
import { onMounted, ref, watch } from 'vue';
import CardProducts from './components/CardProducts.vue';
import Header from './components/Header.vue'


const products = ref([])
const  cart = ref([])
const loading = ref(false)
const error =ref(null)
const URL = 'https://fakestoreapi.com/products'


watch(cart,(newCart)=>{
    if(newCart.length >0){

        saveLocalStorage()
    }else{
        localStorage.removeItem('cart')
    }
},{deep:true})

const fetchApi = async ()=>{
    loading.value=true
    error.value=null
    try {
        const response = await fetch(URL)
        if(!response.ok){
            throw new Error(`Error HTTP:${response.status}`)
            return
        }
        const data = await response.json()
        products.value = data
    } catch (err) {
        console.log('Error',error)
        error.value=err.message
        
    }finally{
        loading.value=false
    }
}

onMounted(async () => {
    try {
        await fetchApi()
        
        const cartStorage = localStorage.getItem('cart')
        if (cartStorage) {
            try {
                // Verificar que no esté vacío
                if (cartStorage.trim() === '') {
                    console.warn('localStorage contiene string vacío')
                    localStorage.removeItem('cart')
                    return
                }
                
                const parsedCart = JSON.parse(cartStorage)
                
                // Validar que sea un array
                if (Array.isArray(parsedCart)) {
                    // Filtrar items válidos por si hay datos corruptos
                    const validCart = parsedCart.filter(item => 
                        item && 
                        typeof item === 'object' && 
                        'id' in item && 
                        'quantity' in item && 
                        typeof item.quantity === 'number' &&
                        item.quantity > 0
                    )
                    
                    cart.value = validCart
                    console.log(`✅ Carrito cargado: ${validCart.length} productos`)
                    
                    if (validCart.length !== parsedCart.length) {
                        console.warn('Se filtraron algunos items inválidos del carrito')
                        // Guardar la versión limpia
                        saveLocalStorage()
                    }
                } else {
                    console.warn('Datos del carrito no son un array')
                    localStorage.removeItem('cart')
                }
                
            } catch (parseError) {
                console.error('❌ Error al parsear carrito desde localStorage:', parseError)
                console.log('Contenido problemático:', cartStorage.substring(0, 100) + '...')
                
                // Limpiar datos corruptos
                localStorage.removeItem('cart')
                
                // Mostrar notificación al usuario (opcional)
                error.value = 'Se encontraron datos corruptos en el carrito y se limpiaron.'
                setTimeout(() => {
                    error.value = null
                }, 3000)
            }
        }
    } catch (err) {
        console.error('Error en onMounted:', err)
        error.value = 'Error al inicializar la aplicación'
    }
})


const saveLocalStorage = ()=>{
    localStorage.setItem('cart',JSON.stringify(cart.value))
}

const addToCart = (product)=>{
    const isExistProduct = cart.value.findIndex((item)=> item.id == product.id)
    if (isExistProduct >=0){
        cart.value[isExistProduct].quantity++

    }else{
        product.quantity=1
        cart.value.push(product)
    }
}


const decrementQuantity = (id)=>{
    const index = cart.value.findIndex((p) =>p.id === id)
    if (cart.value[index].quantity>0){
        cart.value[index].quantity--
    }
}
const incrementQuantity = (id)=>{
    const index = cart.value.findIndex((p) => p.id === id)
    cart.value[index].quantity++

}



const removeProduct = (id)=>{
    cart.value = cart.value.filter((p)=> p.id !== id)
}

const emptyCart = ()=>{
    cart.value.length = 0
}






</script>

<template>
    <main class=" max-w-full min-h-screen bg-neutral-300 pt-24">
        <Header :cart="cart" @increment-quantity="incrementQuantity" @decrement-quantity="decrementQuantity" @remove-product="removeProduct" @empty-cart="emptyCart" />
        <h1 class="text-4xl font-bold text-slate-900 px-6 py-4">Fake Store</h1>
        <div v-if="loading"> Cargando productos...</div>
        <div v-if="error"> Error {{error}}</div>
        <div class="grid grid-cols-2 md:grid-cols-4 content-center gap-4 p-6">
            <CardProducts v-if="!loading && !error" :product="product" v-for="product in products" @add-to-cart="addToCart" />
        </div>

    </main>
</template>

<style scoped>

</style>