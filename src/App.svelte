<script>
  import { Router, Route, Link } from 'svelte-routing';
  import ProductDetail from './components/ProductDetail.svelte';
  import { onMount } from 'svelte';
  import Header from './components/Header.svelte';
  import ProductList from './components/ProductList.svelte';
  import { writable } from 'svelte/store';

  let searchTerm = "";
  let sortOrder = "default";  // Initialize to "default"
  let filter = "";
  let products = writable([]);
  let categories = writable([]);
  let filteredCategories = writable([]);
  let wishlist = writable([]);
  let cart = writable([]);
  let loading = writable(false);
  let error = writable("");

  onMount(() => {
    fetchProducts();
    fetchCategories();
  });

  async function fetchProducts() {
    loading.set(true);
    error.set("");
    try {
      const res = await fetch("https://fakestoreapi.com/products");
      if (!res.ok) throw new Error(`Failed to fetch products: ${res.statusText}`);
      let data = await res.json();
      if (filter) data = data.filter(product => product.category === filter);
      if (sortOrder !== "default") {
        data = data.sort((a, b) => sortOrder === "asc" ? a.price - b.price : b.price - a.price);
      }
      products.set(data);
    } catch (err) {
      console.error("Failed to fetch products:", err);
      error.set("Failed to load products. Please try again later.");
    }
    loading.set(false);
  }

  async function fetchCategories() {
    try {
      const res = await fetch("https://fakestoreapi.com/products/categories");
      if (!res.ok) throw new Error(`Failed to fetch categories: ${res.statusText}`);
      const data = await res.json();
      categories.set(data);
      filteredCategories.set(data);
    } catch (err) {
      console.error("Failed to fetch categories:", err);
      error.set("Failed to load categories. Please try again later.");
    }
  }

  function filterCategories() {
    const term = searchTerm.toLowerCase();
    categories.subscribe(cat => {
      filteredCategories.set(cat.filter(category => category.toLowerCase().includes(term)));
    });
  }

  function resetFilters() {
    filter = "";
    sortOrder = "default";  // Reset sortOrder to "default"
    searchTerm = "";
    fetchProducts();
  }
</script>

<Router>
  <Header {wishlist} {cart} />

  <nav>
    <Link to="/">Home</Link>
  </nav>

  <main>
    <Route path="/" let:params>
      {#if $loading}
        <p>Loading...</p>
      {:else}
        {#if $error}
          <p class="text-red-500">{$error}</p>
        {/if}
        <div class="container mx-auto p-4">
          <div class="flex flex-col md:flex-row md:justify-between md:items-center mb-4">
            <input type="text" bind:value={searchTerm} on:input={filterCategories} placeholder="Search categories..." class="bg-gray-200 text-gray-900 px-4 py-2 rounded mb-4 md:mb-0" />
            <div class="flex flex-col md:flex-row md:space-x-4 mt-4 md:mt-0">
              <select bind:value={filter} on:change={fetchProducts} class="bg-gray-200 text-gray-900 px-4 py-2 rounded">
                <option value="">All Categories</option>
                {#each $filteredCategories as category}
                  <option value={category}>{category}</option>
                {/each}
              </select>
              <select bind:value={sortOrder} on:change={fetchProducts} class="bg-gray-200 text-gray-900 px-4 py-2 rounded">
                <option value="default">Back to Default</option>
                <option value="asc">Price: Low to High</option>
                <option value="desc">Price: High to Low</option>
              </select>
              <button on:click={resetFilters} class="bg-red-500 text-white px-4 py-2 rounded">Reset Filters</button>
            </div>
          </div>
          <ProductList {products} {wishlist} {cart} />
        </div>
      {/if}
    </Route>

    <Route path="/products/:id" let:params>
      <ProductDetail id={params.id} {wishlist} {cart} />
    </Route>
  </main>
</Router>
