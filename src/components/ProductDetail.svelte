<script>
    export let id;
    export let wishlist;
    export let cart;
    import { onMount } from 'svelte';
    import { navigate } from 'svelte-routing';

    let selectedProduct;
    
    function addToCart() {
      cart.update(items => [...items, selectedProduct.id]);
    }

    onMount(() => {
        fetchProduct(id);
    });

    async function fetchProduct(productId) {
        const res = await fetch(`https://fakestoreapi.com/products/${productId}`);
        selectedProduct = await res.json();
    }
</script>

{#if selectedProduct}
<div class="fixed inset-0 bg-gray-800 bg-opacity-75 flex items-center justify-center z-50">
    <div class="bg-white rounded-lg shadow-md p-4 max-w-3xl mx-auto">
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
            <div class="flex justify-center items-center">
                <img src={selectedProduct.image} alt={selectedProduct.title} class="w-2/3 h-auto" />
            </div>
            <div>
                <h1 class="text-2xl font-bold mb-2">{selectedProduct.title}</h1>
                {#if selectedProduct.rating}
                    <div class="flex items-center gap-2 mb-2">
                        <svg class="w-4 h-4 text-yellow-300" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="currentColor" viewBox="0 0 22 20">
                            <path d="M20.924 7.625a1.523 1.523 0 0 0-1.238-1.044l-5.051-.734-2.259-4.577a1.534 1.534 0 0 0-2.752 0L7.365 5.847l-5.051.734A1.535 1.535 0 0 0 1.463 9.2l3.656 3.563-.863 5.031a1.532 1.532 0 0 0 2.226 1.616L11 17.033l4.518 2.375a1.534 1.534 0 0 0 2.226-1.617l-.863-5.03L20.537 8.69a1.534 1.534 0 0 0 .387-1.065z"></path>
                        </svg>    
                        <span>{selectedProduct.rating.rate}</span> 
                        <span>({selectedProduct.rating.count} reviews)</span>
                        <span>R{selectedProduct.price}</span> 
                        <span>{selectedProduct.category}</span>                      
                    </div>
                {/if}
                <p class="text-gray-700 mb-4">{selectedProduct.description}</p>
                <button on:click={addToCart} class="bg-blue-500 text-white py-2 px-4 rounded">Add to Cart</button>
                <button on:click={() => navigate('/')} class="bg-gray-500 text-white py-2 px-4 rounded ml-2">Back to List</button>
            </div>
        </div>
    </div>
</div>
{:else}
<p>Loading...</p>
{/if}