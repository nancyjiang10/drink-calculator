<!--
@component
Beverage Cost Inflation Calculator
Updated: March 1, 2026
-->
<script>
  const INFLATION_RATE = 0.183; // 18.3% inflation

  // Reactive state for beverages list
  let beverages = $state([
    { id: 1, name: 'Coffee', lastYearPrice: 4.50 },
    { id: 2, name: 'Smoothie', lastYearPrice: 7.25 },
    { id: 3, name: 'Soda', lastYearPrice: 2.99 }
  ]);

  let nextId = $state(4);
  let newBeverageName = $state('');
  let newBeveragePrice = $state('');

  // Derived totals
  let totalLastYear = $derived(beverages.reduce((sum, b) => sum + b.lastYearPrice, 0));
  let totalThisYear = $derived(beverages.reduce((sum, b) => sum + b.thisYearPrice, 0));
  let totalInflationCost = $derived(totalThisYear - totalLastYear);

  // Add beverage to the list
  function addBeverage() {
    if (newBeverageName && newBeveragePrice) {
      beverages.push({
        id: nextId++,
        name: newBeverageName,
        lastYearPrice: parseFloat(newBeveragePrice)
      });
      newBeverageName = '';
      newBeveragePrice = '';
    }
  }

  // Remove beverage from list
  function removeBeverage(id) {
    beverages = beverages.filter(b => b.id !== id);
  }
</script>

<svelte:head>
  <title>Beverage Inflation Calculator</title>
  <meta name="description" content="Calculate how much your favorite beverages cost with 18.3% inflation from last year to this year." />
</svelte:head>

<div class="container">
  <h1>🥤 Beverage Inflation Calculator</h1>
  <p class="subtitle">See how much your favorite drinks cost with an 18.3% inflation rate</p>

  <!-- Add New Beverage Section -->
  <div class="card add-beverage-card">
    <h2>Add a Beverage</h2>
    <div class="form-group">
      <input
        type="text"
        placeholder="Beverage name (e.g., Latte, Juice)"
        bind:value={newBeverageName}
        onkeydown={(e) => e.key === 'Enter' && addBeverage()}
      />
      <input
        type="number"
        placeholder="Last year's price ($)"
        bind:value={newBeveragePrice}
        step="0.01"
        onkeydown={(e) => e.key === 'Enter' && addBeverage()}
      />
      <button onclick={addBeverage}>Add Beverage</button>
    </div>
  </div>

  <!-- Beverages List -->
  <div class="beverages-grid">
    {#each beverages as beverage (beverage.id)}
      {@const thisYearPrice = beverage.lastYearPrice * (1 + INFLATION_RATE)}
      {@const increase = thisYearPrice - beverage.lastYearPrice}
      <div class="beverage-card">
        <div class="card-header">
          <h3>{beverage.name}</h3>
          <button class="close-btn" onclick={() => removeBeverage(beverage.id)}>✕</button>
        </div>
        
        <div class="price-row">
          <span class="label">Last Year:</span>
          <span class="price last-year">${beverage.lastYearPrice.toFixed(2)}</span>
        </div>
        
        <div class="price-row highlight">
          <span class="label">This Year:</span>
          <span class="price this-year">${thisYearPrice.toFixed(2)}</span>
        </div>
        
        <div class="price-row">
          <span class="label">Increase (18.3%):</span>
          <span class="price increase">+${increase.toFixed(2)}</span>
        </div>
      </div>
    {/each}
  </div>

  <!-- Totals Summary -->
  <div class="card totals-card">
    <h2>Summary</h2>
    <div class="summary-grid">
      <div class="summary-item">
        <span class="summary-label">Total (Last Year):</span>
        <span class="summary-value">${totalLastYear.toFixed(2)}</span>
      </div>
      <div class="summary-item">
        <span class="summary-label">Total (This Year):</span>
        <span class="summary-value highlight">${totalThisYear.toFixed(2)}</span>
      </div>
      <div class="summary-item">
        <span class="summary-label">Total Inflation Cost:</span>
        <span class="summary-value cost">+${totalInflationCost.toFixed(2)}</span>
      </div>
    </div>
  </div>

</div>

<style>
  :global(body) {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
  }

  .container {
    max-width: 900px;
    margin: 0 auto;
    padding: var(--spacing-lg, 2rem);
  }

  h1 {
    font-size: 2.5rem;
    margin-bottom: 0.5rem;
    color: white;
    text-align: center;
  }

  .subtitle {
    font-size: 1.1rem;
    color: rgba(255, 255, 255, 0.9);
    text-align: center;
    margin-bottom: 2rem;
  }

  .card {
    background: white;
    border-radius: 12px;
    padding: 2rem;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
    margin-bottom: 2rem;
  }

  .card h2 {
    margin-top: 0;
    margin-bottom: 1.5rem;
    color: #333;
    font-size: 1.5rem;
  }

  .add-beverage-card .form-group {
    display: grid;
    grid-template-columns: 1fr 1fr auto;
    gap: 1rem;
  }

  input {
    padding: 0.875rem;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 1rem;
    font-family: inherit;
    transition: all 0.3s ease;
  }

  input:focus {
    outline: none;
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
  }

  button {
    padding: 0.875rem 1.5rem;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
  }

  button:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
  }

  button:active {
    transform: translateY(0);
  }

  .close-btn {
    padding: 0.5rem 0.75rem;
    background: #ff6b6b;
    font-size: 0.875rem;
  }

  .close-btn:hover {
    background: #ff5252;
  }

  .beverages-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 1.5rem;
    margin-bottom: 2rem;
  }

  .beverage-card {
    background: white;
    border-radius: 12px;
    padding: 1.5rem;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }

  .beverage-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
  }

  .card-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 1.5rem;
    gap: 1rem;
  }

  .card-header h3 {
    margin: 0;
    color: #333;
    flex: 1;
  }

  .price-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0.75rem 0;
    border-bottom: 1px solid #f0f0f0;
  }

  .price-row.highlight {
    background: #f8f9ff;
    padding: 0.75rem;
    border-radius: 6px;
    border-bottom: none;
    margin: 0.5rem 0;
  }

  .label {
    color: #666;
    font-size: 0.9rem;
  }

  .price {
    font-weight: 600;
    font-size: 1.1rem;
  }

  .price.last-year {
    color: #667eea;
  }

  .price.this-year {
    color: #764ba2;
    font-size: 1.3rem;
  }

  .price.increase {
    color: #ff6b6b;
  }

  .totals-card {
    background: white;
    border-top: 4px solid linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  }

  .summary-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 1.5rem;
  }

  .summary-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem;
    background: #f8f9fa;
    border-radius: 8px;
  }

  .summary-label {
    color: #666;
    font-weight: 500;
  }

  .summary-value {
    font-size: 1.5rem;
    font-weight: 700;
    color: #667eea;
  }

  .summary-value.highlight {
    color: #764ba2;
  }

  .summary-value.cost {
    color: #ff6b6b;
  }

  @media (max-width: 640px) {
    .add-beverage-card .form-group {
      grid-template-columns: 1fr;
    }

    h1 {
      font-size: 1.75rem;
    }

    .beverages-grid {
      grid-template-columns: 1fr;
    }
  }
</style>
