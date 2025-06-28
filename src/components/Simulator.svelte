<script>
  //SIMULATEUR
  let initialCapital = 1000;
  let annualRate = 5;
  let startMonth = 1;
  let startYear = 2025;
  let monthlySaving = 100;
  let savingsProjection = [];

  function calculateSavings() {
    const rate = annualRate / 100;
    const result = [];
    let value = Number(initialCapital);
    let month = startMonth;
    let year = startYear;

    for (let i = 0; i < 120*5; i++) { // 50 ans
      value += monthlySaving;
      if (month === 12) {
        value += value * rate;
        result.push({ month, year, value });
        month = 1;
        year += 1;
      } else {
        result.push({ month, year, value });
        month++;
      }
    }

    savingsProjection = result;
  }
</script>

<h1>📈 Simulateur d’épargne</h1>
<form on:submit|preventDefault={calculateSavings}>
  <label>
    🏁 Patrimoine initial (€)
    <input type="number" bind:value={initialCapital} />
  </label>
  <label>
    📈 Taux d’intérêt annuel (%) (par exemple 5 pour 5%)
    <input type="number" step="0.1" bind:value={annualRate} />
  </label>
  <label>
    🗓️ Mois de début (1 à 12)
    <input type="number" min="1" max="12" bind:value={startMonth} />
  </label>
  <label>
    📅 Année de départ
    <input type="number" min="1900" bind:value={startYear} />
  </label>
  <label>
    💸 Épargne mensuelle (€)
    <input type="number" bind:value={monthlySaving} />
  </label>
  <button type="submit">Calculer</button>
</form>

{#if savingsProjection.length > 0}
  <h2>📊 Résultats</h2>
  <div style="overflow-x: auto;">
    <table>
      <thead>
        <tr>
          <th>Mois</th>
          <th>Année</th>
          <th>Patrimoine (€)</th>
        </tr>
      </thead>
      <tbody>
        {#each savingsProjection as entry}
          <tr>
            <td>{entry.month}</td>
            <td>{entry.year}</td>
            <td>{entry.value.toFixed(2)}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
{/if}

<!-- STYLE -->
<style>
  form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    padding: 1rem;
    font-size: 1rem;
  }

  label {
    display: flex;
    flex-direction: column;
    font-weight: bold;
    color: #333;
  }

  input {
    font-size: 1.1rem;
    padding: 0.5rem;
    border-radius: 8px;
    border: 1px solid #ccc;
    margin-top: 0.5rem;
  }

  button {
    font-size: 1.1rem;
    padding: 0.8rem;
    margin-top: 0.5rem;
    border: none;
    border-radius: 8px;
    background-color: #007bff;
    color: white;
    cursor: pointer;
  }

  button[type="button"] {
    background-color: #888;
  }

  button:hover {
    opacity: 0.9;
  }

  h1, h2 {
    text-align: center;
    font-size: 1.4rem;
    margin-top: 1rem;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.95rem;
    margin-top: 1rem;
    overflow-x: auto;
    display: block;
  }

  thead {
    background-color: #f0f0f0;
  }

  th, td {
    padding: 0.7rem;
    border: 1px solid #ccc;
    text-align: center;
  }

  @media screen and (min-width: 768px) {
    form {
      max-width: 500px;
      margin: auto;
    }

    table {
      font-size: 1rem;
    }

    h1, h2 {
      font-size: 1.7rem;
    }
  }
</style>