<script>
  import Game from './components/Game.svelte';
  let currentPage = "menu"; // 'menu' | 'game' | 'simulator'



  let isJobChoosen = false;

  const loanTemplates = [
    {
      name: "Crédit conso (5 000 € sur 3 ans)",
      amount: 5000,
      durationMonths: 36,
      annualRate: 6.0 // taux conso assez courant en 2025
    },
    {
      name: "Crédit perso (15 000 € sur 5 ans)",
      amount: 15000,
      durationMonths: 60,
      annualRate: 5.5 // un peu plus bas que conso car somme plus importante
    },
    {
      name: "Crédit immo (180 000 € sur 25 ans)",
      amount: 180000,
      durationMonths: 300,
      annualRate: 3.8 // taux immo courant en 2025 avec bon dossier
    },
    {
      name: "Crédit immo (250 000 € sur 20 ans)",
      amount: 250000,
      durationMonths: 240,
      annualRate: 4.1
    },
    {
      name: "Crédit travaux (20 000 € sur 7 ans)",
      amount: 20000,
      durationMonths: 84,
      annualRate: 5.0
    }
  ];

  const availableProperties = [
    { name: "Studio à Lyon", price: 130000, rent: 550 },
    { name: "T2 à Marseille", price: 180000, rent: 750 },
    { name: "Immeuble en province", price: 300000, rent: 2000 },
    { name: "T3 à Lille", price: 220000, rent: 950 },
    { name: "Maison à Rennes", price: 280000, rent: 1150 },
    { name: "Appartement à Paris (20m²)", price: 250000, rent: 1000 },
    { name: "Studio à Montpellier", price: 110000, rent: 520 },
    { name: "T2 à Strasbourg", price: 170000, rent: 700 },
    { name: "Maison en périphérie de Toulouse", price: 240000, rent: 1000 },
    { name: "Immeuble à Saint-Étienne", price: 220000, rent: 1800 }
  ];
  let ownedProperties = [];

  const initialWorkLeft = 10;

  let month = 1;
  let cash = 1000;
  let savings = 0;
  let workLeft = initialWorkLeft;

  let salaryPerWork = 0;

  const transferAmountSmall = 500;
  const annualExpenses = 700;
  const interestRate = 0.07;
  const workingDivider = 6;


  let message = "";

  function chooseJob(jobName) {
    switch (jobName) {
      case "Caissier":
        salaryPerWork = 1200/workingDivider;
        isJobChoosen = true;
        break;

      case "Commercial":
        salaryPerWork = 2100/workingDivider;
        isJobChoosen = true;
        break;

      case "Expert_Financier":
        salaryPerWork = 3500/workingDivider;
        isJobChoosen = true;
        break;

      case "Jeff_Bezos":
        salaryPerWork = 900000/workingDivider;
        isJobChoosen = true;
        break;
    
      default:
        break;
    }
  }

  function work() {
    if (workLeft > 0) {
      cash += salaryPerWork;
      workLeft--;
      message = `Tu as travaillé et gagné ${salaryPerWork} €.`;
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

  function buy(property) {
    if (savings >= property.price) {
      savings -= property.price;
      ownedProperties = [...ownedProperties, property];
    } else {
      alert("Pas assez d'argent !");
    }
  }

  function nextMonth() {
    month += 1;

    // Dépenses
    let totalLoanPayment = 0;
    loans = loans.map(loan => {
      if (loan.remainingMonths > 0) {
        cash -= loan.monthlyPayment;
        loan.remainingMonths--;
        loan.remainingCapital -= (loan.monthlyPayment - loan.remainingCapital * (loan.annualRate / 12 / 100));
        totalLoanPayment += loan.monthlyPayment;
      }
      return loan;
    });

    // Revenu passif
    let totalRent = ownedProperties.reduce((sum, p) => sum + p.rent, 0);
    cash += totalRent;


    // Intérêts
    let interest = 0;
    if (month%12 == 0) {
      interest = savings * interestRate;
      cash += interest;
    }

    cash -= annualExpenses;

    if (cash < 0) {
      const difference = 0 - cash;
      savings -= difference; // déduire le loyer dans l'épargne
      cash += difference; // remise à zéro

      if (savings - difference < 0) {
        message = "💀 Tu es ruiné !";
        return;
      }
      
    }

    workLeft = initialWorkLeft;
    
    message = `Nouveau mois. Dépenses : ${annualExpenses} €, intérêts : ${interest.toFixed(2)} €`;
    if (totalRent > 0) {
      message += `. Revenu locatif : ${totalRent} €`;
    }
    
    message += '.';

  }

  let loans = []; // liste des prêts contractés

  function createLoan(amount, durationMonths, annualRate) {
    const monthlyRate = annualRate / 12 / 100;
    const monthlyPayment = amount * monthlyRate / (1 - Math.pow(1 + monthlyRate, -durationMonths));
    
    const newLoan = {
      id: crypto.randomUUID(), // ou juste un compteur
      amount,
      durationMonths,
      annualRate,
      monthlyPayment,
      remainingMonths: durationMonths,
      remainingCapital: amount
    };
    loans = [...loans, newLoan];
    savings += amount;
  }

  function calculateMonthlyExpenses() {
    let loanExpenses = loans.reduce((sum, loan) => {
      return loan.remainingMonths > 0 ? sum + loan.monthlyPayment : sum;
    }, 0);
    return loanExpenses.toFixed(2);
  }


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

{#if currentPage === 'menu'}
  <h1>🏦 Menu principal</h1>
  <button on:click={() => currentPage = 'game'}>🎮 Lancer le jeu éducatif</button>
  <button on:click={() => currentPage = 'simulator'}>📈 Simulateur d’épargne</button>
  <button on:click={() => currentPage = 'freedom'}>🗽 Liberté Financière</button>
{/if}

{#if currentPage === 'game'}
  <Game></Game>
{/if}

<!-- SIMULATEUR EPARGNE-->
{#if currentPage === 'simulator'}
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
    <button type="button" on:click={() => currentPage = 'menu'}>Retour au menu</button>
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
{/if}

{#if currentPage === 'freedom'}
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
    <button type="button" on:click={() => currentPage = 'menu'}>Retour au menu</button>
  </form>

  {#if requiredMonthlySaving > 0}
    <p>🎯 Pour générer <strong>{targetPassiveIncome} €</strong>/mois à partir de <strong>{retirementYear}</strong>,</p>
    <p>tu devras épargner <strong>{requiredMonthlySaving.toFixed(2)} €</strong>/mois à partir de maintenant.</p>
    <p>Objectif : constituer un capital de <strong>{capitalTarget.toLocaleString()} €</strong>.</p>
    <p>(Avec un rendement net de 5.6 %/an après flat tax)</p>
  {/if}
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





