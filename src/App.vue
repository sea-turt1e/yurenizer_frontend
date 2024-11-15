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

    <div class="option-heading">
      <h4>(0) 品詞</h4>
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
    <div class="option-heading">
      <h4>(1) 語彙を統一</h4>
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
    <div class="option-heading">
      <h4>(2) 1の段階（語形）を統一</h4>
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
    <div class="option-heading">
      <h4>(3) 2の段階（略語・略称）を統一</h4>
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

  
  <div class="optionExplain">
    <h3>※オプションの説明</h3>
      <p>（お急ぎの方は、デフォルト設定のままご利用ください）</p>

      <p>このライブラリは、文章中の表記の揺れを自動で統一します。</p>

      <h4>表記揺れの例：</h4>
      <ul>
          <li>「PC」と「パソコン」と「コンピュータ」</li>
          <li>「いちご」と「イチゴ」と「苺」</li>
          <li>「とうきょう」と「東京」と「TOKYO」</li>
      </ul>

      <h4>統一レベルの選択</h4>
      <p>どこまで表記を統一するか、以下の3段階から選べます：</p>

      <div class="level-explanation">
          <h5>1. 基本レベル（語彙）</h5>
          <ul>
              <li>外来語、別称、旧称、誤用を基本的な表記に統一</li>
              <li>例：「コンピューター」→「コンピュータ」</li>
              <li>例：「とうきょう」→「東京」</li>
          </ul>

          <h5>2. 中間レベル（語形）</h5>
          <ul>
              <li>アルファベットの略語、その他の略語を正式名称に統一</li>
              <li>例：「パソコン」→「コンピュータ」</li>
              <li>例：「東京都」→「東京」</li>
          </ul>

          <h5>3. 詳細レベル（略語・略称）</h5>
          <ul>
              <li>アルファベット表記、異表記、誤字を統一</li>
              <li>例：「PC」→「コンピュータ」</li>
              <li>例：「TOKYO」→「東京」</li>
          </ul>
      </div>

      <h4>品詞の選択について</h4>
      <p>統一したい品詞（名詞、動詞など）を選択できます。<br>
      ※ 選択しない場合は表記の統一は行われません。</p>

      <p>※1 イメージとしては以下のようなツリー構造になっています。</p>
          <!-- 画像貼り付け -->
    <img src="../public/normalize_tree.png" alt="normalize_tree" width="100%">
    <p>※2 語彙、語形、略語・略称などは<a href="https://github.com/WorksApplications/SudachiDict/blob/develop/docs/synonyms.md" target="_blank" rel="noopener noreferrer">Sudachi同義語辞書</a>に基づいています。詳しくはそちらをご覧ください。</p>
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
      { value: 'lexeme', label: '(1) 語彙' },
      { value: 'wordForm', label: '(2) 語形' },
      { value: 'abbreviation', label: '(3) 略語・略称' },
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

.option-heading {
  margin-top: 0px;
  margin-bottom: 0px;
  text-align: left;
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
.optionExplain {
  margin-top: 20px;
  padding: 10px;
  text-align: left;
}
</style>