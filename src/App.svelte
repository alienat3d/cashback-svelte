<script lang="ts">
  import Select from 'svelte-select';
  import AlfaBankCard from './assets/img/alfa-bank.png';
  import TinkoffBankCard from './assets/img/tinkoff-bank.png';
  import YandexBankCard from './assets/img/ya-pay.png';

  const bankCards = ['Т-банк', 'Альфа банк', 'Яндекс Пэй'];
  const categories = [
    'Все покупки',
    'Супермаркеты',
    'Кафе, бары и рестораны',
    'Фастфуд',
    'Городской транспорт',
    'Ж/д билеты',
    'Развлечения',
    'Такси',
    'Образование',
    'Культура и искусство',
    'Одежда и обувь',
    'Здоровье',
    'Цветы',
    'Автосервис и товары для авто',
    'Для ремонта и декор',
    'Аптека',
    'Билайн услуги',
  ];
  categories.sort();

  $: warning = null;

  let chosenBank: any = null;
  let chosenCategories: any[] = [];
  let tBankCats: any[] = [];
  let alfaCats: any[] = [];
  let yandexCats: any[] = [];

  const handleWarning = (msg: string | null) => (warning = msg);

  const clearSelects = () => {
    chosenBank = null;
    chosenCategories = [];
  };

  const handleSaveBtn = () => {
    if (!chosenBank) {
      handleWarning('Пожалуйста выберите банк');
      return;
    }

    if (!chosenCategories || chosenCategories.length === 0) {
      handleWarning(
        `Пожалуйста выберите категории кэшбэка для «${chosenBank.label}»`,
      );
      return;
    }

    const bankIdx = chosenBank.index;

    if (bankIdx === 0) {
      tBankCats = [...chosenCategories];
    } else if (bankIdx === 1) {
      alfaCats = [...chosenCategories];
    } else if (bankIdx === 2) {
      yandexCats = [...chosenCategories];
    }

    handleWarning(null);
    clearSelects();
  };

  const handleCategoryChange = () => {
    if (chosenCategories && chosenCategories.length > 0) {
      chosenCategories = chosenCategories.sort((a, b) => {
        const nameA = typeof a === 'string' ? a : a.value || a.label || a;
        const nameB = typeof b === 'string' ? b : b.value || b.label || b;
        return nameA.localeCompare(nameB);
      });
    }
  };
</script>

<header></header>
<main class="mx-0 p-4 text-center">
  <section class="cashback">
    <div class="container">
      <div class="cashback__warning">
        {#if warning !== null}{warning}{/if}
      </div>
      <div class="cashback__inner">
        <div class="cashback__choice-inner">
          <Select
            id="banks"
            items="{bankCards}"
            placeholder="Выберите банк"
            bind:value="{chosenBank}"
            on:click="{() => handleWarning(null)}"
          />
          <Select
            id="categories"
            items="{categories}"
            multiple="{true}"
            closeListOnChange="{false}"
            placeholder="Выберите 1 или более категорий кэшбэка"
            bind:value="{chosenCategories}"
            on:change="{handleCategoryChange}"
          />
          <button class="button" type="button" on:click="{handleSaveBtn}"
            >Добавить</button
          >
        </div>
        <div class="cashback__cards">
          <div class="cashback__card">
            <img
              class="cashback__card-image"
              src="{TinkoffBankCard}"
              alt="Карта 'Тинькофф банка'"
              title="Т-банк"
            />
            <div class="cashback__card-categories">
              {#each tBankCats as category}
                <div class="category-tag">{category.value || category}</div>
              {/each}
            </div>
          </div>
          <div class="cashback__card">
            <img
              class="cashback__card-image"
              src="{AlfaBankCard}"
              alt="Карта 'Альфа банка'"
              title="Альфа банк"
            />
            <div class="cashback__card-categories">
              {#each alfaCats as category}
                <div class="category-tag">{category.value || category}</div>
              {/each}
            </div>
          </div>
          <div class="cashback__card">
            <img
              class="cashback__card-image"
              src="{YandexBankCard}"
              alt="Карта 'Яндекс пэй'"
              title="Яндекс пэй"
            />
            <div class="cashback__card-categories">
              {#each yandexCats as category}
                <div class="category-tag">{category.value || category}</div>
              {/each}
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</main>
<footer></footer>
