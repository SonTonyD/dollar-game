<script>
  const initialWorkLeft = 31;

  let month = 1;
  let cash = 1000;
  let savings = 0;
  let workLeft = initialWorkLeft;

  const salaryPerMonth = 1200;
  const salaryPerDay = salaryPerMonth/20;

  const transferAmountSmall = 200;
  const transferAmountMedium = 2000;

  const annualExpenses = 700;
  const interestRate = 0.07;

  let hasProperty = false;
  const propertyCost = 20000;
  const propertyIncome = 1500;

  let message = "";

  function work() {
    if (workLeft > 0) {
      cash += salaryPerDay;
      workLeft--;
      message = `Tu as travaillé et gagné ${salaryPerDay} €.`;
    } else {
      message = "Tu ne peux plus travailler cette année.";
    }
  }

  function transfer(transferAmount) {
    if (cash >= transferAmount) {
      cash -= transferAmount;
      savings += transferAmount;
      message = ` ${transferAmount} € transférés vers l’épargne.`;
    } else {
      message = "Pas assez d'argent.";
    }
  }

  function buyProperty() {
    if (!hasProperty && savings >= propertyCost) {
      savings -= propertyCost;
      hasProperty = true;
      message = "Propriété achetée ! Tu recevras 1500 € chaque année.";
    } else {
      message = "Impossible d’acheter la propriété.";
    }
  }

  function nextYear() {
    month += 1;

    // Dépenses
    

    // Revenu passif
    if (hasProperty) {
      cash += propertyIncome;
    }

    // Intérêts
    const interest = savings * interestRate;
    cash += interest;

    cash -= annualExpenses;

    if (cash < 0) {
      message = "💀 Tu es ruiné !";
      return;
    }

    workLeft = initialWorkLeft;

    message = `Nouveau mois. Dépenses : ${annualExpenses} €, intérêts : ${interest.toFixed(2)} €.` + 
      (hasProperty ? ` + revenu locatif : ${propertyIncome} €.` : "");
  }
</script>

<h1>💰 Jeu éducatif : Intérêts Composés</h1>

<p>Mois : {month}</p>
<p>💵 Compte courant : {cash.toFixed(2)} €</p>
<p>🏦 Compte épargne : {savings.toFixed(2)} €</p>
<p>🧾 Dépenses annuelles : {annualExpenses} €</p>
<p>🛠️ Travail restant : {workLeft} / {initialWorkLeft}</p>
<p>🏠 Propriété : {hasProperty ? 'Oui ✅' : 'Non ❌'}</p>

<div>
  <button on:click={work} disabled={workLeft === 0}>👷 Travailler (+ {salaryPerDay} €)</button>
  <button on:click={() => transfer(transferAmountSmall)}>📥 Transférer vers l’épargne ( { transferAmountSmall } €)</button>
  <button on:click={buyProperty} disabled={hasProperty || savings < propertyCost}>🏠 Acheter propriété (20 000 €)</button>
  <button on:click={nextYear}>⏭️ Nouveau mois </button>
</div>
<div>
  <button on:click={() => transfer(transferAmountMedium)}>📥 Transférer vers l’épargne ( { transferAmountMedium } €)</button>
</div>


<p>{message}</p>
