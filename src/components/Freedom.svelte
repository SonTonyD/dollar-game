<script>
  // FREEDOM
  let targetPassiveIncome = 2000;
  let retirementYear = new Date().getFullYear() + 20;

  let requiredMonthlySaving = 0;
  let capitalTarget = 0;

  function calculateFinancialFreedom() {
    const ruleOfFourCapital = targetPassiveIncome * 12 / 0.04; // 4% rule
    capitalTarget = ruleOfFourCapital;

    const now = new Date();
    const currentYear = now.getFullYear();
    const monthsToInvest = (retirementYear - currentYear) * 12;

    if (monthsToInvest <= 0) {
      requiredMonthlySaving = 0;
      return;
    }

    const netAnnualReturn = 0.08 * (1 - 0.30); // flat tax on 8%
    const r = netAnnualReturn / 12; // taux mensuel

    // Formule de la rente inversée : PMT = FV * r / ((1 + r)^n - 1)
    requiredMonthlySaving = capitalTarget * r / (Math.pow(1 + r, monthsToInvest) - 1);
  }
</script>

<h1>🗽 Objectif Liberté Financière</h1>
<form on:submit|preventDefault={calculateFinancialFreedom}>
  <label>
    💰 Revenu passif souhaité (€/mois)
    <input type="number" bind:value={targetPassiveIncome} />
  </label>
  <label>
    📅 Année de retraite souhaitée
    <input type="number" min={new Date().getFullYear()} bind:value={retirementYear} />
  </label>
  <button type="submit">Calculer</button>
</form>

{#if requiredMonthlySaving > 0}
  <p>🎯 Pour générer <strong>{targetPassiveIncome} €</strong>/mois à partir de <strong>{retirementYear}</strong>,</p>
  <p>tu devras épargner <strong>{requiredMonthlySaving.toFixed(2)} €</strong>/mois à partir de maintenant.</p>
  <p>Objectif : constituer un capital de <strong>{capitalTarget.toLocaleString()} €</strong>.</p>
  <p>(Avec un rendement net de 5.6 %/an après flat tax)</p>
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

  button:hover {
    opacity: 0.9;
  }

  h1 {
    text-align: center;
    font-size: 1.4rem;
    margin-top: 1rem;
  }

  @media screen and (min-width: 768px) {
    form {
      max-width: 500px;
      margin: auto;
    }

    h1 {
      font-size: 1.7rem;
    }
  }
</style>