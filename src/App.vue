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
          'Bearer eyJjdHkiOiJqd3QiLCJlbmMiOiJBMjU2Q0JDLUhTNTEyIiwiYWxnIjoiUlNBLU9BRVAtMjU2In0.p4ivBePSONV9oB3TrzXsJaZvT_ysUjBtJHK9t7e9Jti9BHxSS5W_eRrxZRNJpKUUHcfVHK5mwfqnRR-zRD6PtoGaDbOPhYu3RZ8Z-oRHCjmvoay0erzX6-JqcDNBx2SM_Yj0sTcee79Nfo8a2lAbJcMGBY-OluVqKvtGXdjYoJinYaujD0ODD_H-D_hZ4RjTISiT3J10hMdAXwIAhZols6xBK6NG7KEKxaCIkUexWC2FkZF6ExRab41nbC6YjZ0vn68G358V_E2m5hXGt9bUxGZDW9I-m2Zza9GfL7Nax9RPH4LpcX8PwDoDi2--fxPUcr94P_2lrQQUcdD0BMHBSg.fN8b7tIhtQDwWr0wVfk_Qw.p1D6V6VGUKW-MD_0zFED631qhhwWKDxjjQqFMpmY2F2DbPKSSTPJjTab--SB6anyRxObAqZVXi6WIPAYY3PSI-5mDWvQkGN7MUE9Dvqa5Ep32QrK01TDkkvyPl9gxGrS6AGnZ5gB6bCGF8Gc3Ae_aakpaSqBIKCrffeMDbaQ0q-cDQ4Iu-XJjII78RCUoBlNdXaek1CyVYk-jXIXsgpkK_2zjHbGVNYXLWTc7zXenJvKSiI3o6VMwVr8Jibh4X7fYB_mKBNwTGk9voHW43LpCQ14ORkyFO3iT0b-nHgXtxtiFOrETpBvvXYqKvXMvg_T9OOWugk5nmOzHJNyyE6HcfokiwRrzZ1_L63RXALyKBjDHZ26EGo6O7-gc-ZdBH3Ltcp-la0vzbQhALd9arK4rMjBUeJj711WZvQv1tW5cDfZEO2aZgo4jgJwgkyGyfGxmpsBqEqyoOlB4mCvpnahMx4JMGOZIzxjvcQOAPvkwvZ1qhUALPJ9Lm8y_p9TRZj2uOcJCvFz51URESMU06HoxZEqOnO_OhtRUqByJIqWacj7X5oEwX9vmXywSUK9HGEivh1AKY1lgV31qiqHUQ-kWmP9dm-B5lS-nDROXvzxD8Zpy5dwrmONQ6tqj3LFhp547banXnZPjsXdTS5Ias-cIU0XDoH8vdJbNEXhGpu6f8Rdr225svjF9iwN2Xjlx1cHsPeOld5jkk2BlzfQp9gcmje9u7El8v41cp_vtyowV1Q.4NrG9v47FWfLs-1si4N8Yf9I94zGGa-4p0ZPc0lcQBQ',
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
        update_interval: 0,
      });

      try {
        const response = await fetch(url, {
          mode: 'no-cors',
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
