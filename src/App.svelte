<script lang="ts">
  import { onMount } from 'svelte';
  import Select from 'svelte-select';
  import html2canvas from 'html2canvas';
  import AlfaBankCard from './assets/img/alfa-bank.png';
  import TinkoffBankCard from './assets/img/tinkoff-bank.png';
  import YandexBankCard from './assets/img/ya-pay.png';

  const bankCards = ['Т-банк', 'Альфа банк', 'Яндекс Пэй'];
  const categories = [
    '➗ Все покупки',
    '🚗 Автосервис и товары для авто',
    '💊 Аптека',
    '🚌 Городской транспорт',
    '🛠️ Для ремонта и декор',
    '🚆 Ж/д билеты',
    '🥐 Кафе, бары и рестораны',
    '🎭 Культура и искусство',
    '🎓 Образование',
    '👕 Одежда и обувь',
    '🎠 Развлечения',
    '🛒 Супермаркеты',
    '🚕 Такси',
    '🍔 Фастфуд',
    '💐 Цветы',
    'Билайн услуги',
  ];
  const monthNames = [
    'Январь',
    'Февраль',
    'Март',
    'Апрель',
    'Май',
    'Июнь',
    'Июль',
    'Август',
    'Сентябрь',
    'Октябрь',
    'Ноябрь',
    'Декабрь',
  ];

  let warning: string | null = null;
  let chosenBank: any = null;
  let chosenCategories: any[] = [];
  let tBankCats: any[] = [];
  let alfaCats: any[] = [];
  let yandexCats: any[] = [];

  let targetElement;

  const takeScreenshot = async () => {
    if (targetElement) {
      try {
        const canvas = await html2canvas(targetElement, {
          useCORS: true,
          allowTaint: false,
          scale: 2, // Higher quality
          backgroundColor: null, // Transparent background
        });

        // Convert to blob and download
        canvas.toBlob((blob) => {
          const now = new Date();
          const monthNumber = now.getMonth();
          const year = now.getFullYear();
          const url = URL.createObjectURL(blob);
          const a = document.createElement('a');
          a.href = url;
          a.download = `${monthNames[monthNumber]}_${year}.png`;
          a.click();
          URL.revokeObjectURL(url);
        });
      } catch (error) {
        console.error('Screenshot failed:', error);
      }
    }
  };

  onMount(() => {
    if (typeof localStorage !== 'undefined') {
      try {
        const savedData = localStorage.getItem('bankCashbackData');
        if (savedData) {
          const parsed = JSON.parse(savedData);
          tBankCats = parsed.tBankCats || [];
          alfaCats = parsed.alfaCats || [];
          yandexCats = parsed.yandexCats || [];
        }
      } catch (error) {
        console.error('Error loading from localStorage:', error);
      }
    }
  });

  const saveToLocalStorage = () => {
    if (typeof localStorage !== 'undefined') {
      try {
        const dataToSave = {
          tBankCats,
          alfaCats,
          yandexCats,
          lastUpdated: new Date().toISOString(),
        };
        localStorage.setItem('bankCashbackData', JSON.stringify(dataToSave));
      } catch (error) {
        console.error('Error saving to localStorage:', error);
      }
    }
  };

  const handleWarning = (msg: string | null) => (warning = msg);

  const clearSelects = () => {
    chosenBank = null;
    chosenCategories = [];
  };

  const cleanup = () => {
    tBankCats = [];
    alfaCats = [];
    yandexCats = [];
    saveToLocalStorage();
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

    saveToLocalStorage();
    handleWarning(null);
    clearSelects();
  };

  /* const sortCategories = () => {
    if (chosenCategories && chosenCategories.length > 0) {
      chosenCategories = chosenCategories.sort((a, b) => {
        const nameA = typeof a === 'string' ? a : a.value || a.label || a;
        const nameB = typeof b === 'string' ? b : b.value || b.label || b;
        return nameA.localeCompare(nameB);
      });
    }
  }; */
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
          />
          <button class="button" type="button" on:click="{handleSaveBtn}"
            >Добавить</button
          >
          <button
            class="button button--secondary"
            type="button"
            on:click="{() => cleanup()}"
          >
            Очистить
          </button>
        </div>
        <div
          class="cashback__cards screenshot-area"
          bind:this="{targetElement}"
        >
          <div class="cashback__card">
            <img
              class="cashback__card-image"
              src="{TinkoffBankCard}"
              alt="Карта 'Тинькофф банка'"
              title="Т-банк"
            />
            <div class="cashback__card-categories">
              {#each tBankCats as category}
                <div class="category-tag">{category.value}</div>
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
                <div class="category-tag">{category.value}</div>
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
                <div class="category-tag">{category.value}</div>
              {/each}
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <button class="button mt-24" on:click="{takeScreenshot}"
    >📷 Сделать скриншот</button
  >
</main>
<footer></footer>
