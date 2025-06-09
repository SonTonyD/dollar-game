<script>
  let isJobChoosen = false;

  const availableProperties = [
    { name: "Studio à Lyon", price: 30000, rent: 300 },
    { name: "T2 à Marseille", price: 50000, rent: 600 },
    { name: "Immeuble en province", price: 100000, rent: 1400 }
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

  function nextYear() {
    month += 1;

    // Dépenses
    

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
  <p>🧾 Dépenses mensuelles : {annualExpenses} €</p>
  <p>🛠️ Travail restant : {workLeft} / {initialWorkLeft}</p>
  <p>🏠 Propriété : {ownedProperties.length > 0 ? 'Oui ✅' : 'Non ❌'}</p>

  <div>
    <button on:click={work} disabled={workLeft === 0}>👷 Travailler (+ {salaryPerWork.toFixed(2)} €)</button>
    <button on:click={() => transfer(transferAmountSmall)}>📥 Transférer vers l’épargne ( { transferAmountSmall } €)</button>
    <button on:click={nextYear}>⏭️ Nouveau mois </button>
    
    <h2>🏠 Biens disponibles :</h2>
    <ul>
      {#each availableProperties as property}
        <li>
          <strong>{property.name}</strong> – Prix : {property.price} € – Loyer mensuel : {property.rent} €
          <button on:click={() => buy(property)}>Acheter</button>
        </li>
      {/each}
    </ul>
  </div>
  <h2>📦 Biens possédés :</h2>
  <ul>
    {#each ownedProperties as property}
      <li> <strong> {property.name} </strong>  ({property.rent} €/mois)</li>
    {/each}
  </ul>


  <p>{message}</p>
{/if}

