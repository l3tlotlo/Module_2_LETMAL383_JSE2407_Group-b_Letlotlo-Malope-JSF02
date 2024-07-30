<!-- src/components/ProductModal.svelte -->
<script>
    export let product;
    export let closeModal;
  
    function close() {
      closeModal();
    }
  </script>
  
  <style>
    .modal {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: rgba(0, 0, 0, 0.5);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }
  
    .modal-content {
      background-color: white;
      padding: 20px;
      border-radius: 8px;
      max-width: 500px;
      width: 100%;
    }
  </style>
  
  <div class="modal" on:click={close}>
    <div class="modal-content" on:click|stopPropagation>
      <h2 class="text-xl font-semibold mb-4">{product.title}</h2>
      <img src={product.image} alt={product.title} class="w-full h-40 object-cover mb-4" />
      <p class="text-gray-900">{product.description}</p>
      <p class="text-gray-600 mt-2">${product.price}</p>
      <p class="text-gray-600">{product.category}</p>
      <div class="rating">
        {Array(Math.round(product.rating.rate)).fill().map(() => "⭐")}
        <span>({product.rating.count})</span>
      </div>
      <button on:click={close} class="mt-4 bg-red-500 text-white px-4 py-2 rounded">Close</button>
    </div>
  </div>
  