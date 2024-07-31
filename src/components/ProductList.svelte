<script>
    import ProductCard from './ProductCard.svelte';
    import { writable } from 'svelte/store';
  
    export let products = writable([]);
    export let wishlist;
    export let cart;
  </script>
  
  <div class="grid">
    {#each $products as product}
      <ProductCard {product} {wishlist} {cart} />
    {/each}
  </div>
  
  <style>
    .grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
      gap: 16px;
    }
  </style>
  