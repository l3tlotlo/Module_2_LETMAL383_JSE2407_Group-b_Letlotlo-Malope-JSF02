<script>
    import { navigate } from 'svelte-routing';
    
  
    /**
     * The product object containing details of the product.
     * @type {Object}
     */
    export let product;
  
    /**
     * The wishlist store, which contains IDs of products added to the wishlist.
     * @type {import('svelte/store').Writable<Array<number>>}
     */
    export let wishlist;
  
    /**
     * The cart store, which contains IDs of products added to the cart.
     * @type {import('svelte/store').Writable<Array<number>>}
     */
    export let cart;
  
    /**
     * Toggles the product in the wishlist.
     * Adds the product to the wishlist if it is not already present, 
     * removes it otherwise.
     * @param {number} productId - The ID of the product to toggle in the wishlist.
     */
    function toggleWishlist(productId) {
      wishlist.update(wishlist => wishlist.includes(productId) ? wishlist.filter(id => id !== productId) : [...wishlist, productId]);
    }
  
    /**
     * Toggles the product in the cart.
     * Adds the product to the cart if it is not already present, 
     * removes it otherwise.
     * @param {number} productId - The ID of the product to toggle in the cart.
     */
    function toggleCart(productId) {
      cart.update(cart => cart.includes(productId) ? cart.filter(id => id !== productId) : [...cart, productId]);
    }
  
    /**
     * Navigates to the product detail page.
     * @param {number} productId - The ID of the product to view details for.
     * @param {Event} event - The click event to stop propagation.
     */
    function goToDetailPage(productId, event) {
      event.stopPropagation();
      navigate(`/products/${productId}`);
    }
  </script>
  
  <div class="card" on:click={(event) => goToDetailPage(product.id, event)}>
    <img src={product.image} alt={product.title} />
    <h2 class="text-lg font-semibold">{product.title}</h2>
    <p class="text-gray-900">R{product.price}</p>
    <p class="text-gray-600">{product.category}</p>
  </div>
  
  <style>
    .card {
      background-color: turquoise;
      border: 1px solid #ddd;
      border-radius: 8px;
      overflow: hidden;
      padding: 16px;
      position: relative;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      transition: transform 0.2s, box-shadow 0.2s;
    }
  
    .card:hover {
      transform: scale(1.03);
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }
  
    .card img {
      max-width: 100%;
      height: auto;
      cursor: pointer;
      transition: transform 0.2s;
    }
  
    .card img:hover {
      transform: scale(1.05);
    }
  
    .wishlist-button,
    .cart-button {
      position: absolute;
      bottom: 8px;
      padding: 8px;
      border-radius: 50%;
      cursor: pointer;
    }
  
    .wishlist-button {
      left: 8px;
      background-color: yellow;
    }
  
    .cart-button {
      right: 8px;
      background-color: green;
      color: white;
    }
  
    .card h2 {
      margin-top: 10px;
      font-size: 1.25rem;
    }
  
    .card p {
      margin: 5px 0;
    }
  </style>
  