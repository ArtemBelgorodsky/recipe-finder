<template>
  <div
    class="min-h-screen bg-gray-100 flex items-center justify-center py-12 px-4 sm:px-6 lg:px-8"
  >
    <div class="max-w-md w-full space-y-8">
      <div>
        <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
          Поиск рецептов
        </h2>
        <p class="mt-2 text-center text-sm text-gray-600">
          Введите ингредиенты через запятую
        </p>
      </div>
      <form class="mt-8 space-y-6" @submit.prevent="generateRecipes">
        <div class="rounded-md shadow-sm -space-y-px">
          <div>
            <label for="ingredients" class="sr-only">Ингредиенты</label>
            <input
              id="ingredients"
              v-model="ingredients"
              name="ingredients"
              type="text"
              required
              class="appearance-none rounded-none relative block w-full px-3 py-2 border border-gray-300 placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 focus:z-10 sm:text-sm"
              placeholder="Ингредиенты"
            />
          </div>
        </div>
        <div>
          <button
            type="submit"
            :disabled="loading"
            class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
            :class="{ 'cursor-not-allowed': loading, 'opacity-50': loading }"
          >
            <span
              v-if="loading"
              class="absolute left-0 inset-y-0 flex items-center pl-3"
            >
              <svg
                class="animate-spin h-5 w-5 text-white"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
              >
                <circle
                  class="opacity-25"
                  cx="12"
                  cy="12"
                  r="10"
                  stroke="currentColor"
                  stroke-width="4"
                ></circle>
                <path
                  class="opacity-75"
                  fill="currentColor"
                  d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                ></path>
              </svg>
            </span>
            <span v-if="loading">Загрузка...</span>
            <span v-else>Найти рецепты</span>
          </button>
        </div>
      </form>
      <div v-if="recipes.length" class="mt-8 space-y-6">
        <div
          v-for="(recipe, index) in recipes"
          :key="index"
          class="bg-white shadow overflow-hidden sm:rounded-lg"
        >
          <div class="px-4 py-5 sm:px-6">
            <h3 class="text-lg leading-6 font-medium text-gray-900">
              {{ recipe.title }}
            </h3>
            <p class="mt-1 max-w-2xl text-sm text-gray-500">
              {{ recipe.ingredients.join(', ') }}
            </p>
          </div>
          <div class="border-t border-gray-200">
            <dl>
              <div
                class="bg-gray-50 px-4 py-5 sm:grid sm:grid-cols-3 sm:gap-4 sm:px-6"
              >
                <dt class="text-sm font-medium text-gray-500">Инструкции</dt>
                <dd class="mt-1 text-sm text-gray-900 sm:mt-0 sm:col-span-2">
                  {{ recipe.instructions }}
                </dd>
              </div>
            </dl>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      ingredients: '',
      recipes: [],
      loading: false,
    };
  },
  methods: {
    async generateRecipes() {
      if (!this.ingredients) {
        alert('Пожалуйста, введите ингредиенты.');
        return;
      }

      this.loading = true;
      this.recipes = [];

      const url =
        'https://gigachat.devices.sberbank.ru/api/v1/chat/completions';
      const headers = {
        'Content-Type': 'application/json',
        Accept: 'application/json',
        Authorization:
          'Bearer eyJjdHkiOiJqd3QiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwiYWxnIjoiUlNBLU9BRVAtMjU2In0.rLINxoiYqOQw_UPd92PCfGcLirLi9aKlneJgfCxgQSnYQCaFAd7tNmLOhdNiZ8GFhMJNvrSW5mXtCP9F2pOc23DAPcoS3N7FCCJAXzRwyeFA1Aw6v1abaSZ_XrcRovi1szhN0QxGuucYFscEgaELMOQ07jWR5SLupeSS_dC7m1no2XuF5_zamfrvfPG7ib-n_fSfILTs9kfRBsZZ1--kmxKPceyN1UhNGyrzsNjkidFGcLaGEZundRsbHy_KRrnUToiQ76etE11thV7Z6FlEZ0STEllNJMhmeH04N8FIgrCtgcV6fEiVH_epEyS7pqFItl241H93k5BCASmIQ0m9WA.H2URUKrQE3XOkzsJwgyvnA.19ft4BZS5YRP93eEGUwrFk1m4nt9TbFoE5s609ufDUJWW0yQ4kjAqGVTxSlewWL13m_Ifv4iUQ0Tg5r-VF_CYGYGKIzZ6coEBq1wyPpE-KW4oVt0yMCX7yjkQ3rNWb1xX5UuuNmkb9--DH_r360ABFUhoPOwZquh4ipSqT3WSDTwJRYz00TLvf2Ojd_g1apUopwxmQtonXj5b82cEqM7s0PyEXH8uMBtTltgUCXx7qTpP6bzPOHr_tOnj_elSLGjGTSsD0VJSeSobQm0d1x13qKQzzQiM_xDxUdeU0IWfFWraSzjxui6gYNxIqBOr4bslFOl8mQid9g-2niTUX1l1R8N_ltXzQtEkUc5M32AxnK_o4JUNtE3hB8EwK-EemfsWRZZ4T9isIjAx51fmawiNNt6oUKStmRQ6Jw3sM9wIgw7lrw2G2WuaGg30SajCDKC0NjxxX5gEDo8dF5YbI8IDpF81OIvLHd0HLzXW6o2zEZQ2tJp5TK_7Ea9wlOS3Ycg0JqQFcvJhR_X1rfC6RRFJEUwTkWY5oX1WozN9uzyUlxTcDRAd58FJNQThC4iEEdlB-9v5y8JvoPv_zluiRWBV15Q__EHWxku0GynG5_y4S2Rs738CqE-KwFp7IBWrnvGgEbK2DuFXaNEoPQb8-a5e8X4rYQ_OuhImtLkP7NoO403OsnbKftgRFV08pHGY6qBRurS-wQUrirNTHEFQPl6MP2t3VMsgtlRJQRaseKn030.OzKh5G1xohdS4m3oC4f5MdRjoFOf2jv7v9g0-wrwhXI',
      };

      const body = JSON.stringify({
        model: 'GigaChat',
        messages: [
          {
            role: 'system',
            content: 'Ты профессиональный повар-помощник.',
          },
          {
            role: 'user',
            content: `Предложи рецепты с использованием следующих ингредиентов: ${this.ingredients}. Каждый рецепт должен включать название, список ингредиентов и инструкции по приготовлению.`,
          },
        ],
        stream: false,
      });

      try {
        const response = await fetch(url, {
          method: 'POST',
          headers: headers,
          body: body,
        });

        if (!response.ok) {
          throw new Error('Ошибка при запросе к API');
        }

        const data = await response.json();
        const recipesText = data.choices[0].message.content;
        const recipesArray = recipesText.split('\n\n').map((recipe) => {
          const [title, ingredients, ...instructions] = recipe.split('\n');
          return {
            title: title.replace('Название: ', ''),
            ingredients: ingredients.replace('Ингредиенты: ', '').split(', '),
            instructions: instructions.join('\n').replace('Инструкции: ', ''),
          };
        });

        this.recipes = recipesArray;
      } catch (error) {
        console.error('Ошибка при запросе к API:', error);
        alert('Произошла ошибка при запросе к API.');
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>
