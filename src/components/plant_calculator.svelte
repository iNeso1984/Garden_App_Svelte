<script>
  import { writable } from 'svelte/store';

  let weeksBefore = 0;
  let selectedDate = null; // User-selected date
  const startDate = writable(null);

  const calculateStartDate = () => {
    if (weeksBefore > 0 && selectedDate) {
      const frostDate = new Date(selectedDate);
      frostDate.setDate(frostDate.getDate() - weeksBefore * 7);
      startDate.set(frostDate.toISOString().split('T')[0]);
    } else {
      startDate.set(null);
    }
  };
</script>

<div>
  <h2>Seed Start Calculator</h2>

  <div>
    <label for="frost-date">Select last frost date:</label>
    <input
      id="frost-date"
      type="date"
      bind:value={selectedDate}
      on:change={calculateStartDate}
    />
  </div>

  <div>
    <label for="weeks">Enter weeks before selected date:</label>
    <input
      id="weeks"
      type="number"
      min="0"
      bind:value={weeksBefore}
      on:input={calculateStartDate}
      placeholder="Weeks before"
    />
  </div>

  <div>
    <p>
      {#if $startDate}
        Start seeds on: <strong>{$startDate}</strong>
      {:else}
        Enter weeks to calculate a date.
      {/if}
    </p>
  </div>
</div>

<style>
  h2 {
    margin-bottom: 16px;
  }

  div {
    margin-bottom: 12px;
  }

  label {
    display: block;
    margin-bottom: 4px;
  }

  input {
    padding: 6px;
    margin-bottom: 8px;
    width: 100%;
    max-width: 300px;
  }
</style>
