<!-- src/App.svelte -->
<script>
  import { onMount } from 'svelte';
  import Header from './components/Header.svelte';
  import ProductCard from './components/ProductCard.svelte';
  import ProductModal from './components/ProductModal.svelte';

  let products = [];
  let categories = [];
  let filteredCategories = [];
  let wishlist = [];
  let cart = [];
  let searchTerm = "";
  let sortOrder = "asc";
  let filter = "";
  let loading = false;
  let modalOpen = false;
  let modalProduct = null;
  let error = "";

  onMount(async () => {
    await fetchCategories();
    await fetchProducts();
  });

  async function fetchCategories() {
    try {
      const res = await fetch("https://fakestoreapi.com/products/categories");
      if (!res.ok) throw new Error(`Failed to fetch categories: ${res.statusText}`);
      categories = await res.json();
      filteredCategories = categories;
    } catch (err) {
      console.error("Failed to fetch categories:", err);
      error = "Failed to load categories. Please try again later.";
    }
  }

  async function fetchProducts() {
    loading = true;
    try {
      const res = await fetch("https://fakestoreapi.com/products");
      if (!res.ok) throw new Error(`Failed to fetch products: ${res.statusText}`);
      let data = await res.json();
      if (filter) {
        data = data.filter(product => product.category === filter);
      }
      data = data.sort((a, b) => sortOrder === "asc" ? a.price - b.price : b.price - a.price);
      products = data;
    } catch (err) {
      console.error("Failed to fetch products:", err);
      error = "Failed to load products. Please try again later.";
    }
    loading = false;
  }

  function filterCategories() {
    const term = searchTerm.toLowerCase();
    filteredCategories = categories.filter(category => category.toLowerCase().includes(term));
  }

  function resetFilters() {
    filter = "";
    sortOrder = "asc";
    searchTerm = "";
    filteredCategories = categories;
    fetchProducts();
  }

  async function openModal(productId) {
    try {
      const res = await fetch(`https://fakestoreapi.com/products/${productId}`);
      if (!res.ok) throw new Error(`Failed to fetch product details: ${res.statusText}`);
      modalProduct = await res.json();
      modalOpen = true;
    } catch (err) {
      console.error("Failed to fetch product details:", err);
      error = "Failed to load product details. Please try again later.";
    }
  }

  function closeModal() {
    modalOpen = false;
    modalProduct = null;
  }

  function toggleWishlist(productId) {
    if (wishlist.includes(productId)) {
      wishlist = wishlist.filter(id => id !== productId);
    } else {
      wishlist.push(productId);
    }
  }

  function toggleCart(productId) {
    if (cart.includes(productId)) {
      cart = cart.filter(id => id !== productId);
    } else {
      cart.push(productId);
    }
  }
</script>
