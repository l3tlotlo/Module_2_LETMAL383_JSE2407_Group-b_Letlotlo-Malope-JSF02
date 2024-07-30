<script>
    export let product;
  
    function showDetail() {
      const event = new CustomEvent('show-detail', { detail: product });
      dispatchEvent(event);
    }
  </script>
  
  <div 
    class="bg-white border border-gray-200 rounded-lg shadow-sm overflow-hidden"
    on:click="{showDetail}"
  >
    <img 
      src="{product.image}"
      alt="Product Image"
      class="w-full h-64 object-contain"
    />
    <div class="p-4">
      <h3 
        class="text-lg font-semibold text-gray-900 mb-2 truncate"
      >{product.title}</h3>
      <p 
        class="text-green-400 font-semibold"
      >${product.price.toFixed(2)}</p>
      <span class="inline-flex items-center rounded-md bg-gray-50 px-2 py-1 text-xs font-medium text-gray-600 ring-1 ring-inset ring-gray-500/10">{product.category}</span>
    </div>
  </div>
  