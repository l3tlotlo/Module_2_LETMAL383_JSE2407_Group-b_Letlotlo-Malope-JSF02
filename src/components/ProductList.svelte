<script>
  import { onMount } from 'svelte';
  import ProductCard from './ProductCard.svelte';
  import ProductDetail from './ProductDetail.svelte';

  let products = [];
  let originalProducts = [];
  let cart = [];
  let detailViewOpen = false;
  let selectedProduct = {};
  let loadingInitial = true;
  let sorting = 'default';
  let filterItem = 'All categories';

  async function fetchProducts() {
    loadingInitial = true;
    const url = filterItem !== 'All categories' 
      ? `https://fakestoreapi.com/products/category/${filterItem}` 
      : 'https://fakestoreapi.com/products';
    try {
      const response = await fetch(url);
      const data = await response.json();
      products = data;
      originalProducts = JSON.parse(JSON.stringify(data));
      loadingInitial = false;
      sortProducts();
    } catch (error) {
      console.error('Error fetching products:', error);
    }
  }

  function addToCart(product) {
    cart.push(product);
    alert(`${product.title} has been added to your cart!`);
  }

  function sortProducts() {
    if (sorting !== 'default') {
      products.sort((a, b) => 
        sorting === 'low' ? a.price - b.price : b.price - a.price
      );
    } else {
      products = JSON.parse(JSON.stringify(originalProducts));
    }
  }

  function showDetail(product) {
    selectedProduct = product;
    detailViewOpen = true;
  }

  onMount(fetchProducts);
</script>

<!-- Loading state for initial data -->
{#if loadingInitial}
  <div class="fixed inset-0 bg-gray-800 bg-opacity-75 flex items-center justify-center z-50">
    <div class="text-white">Loading products...</div>
  </div>
{/if}

<!-- Filter Bar and Sort Controls -->
<div class="flex flex-col sm:flex-row justify-between p-4 gap-4">
  <!-- Filter Bar -->
  <form class="relative w-full sm:w-1/2 max-w-xs">
    <button on:click="{() => dropdownOpen = !dropdownOpen}" type="button" class="flex items-center justify-between p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500">
      <span>{filterItem}</span>
      <svg class="w-4 h-4" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 4a1 1 0 0 1 1-1h16a1 1 0 0 1 1 1v1a1 1 0 0 1-1 1H4a1 1 0 0 1-1-1V4zm0 9a1 1 0 0 1 1-1h16a1 1 0 0 1 1 1v1a1 1 0 0 1-1 1H4a1 1 0 0 1-1-1v-1zM4 18a1 1 0 0 1-1-1v-1a1 1 0 0 1 1-1h16a1 1 0 0 1 1 1v1a1 1 0 0 1-1 1H4z" />
      </svg>
    </button>
    <div class="{dropdownOpen ? 'block' : 'hidden'} absolute z-10 w-full mt-2 bg-white rounded-md shadow-lg">
      <ul>
        <li on:click="{() => { filterItem = 'All categories'; fetchProducts(); }}" class="cursor-pointer p-2 hover:bg-gray-200">All categories</li>
        <!-- Categories will be fetched dynamically -->
        {#each category as category}
          <li on:click="{() => { filterItem = category; fetchProducts(); }}" class="cursor-pointer p-2 hover:bg-gray-200">{category}</li>
        {/each}
      </ul>
    </div>
  </form>

  <!-- Sort Dropdown -->
  <div class="relative w-full sm:w-1/2 max-w-xs">
    <select
      id="sort"
      class="p-2.5 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500"
      bind:value={sorting}
      on:change={sortProducts}
    >
      <option value="default">Default</option>
      <option value="low">Price: Low to High</option>
      <option value="high">Price: High to Low</option>
    </select>
  </div>
</div>

<!-- Displaying Products -->
<div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6 p-4">
  {#each products as product}
    <ProductCard {product} on:show-detail="{() => showDetail(product)}" />
  {/each}
</div>

<!-- Detailed View Modal -->
{#if detailViewOpen}
  <ProductDetail {selectedProduct} on:add-to-cart="{addToCart}" on:close-detail="{() => detailViewOpen = false}" />
{/if}
