<script>
    export let rating;
  </script>
  
  <div class="rating">
    {#each Array(Math.round(rating.rate)) as _}⭐{/each}
    <span>({rating.count})</span>
  </div>
  
  <style>
    .rating {
      color: #f1c40f;
      margin: 5px 0;
    }
  </style>
  