// FormComponent.vue
<template>
  <!-- タイトルを表示 -->
  <h1>Yurenizer</h1>
  <h2>表記揺れ解消ツール</h2>
  <div class="form-container">
    <div class="unify-options-group">
      <p>統一レベル</p>
      <select 
        v-model="selectedUnifyLevel" 
        class="select-input"
      >
        <option value="" disabled selected>選択してください</option>
        <option 
          v-for="option in unifyLevel" 
          :key="option.value" 
          :value="option.value"
        >
          {{ option.label }}
        </option>
      </select>
    </div>

    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isTaigen"
          class="checkbox-input"
        />
        体言
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isYougen"
          class="checkbox-input"
        />
        用言
      </label>
    </div>
    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isOtherLanguage"
          class="checkbox-input"
        />
        外国語
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isAlias"
          class="checkbox-input"
        />
        別称
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isOldName"
          class="checkbox-input"
        />
        旧称
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isMisuse"
          class="checkbox-input"
        />
        誤用
      </label>
    </div>
    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isAlfabeticAbbreviation"
          class="checkbox-input"
        />
        アルファベットの略語
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isJapaneseAbbreviation"
          class="checkbox-input"
        />
        アルファベット以外の略語
      </label>
    </div>
    <div class="checkbox-group">
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isAlphabet"
          class="checkbox-input"
        />
        アルファベット
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isOrthographicVariation"
          class="checkbox-input"
        />
        異表記
      </label>
      <label class="checkbox-label">
        <input
          type="checkbox"
          v-model="flgOptions.isMisspeling"
          class="checkbox-input"
        />
        誤字
      </label>
    </div>


    <div class="input-group">
      <label for="text-input"></label>
      <textarea
        id="text-input"
        v-model="inputText"
        type="text"
        class="text-input"
        placeholder="表記揺れを解消したいテキストを入力してください"
      ></textarea>
    </div>



    <button
      @click="handleSubmit"
      :disabled="isLoading"
      class="submit-button"
    >
      {{ isLoading ? '送信中...' : '送信' }}
    </button>

    <p v-if="error" class="error-message">{{ error }}</p>
  </div>

  <div class="returnText">
    <p v-if="responseMessage">{{ responseMessage }}</p>
    <p v-else></p>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import axios from 'axios'

interface Option {
  value: string
  label: string
}


export default defineComponent({
  name: 'FormComponent',
  setup() {
    const inputText = ref('')
    const selectedUnifyLevel = ref('lexeme')
    const isLoading = ref(false)
    const error = ref('')
    const flgOptions = ref({
      isTaigen: true,
      isYougen: false,
      isOtherLanguage: true,
      isAlias: true,
      isOldName: true,
      isMisuse: true,
      isAlfabeticAbbreviation: true,
      isJapaneseAbbreviation: true,
      isAlphabet: true,
      isOrthographicVariation: true,
      isMisspeling: true,
    });
    const responseMessage = ref('')

    const unifyLevel: Option[] = [
      { value: 'lexeme', label: '語彙' },
      { value: 'wordForm', label: '語形' },
      { value: 'abbreviation', label: '略語・略称' },
    ]
    

    const handleSubmit = async () => {
      if (!inputText.value) {
        error.value = 'テキストを入力してください'
        return
      }

      if (!selectedUnifyLevel.value) {
        error.value = 'オプションを選択してください'
        return
      }

      // handleSubmit 関数を以下のように修正
      try {
        isLoading.value = true
        error.value = ''

        // URLSearchParams を使用してクエリパラメータを構築
        const params = new URLSearchParams({
          text: inputText.value,
          unify_level: selectedUnifyLevel.value
        })

        // GET リクエストに変更
        const response = await axios.get(
          `http://127.0.0.1:8000/normalize_text?${params.toString()}`,
          {
            headers: {
              'accept': 'application/json'
            }
          }
        )

        console.log('送信成功:', response.data)
        // 成功時の処理をここに追加
        responseMessage.value = response.data.text


      } catch (e) {
        error.value = '送信に失敗しました。もう一度お試しください。'
        console.error('エラー:', e)
      } finally {
        isLoading.value = false
      }
    }

    return {
      inputText,
      unifyLevel,
      flgOptions,
      selectedUnifyLevel,
      isLoading,
      error,
      handleSubmit,
      responseMessage,
    }
  }
})
</script>

<style scoped>
.form-container {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
}

.input-group {
  margin-bottom: 20px;
}

.select-input {
  width: 200%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
  cursor: pointer;
}

.select-input:focus {
  outline: none;
  border-color: #4CAF50;
}

.text-input {
  width: 100%;
  min-height: 100px;     /* 最小の高さを設定 */
  padding: 10px;         /* パディングを調整 */
  font-size: 16px;
  margin-top: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  resize: vertical;      /* 垂直方向のリサイズのみ許可 */
  vertical-align: top;   /* テキストを上から開始 */
  line-height: 1.5;      /* 行間を調整 */
}

.unify-options-group {
  margin-bottom: 20px;
  display: grid;
  grid-template-columns: repeat(5, 1fr); /* 5つのオプションを横一列に */
  gap: 10px; /* 項目間の間隔 */
}

.radio-item {
  display: flex;
  align-items: center;
  justify-content: center; /* 中央揃え */
  margin: 5px 0;
  padding: 5px;
}

.radio-item input[type="radio"] {
  margin-right: 8px;
}

.submit-button {
  background-color: #4CAF50;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.submit-button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.error-message {
  color: red;
  margin-top: 10px;
}

.checkbox-group {
  margin: 20px 0;
  display: flex;
  gap: 20px;
}

.checkbox-label {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.checkbox-input {
  margin-right: 8px;
}

/* ただのテキスト表示 */
.returnText {
  margin-top: 20px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f9f9f9;
}
</style>