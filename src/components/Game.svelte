<script>
  import Accordion from './Accordion.svelte';

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
</script>

{#if !isJobChoosen}
  <h1> Choix du métier : </h1>
  <button on:click={() => chooseJob("Caissier")}>🧾 Caissier</button>
  <button on:click={() => chooseJob("Commercial")}>💼 Commercial</button>
  <button on:click={() => chooseJob("Expert_Financier")}>📊 Expert Financier</button>
  <button on:click={() => chooseJob("Jeff_Bezos")}>Jeff Bezos</button>
{/if}



{#if isJobChoosen}
  <h1>💰 Jeu éducatif : Intérêts Composés</h1>

  <p>Mois : {month}</p>
  <p>💵 Compte courant : {cash.toFixed(2)} €</p>
  <p>🏦 Compte épargne : {savings.toFixed(2)} € - Intérêts prévisionnels : {(savings * interestRate).toFixed(2)}</p>
  <p>🧾 Dépenses fixes : {annualExpenses} € de loyer et {calculateMonthlyExpenses()} € de mensualités</p>
  <p>📥 Revenus locatifs : {ownedProperties.reduce((sum, p) => sum + p.rent, 0)} € / mois</p>
  <p>🛠️ Travail restant : {workLeft} / {initialWorkLeft}</p>

  <div>
    <button on:click={work} disabled={workLeft === 0}>👷 Travailler (+ {salaryPerWork.toFixed(2)} €)</button>
    <button on:click={() => transfer(transferAmountSmall)}>📥 Transférer vers l’épargne ( { transferAmountSmall } €)</button>
    <button on:click={nextMonth}>⏭️ Nouveau mois </button>
    
    <Accordion title="💸 Emprunter de l'argent">
      {#each loanTemplates as loan}
        <button on:click={() => createLoan(loan.amount, loan.durationMonths, loan.annualRate)}>
          {loan.name} – {loan.annualRate}% sur {loan.durationMonths / 12} ans
        </button>
      {/each}
    </Accordion>

    <Accordion title="💳 Emprunts en cours">
      {#if loans.length > 0}
        <ul>
          {#each loans as loan}
            <li>
              <strong>{loan.amount.toLocaleString()} €</strong> – {loan.annualRate}% sur {loan.durationMonths} mois<br>
              💸 Mensualité : {loan.monthlyPayment.toFixed(2)} € – Capital restant : {loan.remainingCapital.toFixed(2)} € – Mois restants : {loan.remainingMonths}
            </li>
          {/each}
        </ul>
      {:else}
        <p>Aucun emprunt contracté.</p>
      {/if}
    </Accordion>

    <Accordion title="🏠 Biens disponibles">
      <ul>
        {#each availableProperties as property}
          <li>
            <strong>{property.name}</strong> – Prix : {property.price} € – Loyer mensuel : {property.rent} €
            <button on:click={() => buy(property)}>Acheter</button>
          </li>
        {/each}
      </ul>
    </Accordion>
  </div>
  <Accordion title="📦 Biens possédés">
    {#if ownedProperties.length > 0}
      <ul>
        {#each ownedProperties as property}
          <li><strong>{property.name}</strong> ({property.rent} €/mois)</li>
        {/each}
      </ul>
    {:else}
      <p>Aucune propriété achetée pour l’instant.</p>
    {/if}
  </Accordion>
  <p>{message}</p>
{/if}