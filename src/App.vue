// FormComponent.vue
<template>
  <!-- タイトルを表示 -->
  <h1>Yurenizer</h1>
  <h2>表記揺れ解消ツール</h2>
  <div class="form-container">
    <div class="unify-options-group">
      <p>統一レベル</p>
      <select 
        v-model="flgOptions.selectedUnifyLevel" 
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
          v-model="flgOptions.isNonAlfabeticAbbreviation"
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
import { defineComponent, ref, reactive } from 'vue'
import axios from 'axios'

interface Option {
  value: string
  label: string
}


export default defineComponent({
  name: 'FormComponent',
  setup() {
    const inputText = ref('')
    // const selectedUnifyLevel = ref('lexeme')
    const isLoading = ref(false)
    const error = ref('')
    const flgOptions = reactive({
      selectedUnifyLevel: 'lexeme',
      isTaigen: true,
      isYougen: false,
      isOtherLanguage: true,
      isAlias: true,
      isOldName: true,
      isMisuse: true,
      isAlfabeticAbbreviation: true,
      isNonAlfabeticAbbreviation: true,
      isAlphabet: true,
      isOrthographicVariation: true,
      isMisspeling: true,
    })
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

      if (!flgOptions.selectedUnifyLevel) {
        error.value = 'オプションを選択してください'
        return
      }

      try {
        isLoading.value = true
        error.value = ''

        const requestBody = {
          config:{
            unify_level: flgOptions.selectedUnifyLevel,
            taigen: flgOptions.isTaigen,
            yougen: flgOptions.isYougen,
            expansion: "from_another",
            other_language: flgOptions.isOtherLanguage,
            alias: flgOptions.isAlias,
            old_name: flgOptions.isOldName,
            misuse: flgOptions.isMisuse,
            alphabetic_abbreviation: flgOptions.isAlfabeticAbbreviation,
            non_alphabetic_abbreviation: flgOptions.isNonAlfabeticAbbreviation,
            alphabet: flgOptions.isAlphabet,
            orthographic_variation: flgOptions.isOrthographicVariation,
            misspeling: flgOptions.isMisspeling,
            }
        }
        console.log('送信直前の値:', inputText.value)
        console.log('送信直前の値:', requestBody)

        const response = await axios.post(
        'http://127.0.0.1:8000/normalize_text?text=' + inputText.value,
        requestBody,        
        {
          headers: {
            'accept': 'application/json',
            'Content-Type': 'application/json'
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

/* 共通のコンテナ幅を設定 */
.text-input, .returnText {
  width: 100%;
  max-width: 800px;  /* 必要に応じて調整 */
  margin-left: auto;
  margin-right: auto;
  box-sizing: border-box; /* padding を width に含める */
}

.text-input {
  min-height: 100px;
  padding: 10px;
  font-size: 16px;
  margin-top: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  resize: vertical;
  vertical-align: top;
  line-height: 1.5;
}

.returnText {
  /* margin-top: 20px; */
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: #f9f9f9;
  text-align: left;
  line-height: 1.5;
  min-height: 100px;
  display: block;
}
/* returnText内のpタグも調整 */
.returnText p {
  margin: 0;            /* デフォルトのマージンを削除 */
  padding: 0;           /* デフォルトのパディングを削除 */
  text-align: left;     /* 左揃えに設定 */
}
</style>